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
            align-items: center;
            min-height: 100vh;
        }
        canvas {
            display: block;
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
        const grassColor = '#32CD32';
        const particleColor = '#FFFF00';

        // Partikel-Klasse
        class Particle {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.size = Math.random() * 3;
                this.speedX = Math.random() * 2 - 1;
                this.speedY = Math.random() * 2 - 1;
                this.alpha = 1;
            }

            update() {
                this.x += this.speedX;
                this.y += this.speedY;
                this.alpha -= 0.01;
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
                this.stemHeight = 100 + Math.random() * 50;
            }

            createParticles() {
                if (Math.random() < 0.1) {
                    this.particles.push(new Particle(
                        this.x + Math.random() * 40 - 20,
                        this.y - this.stemHeight + Math.random() * 40 - 20
                    ));
                }
            }

            drawStem() {
                // Stängel
                ctx.beginPath();
                ctx.moveTo(this.x, this.y);
                ctx.lineTo(this.x, this.y - this.stemHeight);
                ctx.strokeStyle = grassColor;
                ctx.lineWidth = 2;
                ctx.stroke();

                // Blätter
                for (let i = 0; i < 3; i++) {
                    const leafY = this.y - (this.stemHeight * (0.3 + i * 0.2));
                    const leafSize = 15;
                    
                    ctx.beginPath();
                    ctx.moveTo(this.x, leafY);
                    ctx.quadraticCurveTo(
                        this.x + leafSize * Math.cos(this.angle + i),
                        leafY - leafSize,
                        this.x + leafSize * 2 * Math.cos(this.angle + i),
                        leafY
                    );
                    ctx.strokeStyle = grassColor;
                    ctx.stroke();
                }
            }

            drawFlower() {
                // Blütenblätter
                for (let i = 0; i < 5; i++) {
                    const petalAngle = (i * Math.PI * 2) / 5 + this.angle;
                    const petalX = this.x + Math.cos(petalAngle) * 15;
                    const petalY = this.y - this.stemHeight + Math.sin(petalAngle) * 15;

                    ctx.beginPath();
                    ctx.fillStyle = flowerColor;
                    ctx.ellipse(petalX, petalY, 10, 15, petalAngle, 0, Math.PI * 2);
                    ctx.fill();
                }

                // Blütenmitte
                ctx.beginPath();
                ctx.fillStyle = '#FFFF00';
                ctx.arc(this.x, this.y - this.stemHeight, 8, 0, Math.PI * 2);
                ctx.fill();
            }

            update() {
                this.angle += 0.01;
                this.createParticles();
                
                // Partikel aktualisieren
                this.particles = this.particles.filter(particle => {
                    particle.update();
                    return particle.alpha > 0;
                });
            }

            draw() {
                this.drawStem();
                this.drawFlower();
                
                // Partikel zeichnen
                this.particles.forEach(particle => particle.draw());
            }
        }

        // Grashalme-Klasse
        class Grass {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.height = 30 + Math.random() * 20;
                this.angle = Math.random() * 0.5 - 0.25;
            }

            draw() {
                ctx.beginPath();
                ctx.moveTo(this.x, this.y);
                ctx.lineTo(
                    this.x + Math.sin(this.angle) * this.height,
                    this.y - this.height
                );
                ctx.strokeStyle = grassColor;
                ctx.lineWidth = 2;
                ctx.stroke();
            }

            update() {
                this.angle += Math.sin(Date.now() / 1000) * 0.02;
            }
        }

        // Szene erstellen
        const flowers = [
            new Flower(canvas.width / 2, canvas.height - 50),
            new Flower(canvas.width / 2 - 100, canvas.height - 30),
            new Flower(canvas.width / 2 + 100, canvas.height - 40)
        ];

        const grasses = Array.from({ length: 20 }, () => 
            new Grass(
                Math.random() * canvas.width,
                canvas.height
            )
        );

        // Animations-Schleife
        function animate() {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.05)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            grasses.forEach(grass => {
                grass.update();
                grass.draw();
            });

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
