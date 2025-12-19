<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Poly-Dimension Blog | 多维视界</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400&family=Cinzel:wght@400;700;900&family=Inter:wght@300;400;600&family=Playfair+Display:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
    
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>

    <!-- Anime.js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/animejs/3.2.1/anime.min.js"></script>

    <style>
        /* === 核心基础 === */
        body {
            transition: background-color 0.8s cubic-bezier(0.22, 1, 0.36, 1), color 0.5s ease;
            overflow-x: hidden;
            min-height: 100vh;
            cursor: none; /* 隐藏默认鼠标，使用自定义小人 */
        }
        
        /* 隐藏滚动条但保留功能 */
        ::-webkit-scrollbar { width: 0px; }

        /* SVG 滤镜定义 (用于油画效果) */
        .svg-filters { position: absolute; width: 0; height: 0; pointer-events: none; }

        /* =========================================
           跟随鼠标的小人 (Sprite)
           ========================================= */
        #sprite-container {
            position: fixed;
            top: 0; left: 0;
            pointer-events: none;
            z-index: 9999;
            will-change: transform;
        }
        
        /* 默认形态 (Ethereal) */
        .sprite-body {
            width: 20px; height: 20px;
            background: white;
            border-radius: 50%;
            box-shadow: 0 0 10px rgba(255,255,255,0.8), 0 0 20px var(--accent);
            transition: all 0.3s ease;
            position: relative;
        }
        /* 小尾巴/光晕 */
        .sprite-trail {
            position: absolute;
            top: 50%; left: 50%;
            width: 100%; height: 100%;
            border-radius: 50%;
            background: inherit;
            opacity: 0.5;
            transform: translate(-50%, -50%) scale(1.5);
            filter: blur(4px);
        }

        /* 赛博形态 (Cyber) */
        body.theme-cyber #sprite-container .sprite-body {
            background: #0f0;
            border-radius: 0;
            width: 16px; height: 16px;
            box-shadow: 0 0 0 2px black, 0 0 0 4px #0f0;
            animation: sprite-glitch 0.2s infinite;
        }
        @keyframes sprite-glitch {
            0% { transform: translate(0,0); }
            20% { transform: translate(-2px, 2px); }
            40% { transform: translate(2px, -2px); }
            60% { transform: translate(-2px, -2px); }
            80% { transform: translate(2px, 2px); }
            100% { transform: translate(0,0); }
        }

        /* 油画形态 (Oil) */
        body.theme-oil #sprite-container .sprite-body {
            background: #ffaa00;
            width: 30px; height: 30px;
            border-radius: 50% 50% 50% 0;
            transform: rotate(-45deg);
            box-shadow: 2px 2px 4px rgba(0,0,0,0.3);
            filter: url(#oilFilterSmall); /* 应用油画滤镜 */
        }

        /* =========================================
           MODE 1: 虚空流体 (Ethereal)
           ========================================= */
        .theme-ethereal {
            background: #0a0a12;
            color: #e2e8f0;
            font-family: 'Inter', sans-serif;
            --accent: #60a5fa;
            --card-bg: rgba(255, 255, 255, 0.02);
            --border: rgba(255, 255, 255, 0.08);
        }
        .theme-ethereal .bg-blob { display: block; opacity: 0.5; }
        .theme-ethereal .card {
            background: var(--card-bg);
            backdrop-filter: blur(20px) saturate(180%);
            border: 1px solid var(--border);
            border-radius: 24px;
            overflow: hidden;
            transition: transform 0.6s cubic-bezier(0.16, 1, 0.3, 1), box-shadow 0.6s ease;
        }
        /* 悬浮动效 */
        .theme-ethereal .card:hover {
            transform: translateY(-8px) scale(1.01);
            box-shadow: 0 20px 40px -10px rgba(0,0,0,0.5);
            border-color: rgba(255,255,255,0.2);
        }
        .theme-ethereal .card img {
            mask-image: linear-gradient(to bottom, black 60%, transparent 100%);
            -webkit-mask-image: linear-gradient(to bottom, black 60%, transparent 100%);
        }

        /* =========================================
           MODE 2: 赛博故障 (Cyber)
           ========================================= */
        .theme-cyber {
            background: #050505;
            color: #00ff41;
            font-family: 'Space Mono', monospace;
            --accent: #ff00ff;
            --border: #008F11;
        }
        /* CRT 扫描线增强 */
        .theme-cyber::before {
            content: " "; display: block; position: fixed; top: 0; left: 0; bottom: 0; right: 0;
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06));
            z-index: 50; background-size: 100% 2px, 3px 100%; pointer-events: none;
        }
        .theme-cyber .card {
            background: #000;
            border: 1px solid var(--border);
            position: relative;
            /* 缺角效果 */
            clip-path: polygon(
                0 0, 
                100% 0, 
                100% calc(100% - 20px), 
                calc(100% - 20px) 100%, 
                0 100%
            );
        }
        .theme-cyber .card:hover {
            box-shadow: -4px 4px 0 var(--accent);
            transform: translate(2px, -2px);
        }
        .theme-cyber .card img {
            filter: grayscale(100%) contrast(1.2) brightness(0.8);
            transition: filter 0.3s;
            mix-blend-mode: luminosity;
        }
        .theme-cyber .card:hover img {
            filter: grayscale(0%) contrast(1.1) brightness(1);
            mix-blend-mode: normal;
        }

        /* =========================================
           MODE 3: 印象派油画 (Oil) - NEW
           ========================================= */
        .theme-oil {
            background: #e6dace;
            color: #1a1a1a;
            font-family: 'Playfair Display', serif;
            --accent: #e07a5f;
            /* 模拟画布纹理 */
            background-image: url("data:image/svg+xml,%3Csvg width='100%25' height='100%25' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.8' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.08'/%3E%3C/svg%3E");
        }
        .theme-oil #hero-title {
            filter: url(#oilFilterText); /* 文字油画滤镜 */
            color: #264653;
            letter-spacing: -2px;
        }
        .theme-oil .card {
            background: #f4f1ea;
            box-shadow: 10px 10px 20px rgba(0,0,0,0.1), -5px -5px 15px rgba(255,255,255,0.8);
            border: 8px solid white; /* 模拟画框 */
            border-bottom-width: 24px; /* 拍立得风格底部 */
            transform: rotate(0deg);
            transition: all 0.4s ease;
        }
        .theme-oil .card:nth-child(even) { transform: rotate(1deg); }
        .theme-oil .card:nth-child(odd) { transform: rotate(-1deg); }
        
        .theme-oil .card:hover {
            transform: rotate(0deg) scale(1.05);
            z-index: 10;
            box-shadow: 15px 15px 30px rgba(0,0,0,0.2);
        }
        
        /* 关键：给图片增加油画质感 */
        .theme-oil .card img {
            filter: url(#oilFilterImage) saturate(1.3);
            transition: filter 0.5s;
        }
        .theme-oil .card:hover img {
            filter: saturate(1.5); /* 悬停时色彩更浓郁 */
        }
        .theme-oil nav {
            background: rgba(230, 218, 206, 0.9);
            border-bottom: 1px solid rgba(0,0,0,0.1);
        }

        /* 动效：流体 Blob */
        .bg-blob {
            position: fixed; width: 600px; height: 600px;
            filter: blur(100px); z-index: -1; opacity: 0;
            border-radius: 50%;
            animation: float 15s infinite ease-in-out;
            transition: opacity 1s;
        }
        @keyframes float {
            0%, 100% { transform: translate(0, 0) scale(1); }
            33% { transform: translate(30px, -50px) scale(1.1); }
            66% { transform: translate(-20px, 20px) scale(0.9); }
        }

        /* 布局控制 */
        .grid-layout { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(340px, 1fr)); 
            gap: 3rem; 
        }

        /* 加载状态 */
        .loading-shimmer {
            background: linear-gradient(90deg, transparent 0%, rgba(255,255,255,0.1) 50%, transparent 100%);
            background-size: 200% 100%;
            animation: shimmer 1.5s infinite;
        }
        @keyframes shimmer {
            0% { background-position: -200% 0; }
            100% { background-position: 200% 0; }
        }
    </style>
</head>
<body class="theme-ethereal" id="app">

    <!-- SVG Filters Definition (Hidden) -->
    <svg class="svg-filters">
        <defs>
            <!-- 油画笔触滤镜 -->
            <filter id="oilFilterImage">
                <feTurbulence type="fractalNoise" baseFrequency="0.015" numOctaves="3" result="noise" />
                <feDisplacementMap in="SourceGraphic" in2="noise" scale="5" />
            </filter>
            <!-- 文字边缘粗糙滤镜 -->
            <filter id="oilFilterText">
                <feTurbulence type="fractalNoise" baseFrequency="0.05" numOctaves="1" result="noise" />
                <feDisplacementMap in="SourceGraphic" in2="noise" scale="2" />
            </filter>
            <!-- 小人滤镜 -->
            <filter id="oilFilterSmall">
                <feTurbulence type="fractalNoise" baseFrequency="0.1" numOctaves="2" result="noise" />
                <feDisplacementMap in="SourceGraphic" in2="noise" scale="3" />
            </filter>
        </defs>
    </svg>

    <!-- 动漫小人/光标跟随器 -->
    <div id="sprite-container">
        <div class="sprite-body">
            <div class="sprite-trail"></div>
        </div>
    </div>

    <!-- 背景光斑 (Ethereal 专属) -->
    <div class="bg-blob bg-blue-600 top-[-20%] left-[-10%]"></div>
    <div class="bg-blob bg-purple-600 bottom-[-20%] right-[-10%] animation-delay-2000"></div>
    <div class="bg-blob bg-emerald-500 top-[40%] left-[30%] w-96 h-96 animation-delay-4000 mix-blend-overlay"></div>

    <!-- 导航 -->
    <nav class="fixed top-0 w-full z-40 px-8 py-6 flex justify-between items-center transition-all duration-500 backdrop-blur-sm" id="navbar">
        <div class="text-3xl font-black tracking-tighter cursor-pointer select-none" onclick="setTheme('ethereal')">
            <span id="logo-text">VOID.</span>
        </div>
        
        <!-- 风格切换器 -->
        <div class="flex gap-4 items-center bg-white/5 backdrop-blur-md px-2 py-2 rounded-full border border-white/10 shadow-2xl theme-switcher-container">
            <button onclick="setTheme('ethereal')" class="w-10 h-10 flex items-center justify-center rounded-full hover:bg-white/10 transition group" title="虚空流体">
                <i data-lucide="droplets" class="w-5 h-5 group-hover:scale-110 transition"></i>
            </button>
            <button onclick="setTheme('cyber')" class="w-10 h-10 flex items-center justify-center rounded-full hover:bg-white/10 transition group" title="赛博故障">
                <i data-lucide="terminal-square" class="w-5 h-5 group-hover:scale-110 transition"></i>
            </button>
            <button onclick="setTheme('oil')" class="w-10 h-10 flex items-center justify-center rounded-full hover:bg-white/10 transition group" title="印象派油画">
                <i data-lucide="palette" class="w-5 h-5 group-hover:scale-110 transition"></i>
            </button>
        </div>
    </nav>

    <!-- 主内容 -->
    <main class="container mx-auto px-6 pt-32 pb-24 relative z-10">
        
        <!-- Hero Section -->
        <header class="mb-32 mt-12 grid grid-cols-1 lg:grid-cols-2 gap-12 items-center">
            <div>
                <div class="inline-block mb-4 px-3 py-1 border rounded-full text-xs font-mono uppercase tracking-widest opacity-60" id="hero-tag">
                    System.Status: Online
                </div>
                <h1 class="text-6xl md:text-8xl font-black mb-8 leading-tight tracking-tight select-none" id="hero-title">
                    BEYOND<br>REALITY
                </h1>
                <p class="text-lg md:text-xl opacity-70 max-w-md leading-relaxed" id="hero-desc">
                    探索数字边界之外的存在。当代码凝固成艺术，当逻辑在油彩中溶解。
                </p>
                <div class="mt-10 flex gap-6">
                    <button class="px-8 py-3 bg-current text-black font-bold hover:opacity-80 transition-opacity rounded-full theme-btn-invert">
                        开始探索
                    </button>
                </div>
            </div>
            <!-- 动态 Hero 展示区 -->
            <div class="relative h-[400px] w-full hidden lg:block perspective-1000">
                <!-- 3D 浮动卡片 -->
                <div class="absolute inset-0 bg-gradient-to-br from-blue-500/20 to-purple-500/20 rounded-3xl backdrop-blur-xl border border-white/10 transform rotate-y-12 animate-float-slow" id="hero-visual">
                    <!-- 内容动态注入 -->
                </div>
            </div>
        </header>

        <!-- 过滤器 -->
        <div class="flex gap-8 mb-12 border-b border-current/10 pb-4 font-mono text-sm uppercase tracking-widest opacity-60 overflow-x-auto">
            <span class="cursor-pointer opacity-100 border-b-2 border-current pb-4">All</span>
            <span class="cursor-pointer hover:opacity-100 transition">Code</span>
            <span class="cursor-pointer hover:opacity-100 transition">Art</span>
            <span class="cursor-pointer hover:opacity-100 transition">Essays</span>
            <span class="cursor-pointer hover:opacity-100 transition">Dreams</span>
        </div>

        <!-- 文章网格 -->
        <div class="grid-layout" id="blog-grid">
            <!-- JS 渲染 -->
        </div>

    </main>

    <footer class="py-12 text-center opacity-40 font-mono text-xs">
        SCROLL TO END OF BUFFER
    </footer>

    <script>
        lucide.createIcons();

        // 带头图的数据结构
        const posts = [
            {
                title: "神经漫游：网络意识的诞生",
                category: "CYBER",
                date: "2025.11.02",
                desc: "如果是赛博朋克不是一种审美，而是一种警告？探讨神经接口技术带来的伦理黑洞。",
                image: "https://images.unsplash.com/photo-1550751827-4bd374c3f58b?q=80&w=800&auto=format&fit=crop"
            },
            {
                title: "星夜下的算法",
                category: "ART",
                date: "2025.10.24",
                desc: "AI 绘画是艺术的终结还是新生？模仿梵高笔触的生成式对抗网络研究。",
                image: "https://images.unsplash.com/photo-1549490349-8643362247b5?q=80&w=800&auto=format&fit=crop"
            },
            {
                title: "寂静的几何学",
                category: "DESIGN",
                date: "2025.10.10",
                desc: "极简主义建筑中的光影哲学。当混凝土遇到清晨的第一缕阳光。",
                image: "https://images.unsplash.com/photo-1494438639946-1ebd1d20bf85?q=80&w=800&auto=format&fit=crop"
            },
            {
                title: "失落的信号",
                category: "FICTION",
                date: "2025.09.28",
                desc: "一个关于宇航员在火星轨道失去联系后，独自对着虚空广播了300天的短篇小说。",
                image: "https://images.unsplash.com/photo-1451187580459-43490279c0fa?q=80&w=800&auto=format&fit=crop"
            },
            {
                title: "代码花园",
                category: "TECH",
                date: "2025.09.15",
                desc: "如何像照料植物一样重构你的代码库？有机编程哲学的实践指南。",
                image: "https://images.unsplash.com/photo-1518531933037-91b2f5f229cc?q=80&w=800&auto=format&fit=crop"
            },
            {
                title: "都市霓虹后的孤独",
                category: "LIFE",
                date: "2025.09.01",
                desc: "东京街拍集。记录那些在自动贩卖机前驻足的深夜灵魂。",
                image: "https://images.unsplash.com/photo-1565626424178-c699f660d2c9?q=80&w=800&auto=format&fit=crop"
            }
        ];

        const grid = document.getElementById('blog-grid');
        const body = document.getElementById('app');

        // 渲染文章
        function renderPosts() {
            grid.innerHTML = posts.map((post, index) => `
                <article class="card flex flex-col h-full group cursor-pointer" style="animation-delay: ${index * 100}ms">
                    <!-- 头图区域 -->
                    <div class="h-48 overflow-hidden relative">
                        <img src="${post.image}" alt="${post.title}" class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-700 ease-in-out">
                        <div class="absolute top-4 left-4 bg-black/50 backdrop-blur-md text-white text-[10px] font-bold px-2 py-1 rounded border border-white/20">
                            ${post.category}
                        </div>
                    </div>
                    
                    <!-- 内容区域 -->
                    <div class="p-6 flex flex-col flex-grow relative z-10">
                        <div class="text-xs opacity-50 mb-3 font-mono">${post.date}</div>
                        <h3 class="text-2xl font-bold mb-3 leading-tight group-hover:text-accent transition-colors">${post.title}</h3>
                        <p class="opacity-70 text-sm leading-relaxed mb-6 line-clamp-3">${post.desc}</p>
                        
                        <div class="mt-auto flex items-center text-xs font-bold uppercase tracking-widest opacity-60 group-hover:opacity-100 transition-opacity">
                            Read More <i data-lucide="arrow-up-right" class="w-4 h-4 ml-1"></i>
                        </div>
                    </div>
                </article>
            `).join('');
            lucide.createIcons();
            
            // 入场动画
            anime({
                targets: '.card',
                translateY: [50, 0],
                opacity: [0, 1],
                delay: anime.stagger(100),
                duration: 800,
                easing: 'easeOutExpo'
            });
        }

        // 主题配置
        const themes = {
            'ethereal': {
                logo: 'VOID.',
                title: 'BEYOND<br>REALITY',
                desc: '探索数字边界之外的存在。当代码凝固成艺术，当逻辑在油彩中溶解。',
                btnClass: 'bg-white text-black'
            },
            'cyber': {
                logo: '>_ROOT',
                title: 'SYSTEM<br>OVERRIDE',
                desc: 'DETECTED UNAUTHORIZED ACCESS. REALITY PROTOCOL CORRUPTED. INITIALIZING...',
                btnClass: 'bg-[#0f0] text-black hover:shadow-[0_0_20px_#0f0]'
            },
            'oil': {
                logo: 'MUSEUM.',
                title: 'STARRY<br>DREAMS',
                desc: '生活不仅是眼前的苟且，还有诗和远方。在这里，每一行代码都是画布上的笔触。',
                btnClass: 'bg-[#e07a5f] text-white font-serif italic'
            }
        };

        function setTheme(themeName) {
            body.className = `theme-${themeName}`;
            
            const config = themes[themeName];
            const logo = document.getElementById('logo-text');
            const title = document.getElementById('hero-title');
            const desc = document.getElementById('hero-desc');
            const btn = document.querySelector('.theme-btn-invert');
            
            // 文字内容动画切换
            anime({
                targets: [logo, title, desc],
                opacity: [1, 0],
                duration: 300,
                easing: 'easeInQuad',
                complete: () => {
                    logo.innerHTML = config.logo;
                    title.innerHTML = config.title;
                    desc.innerHTML = config.desc;
                    btn.className = `px-8 py-3 font-bold hover:opacity-80 transition-opacity rounded-full theme-btn-invert ${config.btnClass}`;
                    
                    anime({
                        targets: [logo, title, desc],
                        opacity: [0, 1],
                        duration: 500,
                        easing: 'easeOutQuad'
                    });
                }
            });

            // 重新触发卡片渲染以适应新样式的滤镜
            // renderPosts(); // 可选：如果不希望重置滚动位置，可以只改 CSS
        }

        // =========================================
        // 动漫小人/光标物理引擎
        // =========================================
        const sprite = document.getElementById('sprite-container');
        let mouseX = window.innerWidth / 2;
        let mouseY = window.innerHeight / 2;
        let spriteX = mouseX;
        let spriteY = mouseY;
        let velX = 0;
        let velY = 0;
        const drag = 0.15; // 惯性系数 (越小越滑)
        const spring = 0.1; // 弹性系数

        document.addEventListener('mousemove', (e) => {
            mouseX = e.clientX;
            mouseY = e.clientY;
        });

        function animateSprite() {
            // 简单的弹簧物理
            let dx = mouseX - spriteX;
            let dy = mouseY - spriteY;
            
            velX += dx * spring;
            velY += dy * spring;
            
            velX *= (1 - drag);
            velY *= (1 - drag);
            
            spriteX += velX;
            spriteY += velY;

            // 根据速度计算倾斜/拉伸 (Squash and Stretch)
            const speed = Math.sqrt(velX*velX + velY*velY);
            const angle = Math.atan2(velY, velX) * 180 / Math.PI;
            const stretch = Math.min(1 + speed / 50, 1.5); // 最大拉伸 1.5倍
            const squash = 1 / stretch;

            sprite.style.transform = `
                translate(${spriteX}px, ${spriteY}px) 
                translate(-50%, -50%)
                rotate(${angle}deg)
                scale(${stretch}, ${squash})
            `;

            requestAnimationFrame(animateSprite);
        }

        // 启动逻辑
        renderPosts();
        animateSprite();

        // 交互增强
        const interactiveEls = document.querySelectorAll('a, button, .card');
        interactiveEls.forEach(el => {
            el.addEventListener('mouseenter', () => {
                const body = document.querySelector('.sprite-body');
                body.style.transform = 'scale(1.5)';
                body.style.background = 'var(--accent)';
            });
            el.addEventListener('mouseleave', () => {
                const body = document.querySelector('.sprite-body');
                body.style.transform = 'scale(1)';
                body.style.background = ''; // reset
            });
        });

    </script>
</body>
</html>