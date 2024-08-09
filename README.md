- 👋 Hi, I’m @Anijdw334
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
Anijdw334/Anijdw334 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minha Rede Social</title>
    <style id="theme-style">
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
            transition: background-color 0.3s;
        }
        header {
            background-color: #333;
            color: white;
            padding: 10px 0;
            text-align: center;
        }
        .container {
            width: 80%;
            margin: 20px auto;
            background: white;
            padding: 20px;
            border-radius: 5px;
        }
        #camera, #video, #canvas {
            width: 100%;
            height: auto;
        }
        .hidden {
            display: none;
        }
        .profile-container {
            margin-bottom: 20px;
        }
        .profile-img {
            border-radius: 50%;
            width: 100px;
            height: 100px;
            object-fit: cover;
        }
        .profile-edit, .theme-picker, .short-video, .chat-container {
            margin-top: 20px;
        }
        .chat-messages {
            border: 1px solid #ddd;
            padding: 10px;
            height: 300px;
            overflow-y: auto;
            background: #fafafa;
        }
        .chat-input {
            display: flex;
            margin-top: 10px;
        }
        .chat-input input[type="file"] {
            display: none;
        }
        .chat-input button {
            margin-left: 10px;
        }
    </style>
</head>
<body>
    <header>
        <h1>Minha Rede Social</h1>
    </header>

    <div class="container">
        <!-- Perfil e Tema -->
        <div class="profile-container">
            <img id="profile-img" class="profile-img" src="default-profile.png" alt="Foto do Perfil" />
            <div class="profile-edit">
                <input type="file" id="profile-upload" accept="image/*" />
                <input type="text" id="profile-name" placeholder="Seu Nome" />
                <button onclick="updateProfile()">Atualizar Perfil</button>
            </div>
        </div>

        <div class="theme-picker">
            <label for="theme-color">Escolha a cor do tema:</label>
            <input type="color" id="theme-color" />
            <button onclick="applyTheme()">Aplicar Tema</button>
        </div>

        <!-- Captura de Mídia -->
        <h2>Captura de Mídia</h2>
        <video id="video" autoplay></video>
        <canvas id="canvas" class="hidden"></canvas>
        <button id="start-camera">Iniciar Câmera</button>
        <button id="take-photo" class="hidden">Tirar Foto</button>
        <button id="start-recording" class="hidden">Gravar Vídeo</button>
        <button id="stop-recording" class="hidden">Parar Gravação</button>
        <button id="apply-effect" class="hidden">Aplicar Efeito</button>
        <div id="photo-container" class="hidden">
            <img id="photo" alt="Sua Foto" />
        </div>
        <div id="video-container" class="hidden">
            <video id="recorded-video" controls></video>
        </div>

        <!-- Vídeos Curtos -->
        <div class="short-video">
            <h2>Vídeos Curtos</h2>
            <button id="start-short-recording">Gravar Vídeo Curto</button>
            <button id="stop-short-recording" class="hidden">Parar Gravação</button>
            <div id="short-videos-list">
                <!-- Lista de vídeos curtos gravados será exibida aqui -->
            </div>
        </div>

        <!-- Chat -->
        <div class="chat-container">
            <h2>Chat</h2>
            <div class="chat-messages" id="chat-messages">
                <!-- Mensagens serão exibidas aqui -->
            </div>
            <div class="chat-input">
                <input type="file" id="file-upload" accept="image/*,video/*" />
                <input type="text" id="message-input" placeholder="Digite uma mensagem" />
                <button onclick="sendMessage()">Enviar</button>
                <button onclick="document.getElementById('file-upload').click()">Enviar Mídia</button>
            </div>
        </div>
    </div>

    <script>
        // Funções para Captura de Mídia
        const startCameraButton = document.getElementById('start-camera');
        const takePhotoButton = document.getElementById('take-photo');
        const startRecordingButton = document.getElementById('start-recording');
        const stopRecordingButton = document.getElementById('stop-recording');
        const applyEffectButton = document.getElementById('apply-effect');
        const video = document.getElementById('video');
        const canvas = document.getElementById('canvas');
        const photo = document.getElementById('photo');
        const recordedVideo = document.getElementById('recorded-video');
        const photoContainer = document.getElementById('photo-container');
        const videoContainer = document.getElementById('video-container');
        const startShortRecordingButton = document.getElementById('start-short-recording');
        const stopShortRecordingButton = document.getElementById('stop-short-recording');
        const shortVideosList = document.getElementById('short-videos-list');
        let mediaStream = null;
        let mediaRecorder = null;
        let chunks = [];

        startCameraButton.addEventListener('click', async () => {
            mediaStream = await navigator.mediaDevices.getUserMedia({ video: true });
            video.srcObject = mediaStream;
            video.play();
            startCameraButton.classList.add('hidden');
            takePhotoButton.classList.remove('hidden');
            startRecordingButton.classList.remove('hidden');
        });

        takePhotoButton.addEventListener('click', () => {
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const context = canvas.getContext('2d');
            context.drawImage(video, 0, 0);
            photo.src = canvas.toDataURL('image/png');
            photoContainer.classList.remove('hidden');
        });

        startRecordingButton.addEventListener('click', () => {
            mediaRecorder = new MediaRecorder(mediaStream);
            mediaRecorder.start();
            startRecordingButton.classList.add('hidden');
            stopRecordingButton.classList.remove('hidden');
            mediaRecorder.ondataavailable = (event) => {
                chunks.push(event.data);
            };
            mediaRecorder.onstop = () => {
                const blob = new Blob(chunks, { type: 'video/mp4' });
                const url = URL.createObjectURL(blob);
                recordedVideo.src = url;
                videoContainer.classList.remove('hidden');
                chunks = [];
            };
        });

        stopRecordingButton.addEventListener('click', () => {
            mediaRecorder.stop();
            stopRecordingButton.classList.add('hidden');
            startRecordingButton.classList.remove('hidden');
        });

        applyEffectButton.addEventListener('click', () => {
            photo.classList.toggle('effect');
        });

        // Funções para Edição de Perfil
        function updateProfile() {
            const profileImgInput = document.getElementById('profile-upload');
            const profileNameInput = document.getElementById('profile-name');
            const profileImg = document.getElementById('profile-img');

            if (profileImgInput.files[0]) {
                const reader = new FileReader();
                reader.onload = function (e) {
                    profileImg.src = e.target.result;
                };
                reader.readAsDataURL(profileImgInput.files[0]);
            }

            if (profileNameInput.value) {
                document.title = `Perfil de ${profileNameInput.value}`;
            }
        }

        // Funções para Edição de Tema
        function applyTheme() {
            const themeColorInput = document.getElementById('theme-color');
            const themeStyle = document.getElementById('theme-style');
            themeStyle.innerHTML = `
                body {
                    background-color: ${themeColorInput.value};
                }
                header {
                    background-color: ${darkenColor(themeColorInput.value, 0.2)};
                }
            `;
        }

        function darkenColor(color, percent) {
            const num = parseInt(color.slice(1), 16);
            const r = (num >> 16) + Math.round((255 - (num >> 16)) * percent);
            const g = ((num >> 8) & 0x00FF) + Math.round((255 - ((num >> 8) & 0x00FF)) * percent);
            const b = (num & 0x0000FF) + Math.round((255 - (num & 0x0000FF)) * percent);
            return `#${(1 << 24 | r << 16 | g << 8 | b).toString(16).slice(1)}`;
        }

        // Funções para Vídeos Curtos
        startShortRecordingButton.addEventListener('click', () => {
            mediaRecorder = new MediaRecorder(mediaStream, { mimeType: 'video/webm' });
            mediaRecorder.start();
            startShortRecordingButton.classList.add('hidden');
            stopShortRecordingButton.classList.remove('hidden');
            mediaRecorder.ondataavailable = (event) => {
                chunks.push(event.data);
            };
            mediaRecorder.onstop = () => {
                const blob = new Blob(chunks, { type: 'video/webm' });
                const url = URL.createObjectURL(blob);
                const videoElement = document.createElement('video');
                videoElement.src = url;
                videoElement.controls = true;
                shortVideosList.appendChild(videoElement);
                chunks = [];
            };
        });

        stopShortRecordingButton.addEventListener('click', () => {
            mediaRecorder.stop();
            stopShortRecordingButton.classList.add('hidden');
            startShortRecordingButton.classList.remove('hidden');
        });

        // Funções para Chat
        function sendMessage() {
            const messageInput = document.getElementById('message-input');
            const chatMessages = document.getElementById('chat-messages');

            if (messageInput.value.trim() !== '') {
                const messageDiv = document.createElement('div');
                messageDiv.textContent = messageInput.value;
                chatMessages.appendChild(messageDiv);
                messageInput.value = '';
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
        }

        // Funções para Envio de Arquivos
        document.getElementById('file-upload').addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (file) {
                const chatMessages = document.getElementById('chat-messages');
                const fileURL = URL.createObjectURL(file);
                const fileDiv = document.createElement('div');
                if (file.type.startsWith('image/')) {
                    const img = document.createElement('img');
                    img.src = fileURL;  
                    img.style.maxWidth = '100%';
                    fileDiv.appendChild(img);
                } else if (file.type.startsWith('video/')) {
                    const video = document.createElement('video');
                    video.src = fileURL;
                    video.controls = true;
                    video.style.maxWidth = '100%';
                    fileDiv.appendChild(video);
                }
                chatMessages.appendChild(fileDiv);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
        });
    </script>
</body>
</html>
