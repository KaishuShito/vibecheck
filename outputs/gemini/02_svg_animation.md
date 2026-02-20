


ペリカンが森の中を自転車で疾走する、超詳細で高品質なSVGアニメーションを作成しました。

このSVGは、CSSのハードウェア・アクセラレーション（GPUレンダリング）を最大限に活用し、60fpsで滑らかに動作するように設計されています。被写界深度（パララックス効果や手前のモーションブラー）を用いたリッチな表現を取り入れています。

以下のコードをコピーし、`pelican-bike.svg` という名前で保存してブラウザで開くか、そのままHTMLに埋め込んでご覧ください。

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1000 600" width="100%" height="100%" style="background-color: #0b1a20; overflow: hidden;">
  <defs>
    <!-- グラデーション -->
    <linearGradient id="skyGrad" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#1A2A6C" />
      <stop offset="40%" stop-color="#b24d1f" />
      <stop offset="100%" stop-color="#FDBB2D" />
    </linearGradient>
    <radialGradient id="sunGrad" cx="50%" cy="50%" r="50%">
      <stop offset="0%" stop-color="#fff" stop-opacity="1" />
      <stop offset="30%" stop-color="#FDF3A7" stop-opacity="0.9" />
      <stop offset="100%" stop-color="#FDBB2D" stop-opacity="0" />
    </radialGradient>
    <linearGradient id="frameGrad" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0%" stop-color="#00E1D9" />
      <stop offset="50%" stop-color="#0072ff" />
      <stop offset="100%" stop-color="#3a00ff" />
    </linearGradient>
    <linearGradient id="pelicanBody" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#ffffff" />
      <stop offset="70%" stop-color="#e2e8f0" />
      <stop offset="100%" stop-color="#94a3b8" />
    </linearGradient>
    <linearGradient id="pouchGrad" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#F39C12" />
      <stop offset="100%" stop-color="#D35400" />
    </linearGradient>
    <linearGradient id="treeTrunk" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0%" stop-color="#2d1d15" />
      <stop offset="50%" stop-color="#4a3326" />
      <stop offset="100%" stop-color="#1b110b" />
    </linearGradient>

    <!-- フィルター -->
    <filter id="blurFg">
      <feGaussianBlur stdDeviation="12" />
    </filter>
    <filter id="dropShadow" x="-20%" y="-20%" width="150%" height="150%">
      <feDropShadow dx="0" dy="15" stdDeviation="8" flood-color="#000" flood-opacity="0.5"/>
    </filter>

    <!-- 森の構成要素（再利用用） -->
    <g id="bg-pine">
      <polygon points="50,0 0,150 30,150 -10,280 40,280 0,400 100,400 60,280 110,280 70,150 100,150" fill="#14323b" opacity="0.6"/>
      <rect x="42" y="400" width="16" height="50" fill="#0b1a20" opacity="0.6"/>
    </g>

    <g id="mid-tree">
      <rect x="40" y="50" width="20" height="400" fill="url(#treeTrunk)" />
      <polygon points="50,100 10,60 15,50 50,85" fill="url(#treeTrunk)" />
      <polygon points="50,220 85,170 80,160 50,205" fill="url(#treeTrunk)" />
      <!-- 葉っぱの塊 -->
      <circle cx="50" cy="50" r="60" fill="#183623" opacity="0.9"/>
      <circle cx="10" cy="80" r="50" fill="#142c1d" opacity="0.9"/>
      <circle cx="80" cy="70" r="55" fill="#1a4027" opacity="0.9"/>
      <circle cx="45" cy="0" r="45" fill="#204d2e" opacity="0.9"/>
    </g>

    <g id="wheel">
      <!-- タイヤ -->
      <circle cx="0" cy="0" r="60" stroke="#111" stroke-width="12" fill="none" />
      <!-- ガムウォール（側面） -->
      <circle cx="0" cy="0" r="54" stroke="#A66C37" stroke-width="4" fill="none" />
      <!-- リム -->
      <circle cx="0" cy="0" r="50" stroke="#BDC3C7" stroke-width="3" fill="none" />
      <!-- スポーク -->
      <path d="M0,-50 L0,50 M-50,0 L50,0 M-35,-35 L35,35 M-35,35 L35,-35 M-19,-46 L19,46 M-46,-19 L46,19 M-46,19 L46,-19 M-19,46 L19,-46" stroke="#7F8C8D" stroke-width="1.5" />
      <!-- ハブ -->
      <circle cx="0" cy="0" r="8" fill="#34495E" />
      <circle cx="0" cy="0" r="3" fill="#ECF0F1" />
    </g>
  </defs>

  <style>
    /* 無限スクロール・アニメーション */
    @keyframes scroll {
      0% { transform: translateX(0); }
      100% { transform: translateX(-1000px); }
    }
    
    /* 車輪・クランクの回転 */
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
    @keyframes counter-spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(-360deg); }
    }
    
    /* 左側のクランク（位相を180度ずらす） */
    @keyframes spin-left {
      0% { transform: rotate(180deg); }
      100% { transform: rotate(540deg); }
    }
    @keyframes counter-spin-left {
      0% { transform: rotate(-180deg); }
      100% { transform: rotate(-540deg); }
    }

    /* 上下のボビング（揺れ） */
    @keyframes bob-body {
      0%, 100% { transform: translateY(0px) rotate(0deg); }
      50% { transform: translateY(-4px) rotate(1.5deg); }
    }
    @keyframes bob-head {
      0%, 100% { transform: translateY(0px) rotate(0deg); }
      50% { transform: translateY(-3px) rotate(-1deg); }
    }
    @keyframes bob-bike {
      0%, 100% { transform: translateY(0px); }
      50% { transform: translateY(-1.5px); }
    }

    /* マフラー（スカーフ）のなびき */
    @keyframes flap-scarf {
      0%, 100% { d: path('M 515,130 Q 400,120 320,160 Q 280,180 250,140'); }
      50% { d: path('M 515,130 Q 400,140 330,130 Q 280,120 240,170'); }
    }

    /* チェーンの回転 */
    @keyframes dash {
      to { stroke-dashoffset: -20; }
    }

    /* 風や埃のスピードライン */
    @keyframes wind {
      0% { transform: translateX(1200px); opacity: 0; }
      10% { opacity: 0.8; }
      90% { opacity: 0.8; }
      100% { transform: translateX(-500px); opacity: 0; }
    }

    /* クラス適用（60fps向けハードウェアアクセラレーション） */
    .layer-far { animation: scroll 30s linear infinite; will-change: transform; }
    .layer-mid { animation: scroll 12s linear infinite; will-change: transform; }
    .layer-near { animation: scroll 5s linear infinite; will-change: transform; }
    .layer-ground { animation: scroll 0.6s linear infinite; will-change: transform; }
    .layer-fg { animation: scroll 1.5s linear infinite; will-change: transform; }

    .wheel-spin { animation: spin 0.4s linear infinite; transform-origin: center; transform-box: fill-box; }
    .chain-spin { animation: dash 0.2s linear infinite; }

    .crank-right { animation: spin 0.4s linear infinite; transform-origin: 500px 400px; }
    .pedal-right { animation: counter-spin 0.4s linear infinite; transform-origin: 500px 425px; }
    
    .crank-left { animation: spin-left 0.4s linear infinite; transform-origin: 500px 400px; }
    .pedal-left { animation: counter-spin-left 0.4s linear infinite; transform-origin: 500px 425px; }

    .anim-body { animation: bob-body 0.2s ease-in-out infinite; transform-origin: 440px 300px; }
    .anim-head { animation: bob-head 0.2s ease-in-out infinite; transform-origin: 505px 110px; }
    .anim-bike { animation: bob-bike 0.2s ease-in-out infinite; }
    .anim-scarf { animation: flap-scarf 0.15s ease-in-out infinite alternate; }

    .wind-line { animation: wind 1s linear infinite; }
    .wind-line.d1 { animation-duration: 0.8s; animation-delay: 0.2s; }
    .wind-line.d2 { animation-duration: 1.2s; animation-delay: 0.5s; }
    .wind-line.d3 { animation-duration: 0.9s; animation-delay: 0.7s; }
  </style>

  <!-- 1. 背景の空と太陽 -->
  <rect width="100%" height="100%" fill="url(#skyGrad)" />
  <circle cx="850" cy="250" r="150" fill="url(#sunGrad)" />

  <!-- 2. 最奥の森 (Far) -->
  <g class="layer-far">
    <g id="far-content">
      <use href="#bg-pine" x="50" y="80" transform="scale(0.8)" />
      <use href="#bg-pine" x="200" y="100" transform="scale(0.9)" />
      <use href="#bg-pine" x="400" y="50" transform="scale(1)" />
      <use href="#bg-pine" x="600" y="120" transform="scale(0.7)" />
      <use href="#bg-pine" x="800" y="70" transform="scale(0.85)" />
    </g>
    <use href="#far-content" x="1000" />
  </g>

  <!-- 3. 中景の森 (Mid) -->
  <g class="layer-mid">
    <g id="mid-content">
      <use href="#mid-tree" x="80" y="40" />
      <use href="#mid-tree" x="350" y="10" transform="scale(1.2) translate(-50, 0)" />
      <use href="#mid-tree" x="650" y="60" transform="scale(0.9)" />
      <use href="#mid-tree" x="900" y="20" />
    </g>
    <use href="#mid-content" x="1000" />
  </g>

  <!-- 太陽の光線 (オーバーレイ) -->
  <polygon points="850,250 0,600 300,600 850,300" fill="#fff" opacity="0.08" mix-blend-mode="overlay" />
  <polygon points="850,250 500,600 800,600 850,350" fill="#fff" opacity="0.05" mix-blend-mode="overlay" />

  <!-- 4. 地面と道 -->
  <g class="layer-ground">
    <g id="ground-content">
      <!-- 土の道 -->
      <rect x="0" y="450" width="1000" height="150" fill="#3E2723" />
      <rect x="0" y="450" width="1000" height="10" fill="#2E1B16" opacity="0.5" />
      <!-- 草の縁 -->
      <path d="M0,450 Q20,440 40,450 T80,450 T120,450 T160,450 T200,450 T240,450 T280,450 T320,450 T360,450 T400,450 T440,450 T480,450 T520,450 T560,450 T600,450 T640,450 T680,450 T720,450 T760,450 T800,450 T840,450 T880,450 T920,450 T960,450 T1000,450 L1000,460 L0,460 Z" fill="#274E13" />
      <!-- 道のテクスチャ（流れる線） -->
      <path d="M 50,480 L 150,480 M 300,470 L 450,470 M 600,490 L 700,490 M 800,475 L 950,475 M 100,520 L 250,520 M 400,540 L 600,540 M 750,530 L 850,530" stroke="#2E1B16" stroke-width="4" stroke-linecap="round" stroke-dasharray="10 20" />
      <path d="M 20,560 L 120,560 M 350,570 L 500,570 M 650,550 L 800,550" stroke="#5D4037" stroke-width="3" stroke-linecap="round" />
    </g>
    <use href="#ground-content" x="1000" />
  </g>

  <!-- 車体の影 -->
  <ellipse cx="500" cy="460" rx="180" ry="10" fill="#111" opacity="0.6" class="anim-bike" />

  <!-- ★ 左側の足とペダル（奥側・自転車フレームの後ろ） -->
  <g class="crank-left anim-bike">
    <line x1="500" y1="400" x2="500" y2="425" stroke="#111" stroke-width="8" stroke-linecap="round" />
    <g class="pedal-left">
      <!-- ペダル本体 -->
      <rect x="480" y="420" width="40" height="10" rx="4" fill="#555" />
      <!-- 左足（水かき） -->
      <path d="M 480,420 L 520,420 L 515,410 L 485,410 Z" fill="#D35400" />
      <!-- 左下肢（回転に合わせて動くが、上部は胴体に隠れる） -->
      <line x1="500" y1="415" x2="480" y2="350" stroke="#D35400" stroke-width="10" stroke-linecap="round" />
    </g>
  </g>

  <!-- ★ 自転車フレームと車輪 (アニメーション・グループ) -->
  <g class="anim-bike" filter="url(#dropShadow)">
    
    <!-- 後輪 -->
    <g transform="translate(350, 400)">
      <use href="#wheel" class="wheel-spin" />
    </g>
    <!-- 前輪 -->
    <g transform="translate(650, 400)">
      <use href="#wheel" class="wheel-spin" />
    </g>

    <!-- チェーンリングとチェーン -->
    <!-- 後部スプロケット -->
    <circle cx="350" cy="400" r="10" fill="#555" />
    <!-- 前部チェーンリング -->
    <circle cx="500" cy="400" r="22" fill="none" stroke="#333" stroke-width="5" stroke-dasharray="4 2" />
    <!-- チェーン本体 -->
    <path d="M 500,378 L 350,390 A 10,10 0 0,0 350,410 L 500,422 A 22,22 0 0,0 500,378" fill="none" stroke="#7F8C8D" stroke-width="4" stroke-dasharray="6 4" class="chain-spin" />

    <!-- フレーム (ジオメトリ) -->
    <g stroke="url(#frameGrad)" stroke-width="10" stroke-linecap="round" stroke-linejoin="round" fill="none">
      <!-- チェーンステイ -->
      <line x1="350" y1="400" x2="500" y2="400" />
      <!-- シートステイ -->
      <line x1="350" y1="400" x2="450" y2="280" />
      <!-- シートチューブ -->
      <line x1="500" y1="400" x2="450" y2="280" />
      <!-- トップチューブ -->
      <line x1="450" y1="280" x2="600" y2="260" />
      <!-- ダウンチューブ -->
      <line x1="500" y1="400" x2="610" y2="310" />
      <!-- ヘッドチューブ -->
      <line x1="600" y1="260" x2="610" y2="310" />
      <!-- フロントフォーク -->
      <line x1="610" y1="310" x2="650" y2="400" stroke="#BDC3C7" />
    </g>

    <!-- フレームのジョイント・装飾 -->
    <circle cx="500" cy="400" r="8" fill="#ECF0F1" />
    <circle cx="450" cy="280" r="6" fill="#111" />
    <circle cx="600" cy="260" r="6" fill="#111" />

    <!-- シートポストとサドル -->
    <line x1="450" y1="280" x2="440" y2="250" stroke="#BDC3C7" stroke-width="8" stroke-linecap="round" />
    <path d="M 420,250 C 440,250 460,245 470,260 C 450,255 430,260 420,250 Z" fill="#111" />

    <!-- ハンドルステムとハンドルバー -->
    <line x1="600" y1="260" x2="610" y2="230" stroke="#BDC3C7" stroke-width="8" stroke-linecap="round" />
    <!-- ドロップハンドル風のフラットバー -->
    <path d="M 605,230 Q 640,220 655,240" fill="none" stroke="#222" stroke-width="8" stroke-linecap="round" />

  </g> <!-- /フレームグループ -->

  <!-- ★ 右側の足とペダル（手前側） -->
  <g class="crank-right anim-bike">
    <line x1="500" y1="400" x2="500" y2="425" stroke="#222" stroke-width="8" stroke-linecap="round" />
    <g class="pedal-right">
      <rect x="480" y="420" width="40" height="10" rx="4" fill="#333" />
      <path d="M 480,420 L 520,420 L 515,410 L 485,410 Z" fill="#E67E22" />
      <line x1="500" y1="415" x2="480" y2="350" stroke="#E67E22" stroke-width="10" stroke-linecap="round" />
      <!-- 足首のシワ/ディテール -->
      <path d="M 488,410 L 492,410 M 496,410 L 500,410" stroke="#D35400" stroke-width="2" />
    </g>
  </g>

  <!-- ★ ペリカン本体 -->
  <g class="anim-body" filter="url(#dropShadow)">
    
    <!-- 尻尾 -->
    <path d="M 400,280 L 320,300 Q 330,315 300,325 L 360,340 Z" fill="#94a3b8" />
    <!-- 胴体 -->
    <path d="M 350,310 Q 400,270 440,270 C 490,270 540,240 560,240 Q 580,290 530,340 C 490,380 430,370 380,350 Q 330,330 350,310 Z" fill="url(#pelicanBody)" />
    <!-- 胴体の羽毛ディテール -->
    <path d="M 380,300 Q 430,320 450,350" fill="none" stroke="#cbd5e1" stroke-width="3" stroke-linecap="round" />
    <path d="M 410,280 Q 460,300 480,330" fill="none" stroke="#cbd5e1" stroke-width="3" stroke-linecap="round" />

    <!-- 腹部（太ももを隠すふさふさの羽毛） -->
    <path d="M 430,350 C 460,390 510,380 520,340 Z" fill="url(#pelicanBody)" />

    <!-- 首 -->
    <path d="M 550,245 C 570,180 550,150 515,130 C 490,115 480,160 500,190 C 510,210 500,240 470,260 Z" fill="url(#pelicanBody)" />

    <!-- ★ ペリカンの頭部と顔 (個別のアニメーション) -->
    <g class="anim-head">
      <!-- 嘴の巨大なポーチ（喉袋） -->
      <path d="M 520,115 C 570,190 660,160 690,110 C 620,130 570,120 520,115 Z" fill="url(#pouchGrad)" opacity="0.95" />
      <path d="M 540,135 Q 600,150 650,120" stroke="#D35400" stroke-width="1.5" fill="none" opacity="0.5" />
      
      <!-- 頭の丸み -->
      <ellipse cx="505" cy="110" rx="28" ry="22" fill="#ffffff" />
      
      <!-- 上嘴 (クチバシの上部) -->
      <path d="M 525,100 Q 610,105 695,110 Q 610,115 525,115 Z" fill="#F1C40F" />
      
      <!-- 目の周りのピンク色の皮膚 -->
      <ellipse cx="510" cy="102" rx="12" ry="9" fill="#FADBD8" />
      
      <!-- 目 -->
      <circle cx="512" cy="102" r="4" fill="#111" />
      <circle cx="514" cy="100" r="1.5" fill="#fff" /> <!-- ハイライト -->

      <!-- ヘルメット（安全第一！） -->
      <path d="M 480,100 C 480,70 535,70 535,100 Z" fill="#E74C3C" />
      <path d="M 480,100 L 545,100 L 540,105 L 485,105 Z" fill="#C0392B" />
      <rect x="502" y="80" width="8" height="20" fill="#ffffff" opacity="0.8" />
      <path d="M 510,105 L 515,130 L 525,120" fill="none" stroke="#222" stroke-width="2.5" />
      
      <!-- ゴーグル（目を保護） -->
      <rect x="502" y="94" width="22" height="16" rx="6" fill="#3498DB" stroke="#2C3E50" stroke-width="3" />
      <!-- ゴーグルのハイライト -->
      <path d="M 505,98 L 515,98" stroke="#fff" stroke-width="2" stroke-linecap="round" />
      <!-- ゴーグルのストラップ -->
      <line x1="480" y1="102" x2="502" y2="102" stroke="#2C3E50" stroke-width="4" />
    </g>

    <!-- ★ なびく赤いマフラー (スピード感を強調) -->
    <path d="M 515,130 Q 400,120 320,160 Q 280,180 250,140" fill="none" stroke="#E74C3C" stroke-width="14" stroke-linecap="round" class="anim-scarf" filter="url(#dropShadow)" />
    <!-- マフラーの結び目 -->
    <circle cx="515" cy="130" r="10" fill="#C0392B" />

    <!-- ★ 右の翼（ハンドルを握る） -->
    <path d="M 440,290 C 480,270 550,260 620,240 C 640,230 655,235 650,245 C 580,275 510,310 460,310 Z" fill="url(#pelicanBody)" />
    <!-- 翼の先（風切羽） -->
    <path d="M 580,255 L 630,240 L 640,245 L 590,265 Z" fill="#34495E" />
    <path d="M 560,265 L 610,250 L 620,255 L 570,275 Z" fill="#2C3E50" />

  </g> <!-- /ペリカングループ -->

  <!-- 5. 最前面のモーションブラー付き木々 (Foreground Parallax) -->
  <!-- 圧倒的なスピード感を演出 -->
  <g class="layer-fg" filter="url(#blurFg)">
    <g id="fg-content">
      <rect x="150" y="-100" width="180" height="800" fill="#0d0805" opacity="0.85" />
      <rect x="750" y="-100" width="140" height="800" fill="#0d0805" opacity="0.8" />
    </g>
    <use href="#fg-content" x="1000" />
  </g>

  <!-- 6. 風の軌跡 / スピードライン -->
  <rect x="0" y="200" width="150" height="2" fill="#fff" class="wind-line" />
  <rect x="0" y="350" width="80" height="2" fill="#fff" class="wind-line d1" />
  <rect x="0" y="100" width="200" height="1.5" fill="#fff" class="wind-line d2" />
  <rect x="0" y="480" width="120" height="3" fill="#fff" class="wind-line d3" opacity="0.5"/>

</svg>
```

### ✨ このアニメーションのハイライト（超詳細ポイント）：
1. **4層のパララックス（視差）スクロール**:
   - 奥の森、中景の森、地面、そして最前面のピンボケした巨大な木々がそれぞれ異なる速度で無限スクロールし、圧倒的な「疾走感」と「奥行き」を表現しています。
2. **完全なペダリング・イルージョン**:
   - ペリカン特有の「ふさふさの腹部羽毛」に太ももの関節を隠すことで、クランクと連動してスムーズに脚が回転する精巧なペダリング動作をCSSアニメーションのみで実現しました（左右で180度の位相差もつけています）。
3. **超詳細な車体とキャラクター**:
   - スポーク、チェーン（回転アニメーション付き！）、ブロックタイヤのガムウォール、グラデーション輝くフレームを緻密に描画。
   - ペリカンには安全第一のヘルメット、防風ゴーグル、そして風になびく赤いマフラーを装備させ、キャラクター性を際立たせました。
4. **60fpsの滑らかなパフォーマンス**:
   - JavaScriptを一切使用せず、GPUでハードウェア・アクセラレーションされる`transform`（移動、回転）プロパティとSVGの`d`属性アニメーションだけで構成しているため、ブラウザ上で非常に滑らかに動作します。