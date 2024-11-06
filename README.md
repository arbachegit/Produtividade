<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quiz Interativo com Risk</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .card {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 600px;
            overflow: hidden;
        }
        .card-content {
            padding: 20px;
        }
        .message {
            display: flex;
            align-items: flex-start;
            margin-bottom: 20px;
            position: relative;
        }
        .avatar {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 24px;
            flex-shrink: 0;
            z-index: 2;
        }
        .message-content {
            border-radius: 18px;
            padding: 10px 15px;
            max-width: calc(100% - 130px);
            margin: 0 15px;
        }
        .risk-message .message-content {
            background-color: #006400; /* Verde escuro */
            color: white;
        }
        .user-message {
            flex-direction: row-reverse;
        }
        .user-message .message-content {
            background-color: #00008B; /* Azul escuro */
            color: white;
        }
        .options {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .option-button {
            background-color: #00008B; /* Azul escuro */
            border: none;
            color: white;
            padding: 15px 32px;
            text-align: left;
            text-decoration: none;
            display: flex;
            align-items: center;
            justify-content: space-between;
            font-size: 16px;
            cursor: pointer;
            border-radius: 8px;
            transition: background-color 0.3s;
        }
        .option-button:hover {
            background-color: #0000CD; /* Azul médio */
        }
        .option-button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
        }
        .loading {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 50px;
        }
        .loading-dot {
            width: 10px;
            height: 10px;
            background-color: #333;
            border-radius: 50%;
            margin: 0 5px;
            animation: bounce 0.6s infinite alternate;
        }
        .loading-dot:nth-child(2) {
            animation-delay: 0.2s;
        }
        .loading-dot:nth-child(3) {
            animation-delay: 0.4s;
        }
        @keyframes bounce {
            to {
                transform: translateY(-10px);
            }
        }
        .close-button {
            background-color: #4CAF50;
            border: none;
            color: white;
            padding: 10px 20px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            margin: 4px 2px;
            cursor: pointer;
            border-radius: 4px;
            transition: background-color 0.3s;
        }
        .close-button:hover {
            background-color: #45a049;
        }
        .end-message {
            text-align: center;
            font-size: 24px;
            margin-top: 20px;
        }
        @media (max-width: 480px) {
            .card {
                max-width: 100%;
                border-radius: 0;
            }
            .card-content {
                padding: 10px;
            }
            .avatar {
                width: 40px;
                height: 40px;
                font-size: 20px;
            }
            .message-content {
                max-width: calc(100% - 110px);
            }
            .option-button {
                padding: 10px 20px;
            }
        }
    </style>
</head>
<body>
    <div id="quiz-container" class="card">
        <div class="card-content">
            <!-- Quiz content will be dynamically inserted here -->
        </div>
    </div>
    <script>
        const quizData = {
            question: "Os comportamentos de produtividade podem ajudar para que uma empresa inove?",
            options: [
                { id: 1, text: "A produtividade tem foco exclusivo em transformar qualquer coisa em algo tangível " },
                { id: 2, text: "A produtividade serve para fazer com que os processos sejam executados com a máxima eficiência." }
            ],
            correctAnswer: 2
        };
        let step = 0;
        let selectedAnswer = null;
        let isCorrect = null;
        function renderQuiz() {
            const container = document.querySelector('.card-content');
            container.innerHTML = '';
            // Render question
            container.appendChild(createMessage('Risk pergunta:', quizData.question, '🤖', '#3498db', false));
            if (step === 0) {
                // Render options
                const optionsContainer = document.createElement('div');
                optionsContainer.className = 'options';
                quizData.options.forEach(option => {
                    const button = document.createElement('button');
                    button.className = 'option-button';
                    button.innerHTML = `
                        <span>${option.text}</span>
                        <span class="avatar" style="background-color: #2ecc71; color: white;">😊</span>
                    `;
                    button.onclick = () => handleSelectAnswer(option.id);
                    optionsContainer.appendChild(button);
                });
                container.appendChild(optionsContainer);
            }
            if (step >= 1 && selectedAnswer) {
                // Render user answer
                const selectedOption = quizData.options.find(opt => opt.id === selectedAnswer);
                container.appendChild(createMessage('Resposta:', selectedOption.text, '😊', '#2ecc71', true));
            }
            if (step === 1) {
                // Render loading animation
                const loading = document.createElement('div');
                loading.className = 'loading';
                loading.innerHTML = '<div class="loading-dot"></div><div class="loading-dot"></div><div class="loading-dot"></div>';
                container.appendChild(loading);
            }
            if (step === 2) {
                // Render final answer
                const correctOption = quizData.options.find(opt => opt.id === quizData.correctAnswer);
                const message = `
                    <p>${isCorrect ? 'Parabéns! Você acertou!' : 'Ops! Não foi dessa vez.'}</p>
                    <p>A resposta correta é: <strong>${correctOption.text}</strong></p>
                    <p>A produtividade possui o foco em transformar “qualquer coisa” em algo que funcione, ou seja, ideias, processos, tarefas, ou seja, tudo aquilo que precisa de fato acontecer. Sem a produtividade a inovação ficaria apenas na “ideia” 💡</p>
                `;
                container.appendChild(createMessage('Risk responde:', message, '🤖', '#3498db', false));
                // Adiciona os três pontinhos após a resposta do Risk
                setTimeout(() => {
                    const loadingDots = document.createElement('div');
                    loadingDots.className = 'loading';
                    loadingDots.innerHTML = '<div class="loading-dot"></div><div class="loading-dot"></div><div class="loading-dot"></div>';
                    container.appendChild(loadingDots);
                    // Remove os três pontinhos e adiciona a sugestão de encerrar o quiz
                    setTimeout(() => {
                        loadingDots.remove();
                        const closeMessage = createMessage('Risk sugere:', 'Você gostaria de encerrar este quiz?', '🤖', '#3498db', false);
                        container.appendChild(closeMessage);
                        const closeButton = document.createElement('button');
                        closeButton.className = 'close-button';
                        closeButton.textContent = 'Tchau!!!✌🎉✌️';
                        closeButton.onclick = () => {
                            container.innerHTML = '<div class="end-message">Aprendeu? Vamos voltar? 😬 </div>'; };
                        container.appendChild(closeButton);
                    }, 2000);
                }, 1000);
            }
        }
        function createMessage(title, content, avatar, avatarBgColor, isUser) {
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${isUser ? 'user-message' : 'risk-message'}`;
            messageDiv.innerHTML = `
                ${isUser ? '' : `<div class="avatar" style="background-color: ${avatarBgColor}; color: white;">${avatar}</div>`}
                <div class="message-content">
                    <p><strong>${title}</strong></p>
                    <p>${content}</p>
                </div>
                ${isUser ? `<div class="avatar" style="background-color: ${avatarBgColor}; color: white;">${avatar}</div>` : ''}
            `;
            return messageDiv;
        }
        function handleSelectAnswer(answerId) {
            selectedAnswer = answerId;
            isCorrect = answerId === quizData.correctAnswer;
            step = 1;
            renderQuiz();
            setTimeout(() => {
                step = 2;
                renderQuiz();
            }, 5000);
        }
        // Initial render
        renderQuiz();
    </script>
</body>
</html>
