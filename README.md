<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Catapulta Física Interactiva</title>
    <!-- Tailwind CSS para diseño rápido y moderno -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome para iconos bonitos -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body {
            user-select: none;
            -webkit-user-select: none;
        }
        canvas {
            touch-action: none;
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col font-sans overflow-x-hidden">

    <!-- Encabezado / Navbar -->
    <header class="bg-slate-800 border-b border-slate-700 py-4 px-6 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-col sm:flex-row justify-between items-center gap-4">
            <div class="flex items-center gap-3">
                <div class="bg-amber-600 p-2.5 rounded-lg text-white shadow-lg shadow-amber-900/40">
                    <i class="fa-solid fa-rocket launch-icon text-xl"></i>
                </div>
                <div>
                    <h1 class="text-2xl font-black tracking-tight text-white">Catapult<span class="text-amber-500">Sim 2D</span></h1>
                    <p class="text-xs text-slate-400">Física de tensión, proyección de trayectoria y destrucción táctica</p>
                </div>
            </div>
            <div class="flex items-center gap-3">
                <button id="btn-reset-castle" class="bg-slate-700 hover:bg-slate-600 text-slate-200 px-4 py-2 rounded-lg text-sm font-semibold transition-colors flex items-center gap-2 border border-slate-600">
                    <i class="fa-solid fa-cubes"></i> Reconstruir Estructura
                </button>
                <button id="btn-reset-stats" class="bg-slate-700 hover:bg-slate-600 text-slate-200 px-4 py-2 rounded-lg text-sm font-semibold transition-colors flex items-center gap-2 border border-slate-600">
                    <i class="fa-solid fa-rotate"></i> Reiniciar Todo
                </button>
            </div>
        </div>
    </header>

    <!-- Área de Juego Principal -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 lg:p-6 grid grid-cols-1 lg:grid-cols-4 gap-6">
        
        <!-- Panel Izquierdo: Controles y Parámetros -->
        <div class="lg:col-span-1 flex flex-col gap-6">
            
            <!-- Tipo de Proyectil -->
            <div class="bg-slate-800 p-5 rounded-xl border border-slate-700 shadow-xl">
                <h3 class="text-sm font-semibold uppercase tracking-wider text-slate-400 mb-4 flex items-center gap-2">
                    <i class="fa-solid fa-circle text-amber-500"></i> Seleccionar Proyectil
                </h3>
                <div class="grid grid-cols-3 gap-2" id="ammo-selector">
                    <button data-type="stone" class="ammo-btn p-3 bg-amber-600/20 border-2 border-amber-500 rounded-lg text-center flex flex-col items-center gap-1.5 transition-all hover:scale-105">
                        <span class="text-2xl">🪨</span>
                        <span class="text-xs font-bold text-slate-200">Piedra</span>
                    </button>
                    <button data-type="bomb" class="ammo-btn p-3 bg-slate-700/50 border-2 border-transparent rounded-lg text-center flex flex-col items-center gap-1.5 transition-all hover:scale-105">
                        <span class="text-2xl">💣</span>
                        <span class="text-xs font-bold text-slate-200">Bomba</span>
                    </button>
                    <button data-type="rubber" class="ammo-btn p-3 bg-slate-700/50 border-2 border-transparent rounded-lg text-center flex flex-col items-center gap-1.5 transition-all hover:scale-105">
                        <span class="text-2xl">⚽</span>
                        <span class="text-xs font-bold text-slate-200">Rebotadora</span>
                    </button>
                </div>
            </div>

            <!-- Ajustes de Entorno / Simulación -->
            <div class="bg-slate-800 p-5 rounded-xl border border-slate-700 shadow-xl flex-1 flex flex-col gap-5">
                <h3 class="text-sm font-semibold uppercase tracking-wider text-slate-400 flex items-center gap-2">
                    <i class="fa-solid fa-sliders text-amber-500"></i> Ajustes de Física
                </h3>
                
                <!-- Gravedad -->
                <div class="flex flex-col gap-1.5">
                    <div class="flex justify-between text-xs font-medium">
                        <span class="text-slate-300">Gravedad</span>
                        <span id="val-gravity" class="text-amber-400 font-bold">9.8 m/s²</span>
                    </div>
                    <input type="range" id="slider-gravity" min="2" max="25" step="0.5" value="9.8" 
                           class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer accent-amber-500">
                    <span class="text-[10px] text-slate-500">Afecta la caída del proyectil hacia el suelo</span>
                </div>

                <!-- Elasticidad / Fuerza de la Catapulta -->
                <div class="flex flex-col gap-1.5">
                    <div class="flex justify-between text-xs font-medium">
                        <span class="text-slate-300">Tensión Elástica</span>
                        <span id="val-elasticity" class="text-amber-400 font-bold">1.0x</span>
                    </div>
                    <input type="range" id="slider-elasticity" min="0.4" max="2.0" step="0.1" value="1.0" 
                           class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer accent-amber-500">
                    <span class="text-[10px] text-slate-500">Multiplicador de la potencia de empuje del resorte</span>
                </div>

                <!-- Resistencia del Viento -->
                <div class="flex flex-col gap-1.5">
                    <div class="flex justify-between text-xs font-medium">
                        <span class="text-slate-300">Viento</span>
                        <span id="val-wind" class="text-amber-400 font-bold">Sin Viento</span>
                    </div>
                    <input type="range" id="slider-wind" min="-15" max="15" step="1" value="0" 
                           class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer accent-amber-500">
                    <span class="text-[10px] text-slate-500">Fuerza lateral constante (viento a favor/contra)</span>
                </div>

                <!-- Material de Bloques -->
                <div class="flex flex-col gap-1.5">
                    <label class="text-xs font-medium text-slate-300">Material de la Estructura</label>
                    <select id="select-block-material" class="bg-slate-700 text-slate-200 text-xs rounded-lg p-2.5 outline-none border border-slate-600 focus:border-amber-500">
                        <option value="wood">Madera (Equilibrado)</option>
                        <option value="ice">Hielo (Frágil / Resbaladizo)</option>
                        <option value="stone">Piedra Pesada (Alta Resistencia)</option>
                    </select>
                </div>

                <!-- Marcador de estadísticas rápidas -->
                <div class="mt-auto pt-4 border-t border-slate-700/60 grid grid-cols-2 gap-3 text-center">
                    <div class="bg-slate-900/40 p-2 rounded-lg">
                        <span class="block text-[10px] uppercase text-slate-500 font-bold">Lanzamientos</span>
                        <span id="stat-shots" class="text-xl font-black text-slate-200">0</span>
                    </div>
                    <div class="bg-slate-900/40 p-2 rounded-lg">
                        <span class="block text-[10px] uppercase text-slate-500 font-bold">Max Distancia</span>
                        <span id="stat-distance" class="text-xl font-black text-amber-500">0.0m</span>
                    </div>
                </div>
            </div>
        </div>

        <!-- Panel Central/Derecho: El lienzo de simulación -->
        <div class="lg:col-span-3 flex flex-col gap-4">
            
            <!-- Lienzo del simulador -->
            <div class="relative bg-slate-950 rounded-2xl overflow-hidden border border-slate-850 shadow-2xl flex-1 min-h-[500px] flex items-stretch">
                <canvas id="canvas-simulator" class="w-full h-full block cursor-crosshair"></canvas>
                
                <!-- Superposición de guía / instrucciones flotantes -->
                <div class="absolute top-4 left-4 pointer-events-none flex flex-col gap-2">
                    <div class="bg-slate-900/90 backdrop-blur px-3 py-2 rounded-lg border border-slate-700 text-xs flex items-center gap-2">
                        <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                        <span class="text-slate-300">Presiona y arrastra la copa de la catapulta hacia atrás</span>
                    </div>
                </div>

                <!-- Barra de Tensión Visual Integrada en Interfaz (por si acaso) -->
                <div class="absolute bottom-4 left-4 right-4 bg-slate-900/80 backdrop-blur p-3 rounded-xl border border-slate-700 flex flex-col gap-1.5 max-w-md pointer-events-none">
                    <div class="flex justify-between text-xs font-bold">
                        <span class="text-slate-400 uppercase tracking-wide">Fuerza de Lanzamiento (Tensión)</span>
                        <span id="tension-percentage" class="text-emerald-400">0%</span>
                    </div>
                    <div class="w-full bg-slate-800 rounded-full h-3.5 overflow-hidden p-[2px] border border-slate-700">
                        <div id="tension-bar-fill" class="h-full bg-emerald-500 rounded-full w-[0%] transition-all duration-75"></div>
                    </div>
                </div>
                
                <!-- Telemetría Dinámica -->
                <div class="absolute top-4 right-4 bg-slate-900/90 backdrop-blur p-3 rounded-xl border border-slate-700 text-xs flex flex-col gap-1 pointer-events-none min-w-[150px]">
                    <div class="text-[10px] font-bold text-slate-500 uppercase">Telemetría de Impacto</div>
                    <div class="flex justify-between"><span class="text-slate-400">Vel. Salida:</span> <span id="tele-speed" class="font-mono text-amber-400">0.0 m/s</span></div>
                    <div class="flex justify-between"><span class="text-slate-400">Ángulo:</span> <span id="tele-angle" class="font-mono text-amber-400">0°</span></div>
                    <div class="flex justify-between"><span class="text-slate-400">Último Alcance:</span> <span id="tele-last" class="font-mono text-emerald-400">0.0 m</span></div>
                </div>
            </div>

            <!-- Leyenda y Guía de Atajos de Teclado -->
            <div class="bg-slate-800/60 border border-slate-700/60 p-4 rounded-xl flex flex-wrap gap-4 items-center justify-between text-xs text-slate-400">
                <div class="flex gap-4 flex-wrap">
                    <span class="flex items-center gap-1.5"><kbd class="bg-slate-700 px-1.5 py-0.5 rounded text-slate-200">Arras. Clic Izq</kbd> Tensar Catapulta</span>
                    <span class="flex items-center gap-1.5"><kbd class="bg-slate-700 px-1.5 py-0.5 rounded text-slate-200">Soltar Clic</kbd> Disparar Proyectil</span>
                    <span class="flex items-center gap-1.5"><kbd class="bg-slate-700 px-1.5 py-0.5 rounded text-slate-200">R</kbd> Reconstruir Castillo</span>
                </div>
                <div class="text-slate-500">Físicas renderizadas en tiempo real en HTML5 Canvas</div>
            </div>
        </div>
    </main>

    <!-- Modal de Información de Explosión / Efectos de Impacto -->
    <div id="explosion-notification" class="fixed bottom-6 right-6 bg-amber-500 text-slate-950 px-4 py-3 rounded-lg font-bold text-sm shadow-2xl flex items-center gap-2 transform translate-y-24 opacity-0 transition-all duration-300 pointer-events-none z-50">
        <i class="fa-solid fa-burst text-lg"></i>
        <span id="notification-text">¡Impacto Crítico Detectado!</span>
    </div>

    <!-- Script del Motor de Física de la Catapulta -->
    <script>
        // --- CONFIGURACIÓN E INICIALIZACIÓN ---
        const canvas = document.getElementById('canvas-simulator');
        const ctx = canvas.getContext('2d');

        // Adaptar canvas a su contenedor
        function resizeCanvas() {
            const rect = canvas.parentElement.getBoundingClientRect();
            canvas.width = rect.width;
            canvas.height = rect.height;
        }
        resizeCanvas();
        window.addEventListener('resize', () => {
            resizeCanvas();
            initLevel(); // Re-inicializar para ajustar las posiciones de la estructura
        });

        // Controles de UI
        const sliderGravity = document.getElementById('slider-gravity');
        const sliderElasticity = document.getElementById('slider-elasticity');
        const sliderWind = document.getElementById('slider-wind');
        const selectBlockMaterial = document.getElementById('select-block-material');
        const ammoButtons = document.querySelectorAll('.ammo-btn');
        const tensionBarFill = document.getElementById('tension-bar-fill');
        const tensionPercentageText = document.getElementById('tension-percentage');
        
        const valGravity = document.getElementById('val-gravity');
        const valElasticity = document.getElementById('val-elasticity');
        const valWind = document.getElementById('val-wind');
        
        const statShots = document.getElementById('stat-shots');
        const statDistance = document.getElementById('stat-distance');
        
        const teleSpeed = document.getElementById('tele-speed');
        const teleAngle = document.getElementById('tele-angle');
        const teleLast = document.getElementById('tele-last');
        
        const btnResetCastle = document.getElementById('btn-reset-castle');
        const btnResetStats = document.getElementById('btn-reset-stats');

        // --- PARÁMETROS DE SIMULACIÓN Y CONSTANTES ---
        const PHYSICS = {
            gravity: 9.8,
            elasticity: 1.0,
            wind: 0,
            scale: 15, // Píxeles por metro
            groundHeight: 80, // Distancia desde abajo
        };

        const MATERIALS = {
            wood: { name: 'Madera', density: 1.2, color: '#C19A6B', stroke: '#8B5A2B', health: 40, friction: 0.15, restitution: 0.2 },
            ice: { name: 'Hielo', density: 0.8, color: '#A5F2F3', stroke: '#50D2D6', health: 15, friction: 0.02, restitution: 0.4 },
            stone: { name: 'Piedra', density: 2.5, color: '#808080', stroke: '#505050', health: 90, friction: 0.3, restitution: 0.1 }
        };

        const PROJECTILES = {
            stone: {
                name: 'Piedra Estándar',
                radius: 14,
                mass: 5,
                color: '#6e7f80',
                outline: '#414a4c',
                emoji: '🪨',
                restitution: 0.3,
                isBomb: false
            },
            bomb: {
                name: 'Bomba de Demolición',
                radius: 16,
                mass: 8,
                color: '#262626',
                outline: '#1a1a1a',
                emoji: '💣',
                restitution: 0.1,
                isBomb: true,
                fuse: 120 // Cuadros antes de explotar tras impacto
            },
            rubber: {
                name: 'Pelota Ligera',
                radius: 12,
                mass: 2,
                color: '#ff4d4d',
                outline: '#cc0000',
                emoji: '⚽',
                restitution: 0.85,
                isBomb: false
            }
        };

        // Estado de juego global
        let state = {
            currentAmmo: 'stone',
            shotsFired: 0,
            maxDistance: 0,
            activeProjectiles: [],
            blocks: [],
            particles: [],
            blockMaterial: 'wood',
            lastShootSpeed: 0,
            lastShootAngle: 0,
            lastDistance: 0,
        };

        // Configuración de la Catapulta
        const catapult = {
            baseX: 180,
            baseY: 0, // Se calcula según el alto de la pantalla
            pivotX: 180,
            pivotY: 0,
            armLength: 100,
            currentArmAngle: -Math.PI / 4, // Ángulo en reposo
            restingAngle: -Math.PI / 4,
            cupX: 0,
            cupY: 0,
            isDragging: false,
            dragX: 0,
            dragY: 0,
            maxDragDistance: 130, // Máxima distancia para jalar
            releaseForceMult: 0.32, // Escala de fuerza transferida al proyectil
        };

        // Configurar la posición vertical de la base de la catapulta adaptada al tamaño del canvas
        function updateCatapultPositions() {
            catapult.baseY = canvas.height - PHYSICS.groundHeight;
            catapult.pivotX = catapult.baseX;
            catapult.pivotY = catapult.baseY - 70; // 70px de altura de la base de madera
            
            // Posición de la taza en reposo
            catapult.cupX = catapult.pivotX + Math.cos(catapult.restingAngle) * catapult.armLength;
            catapult.cupY = catapult.pivotY + Math.sin(catapult.restingAngle) * catapult.armLength;
        }

        // --- SISTEMA DE PARTÍCULAS ---
        class Particle {
            constructor(x, y, vx, vy, color, size, life, decay, type = 'smoke') {
                this.x = x;
                this.y = y;
                this.vx = vx;
                this.vy = vy;
                this.color = color;
                this.size = size;
                this.life = life; // Opacidad máxima / tiempo
                this.maxLife = life;
                this.decay = decay; // Cuánto disminuye la vida por frame
                this.type = type;
            }

            update() {
                this.x += this.vx;
                this.y += this.vy;
                if (this.type === 'debris') {
                    this.vy += PHYSICS.gravity * 0.05; // Gravedad suave para astillas
                }
                this.life -= this.decay;
            }

            draw() {
                ctx.save();
                ctx.globalAlpha = Math.max(0, this.life / this.maxLife);
                ctx.fillStyle = this.color;
                ctx.beginPath();
                if (this.type === 'star') {
                    // Dibujar estrellita
                    const rot = Math.PI / 2 * 3;
                    let x = this.x;
                    let y = this.y;
                    let step = Math.PI / 5;
                    ctx.moveTo(this.x, this.y - this.size);
                    for (let i = 0; i < 5; i++) {
                        x = this.x + Math.cos(rot + i * step * 2) * this.size;
                        y = this.y + Math.sin(rot + i * step * 2) * this.size;
                        ctx.lineTo(x, y);
                    }
                    ctx.closePath();
                    ctx.fill();
                } else {
                    ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                    ctx.fill();
                }
                ctx.restore();
            }
        }

        function createExplosion(x, y, color, count = 20) {
            for (let i = 0; i < count; i++) {
                const angle = Math.random() * Math.PI * 2;
                const speed = 2 + Math.random() * 6;
                const size = 2 + Math.random() * 5;
                const life = 30 + Math.random() * 30;
                state.particles.push(new Particle(
                    x, y,
                    Math.cos(angle) * speed,
                    Math.sin(angle) * speed,
                    color,
                    size,
                    life,
                    1,
                    'smoke'
                ));
            }
        }

        function createDebris(x, y, color, count = 8) {
            for (let i = 0; i < count; i++) {
                const angle = -Math.random() * Math.PI; // Solo hacia arriba/lados
                const speed = 1 + Math.random() * 4;
                const size = 1.5 + Math.random() * 3;
                const life = 20 + Math.random() * 20;
                state.particles.push(new Particle(
                    x, y,
                    Math.cos(angle) * speed,
                    Math.sin(angle) * speed,
                    color,
                    size,
                    life,
                    1,
                    'debris'
                ));
            }
        }

        // --- CLASE PROYECTIL ---
        class Projectile {
            constructor(x, y, vx, vy, type) {
                this.x = x;
                this.y = y;
                this.vx = vx;
                this.vy = vy;
                this.type = type;
                
                const props = PROJECTILES[type];
                this.radius = props.radius;
                this.mass = props.mass;
                this.color = props.color;
                this.outline = props.outline;
                this.emoji = props.emoji;
                this.restitution = props.restitution;
                this.isBomb = props.isBomb;
                this.originalVx = vx; // Para telemetría
                this.originalVy = vy;

                this.trail = [];
                this.isActive = true;
                this.collided = false;
                this.fuseTimer = props.isBomb ? props.fuse : 0;
                this.hasExploded = false;
                this.isResting = false;
            }

            update() {
                if (this.isResting) return;

                // Guardar rastro
                this.trail.push({ x: this.x, y: this.y });
                if (this.trail.length > 35) this.trail.shift();

                // Aplicar aceleración por fuerzas (Fórmula de Euler)
                // Fuerza del viento (proporcional al radio/resistencia)
                const windAcc = PHYSICS.wind * 0.05 / this.mass;
                this.vx += windAcc;
                // Gravedad
                this.vy += (PHYSICS.gravity * 0.15); // Factor de escala para hacerlo visualmente agradable

                // Actualizar posición
                this.x += this.vx;
                this.y += this.vy;

                // Generar humo de arrastre si es bomba
                if (this.isBomb && Math.random() < 0.4) {
                    state.particles.push(new Particle(this.x, this.y, -this.vx*0.2, -this.vy*0.2, '#ffa500', 3, 15, 0.8));
                }

                // Colisión con el suelo
                const groundY = canvas.height - PHYSICS.groundHeight;
                if (this.y + this.radius >= groundY) {
                    this.y = groundY - this.radius;
                    this.vy = -this.vy * this.restitution;
                    this.vx *= 0.8; // Fricción con el suelo
                    
                    if (!this.collided) {
                        this.collided = true;
                        createDebris(this.x, this.y + this.radius, '#8B5A2B', 5);
                        triggerImpactTelemetry(this);
                    }

                    // Determinar si ya se detuvo del todo
                    if (Math.abs(this.vy) < 0.2 && Math.abs(this.vx) < 0.1) {
                        this.vy = 0;
                        this.vx = 0;
                        this.isResting = true;
                    }
                }

                // Colisiones con paredes laterales
                if (this.x - this.radius < 0) {
                    this.x = this.radius;
                    this.vx = -this.vx * this.restitution;
                } else if (this.x + this.radius > canvas.width) {
                    this.x = canvas.width - this.radius;
                    this.vx = -this.vx * this.restitution;
                }

                // Manejo de fusible de bomba
                if (this.isBomb && this.collided && !this.hasExploded) {
                    this.fuseTimer--;
                    if (this.fuseTimer <= 0) {
                        this.explode();
                    }
                }
            }

            explode() {
                if (this.hasExploded) return;
                this.hasExploded = true;
                this.isActive = false;

                // Efecto visual
                createExplosion(this.x, this.y, '#ff4500', 40);
                createExplosion(this.x, this.y, '#ffd700', 30);
                createExplosion(this.x, this.y, '#ffffff', 15);

                // Ondas expansivas en bloques
                const explosionRadius = 140;
                const explosionForce = 35;

                state.blocks.forEach(block => {
                    const dx = block.x + block.width/2 - this.x;
                    const dy = block.y + block.height/2 - this.y;
                    const dist = Math.sqrt(dx*dx + dy*dy);

                    if (dist < explosionRadius && dist > 0) {
                        const forceFactor = (explosionRadius - dist) / explosionRadius;
                        const angle = Math.atan2(dy, dx);
                        
                        block.vx += Math.cos(angle) * explosionForce * forceFactor / block.mass;
                        block.vy += Math.sin(angle) * explosionForce * forceFactor / block.mass;
                        
                        // Daño al bloque
                        const damage = Math.round(120 * forceFactor);
                        block.health -= damage;
                    }
                });

                showToast("¡EXPLOSIÓN DETONADA! Estructuras dañadas.");
            }

            draw() {
                // Dibujar rastro suave
                if (this.trail.length > 1) {
                    ctx.beginPath();
                    ctx.moveTo(this.trail[0].x, this.trail[0].y);
                    for (let i = 1; i < this.trail.length; i++) {
                        ctx.lineTo(this.trail[i].x, this.trail[i].y);
                    }
                    ctx.strokeStyle = `rgba(245, 158, 11, ${this.isBomb ? '0.15' : '0.35'})`;
                    ctx.lineWidth = this.radius * 0.7;
                    ctx.lineCap = 'round';
                    ctx.lineJoin = 'round';
                    ctx.stroke();
                }

                // Dibujar proyectil principal
                ctx.save();
                ctx.translate(this.x, this.y);
                
                // Efecto de rotación basado en velocidad
                const rotation = (Date.now() / 150) * (this.vx > 0 ? 1 : -1);
                ctx.rotate(rotation);

                // Cuerpo
                ctx.fillStyle = this.color;
                ctx.strokeStyle = this.outline;
                ctx.lineWidth = 2.5;
                ctx.beginPath();
                ctx.arc(0, 0, this.radius, 0, Math.PI * 2);
                ctx.fill();
                ctx.stroke();

                // Detalles específicos por tipo
                if (this.type === 'stone') {
                    // Grietas de piedra
                    ctx.strokeStyle = 'rgba(0,0,0,0.15)';
                    ctx.beginPath();
                    ctx.moveTo(-this.radius * 0.4, -this.radius * 0.2);
                    ctx.lineTo(this.radius * 0.3, this.radius * 0.3);
                    ctx.moveTo(-this.radius * 0.2, this.radius * 0.4);
                    ctx.lineTo(this.radius * 0.2, -this.radius * 0.5);
                    ctx.stroke();
                } else if (this.type === 'bomb') {
                    // Detalle de la mecha de la bomba y chispa
                    ctx.restore(); // Deshacer rotación para que la mecha siempre apunte arriba
                    ctx.save();
                    ctx.translate(this.x, this.y);

                    ctx.strokeStyle = '#a0522d';
                    ctx.lineWidth = 3;
                    ctx.beginPath();
                    ctx.moveTo(0, -this.radius + 2);
                    ctx.quadraticCurveTo(8, -this.radius - 8, 12, -this.radius - 12);
                    ctx.stroke();

                    // Mecha ardiendo (chispitas)
                    if (this.collided && !this.hasExploded) {
                        ctx.fillStyle = '#ff3300';
                        ctx.beginPath();
                        ctx.arc(12, -this.radius - 12, 4 + Math.sin(Date.now()/50)*2, 0, Math.PI*2);
                        ctx.fill();
                    } else {
                        ctx.fillStyle = '#ffcc00';
                        ctx.beginPath();
                        ctx.arc(12, -this.radius - 12, 2.5, 0, Math.PI*2);
                        ctx.fill();
                    }
                } else if (this.type === 'rubber') {
                    // Líneas deportivas/brillo
                    ctx.strokeStyle = 'rgba(255,255,255,0.4)';
                    ctx.beginPath();
                    ctx.arc(0, 0, this.radius * 0.6, Math.PI, Math.PI * 1.8);
                    ctx.stroke();
                }

                ctx.restore();
            }
        }

        // --- CLASE BLOQUE (ESTRUCTURA DESTRIBUIBLE) ---
        class Block {
            constructor(x, y, width, height, materialType) {
                this.x = x;
                this.y = y;
                this.width = width;
                this.height = height;
                this.materialType = materialType;
                
                const props = MATERIALS[materialType];
                this.density = props.density;
                this.maxHealth = props.health;
                this.health = props.health;
                this.color = props.color;
                this.stroke = props.stroke;
                this.friction = props.friction;
                this.restitution = props.restitution;
                this.mass = (width * height * this.density) / 1000;

                this.vx = 0;
                this.vy = 0;
                this.isActive = true;
                this.isResting = false;
            }

            update() {
                if (this.health <= 0) {
                    this.isActive = false;
                    createDebris(this.x + this.width/2, this.y + this.height/2, this.color, 12);
                    return;
                }

                // Gravedad
                this.vy += PHYSICS.gravity * 0.15;
                
                // Actualizar posición
                this.x += this.vx;
                this.y += this.vy;

                // Fricción ambiental
                this.vx *= 0.98;
                this.vy *= 0.98;

                // Suelo de colisión
                const groundY = canvas.height - PHYSICS.groundHeight;
                if (this.y + this.height >= groundY) {
                    this.y = groundY - this.height;
                    this.vy = -this.vy * this.restitution;
                    this.vx *= (1 - this.friction);

                    if (Math.abs(this.vy) < 0.25) this.vy = 0;
                    if (Math.abs(this.vx) < 0.15) this.vx = 0;
                }

                // Colisiones con bordes laterales
                if (this.x < 0) {
                    this.x = 0;
                    this.vx = -this.vx * this.restitution;
                } else if (this.x + this.width > canvas.width) {
                    this.x = canvas.width - this.width;
                    this.vx = -this.vx * this.restitution;
                }
            }

            draw() {
                ctx.save();
                
                // Opacidad según vida
                const damagePercent = this.health / this.maxHealth;
                ctx.fillStyle = this.color;
                ctx.strokeStyle = this.stroke;
                ctx.lineWidth = 1.5;

                // Dibujar bloque
                ctx.beginPath();
                ctx.roundRect(this.x, this.y, this.width, this.height, 4);
                ctx.fill();
                ctx.stroke();

                // Mostrar grietas si está dañado
                if (damagePercent < 0.75) {
                    ctx.strokeStyle = 'rgba(0,0,0,0.2)';
                    ctx.lineWidth = 1;
                    ctx.beginPath();
                    ctx.moveTo(this.x + this.width*0.2, this.y + this.height*0.2);
                    ctx.lineTo(this.x + this.width*0.4, this.y + this.height*0.7);
                    if (damagePercent < 0.4) {
                        ctx.lineTo(this.x + this.width*0.8, this.y + this.height*0.8);
                        ctx.moveTo(this.x + this.width*0.7, this.y + this.height*0.1);
                        ctx.lineTo(this.x + this.width*0.3, this.y + this.height*0.5);
                    }
                    ctx.stroke();
                }

                ctx.restore();
            }
        }

        // --- CONSTRUIR ESCENARIOS ---
        function initLevel() {
            state.blocks = [];
            state.activeProjectiles = [];
            state.particles = [];
            
            updateCatapultPositions();

            // Ubicación base de la estructura de bloques
            const startX = canvas.width - 250;
            const groundY = canvas.height - PHYSICS.groundHeight;

            // Construir una torre clásica de Angry Birds
            const bWidth = 35;
            const bHeight = 45;

            // Fila 1 (Base)
            state.blocks.push(new Block(startX, groundY - bHeight, bWidth, bHeight, state.blockMaterial));
            state.blocks.push(new Block(startX + 65, groundY - bHeight, bWidth, bHeight, state.blockMaterial));
            state.blocks.push(new Block(startX + 130, groundY - bHeight, bWidth, bHeight, state.blockMaterial));

            // Vigas de unión horizontales
            state.blocks.push(new Block(startX - 10, groundY - bHeight - 15, 185, 15, state.blockMaterial));

            // Fila 2
            state.blocks.push(new Block(startX + 20, groundY - bHeight*2 - 15, bWidth, bHeight, state.blockMaterial));
            state.blocks.push(new Block(startX + 110, groundY - bHeight*2 - 15, bWidth, bHeight, state.blockMaterial));

            // Segunda viga
            state.blocks.push(new Block(startX + 10, groundY - bHeight*2 - 30, 145, 15, state.blockMaterial));

            // Copa de la Torre (Pirámide de arriba)
            state.blocks.push(new Block(startX + 65, groundY - bHeight*3 - 30, bWidth, bHeight, state.blockMaterial));
        }

        // --- DETECTAR COLISIONES ENTRE BLOQUES Y PROYECTILES ---
        function checkCollisions() {
            // 1. Proyectiles contra Bloques
            for (let p of state.activeProjectiles) {
                if (!p.isActive) continue;

                for (let b of state.blocks) {
                    if (!b.isActive) continue;

                    // Encontrar el punto más cercano en el rectángulo del bloque al círculo del proyectil
                    const closestX = Math.max(b.x, Math.min(p.x, b.x + b.width));
                    const closestY = Math.max(b.y, Math.min(p.y, b.y + b.height));

                    // Calcular distancia
                    const dx = p.x - closestX;
                    const dy = p.y - closestY;
                    const distance = Math.sqrt(dx*dx + dy*dy);

                    if (distance < p.radius) {
                        // ¡Colisión detectada!
                        p.collided = true;
                        
                        // Determinar vector normal de colisión para resolver físicas de rebote
                        let normalX = 0;
                        let normalY = 0;

                        if (distance === 0) {
                            // En el caso muy raro de superposición exacta
                            normalY = -1;
                        } else {
                            normalX = dx / distance;
                            normalY = dy / distance;
                        }

                        // Resolver posición (empujar al proyectil fuera del bloque)
                        p.x = closestX + normalX * p.radius;
                        p.y = closestY + normalY * p.radius;

                        // Calcular velocidad relativa
                        const rvx = p.vx - b.vx;
                        const rvy = p.vy - b.vy;
                        const velAlongNormal = rvx * normalX + rvy * normalY;

                        // Solo resolver si se mueven uno hacia el otro
                        if (velAlongNormal < 0) {
                            // Coeficiente de restitución mutuo
                            const restitution = Math.min(p.restitution, b.restitution);

                            // Impulso escalar
                            let impulseScalar = -(1 + restitution) * velAlongNormal;
                            impulseScalar /= (1/p.mass + 1/b.mass);

                            // Aplicar impulso a cada cuerpo
                            p.vx += (impulseScalar / p.mass) * normalX;
                            p.vy += (impulseScalar / p.mass) * normalY;

                            b.vx -= (impulseScalar / b.mass) * normalX;
                            b.vy -= (impulseScalar / b.mass) * normalY;

                            // Calcular daño al bloque según la fuerza del impacto (energía cinética relativa)
                            const impactSpeed = Math.abs(velAlongNormal);
                            const damage = Math.round(impactSpeed * p.mass * 0.7);
                            if (damage > 3) {
                                b.health -= damage;
                                createDebris(closestX, closestY, b.color, 4);
                            }
                        }
                    }
                }
            }

            // 2. Colisión entre Bloques (Básico para simular colapso estructural)
            for (let i = 0; i < state.blocks.length; i++) {
                const b1 = state.blocks[i];
                if (!b1.isActive) continue;

                for (let j = i + 1; j < state.blocks.length; j++) {
                    const b2 = state.blocks[j];
                    if (!b2.isActive) continue;

                    // Chequeo de colisión caja con caja (AABB)
                    if (b1.x < b2.x + b2.width &&
                        b1.x + b1.width > b2.x &&
                        b1.y < b2.y + b2.height &&
                        b1.y + b1.height > b2.y) {
                        
                        // Resolver colisión por solapamiento menor
                        const overlapX = Math.min(b1.x + b1.width - b2.x, b2.x + b2.width - b1.x);
                        const overlapY = Math.min(b1.y + b1.height - b2.y, b2.y + b2.height - b1.y);

                        if (overlapX < overlapY) {
                            // Empujar lateralmente
                            const dir = (b1.x + b1.width/2 > b2.x + b2.width/2) ? 1 : -1;
                            b1.x += overlapX * 0.5 * dir;
                            b2.x -= overlapX * 0.5 * dir;

                            // Transferir momento
                            const tempVx = b1.vx;
                            b1.vx = b2.vx * 0.5;
                            b2.vx = tempVx * 0.5;
                        } else {
                            // Empujar verticalmente
                            const dir = (b1.y + b1.height/2 > b2.y + b2.height/2) ? 1 : -1;
                            b1.y += overlapY * 0.5 * dir;
                            b2.y -= overlapY * 0.5 * dir;

                            // Transferir momento vertical
                            const tempVy = b1.vy;
                            b1.vy = b2.vy * 0.5;
                            b2.vy = tempVy * 0.5;
                        }
                    }
                }
            }
        }

        // --- SISTEMA DE DIBUJO DE TRAYECTORIA PREVISTA ---
        // Calcula matemáticamente la parábola que el proyectil tomará antes de soltarse
        function drawProjectedTrajectory(tensionX, tensionY) {
            // Fuerza proporcional al estiramiento
            const forceX = -tensionX * catapult.releaseForceMult * PHYSICS.elasticity;
            const forceY = -tensionY * catapult.releaseForceMult * PHYSICS.elasticity;

            const ammoProps = PROJECTILES[state.currentAmmo];
            const mass = ammoProps.mass;

            // Velocidades de inicio simuladas
            let simVx = forceX / mass;
            let simVy = forceY / mass;

            // Posición inicial (la copa tensada)
            let simX = catapult.pivotX + tensionX;
            let simY = catapult.pivotY + tensionY;

            ctx.save();
            ctx.beginPath();
            ctx.setLineDash([5, 6]);
            ctx.lineWidth = 3;

            // Cambiar dinámicamente el color de la línea según la fuerza cargada
            const pullDist = Math.sqrt(tensionX*tensionX + tensionY*tensionY);
            const intensity = pullDist / catapult.maxDragDistance;
            
            // De verde a rojo degradado para la trayectoria proyectada
            const r = Math.min(255, Math.floor(intensity * 255));
            const g = Math.min(255, Math.floor((1 - intensity) * 255 + 100));
            ctx.strokeStyle = `rgba(${r}, ${g}, 0, 0.75)`;

            ctx.moveTo(simX, simY);

            // Simular hasta 60 pasos en el futuro
            for (let i = 0; i < 45; i++) {
                // Aplicar el viento simulado por paso
                const windAcc = PHYSICS.wind * 0.05 / mass;
                simVx += windAcc;
                simVy += (PHYSICS.gravity * 0.15); // Gravedad simulada

                simX += simVx;
                simY += simVy;

                // Parar simulación si toca el suelo previsto
                const groundY = canvas.height - PHYSICS.groundHeight;
                if (simY >= groundY) {
                    ctx.lineTo(simX, groundY);
                    break;
                }
                ctx.lineTo(simX, simY);
            }
            ctx.stroke();
            ctx.restore();
        }

        // --- CONTROLADORES DE EVENTOS MOUSE / TOUCH ---
        function getMousePos(e) {
            const rect = canvas.getBoundingClientRect();
            // Soporte táctil y ratón combinado
            const clientX = e.touches ? e.touches[0].clientX : e.clientX;
            const clientY = e.touches ? e.touches[0].clientY : e.clientY;
            return {
                x: clientX - rect.left,
                y: clientY - rect.top
            };
        }

        function handleStart(e) {
            const pos = getMousePos(e);
            
            // Detectar si se está haciendo clic cerca de la copa cargadora
            const dx = pos.x - catapult.cupX;
            const dy = pos.y - catapult.cupY;
            const dist = Math.sqrt(dx*dx + dy*dy);

            // Permitir arrastre si la distancia es menor a 45 píxeles
            if (dist < 45) {
                catapult.isDragging = true;
                catapult.dragX = pos.x;
                catapult.dragY = pos.y;
            }
        }

        function handleMove(e) {
            if (!catapult.isDragging) return;
            
            const pos = getMousePos(e);

            // Calcular distancia desde el pivote de la catapulta
            const dx = pos.x - catapult.pivotX;
            const dy = pos.y - catapult.pivotY;
            const dist = Math.sqrt(dx*dx + dy*dy);

            if (dist <= catapult.maxDragDistance) {
                catapult.dragX = pos.x;
                catapult.dragY = pos.y;
            } else {
                // Limitar al círculo de tensión máxima permisible
                const angle = Math.atan2(dy, dx);
                catapult.dragX = catapult.pivotX + Math.cos(angle) * catapult.maxDragDistance;
                catapult.dragY = catapult.pivotY + Math.sin(angle) * catapult.maxDragDistance;
            }

            // Actualizar interfaz con el porcentaje de tensión actual
            const currentPull = Math.sqrt(Math.pow(catapult.dragX - catapult.pivotX, 2) + Math.pow(catapult.dragY - catapult.pivotY, 2));
            const pct = Math.min(100, Math.round((currentPull / catapult.maxDragDistance) * 100));
            
            // Cambiar color de la barra según tensión
            tensionPercentageText.innerText = `${pct}%`;
            tensionBarFill.style.width = `${pct}%`;

            if (pct < 35) {
                tensionBarFill.className = "h-full bg-emerald-500 rounded-full transition-all duration-75";
                tensionPercentageText.className = "text-emerald-400";
            } else if (pct < 75) {
                tensionBarFill.className = "h-full bg-amber-500 rounded-full transition-all duration-75";
                tensionPercentageText.className = "text-amber-400";
            } else {
                tensionBarFill.className = "h-full bg-rose-600 rounded-full transition-all duration-75";
                tensionPercentageText.className = "text-rose-500";
            }
        }

        function handleEnd() {
            if (!catapult.isDragging) return;
            catapult.isDragging = false;

            // Calcular tensión de lanzamiento
            const tensionX = catapult.dragX - catapult.pivotX;
            const tensionY = catapult.dragY - catapult.pivotY;
            const pullDistance = Math.sqrt(tensionX*tensionX + tensionY*tensionY);

            // Disparar solo si se jaló lo suficiente (evitar micro-clics accidentales)
            if (pullDistance > 15) {
                shoot(tensionX, tensionY);
            } else {
                // Resetear barra
                tensionBarFill.style.width = "0%";
                tensionPercentageText.innerText = "0%";
            }
        }

        // Registrar eventos
        canvas.addEventListener('mousedown', handleStart);
        canvas.addEventListener('mousemove', handleMove);
        window.addEventListener('mouseup', handleEnd);

        canvas.addEventListener('touchstart', handleStart, { passive: true });
        canvas.addEventListener('touchmove', handleMove, { passive: true });
        window.addEventListener('touchend', handleEnd);

        // --- ACCIÓN DE DISPARO DE LA CATAPULTA ---
        function shoot(tensionX, tensionY) {
            // Fuerza proporcional contraria al estiramiento
            const forceX = -tensionX * catapult.releaseForceMult * PHYSICS.elasticity;
            const forceY = -tensionY * catapult.releaseForceMult * PHYSICS.elasticity;

            const ammoProps = PROJECTILES[state.currentAmmo];
            const mass = ammoProps.mass;

            // V = F / M
            const vx = forceX / mass;
            const vy = forceY / mass;

            // Crear y lanzar proyectil activo desde la posición liberada de la copa
            const startX = catapult.pivotX + tensionX;
            const startY = catapult.pivotY + tensionY;

            const projectile = new Projectile(startX, startY, vx, vy, state.currentAmmo);
            state.activeProjectiles.push(projectile);

            // Actualizar estadísticas globales
            state.shotsFired++;
            statShots.innerText = state.shotsFired;

            // Telemetría de Salida
            const launchSpeed = Math.sqrt(vx*vx + vy*vy);
            const launchAngle = Math.abs(Math.round(Math.atan2(-vy, vx) * 180 / Math.PI));

            state.lastShootSpeed = launchSpeed;
            state.lastShootAngle = launchAngle;

            teleSpeed.innerText = `${launchSpeed.toFixed(1)} m/s`;
            teleAngle.innerText = `${launchAngle}°`;

            // Efecto visual instantáneo en pivote
            createExplosion(catapult.pivotX, catapult.pivotY, '#C19A6B', 8);

            // Animación rápida de retroceso de la barra
            tensionBarFill.style.width = "0%";
            tensionPercentageText.innerText = "0%";
        }

        // Mostrar telemetría detallada del impacto al chocar o tocar suelo
        function triggerImpactTelemetry(p) {
            // Distancia horizontal recorrida desde la catapulta (píxeles a metros)
            const distanceInPixels = Math.abs(p.x - catapult.baseX);
            const distanceInMeters = (distanceInPixels / PHYSICS.scale).toFixed(1);
            
            state.lastDistance = distanceInMeters;
            teleLast.innerText = `${distanceInMeters} m`;

            if (parseFloat(distanceInMeters) > parseFloat(state.maxDistance)) {
                state.maxDistance = distanceInMeters;
                statDistance.innerText = `${distanceInMeters}m`;
            }

            // Alerta flotante
            showToast(`¡Impacto! Alcance: ${distanceInMeters} metros.`);
        }

        // --- RENDERIZACIÓN DE ESCENA Y ENTORNOS ---
        function drawCatapult() {
            const groundY = canvas.height - PHYSICS.groundHeight;

            // Determinar la posición de la copa/brazo dinámicamente si se está jalando
            if (catapult.isDragging) {
                catapult.cupX = catapult.dragX;
                catapult.cupY = catapult.dragY;
                
                // Dibujar línea guía de proyección predictiva
                drawProjectedTrajectory(catapult.dragX - catapult.pivotX, catapult.dragY - catapult.pivotY);
            } else {
                // Recuperación suave del resorte a la posición de reposo
                const targetX = catapult.pivotX + Math.cos(catapult.restingAngle) * catapult.armLength;
                const targetY = catapult.pivotY + Math.sin(catapult.restingAngle) * catapult.armLength;

                catapult.cupX += (targetX - catapult.cupX) * 0.25;
                catapult.cupY += (targetY - catapult.cupY) * 0.25;
            }

            // Dibujar Gomas/Tensores Elásticos traseros (Efecto Tirachinas)
            if (catapult.isDragging) {
                ctx.save();
                ctx.strokeStyle = '#3e2723';
                ctx.lineWidth = 5;
                ctx.beginPath();
                // Tensor trasero
                ctx.moveTo(catapult.pivotX - 35, catapult.pivotY - 25);
                ctx.lineTo(catapult.cupX, catapult.cupY);
                // Tensor delantero
                ctx.moveTo(catapult.pivotX + 10, catapult.pivotY - 25);
                ctx.lineTo(catapult.cupX, catapult.cupY);
                ctx.stroke();
                ctx.restore();
            }

            // 1. Dibujar estructura estática (Base de la catapulta)
            ctx.save();
            ctx.fillStyle = '#6d4c41'; // Madera oscura para base
            ctx.strokeStyle = '#3e2723';
            ctx.lineWidth = 3;

            // Ruedas/Soporte inferior
            ctx.beginPath();
            ctx.roundRect(catapult.baseX - 55, catapult.baseY - 15, 110, 15, 4);
            ctx.fill();
            ctx.stroke();

            // Poste Vertical Principal
            ctx.beginPath();
            ctx.moveTo(catapult.pivotX - 10, catapult.baseY - 15);
            ctx.lineTo(catapult.pivotX - 10, catapult.pivotY);
            ctx.lineTo(catapult.pivotX + 10, catapult.pivotY);
            ctx.lineTo(catapult.pivotX + 10, catapult.baseY - 15);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // 2. Brazo de la Catapulta (Movible)
            ctx.beginPath();
            ctx.strokeStyle = '#8d6e63';
            ctx.lineWidth = 7;
            ctx.lineCap = 'round';
            ctx.moveTo(catapult.pivotX, catapult.pivotY);
            ctx.lineTo(catapult.cupX, catapult.cupY);
            ctx.stroke();

            // 3. Copa / Cucharón en el extremo del brazo
            ctx.beginPath();
            ctx.fillStyle = '#b0bec5'; // Metal
            ctx.strokeStyle = '#546e7a';
            ctx.lineWidth = 3;
            ctx.arc(catapult.cupX, catapult.cupY, 15, 0, Math.PI, true); // Semicírculo
            ctx.fill();
            ctx.stroke();

            // Dibujar mini proyectil cargado en la copa si no se ha lanzado
            if (catapult.isDragging) {
                const ammoProps = PROJECTILES[state.currentAmmo];
                ctx.fillStyle = ammoProps.color;
                ctx.strokeStyle = ammoProps.outline;
                ctx.beginPath();
                ctx.arc(catapult.cupX, catapult.cupY - 4, ammoProps.radius * 0.7, 0, Math.PI * 2);
                ctx.fill();
                ctx.stroke();
            }

            // Pasador de Pivote (Tornillo de rotación central)
            ctx.beginPath();
            ctx.fillStyle = '#37474f';
            ctx.arc(catapult.pivotX, catapult.pivotY, 6, 0, Math.PI * 2);
            ctx.fill();

            ctx.restore();
        }

        // Dibujar el Escenario (Cielo, Suelo, Sol)
        function drawEnvironment() {
            const groundY = canvas.height - PHYSICS.groundHeight;

            // Cielo degradado sutil
            const skyGrad = ctx.createLinearGradient(0, 0, 0, canvas.height);
            skyGrad.addColorStop(0, '#0f172a'); // Azul oscuro
            skyGrad.addColorStop(0.7, '#1e1b4b'); // Púrpura cósmico
            skyGrad.addColorStop(1, '#311042'); // Tonos cálidos en horizonte
            ctx.fillStyle = skyGrad;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Luna decorativa grande
            ctx.save();
            ctx.fillStyle = 'rgba(255, 255, 255, 0.04)';
            ctx.beginPath();
            ctx.arc(canvas.width - 150, 110, 80, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = 'rgba(255, 255, 255, 0.08)';
            ctx.beginPath();
            ctx.arc(canvas.width - 150, 110, 50, 0, Math.PI * 2);
            ctx.fill();
            ctx.restore();

            // Dibujar Nubes lejanas
            ctx.fillStyle = 'rgba(255, 255, 255, 0.05)';
            ctx.beginPath();
            ctx.arc(200, 120, 30, 0, Math.PI*2);
            ctx.arc(240, 110, 45, 0, Math.PI*2);
            ctx.arc(280, 120, 30, 0, Math.PI*2);
            ctx.fill();

            // Suelo Sólido
            const groundGrad = ctx.createLinearGradient(0, groundY, 0, canvas.height);
            groundGrad.addColorStop(0, '#15803d'); // Césped
            groundGrad.addColorStop(0.15, '#1e3a1e'); // Hierba profunda
            groundGrad.addColorStop(1, '#0c1a0c'); // Tierra
            
            ctx.fillStyle = groundGrad;
            ctx.fillRect(0, groundY, canvas.width, PHYSICS.groundHeight);

            // Línea superior del césped para definir contraste
            ctx.strokeStyle = '#22c55e';
            ctx.lineWidth = 3;
            ctx.beginPath();
            ctx.moveTo(0, groundY);
            ctx.lineTo(canvas.width, groundY);
            ctx.stroke();

            // Dibujar dirección del viento si está activo
            if (PHYSICS.wind !== 0) {
                ctx.save();
                ctx.strokeStyle = 'rgba(255, 255, 255, 0.15)';
                ctx.lineWidth = 1.5;
                ctx.setLineDash([10, 20]);
                const windY = 70;
                ctx.beginPath();
                ctx.moveTo(PHYSICS.wind > 0 ? 50 : canvas.width - 50, windY);
                ctx.lineTo(PHYSICS.wind > 0 ? 250 : canvas.width - 250, windY);
                ctx.stroke();

                // Flecha indicadora
                ctx.fillStyle = 'rgba(255, 255, 255, 0.25)';
                ctx.font = '11px sans-serif';
                ctx.fillText(
                    PHYSICS.wind > 0 ? "VIENTO ➔" : "⬅ VIENTO", 
                    PHYSICS.wind > 0 ? 115 : canvas.width - 180, 
                    windY - 8
                );
                ctx.restore();
            }
        }

        // --- BUCLE PRINCIPAL DE ANIMACIÓN Y FÍSICA ---
        function loop() {
            // 1. Limpiar pantalla y dibujar el fondo
            drawEnvironment();

            // 2. Actualizar y Dibujar Partículas
            for (let i = state.particles.length - 1; i >= 0; i--) {
                const p = state.particles[i];
                p.update();
                p.draw();
                if (p.life <= 0) {
                    state.particles.splice(i, 1);
                }
            }

            // 3. Dibujar la Catapulta en su estado actual (Cargada, jalada o suelta)
            drawCatapult();

            // 4. Actualizar y Dibujar Bloques
            for (let i = state.blocks.length - 1; i >= 0; i--) {
                const b = state.blocks[i];
                b.update();
                if (b.isActive) {
                    b.draw();
                } else {
                    state.blocks.splice(i, 1);
                }
            }

            // 5. Actualizar y Dibujar Proyectiles Activos
            for (let i = state.activeProjectiles.length - 1; i >= 0; i--) {
                const p = state.activeProjectiles[i];
                p.update();
                if (p.isActive) {
                    p.draw();
                } else {
                    state.activeProjectiles.splice(i, 1);
                }
            }

            // 6. Validar colisiones múltiples
            checkCollisions();

            // Volver a llamar en el siguiente frame
            requestAnimationFrame(loop);
        }

        // --- CONTROLADORES DE INTERFAZ E INPUT ---

        // Selector de Munición
        ammoButtons.forEach(btn => {
            btn.addEventListener('click', () => {
                ammoButtons.forEach(b => {
                    b.classList.remove('bg-amber-600/20', 'border-amber-500');
                    b.classList.add('bg-slate-700/50', 'border-transparent');
                });
                btn.classList.add('bg-amber-600/20', 'border-amber-500');
                btn.classList.remove('bg-slate-700/50', 'border-transparent');
                
                state.currentAmmo = btn.getAttribute('data-type');
            });
        });

        // Sliders dinámicos
        sliderGravity.addEventListener('input', (e) => {
            PHYSICS.gravity = parseFloat(e.target.value);
            valGravity.innerText = `${PHYSICS.gravity.toFixed(1)} m/s²`;
        });

        sliderElasticity.addEventListener('input', (e) => {
            PHYSICS.elasticity = parseFloat(e.target.value);
            valElasticity.innerText = `${PHYSICS.elasticity.toFixed(1)}x`;
        });

        sliderWind.addEventListener('input', (e) => {
            PHYSICS.wind = parseInt(e.target.value);
            if (PHYSICS.wind === 0) {
                valWind.innerText = "Sin Viento";
            } else {
                valWind.innerText = `${PHYSICS.wind > 0 ? '+' : ''}${PHYSICS.wind} m/s`;
            }
        });

        selectBlockMaterial.addEventListener('change', (e) => {
            state.blockMaterial = e.target.value;
            initLevel(); // Regenerar para aplicar el nuevo material
            showToast(`Estructura cambiada a: ${MATERIALS[state.blockMaterial].name}`);
        });

        // Botones de reinicio
        btnResetCastle.addEventListener('click', () => {
            initLevel();
            showToast("Estructura reconstruida.");
        });

        btnResetStats.addEventListener('click', () => {
            state.shotsFired = 0;
            state.maxDistance = 0;
            statShots.innerText = "0";
            statDistance.innerText = "0.0m";
            
            teleSpeed.innerText = "0.0 m/s";
            teleAngle.innerText = "0°";
            teleLast.innerText = "0.0 m";
            
            initLevel();
            showToast("Simulación e historial reiniciados por completo.");
        });

        // Atajos de Teclado
        window.addEventListener('keydown', (e) => {
            if (e.key === 'r' || e.key === 'R') {
                initLevel();
                showToast("Estructura reconstruida.");
            }
        });

        // Mostrar notificaciones Toast personalizadas
        const notification = document.getElementById('explosion-notification');
        const notificationText = document.getElementById('notification-text');
        let toastTimeout;

        function showToast(text) {
            clearTimeout(toastTimeout);
            notificationText.innerText = text;
            
            // Animación CSS con Tailwind
            notification.classList.remove('translate-y-24', 'opacity-0');
            notification.classList.add('translate-y-0', 'opacity-100');

            toastTimeout = setTimeout(() => {
                notification.classList.remove('translate-y-0', 'opacity-100');
                notification.classList.add('translate-y-24', 'opacity-0');
            }, 3000);
        }

        // --- INICIAR TODO ---
        initLevel();
        loop(); // Arrancar el bucle físico infinito

    </script>
</body>
</html>

