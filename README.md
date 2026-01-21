# EXAMEN-DE-REFUGIADOS
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Práctica de Examen: Reglamento de Personas Refugiadas</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; background-color: #f4f4f4; }
        .container { max-width: 700px; margin: auto; background: white; padding: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        .question { margin-bottom: 20px; display: none; }
        .question.active { display: block; }
        .options { margin-left: 20px; }
        button { padding: 10px 20px; background: #28a745; color: white; border: none; cursor: pointer; border-radius: 4px; margin: 5px; }
        button:hover { background: #218838; }
        #timer { font-weight: bold; color: red; }
        #feedback { margin-top: 10px; padding: 10px; border-radius: 4px; display: none; }
        .correct { background-color: #d4edda; color: #155724; }
        .incorrect { background-color: #f8d7da; color: #721c24; }
        #result { margin-top: 20px; font-weight: bold; display: none; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Práctica de Examen: Reglamento de Personas Refugiadas</h1>
        <p>Tienes 10 minutos para responder 10 preguntas aleatorias. ¡Buena suerte!</p>
        <div id="timer">Tiempo restante: 10:00</div>
        <form id="quiz-form">
            <!-- Preguntas se generan dinámicamente -->
        </form>
        <div id="feedback"></div>
        <div id="result"></div>
    </div>

    <script>
        const questions = [
            {
                question: "1. ¿Qué es un refugiado según el Reglamento?",
                options: ["a) Una persona que viaja por turismo.", "b) Una persona que teme persecución por motivos de raza, religión, nacionalidad, etc.", "c) Un inmigrante económico.", "d) Un ciudadano mexicano."],
                answer: "b",
                explanation: "Según el PDF, un refugiado es alguien que teme persecución por motivos específicos, como se define en la Convención de Ginebra."
            },
            {
                question: "2. ¿Dónde se presenta la solicitud de refugio?",
                options: ["a) En cualquier aeropuerto.", "b) Ante el Instituto Nacional de Migración (INM).", "c) En la embajada de origen.", "d) En un consulado extranjero."],
                answer: "b",
                explanation: "El INM es la autoridad competente para recibir y procesar solicitudes, como se detalla en el Reglamento."
            },
            {
                question: "3. ¿Qué derechos tienen los solicitantes de refugio durante el proceso?",
                options: ["a) Ninguno hasta la aprobación.", "b) Protección contra deportación y acceso a servicios básicos.", "c) Derechos solo si son menores.", "d) Inmediata ciudadanía."],
                answer: "b",
                explanation: "El PDF establece que los solicitantes tienen derechos humanos básicos, incluyendo no ser deportados mientras se evalúa la solicitud."
            },
            {
                question: "4. ¿Cuánto tiempo toma decidir sobre una solicitud de refugio?",
                options: ["a) 1 día.", "b) Hasta 45 días hábiles, prorrogable.", "c) 1 año.", "d) Indefinidamente."],
                answer: "b",
                explanation: "El procedimiento establece un plazo inicial de 45 días hábiles, con posibilidad de extensión según el Reglamento."
            },
            {
                question: "5. ¿Qué sucede si se rechaza la solicitud?",
                options: ["a) Se deporta inmediatamente.", "b) Se puede interponer recurso de revisión.", "c) Se concede automáticamente.", "d) Se ignora."],
                answer: "b",
                explanation: "El PDF permite apelar la decisión ante instancias superiores, como se describe en los procedimientos."
            },
            {
                question: "6. ¿Quién determina la condición de refugiado?",
                options: ["a) El presidente de México.", "b) El INM a través de sus comisionados.", "c) La policía federal.", "d) Las ONG."],
                answer: "b",
                explanation: "La autoridad es el INM, con comisionados especializados en el tema."
            },
            {
                question: "7. ¿Qué obligaciones tienen los refugiados reconocidos?",
                options: ["a) Regresar a su país inmediatamente.", "b) Respetar las leyes mexicanas y no cometer delitos.", "c) Pagar impuestos altos.", "d) No trabajar."],
                answer: "b",
                explanation: "Deben cumplir con las leyes locales, como se establece en el Reglamento."
            },
            {
                question: "8. ¿Pueden los refugiados acceder a educación y salud?",
                options: ["a) No, solo a refugio temporal.", "b) Sí, tienen derechos equivalentes a residentes.", "c) Solo si pagan.", "d) No, es exclusivo para ciudadanos."],
                answer: "b",
                explanation: "El PDF garantiza acceso a servicios esenciales como educación y salud."
            },
            {
                question: "9. ¿Qué es el asilo político?",
                options: ["a) Igual a refugio.", "b) Protección adicional para casos graves.", "c) Solo para diplomáticos.", "d) No se menciona."],
                answer: "b",
                explanation: "El Reglamento diferencia refugio de asilo, otorgando protección especial en situaciones extremas."
            },
            {
                question: "10. ¿Cómo se protege la confidencialidad en el proceso?",
                options: ["a) No se protege.", "b) Se garantiza la privacidad de la información.", "c) Se publica en medios.", "d) Solo para casos aprobados."],
                answer: "b",
                explanation: "El PDF enfatiza la confidencialidad para proteger a los solicitantes."
            }
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let timeLeft = 600; // 10 minutos en segundos
        let timerInterval;
        let shuffledQuestions = [...questions].sort(() => Math.random() - 0.5); // Aleatorizar

        function startTimer() {
            timerInterval = setInterval(() => {
                timeLeft--;
                const minutes = Math.floor(timeLeft / 60);
                const seconds = timeLeft % 60;
                document.getElementById('timer').textContent = `Tiempo restante: ${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
                if (timeLeft <= 0) {
                    clearInterval(timerInterval);
                    showResult();
                }
            }, 1000);
        }

        function loadQuestion() {
            if (currentQuestionIndex >= shuffledQuestions.length) {
                showResult();
                return;
            }
            const q = shuffledQuestions[currentQuestionIndex];
            document.getElementById('quiz-form').innerHTML = `
                <div class="question active">
                    <p>${q.question}</p>
                    <div class="options">
                        ${q.options.map((opt, i) => `<input type="radio" name="q" value="${String.fromCharCode(97 + i)}"> ${opt}<br>`).join('')}
                    </div>
                    <button type="button" onclick="checkAnswer()">Responder</button>
                </div>
            `;
        }

        function checkAnswer() {
            const selected = document.querySelector('input[name="q"]:checked');
            if (!selected) {
                alert('Selecciona una opción.');
                return;
            }
            const feedback = document.getElementById('feedback');
            const q = shuffledQuestions[currentQuestionIndex];
            if (selected.value === q.answer) {
                score++;
                feedback.className = 'correct';
                feedback.textContent = `¡Correcto! ${q.explanation}`;
            } else {
                feedback.className = 'incorrect';
                feedback.textContent = `Incorrecto. ${q.explanation}`;
            }
            feedback.style.display = 'block';
            setTimeout(() => {
                feedback.style.display = 'none';
                currentQuestionIndex++;
                loadQuestion();
            }, 3000); // Muestra feedback por 3 segundos
        }

        function showResult() {
            clearInterval(timerInterval);
            const percentage = (score / shuffledQuestions.length) * 100;
            let message = `Examen completado. Tu nota: ${score}/${shuffledQuestions.length} (${percentage.toFixed(1)}%). `;
            if (percentage >= 80) message += "¡Excelente! Estás preparado.";
            else if (percentage >= 60) message += "Bien, pero practica más.";
            else message += "Necesitas estudiar más el PDF.";
            document.getElementById('result').innerHTML = message;
            document.getElementById('result').style.display = 'block';
            document.getElementById('quiz-form').innerHTML = '';
        }

        // Iniciar
        startTimer();
        loadQuestion();
    </script>
</body>
</html>
