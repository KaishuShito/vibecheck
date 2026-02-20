


Webブラウザ上でそのまま動かせる、シンセウェイヴ（Synthwave）風のレトロな3D宇宙船ゲームを作成しました。

**Three.js**を使用した3D描画に加えて、**ポストプロセッシング（UnrealBloomPass）**によるネオンの輝き、パーティクルによる星屑と爆発エフェクトを搭載し、リッチな視覚効果を実現しています。また、マウスの動きに追従して機体が滑らかに傾く（バンキング）など、インタラクションも洗練させています。

以下のコードを `index.html` として保存し、Webブラウザ（ChromeやEdgeなど）で開くだけで遊べます。

### レトロ3Dスペースシューター (`index.html`)

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Retro 3D Space Shooter</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #000;
            font-family: 'Courier New', Courier, monospace;
            user-select: none;
        }
        #ui-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 20px;
            box-sizing: border-box;
            color: #0ff;
            text-shadow: 0 0 10px #0ff;
        }
        #score-display {
            font-size: 24px;
            font-weight: bold;
        }
        #instructions {
            font-size: 16px;
            text-align: center;
            opacity: 0.8;
            margin-bottom: 20px;
        }
        #game-over {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 48px;
            color: #f0f;
            text-shadow: 0 0 20px #f0f;
            display: none;
            text-align: center;
            pointer-events: auto;
        }
        #restart-btn {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 20px;
            background: transparent;
            color: #0ff;
            border: 2px solid #0ff;
            cursor: pointer;
            box-shadow: 0 0 10px #0ff inset, 0 0 10px #0ff;
            text-transform: uppercase;
            font-family: 'Courier New', Courier, monospace;
            transition: 0.3s;
        }
        #restart-btn:hover {
            background: #0ff;
            color: #000;
        }
    </style>
    <!-- Three.jsとそのアドオンをCDNからインポートするための設定 -->
    <script type="importmap">
        {
            "imports": {
                "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
                "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
            }
        }
    </script>
</head>
<body>
    <div id="ui-container">
        <div id="score-display">SCORE: 0</div>
        <div id="instructions">マウスで移動 | クリックで射撃</div>
    </div>
    <div id="game-over">
        <div>SYSTEM FAILURE</div>
        <button id="restart-btn">REBOOT</button>
    </div>

    <script type="module">
        import * as THREE from 'three';
        import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
        import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
        import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';

        // --- 基本設定 ---
        let scene, camera, renderer, composer;
        let ship, gridHelper, stars;
        let obstacles = [];
        let lasers = [];
        let explosions =[];
        let score = 0;
        let isGameOver = false;
        let gameSpeed = 1.0;

        // マウスインタラクション用変数
        let mouseX = 0;
        let mouseY = 0;
        let targetX = 0;
        let targetY = 0;

        // UI要素
        const scoreElement = document.getElementById('score-display');
        const gameOverElement = document.getElementById('game-over');
        const restartBtn = document.getElementById('restart-btn');

        init();
        animate();

        function init() {
            // 1. シーンとカメラ
            scene = new THREE.Scene();
            scene.fog = new THREE.FogExp2(0x000000, 0.015);

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(0, 5, 15);
            camera.lookAt(0, 0, 0);

            // 2. レンダラー
            renderer = new THREE.WebGLRenderer({ antialias: false });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(window.devicePixelRatio);
            document.body.appendChild(renderer.domElement);

            // 3. ポストプロセッシング (ネオン効果 / Bloom)
            const renderScene = new RenderPass(scene, camera);
            const bloomPass = new UnrealBloomPass(new THREE.Vector2(window.innerWidth, window.innerHeight), 1.5, 0.4, 0.85);
            bloomPass.threshold = 0.1;
            bloomPass.strength = 2.0; // 発光の強さ
            bloomPass.radius = 0.5;

            composer = new EffectComposer(renderer);
            composer.addPass(renderScene);
            composer.addPass(bloomPass);

            // 4. オブジェクトの作成
            createEnvironment();
            createShip();

            // 5. イベントリスナー
            window.addEventListener('resize', onWindowResize);
            document.addEventListener('mousemove', onMouseMove);
            document.addEventListener('mousedown', shoot);
            restartBtn.addEventListener('click', restartGame);
        }

        // --- 環境作成 (ワイヤーフレームグリッドと星屑) ---
        function createEnvironment() {
            // レトロなワイヤーフレームグリッド
            gridHelper = new THREE.GridHelper(200, 100, 0xff00ff, 0x440044);
            gridHelper.position.y = -5;
            scene.add(gridHelper);

            // 星屑 (流れるパーティクル)
            const starGeo = new THREE.BufferGeometry();
            const starCount = 2000;
            const starPos = new Float32Array(starCount * 3);
            for(let i=0; i < starCount * 3; i++) {
                starPos = (Math.random() - 0.5) * 200;
            }
            starGeo.setAttribute('position', new THREE.BufferAttribute(starPos, 3));
            const starMat = new THREE.PointsMaterial({ color: 0xffffff, size: 0.2 });
            stars = new THREE.Points(starGeo, starMat);
            scene.add(stars);
        }

        // --- 自機の作成 ---
        function createShip() {
            const geometry = new THREE.ConeGeometry(1, 3, 4);
            geometry.rotateX(Math.PI / 2); // 前を向かせる
            
            const material = new THREE.MeshStandardMaterial({
                color: 0x00ffff,
                emissive: 0x0088ff,
                emissiveIntensity: 0.5,
                wireframe: true
            });
            
            ship = new THREE.Mesh(geometry, material);
            scene.add(ship);

            // ライト
            const ambientLight = new THREE.AmbientLight(0x404040);
            scene.add(ambientLight);
            const pointLight = new THREE.PointLight(0x00ffff, 2, 50);
            pointLight.position.set(0, 2, 5);
            scene.add(pointLight);
        }

        // --- 障害物の生成 ---
        function spawnObstacle() {
            if (isGameOver) return;
            
            // 難易度（スピード）の上昇
            gameSpeed += 0.001;

            const size = Math.random() * 1.5 + 0.5;
            const geometry = new THREE.BoxGeometry(size, size, size);
            const material = new THREE.MeshStandardMaterial({
                color: 0xff00ff,
                emissive: 0xff0055,
                emissiveIntensity: 0.8,
                wireframe: Math.random() > 0.3
            });
            const obstacle = new THREE.Mesh(geometry, material);
            
            // ランダムな位置から迫ってくる
            obstacle.position.set((Math.random() - 0.5) * 40, (Math.random() - 0.5) * 20, -100);
            obstacle.rotation.set(Math.random(), Math.random(), Math.random());
            
            scene.add(obstacle);
            obstacles.push(obstacle);
        }

        // --- 射撃 (レーザー) ---
        function shoot() {
            if (isGameOver) return;

            const geometry = new THREE.CylinderGeometry(0.1, 0.1, 2, 8);
            geometry.rotateX(Math.PI / 2);
            const material = new THREE.MeshBasicMaterial({ color: 0x00ffff });
            const laser = new THREE.Mesh(geometry, material);

            laser.position.copy(ship.position);
            laser.position.z -= 2; // 機体の先端から発射
            
            scene.add(laser);
            lasers.push(laser);
        }

        // --- 爆発エフェクトの生成 ---
        function createExplosion(position) {
            const particleCount = 30;
            const geometry = new THREE.BufferGeometry();
            const positions = new Float32Array(particleCount * 3);
            const velocities =[];

            for (let i = 0; i < particleCount; i++) {
                positions = position.x;
                positions = position.y;
                positions = position.z;

                velocities.push(new THREE.Vector3(
                    (Math.random() - 0.5) * 2,
                    (Math.random() - 0.5) * 2,
                    (Math.random() - 0.5) * 2
                ));
            }

            geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            const material = new THREE.PointsMaterial({ color: 0xffa500, size: 0.5, transparent: true });
            const particleSystem = new THREE.Points(geometry, material);
            
            scene.add(particleSystem);
            explosions.push({ system: particleSystem, velocities: velocities, life: 1.0 });
        }

        // --- 入力処理 ---
        function onMouseMove(event) {
            // マウス座標を -1 から 1 の範囲に正規化
            mouseX = (event.clientX / window.innerWidth) * 2 - 1;
            mouseY = -(event.clientY / window.innerHeight) * 2 + 1;
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
            composer.setSize(window.innerWidth, window.innerHeight);
        }

        function restartGame() {
            // 初期化
            isGameOver = false;
            score = 0;
            gameSpeed = 1.0;
            scoreElement.innerText = `SCORE: ${score}`;
            gameOverElement.style.display = 'none';
            ship.visible = true;
            
            // 既存のオブジェクトを消去
            obstacles.forEach(obs => scene.remove(obs));
            lasers.forEach(laser => scene.remove(laser));
            explosions.forEach(exp => scene.remove(exp.system));
            obstacles = [];
            lasers = [];
            explosions =[];
        }

        // --- メインループ ---
        function animate() {
            requestAnimationFrame(animate);

            if (!isGameOver) {
                // 機体の移動 (Lerpで滑らかに追従)
                targetX = mouseX * 20;
                targetY = mouseY * 10;
                
                ship.position.x += (targetX - ship.position.x) * 0.1;
                ship.position.y += (targetY - ship.position.y) * 0.1;

                // バンキング (移動に応じた機体の傾き計算)
                // x方向の移動でロール(z軸回転)、y方向の移動でピッチ(x軸回転)
                ship.rotation.z = (ship.position.x - targetX) * 0.1;
                ship.rotation.x = (targetY - ship.position.y) * 0.1;
                ship.rotation.y = (ship.position.x - targetX) * 0.05;

                // グリッドと星屑のアニメーション (前進しているように見せる)
                gridHelper.position.z += 1 * gameSpeed;
                if (gridHelper.position.z > 10) gridHelper.position.z = 0;

                const starPositions = stars.geometry.attributes.position.array;
                for(let i=2; i < starPositions.length; i+=3) {
                    starPositions += 2 * gameSpeed;
                    if(starPositions > 50) starPositions = -150;
                }
                stars.geometry.attributes.position.needsUpdate = true;

                // 障害物の生成タイミング
                if (Math.random() < 0.05 * gameSpeed) spawnObstacle();

                // 障害物の更新と衝突判定
                for (let i = obstacles.length - 1; i >= 0; i--) {
                    const obs = obstacles;
                    obs.position.z += 1 * gameSpeed;
                    obs.rotation.x += 0.02;
                    obs.rotation.y += 0.02;

                    // プレイヤーとの衝突判定
                    if (obs.position.distanceTo(ship.position) < 2.0) {
                        createExplosion(ship.position);
                        ship.visible = false;
                        isGameOver = true;
                        gameOverElement.style.display = 'block';
                    }

                    // 画面外に出たら削除
                    if (obs.position.z > 20) {
                        scene.remove(obs);
                        obstacles.splice(i, 1);
                    }
                }

                // レーザーの更新と衝突判定
                for (let i = lasers.length - 1; i >= 0; i--) {
                    const laser = lasers;
                    laser.position.z -= 2;

                    let hit = false;
                    for (let j = obstacles.length - 1; j >= 0; j--) {
                        const obs = obstacles;
                        if (laser.position.distanceTo(obs.position) < 2.5) {
                            createExplosion(obs.position);
                            scene.remove(obs);
                            obstacles.splice(j, 1);
                            hit = true;
                            score += 100;
                            scoreElement.innerText = `SCORE: ${score}`;
                            break;
                        }
                    }

                    if (hit || laser.position.z < -100) {
                        scene.remove(laser);
                        lasers.splice(i, 1);
                    }
                }
            }

            // 爆発パーティクルの更新
            for (let i = explosions.length - 1; i >= 0; i--) {
                const exp = explosions;
                const positions = exp.system.geometry.attributes.position.array;
                
                for (let j = 0; j < exp.velocities.length; j++) {
                    positions += exp.velocities.x;
                    positions += exp.velocities.y;
                    positions += exp.velocities.z;
                }
                exp.system.geometry.attributes.position.needsUpdate = true;
                
                exp.life -= 0.05;
                exp.system.material.opacity = exp.life;

                if (exp.life <= 0) {
                    scene.remove(exp.system);
                    explosions.splice(i, 1);
                }
            }

            // レンダリング (Bloom込み)
            composer.render();
        }
    </script>
</body>
</html>
```

### こだわりポイント（リッチな視覚効果と改良されたインタラクション）
1. **UnrealBloomPassによる発光表現 (Post-Processing)**
   マテリアルの `emissive`（発光色）と `wireframe` を組み合わせ、レトロSF・サイバーパンク感のあるネオンの光り方を再現しています。
2. **滑らかなバンキング (Lerpアニメーション)**
   機体がマウスを追従する際、単純にX・Y座標を移動させるだけでなく、進行方向に向かって機体が傾く（ロール＆ピッチする）ように数式を組んでいます。これにより、操作の手触りが大幅に向上しています。
3. **パーティクル制御**
   無限に迫り来る「星屑（Starfield）」に加え、敵を破壊した際や自分が被弾した際に飛散する「爆発エフェクト」を BufferGeometry の配列計算によって軽量かつダイナミックに描画しています。
4. **難易度の上昇 (ゲームループ)**
   時間が経つ（スコアが上がる）につれて、迫り来るスピードと敵の出現頻度が徐々に上がるようにプログラミングされています。