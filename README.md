<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tablas de Multiplicar - ¡Atrapa el Resultado!</title>
    <style>
        :root {
            --primary: #4F46E5;
            --primary-light: #EEF2F6;
            --accent: #10B981;
            --warning: #F59E0B;
            --danger: #EF4444;
            --dark: #1F2937;
            --bg-bubble: #E0F2FE;
            --text-bubble: #0369A1;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Arial Rounded MT Bold', sans-serif; }

        body {
            background-color: #EEF2F6;
            background-image: radial-gradient(#6366F1 15%, transparent 16%), radial-gradient(#10B981 15%, transparent 16%);
            background-size: 40px 40px;
            background-position: 0 0, 20px 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .tablero-juego {
            background: white;
            width: 100%;
            max-width: 700px;
            border-radius: 32px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.12);
            border: 6px solid var(--primary);
            overflow: hidden;
        }

        .panel-superior {
            background: var(--primary);
            color: white;
            padding: 20px 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 22px;
        }

        .indicador {
            background: rgba(255, 255, 255, 0.2);
            padding: 6px 16px;
            border-radius: 50px;
            font-weight: bold;
        }

        .pantalla-inicio, .pantalla-final {
            padding: 50px 30px;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .cuerpo-juego {
            padding: 30px;
            display: none;
            flex-direction: column;
            align-items: center;
        }

        .titulo-bienvenida { font-size: 34px; color: var(--dark); margin-bottom: 10px; }
        .subtitulo-bienvenida { font-size: 18px; color: #4B5563; max-width: 450px; line-height: 1.5; }

        .progreso-texto {
            font-size: 18px;
            color: #6B7280;
            margin-bottom: 10px;
        }

        .tarjeta-operacion {
            background: #F5F3FF;
            border: 4px solid #C7D2FE;
            border-radius: 24px;
            width: 100%;
            max-width: 400px;
            padding: 25px;
            font-size: 64px;
            color: var(--primary);
            text-align: center;
            box-shadow: 0 8px 16px rgba(79, 70, 229, 0.1);
            margin-bottom: 25px;
            position: relative;
        }

        .zona-interactiva {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 40px;
            width: 100%;
            margin-bottom: 20px;
        }

        .contenedor-avatar {
            background: #F3F4F6;
            width: 120px;
            height: 120px;
            border-radius: 50%;
            border: 5px solid #E5E7EB;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 80px;
            box-shadow: 0 8px 0 #D1D5DB;
        }
        .saltar { animation: jump 0.4s ease; }
        @keyframes jump {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.15) translateY(-15px); }
        }

        .cuadricula-burbujas {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            width: 100%;
            max-width: 380px;
        }

        .burbuja {
            background: var(--bg-bubble);
            color: var(--text-bubble);
            border: 3px solid #BAE6FD;
            border-radius: 20px;
            padding: 20px;
            font-size: 36px;
            font-weight: bold;
            cursor: pointer;
            text-align: center;
            box-shadow: 0 8px 0 #93C5FD;
            transition: all 0.15s ease;
            position: relative;
            overflow: hidden;
        }
        .burbuja:hover {
            transform: translateY(-2px);
            background: #E0F2FE;
        }
        .burbuja:active {
            transform: translateY(6px);
            box-shadow: 0 2px 0 #93C5FD;
        }

        .correcta-click { background: #D1FAE5 !important; color: #065F46 !important; border-color: #34D399 !important; box-shadow: 0 4px 0 #34D399 !important; }
        .incorrecta-click { background: #FEE2E2 !important; color: #991B1B !important; border-color: #F87171 !important; box-shadow: 0 4px 0 #F87171 !important; opacity: 0.5; pointer-events: none; }

        .alerta-feedback {
            font-size: 22px;
            font-weight: bold;
            margin-top: 15px;
            min-height: 30px;
        }

        .pantalla-final { display: none; }
        .titulo-final { font-size: 36px; color: var(--dark); margin-bottom: 10px; }
        .puntuacion-gigante { font-size: 85px; color: var(--accent); font-weight: 900; margin: 15px 0; }
        .detalles-finales { font-size: 20px; color: #4B5563; margin-bottom: 15px; line-height: 1.6; }
        
        .btn-accion {
            background: var(--accent);
            color: white;
            border: none;
            padding: 16px 50px;
            font-size: 24px;
            font-weight: bold;
            border-radius: 20px;
            cursor: pointer;
            box-shadow: 0 6px 0 #047857;
            transition: transform 0.1s;
        }
        .btn-accion:active { transform: translateY(4px); box-shadow: none; }
        
        .btn-empezar {
            background: var(--primary);
            box-shadow: 0 6px 0 #3730A3;
        }
    </style>
</head>
<body>

    <div class="tablero-juego">
        <div class="panel-superior">
            <span>🚀 Atrapa la Respuesta</span>
            <div class="indicador">⏱️ <span id="tiempo-marcador">0s</span></div>
        </div>

        <div id="escenario-inicio" class="pantalla-inicio">
            <h1 class="titulo-bienvenida">¡Súper Reto Matemático! 🎓</h1>
            <div style="font-size: 70px;">🎯🧠</div>
            <p class="subtitulo-bienvenida">
                ¿Estás listo? Deberás resolver <b>30 operaciones aleatorias</b> que incluyen las tablas desde el <b>2 hasta el 12</b>. ¡Revienta las burbujas correctas lo más rápido posible!
            </p>
            <button class="btn-accion btn-empezar" onclick="comenzarRetoOficial()">¡EMPEZAR PARTIDA! 🚀</button>
        </div>

        <div id="escenario-activo" class="cuerpo-juego">
            <p class="progreso-texto">Operación <span id="ronda-actual">1</span> de 30</p>
            
            <div class="tarjeta-operacion" id="cuadro-multiplicacion">-- × --</div>

            <div class="zona-interactiva">
                <div class="contenedor-avatar" id="avatar-robot">🤖</div>
                
                <div class="cuadricula-burbujas" id="contenedor-burbujas">
                    </div>
            </div>

            <div class="alerta-feedback" id="texto-alerta"></div>
        </div>

        <div id="escenario-final" class="pantalla-final">
            <h2 class="titulo-final">¡Misión Cumplida! 🎯</h2>
            <div class="puntuacion-gigante" id="marcador-porcentaje">0%</div>
            <p class="detalles-finales" id="resumen-texto"></p>
            <button class="btn-accion" onclick="reiniciarPartidaTodo()">Comenzar Otra Partida 🔄</button>
        </div>
    </div>

    <script>
        let bancoTablas = [];
        let partidaActual = [];
        let indicePregunta = 0;
        const totalPreguntasPartida = 30; // Modificado a 30 preguntas por juego
        
        let aciertosDirectos = 0;
        let fallosEnPreguntaActual = 0;
        let tiempoSegundos = 0;
        let cronometroId = null;
        let preguntaActiva = null;

        // Construye el banco incluyendo las nuevas tablas del 11 y del 12
        function construirBancoTablas() {
            bancoTablas = [];
            for (let i = 2; i <= 12; i++) { // Rango ampliado hasta la tabla del 12
                for (let j = 1; j <= 10; j++) {
                    bancoTablas.push({
                        texto: `${i} × ${j}`,
                        resultado: i * j
                    });
                }
            }
        }

        let ctxAudio = null;
        function activarHerramientaAudio() {
            if (!ctxAudio) ctxAudio = new (window.AudioContext || window.webkitAudioContext)();
        }

        function reproducirSonidoArcade(tipo, tonoFijo = null) {
            activarHerramientaAudio();
            if (!ctxAudio) return;

            const oscilador = ctxAudio.createOscillator();
            const nodoVolumen = ctxAudio.createGain();
            oscilador.connect(nodoVolumen);
            nodoVolumen.connect(ctxAudio.destination);

            if (tipo === 'correcto') {
                oscilador.type = 'triangle';
                oscilador.frequency.setValueAtTime(440, ctxAudio.currentTime);
                oscilador.frequency.exponentialRampToValueAtTime(880, ctxAudio.currentTime + 0.3);
                nodoVolumen.gain.setValueAtTime(0.7, ctxAudio.currentTime);
                nodoVolumen.gain.exponentialRampToValueAtTime(0.01, ctxAudio.currentTime + 0.3);
                oscilador.start(); oscilador.stop(ctxAudio.currentTime + 0.3);
            } else if (tipo === 'incorrecto') {
                oscilador.type = 'sawtooth';
                oscilador.frequency.setValueAtTime(150, ctxAudio.currentTime);
                oscilador.frequency.linearRampToValueAtTime(70, ctxAudio.currentTime + 0.4);
                nodoVolumen.gain.setValueAtTime(0.6, ctxAudio.currentTime);
                nodoVolumen.gain.exponentialRampToValueAtTime(0.01, ctxAudio.currentTime + 0.4);
                oscilador.start(); oscilador.stop(ctxAudio.currentTime + 0.4);
            }
        }

        function gestionarTiempo() {
            clearInterval(cronometroId);
            cronometroId = setInterval(() => {
                tiempoSegundos++;
                document.getElementById('tiempo-marcador').innerText = tiempoSegundos + 's';
            }, 1000);
        }

        function comenzarRetoOficial() {
            activarHerramientaAudio(); 
            construirBancoTablas();
            
            // Mezcla aleatoria completa del pool extendido (2 al 12) y selección de 30 ejercicios únicos
            partidaActual = [...bancoTablas].sort(() => Math.random() - 0.5).slice(0, totalPreguntasPartida);
            indicePregunta = 0;
            aciertosDirectos = 0;
            tiempoSegundos = 0;
            
            document.getElementById('tiempo-marcador').innerText = '0s';
            document.getElementById('escenario-inicio').style.display = 'none';
            document.getElementById('escenario-activo').style.display = 'flex';
            document.getElementById('escenario-final').style.display = 'none';
            
            cargarRondaPregunta();
            gestionarTiempo();
        }

        function generarValoresFalsos(correcto) {
            let falsos = new Set([correcto]);
            while (falsos.size < 4) {
                let margen = Math.floor(Math.random() * 16) - 8;
                let valorAlternativo = correcto + (margen === 0 ? 5 : margen);
                // Ajuste de rango máximo a 130 para acomodar las respuestas altas del 12x10
                if (valorAlternativo > 0 && valorAlternativo <= 130) {
                    falsos.add(valorAlternativo);
                }
            }
            return Array.from(falsos).sort(() => Math.random() - 0.5);
        }

        function cargarRondaPregunta() {
            fallosEnPreguntaActual = 0;
            document.getElementById('texto-alerta').innerText = '';
            
            const avatar = document.getElementById('avatar-robot');
            avatar.innerText = '🤖';
            avatar.classList.remove('saltar');

            preguntaActiva = partidaActual[indicePregunta];
            document.getElementById('ronda-actual').innerText = (indicePregunta + 1);
            document.getElementById('cuadro-multiplicacion').innerText = preguntaActiva.texto;

            const opcionesMezcladas = generarValoresFalsos(preguntaActiva.resultado);
            const panelOpciones = document.getElementById('contenedor-burbujas');
            panelOpciones.innerHTML = '';

            opcionesMezcladas.forEach(num => {
                const bttn = document.createElement('div');
                bttn.className = 'burbuja';
                bttn.innerText = num;
                bttn.onclick = () => procesarSeleccion(num, bttn);
                panelOpciones.appendChild(bttn);
            });
        }

        function procesarSeleccion(numero, nodoHTML) {
            const avatar = document.getElementById('avatar-robot');
            const alertBox = document.getElementById('texto-alerta');

            if (numero === preguntaActiva.resultado) {
                reproducirSonidoArcade('correcto');
                nodoHTML.classList.add('correcta-click');
                avatar.innerText = '😎';
                avatar.classList.add('saltar');
                alertBox.innerText = '¡Burbuja correcta! ✨';
                alertBox.style.color = '#10B981';

                if (fallosEnPreguntaActual === 0) aciertosDirectos++;

                document.querySelectorAll('.burbuja').forEach(b => b.style.pointerEvents = 'none');

                setTimeout(() => {
                    if (indicePregunta < totalPreguntasPartida - 1) {
                        indicePregunta++;
                        cargarRondaPregunta();
                    } else {
                        desplegarPantallaFinal();
                    }
                }, 1300);
            } else {
                reproducirSonidoArcade('incorrecto');
                nodoHTML.classList.add('incorrecta-click');
                avatar.innerText = '💥';
                alertBox.innerText = '¡Esa burbuja no es! Intenta otra 👇';
                alertBox.style.color = '#EF4444';
                fallosEnPreguntaActual++;
            }
        }

        function desplegarPantallaFinal() {
            clearInterval(cronometroId);
            document.getElementById('escenario-activo').style.display = 'none';
            document.getElementById('escenario-final').style.display = 'flex';

            const scorePorcentaje = Math.round((aciertosDirectos / totalPreguntasPartida) * 100);
            document.getElementById('marcador-porcentaje').innerText = scorePorcentaje + '%';

            let feedTexto = `Resolviste el laboratorio de multiplicación en un tiempo total de <b>${tiempoSegundos} segundos</b>. `;
            if (scorePorcentaje === 100) {
                feedTexto += "¡Dominio maestro total! Respondiste las 30 operaciones complejas sin equivocarte ni una vez. 🏆🏅";
            } else if (scorePorcentaje >= 75) {
                feedTexto += "¡Increíble! Superar un maratón de 30 preguntas incluyendo el 11 y el 12 demuestra una agilidad gigante. ⚡";
            } else {
                feedTexto += "¡Buen intento! Las tablas del 11 y 12 son más difíciles, pero resolver 30 ejercicios seguidos entrenará tu cerebro al máximo. ¡Sigue así! 📚💪";
            }
            document.getElementById('resumen-texto').innerHTML = feedTexto;
        }

        function reiniciarPartidaTodo() {
            clearInterval(cronometroId);
            tiempoSegundos = 0;
            document.getElementById('tiempo-marcador').innerText = '0s';
            
            // Regresa a la pantalla inicial. Al dar click a "Empezar", se reconstruirá y barajará un pool totalmente nuevo.
            document.getElementById('escenario-final').style.display = 'none';
            document.getElementById('escenario-activo').style.display = 'none';
            document.getElementById('escenario-inicio').style.display = 'flex';
        }
    </script>
</body>
</html>
