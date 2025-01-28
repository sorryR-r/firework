<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>🎇 新春惊喜彩蛋</title>
    <style>
        :root {
            --gold: #ffd700;
            --red: #e74c3c;
        }
        
        body {
            margin: 0;
            background: #0a0a23;
            color: var(--gold);
            font-family: 'Microsoft YaHei', sans-serif;
            overflow-x: hidden;
        }

        .container {
            min-height: 100vh;
            padding: 20px;
            position: relative;
        }

        .scroll-section {
            transition: transform 0.8s;
            transform: translateY(100%);
        }

        .revealed { transform: translateY(0); }

        .blessing {
            text-align: center;
            margin: 30vh auto;
            opacity: 0;
            transition: opacity 1s;
        }

        .secret-link {
            display: inline-block;
            border-bottom: 2px dashed var(--gold);
            cursor: pointer;
            position: relative;
            margin: 0 5px;
        }

        .secret-link::after {
            content: "✨";
            position: absolute;
            right: -20px;
            top: -5px;
            animation: sparkle 1.5s infinite;
        }

        .firework-trigger {
            width: 60px;
            height: 60px;
            background: var(--red);
            border-radius: 50%;
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            cursor: pointer;
            opacity: 0;
            transition: all 0.5s;
            box-shadow: 0 0 20px var(--red);
        }

        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        @keyframes sparkle {
            0% { opacity: 0; }
            50% { opacity: 1; }
            100% { opacity: 0; }
        }

        canvas {
            position: fixed;
            top: 0;
            left: 0;
            z-index: -1;
        }
    </style>
</head>
<body>
    <canvas id="fireworks"></canvas>
    
    <div class="container">
        <!-- 第一阶段：初始文字 -->
        <div class="blessing" id="phase1">
            <h2>亲爱的朋友：</h2>
            <p>在这辞旧迎新的时刻</p>
            <p>请轻触<span class="secret-link" onclick="revealPhase2()">神秘福袋</span></p>
            <p>开启属于你的新春祝福</p>
        </div>

        <!-- 第二阶段：滑动解锁 -->
        <div class="scroll-section" id="phase2">
            <div class="blessing">
                <h3>🐍 蛇年运势解锁中...</h3>
                <p>▢▢▢▢▢▢ 30%</p>
                <p>向上滑动屏幕加速解锁</p>
            </div>
        </div>

        <!-- 第三阶段：烟花触发按钮 -->
        <div id="phase3" class="firework-trigger"></div>
    </div>

    <script>
        // 第一阶段：点击福袋
        function revealPhase2() {
            document.getElementById('phase1').style.opacity = 0;
            document.getElementById('phase2').classList.add('revealed');
            initScrollDetection();
        }

        // 第二阶段：滑动解锁
        let scrollCount = 0;
        function initScrollDetection() {
            let startY;
            document.addEventListener('touchstart', e => {
                startY = e.touches[0].clientY;
            }, false);

            document.addEventListener('touchmove', e => {
                const currentY = e.touches[0].clientY;
                if (startY - currentY > 50) { // 上滑
                    scrollCount++;
                    updateProgress();
                    startY = currentY;
                }
            }, false);
        }

        function updateProgress() {
            const progress = Math.min(30 + scrollCount*10, 100);
            document.querySelector('#phase2 p').innerHTML = 
                `▣${'▢'.repeat(9 - Math.floor(progress/10))} ${progress}%`;

            if (progress >= 100) {
                showFireworkTrigger();
            }
        }

        // 第三阶段：显示烟花按钮
        function showFireworkTrigger() {
            const trigger = document.querySelector('.firework-trigger');
            trigger.style.opacity = '1';
            trigger.addEventListener('click', startFireworks);
            trigger.style.animation = 'float 2s infinite';
        }

        // 烟花效果
        class Firework {
            constructor() {
                this.canvas = document.getElementById('fireworks');
                this.ctx = this.canvas.getContext('2d');
                this.resize();
                window.addEventListener('resize', () => this.resize());
            }

            resize() {
                this.canvas.width = window.innerWidth;
                this.canvas.height = window.innerHeight;
            }

            launch(x, y) {
                const particles = [];
                const colors = ['#FF1177', '#FFCC00', '#22FF88', '#00FFFF'];
                
                for(let i=0; i<100; i++) {
                    particles.push({
                        x, y,
                        vx: (Math.random()-0.5)*8,
                        vy: (Math.random()-0.5)*8 - 2,
                        color: colors[Math.floor(Math.random()*colors.length)],
                        life: 1
                    });
                }

                const animate = () => {
                    particles.forEach(p => {
                        p.x += p.vx;
                        p.y += p.vy;
                        p.vy += 0.1;
                        p.life -= 0.02;

                        this.ctx.beginPath();
                        this.ctx.arc(p.x, p.y, 2, 0, Math.PI*2);
                        this.ctx.fillStyle = p.color;
                        this.ctx.globalAlpha = p.life;
                        this.ctx.fill();
                    });

                    particles.filter(p => p.life > 0);
                    requestAnimationFrame(animate);
                };

                animate();
            }
        }

        const firework = new Firework();
        function startFireworks() {
            setInterval(() => {
                const x = Math.random() * window.innerWidth;
                const y = Math.random() * window.innerHeight;
                firework.launch(x, y);
            }, 500);
            
            document.body.style.background = 'radial-gradient(circle, #2c3e50, #0a0a23)';
            document.getElementById('phase3').style.display = 'none';
        }

        // 初始化渐现动画
        setTimeout(() => {
            document.getElementById('phase1').style.opacity = 1;
        }, 500);
    </script>
</body>
</html>
