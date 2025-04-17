<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Magische Blumen Animation</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: #000;
            display: flex;
            justify-content: center;
            align-items: flex-end;
            min-height: 100vh;
        }
        canvas {
            display: block;
            width: 100%;
            height: 100vh;
        }
    </style>
</head>
<body>
    <canvas id="canvas"></canvas>
    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');

        // Canvas-Größe an Fenster anpassen
        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Farben
        const flowerColor = '#40E0D0';
        const grassColor = '#4a5';
        const particleColor = '#ff0';

        // Partikel-Klasse
        class Particle {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.size = Math.random() * 2 + 1;
                this.speedX = Math.random() * 1 - 0.5;
                this.speedY = Math.random() * -1 - 0.5;
                this.alpha = 1;
            }

            update() {
                this.x += this.speedX;
                this.y += this.speedY;
                this.alpha -= 0.02;
            }

            draw() {
                ctx.save();
                ctx.globalAlpha = this.alpha;
                ctx.fillStyle = particleColor;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fill();
                ctx.restore();
            }
        }

        // Blumen-Klasse
        class Flower {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.particles = [];
                this.angle = 0;
                this.stemHeight = 60;
                this.petalSize = 20;
            }

            createParticles() {
                if (Math.random() < 0.2) {
                    this.particles.push(new Particle(
                        this.x + Math.random() * 20 - 10,
                        this.y - this.stemHeight + Math.random() * 20 - 10
                    ));
                }
            }

            drawStem() {
                // Grashalme im Hintergrund
                for (let i = -2; i <= 2; i++) {
                    ctx.beginPath();
                    ctx.moveTo(this.x + i * 15, this.y);
                    ctx.lineTo(this.x + i * 15 + Math.sin(this.angle + i) * 10, 
                             this.y - 70 - Math.abs(i * 5));
                    ctx.strokeStyle = grassColor;
                    ctx.lineWidth = 2;
                    ctx.stroke();
                }
            }

            drawFlower() {
                const centerX = this.x;
                const centerY = this.y - this.stemHeight;

                // Blütenblätter
                for (let i = 0; i < 5; i++) {
                    const petalAngle = (i * Math.PI * 2) / 5 + this.angle;
                    ctx.beginPath();
                    ctx.fillStyle = flowerColor;
                    ctx.ellipse(
                        centerX + Math.cos(petalAngle) * 5,
                        centerY + Math.sin(petalAngle) * 5,
                        this.petalSize,
                        this.petalSize,
                        petalAngle,
                        0,
                        Math.PI * 2
                    );
                    ctx.fill();
                }

                // Blütenmitte
                ctx.beginPath();
                ctx.fillStyle = '#ff0';
                ctx.arc(centerX, centerY, 10, 0, Math.PI * 2);
                ctx.fill();
            }

            update() {
                this.angle += 0.005;
                this.createParticles();
                
                this.particles = this.particles.filter(particle => {
                    particle.update();
                    return particle.alpha > 0;
                });
            }

            draw() {
                this.drawStem();
                this.drawFlower();
                this.particles.forEach(particle => particle.draw());
            }
        }

        // Szene erstellen
        const flowers = [
            new Flower(canvas.width / 2, canvas.height - 20),
            new Flower(canvas.width / 2 - 80, canvas.height - 20),
            new Flower(canvas.width / 2 + 80, canvas.height - 20)
        ];

        // Animations-Schleife
        function animate() {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.05)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            flowers.forEach(flower => {
                flower.update();
                flower.draw();
            });

            requestAnimationFrame(animate);
        }

        animate();
    </script>
</body>
</html>
