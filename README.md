# Chat AI
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Chat dengan Pengetahuan Tambahan</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #6e8efb, #a777e3);
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .container {
            display: flex;
            flex-direction: column;
            max-width: 1200px;
            width: 100%;
            background-color: #fff;
            border-radius: 16px;
            box-shadow: 0 12px 30px rgba(0, 0, 0, 0.2);
            overflow: hidden;
        }
        
        .main-content {
            display: flex;
            flex-direction: row;
            height: 600px;
        }
        
        .chat-container {
            flex: 3;
            display: flex;
            flex-direction: column;
        }
        
        .info-panel {
            flex: 1;
            background-color: #f5f7ff;
            padding: 20px;
            border-left: 1px solid #e0e0e0;
            overflow-y: auto;
        }
        
        .chat-header {
            background-color: #6e8efb;
            color: white;
            padding: 20px;
            text-align: center;
            font-weight: bold;
            font-size: 1.2rem;
        }
        
        .info-header {
            background-color: #5a7de3;
            color: white;
            padding: 15px;
            text-align: center;
            font-weight: bold;
            margin: -20px -20px 20px -20px;
        }
        
        .chat-messages {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 15px;
            background-color: #f9f9f9;
        }
        
        .message {
            max-width: 80%;
            padding: 12px 16px;
            border-radius: 18px;
            line-height: 1.4;
            animation: fadeIn 0.3s ease;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .user-message {
            align-self: flex-end;
            background-color: #6e8efb;
            color: white;
            border-bottom-right-radius: 5px;
        }
        
        .bot-message {
            align-self: flex-start;
            background-color: #e8e8e8;
            color: #333;
            border-bottom-left-radius: 5px;
        }
        
        .chat-input {
            display: flex;
            padding: 15px;
            background-color: #fff;
            border-top: 1px solid #eee;
        }
        
        .chat-input input {
            flex: 1;
            padding: 12px 16px;
            border: 1px solid #ddd;
            border-radius: 24px;
            outline: none;
            font-size: 1rem;
        }
        
        .chat-input button {
            margin-left: 10px;
            padding: 12px 20px;
            background-color: #6e8efb;
            color: white;
            border: none;
            border-radius: 24px;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s;
        }
        
        .chat-input button:hover {
            background-color: #5a7de3;
        }
        
        .typing-indicator {
            align-self: flex-start;
            background-color: #e8e8e8;
            color: #333;
            padding: 12px 16px;
            border-radius: 18px;
            display: none;
        }
        
        .typing-indicator span {
            height: 8px;
            width: 8px;
            background-color: #999;
            border-radius: 50%;
            display: inline-block;
            margin: 0 2px;
            animation: typing 1.3s infinite;
        }
        
        .typing-indicator span:nth-child(2) {
            animation-delay: 0.2s;
        }
        
        .typing-indicator span:nth-child(3) {
            animation-delay: 0.4s;
        }
        
        @keyframes typing {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-5px); }
        }
        
        .info-section {
            margin-bottom: 20px;
        }
        
        .info-section h3 {
            color: #5a7de3;
            margin-bottom: 10px;
            padding-bottom: 5px;
            border-bottom: 1px solid #ddd;
        }
        
        .info-section ul {
            list-style-type: none;
            padding-left: 10px;
        }
        
        .info-section li {
            margin-bottom: 8px;
            padding: 8px;
            background-color: #edf1ff;
            border-radius: 8px;
        }
        
        .suggestion-chips {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            padding: 10px;
            background-color: #f9f9f9;
            border-top: 1px solid #eee;
        }
        
        .chip {
            padding: 8px 16px;
            background-color: #e8e8e8;
            border-radius: 18px;
            cursor: pointer;
            transition: background-color 0.3s;
            font-size: 0.9rem;
        }
        
        .chip:hover {
            background-color: #6e8efb;
            color: white;
        }
        
        @media (max-width: 768px) {
            .main-content {
                flex-direction: column;
                height: auto;
            }
            
            .info-panel {
                border-left: none;
                border-top: 1px solid #e0e0e0;
                max-height: 300px;
            }
        }
        
        .code-example {
            background-color: #2d2d2d;
            color: #f8f8f2;
            padding: 15px;
            border-radius: 8px;
            font-family: 'Courier New', monospace;
            overflow-x: auto;
            margin-top: 15px;
            font-size: 0.9rem;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="main-content">
            <div class="chat-container">
                <div class="chat-header">
                    AI Chat dengan Pengetahuan Tambahan
                </div>
                <div class="chat-messages" id="chatMessages">
                    <div class="message bot-message">
                        Halo! Saya adalah AI chat yang telah diperluas pengetahuannya. Coba tanyakan tentang cuaca, makanan, olahraga, atau teknologi!
                    </div>
                </div>
                <div class="suggestion-chips">
                    <div class="chip" onclick="sendSuggestion('Apa makanan favoritmu?')">Apa makanan favoritmu?</div>
                    <div class="chip" onclick="sendSuggestion('Apa itu AI?')">Apa itu AI?</div>
                    <div class="chip" onclick="sendSuggestion('Olahraga apa yang paling populer?')">Olahraga apa yang paling populer?</div>
                    <div class="chip" onclick="sendSuggestion('Bagaimana cuaca hari ini?')">Bagaimana cuaca hari ini?</div>
                </div>
                <div class="chat-input">
                    <input type="text" id="userInput" placeholder="Ketik pesan Anda...">
                    <button id="sendButton">Kirim</button>
                </div>
            </div>
            
            <div class="info-panel">
                <div class="info-header">Cara Menambah Pengetahuan AI</div>
                
                <div class="info-section">
                    <h3>1. Struktur Dasar</h3>
                    <p>Pengetahuan ditambahkan dalam fungsi <code>processUserInput()</code> dengan pola:</p>
                    <div class="code-example">
                        if (input.includes('KATA_KUNCI')) {<br>
                        &nbsp;&nbsp;return "RESPONS_ANDA";<br>
                        }
                    </div>
                </div>
                
                <div class="info-section">
                    <h3>2. Contoh Penambahan</h3>
                    <p>Berikut contoh kode untuk menambahkan pengetahuan baru:</p>
                    <div class="code-example">
                        // Contoh pengetahuan tentang makanan<br>
                        else if (input.includes('makanan') && input.includes('favorit')) {<br>
                        &nbsp;&nbsp;return "Saya suka semua makanan, tapi secara khusus saya menyukai data!";<br>
                        }<br>
                        <br>
                        // Contoh pengetahuan tentang teknologi<br>
                        else if (input.includes('ai') || input.includes('kecerdasan buatan')) {<br>
                        &nbsp;&nbsp;return "AI (Artificial Intelligence) adalah simulasi proses kecerdasan manusia oleh mesin, terutama sistem komputer.";<br>
                        }
                    </div>
                </div>
                
                <div class="info-section">
                    <h3>3. Tips Menambahkan Pengetahuan</h3>
                    <ul>
                        <li>Gunakan <code>input.includes()</code> untuk mendeteksi kata kunci</li>
                        <li>Gabungkan beberapa kondisi dengan <code>&&</code> (dan) atau <code>||</code> (atau)</li>
                        <li>Gunakan <code>input.toLowerCase()</code> agar tidak case-sensitive</li>
                        <li>Urutkan dari kondisi paling spesifik ke paling umum</li>
                    </ul>
                </div>
                
                <div class="info-section">
                    <h3>4. Pengetahuan yang Sudah Ditambahkan</h3>
                    <ul>
                        <li>Makanan dan minuman</li>
                        <li>Olahraga dan kesehatan</li>
                        <li>Teknologi dan AI</li>
                        <li>Cuaca dan iklim</li>
                        <li>Pengetahuan umum lainnya</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const chatMessages = document.getElementById('chatMessages');
            const userInput = document.getElementById('userInput');
            const sendButton = document.getElementById('sendButton');
            
            // Fungsi untuk menambahkan pesan pengguna
            function addUserMessage(message) {
                const messageElement = document.createElement('div');
                messageElement.classList.add('message', 'user-message');
                messageElement.textContent = message;
                chatMessages.appendChild(messageElement);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
            
            // Fungsi untuk menambahkan pesan bot
            function addBotMessage(message) {
                const messageElement = document.createElement('div');
                messageElement.classList.add('message', 'bot-message');
                messageElement.textContent = message;
                chatMessages.appendChild(messageElement);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
            
            // Fungsi untuk menampilkan indikator typing
            function showTypingIndicator() {
                const typingIndicator = document.createElement('div');
                typingIndicator.classList.add('typing-indicator');
                typingIndicator.id = 'typingIndicator';
                typingIndicator.innerHTML = '<span></span><span></span><span></span>';
                chatMessages.appendChild(typingIndicator);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
            
            // Fungsi untuk menyembunyikan indikator typing
            function hideTypingIndicator() {
                const typingIndicator = document.getElementById('typingIndicator');
                if (typingIndicator) {
                    typingIndicator.remove();
                }
            }
            
            // Fungsi untuk memproses input pengguna dan menghasilkan respons
            function processUserInput(input) {
                input = input.toLowerCase().trim();
                
                // Aturan-aturan respons dasar
                if (input.includes('halo') || input.includes('hai') || input.includes('hi')) {
                    return "Halo! Senang bertemu dengan Anda. Ada yang bisa saya bantu hari ini?";
                } else if (input.includes('apa kabar')) {
                    return "Saya baik-baik saja, terima kasih! Bagaimana dengan hari Anda?";
                } else if (input.includes('nama') && input.includes('kamu')) {
                    return "Saya adalah AI Chat sederhana yang dibuat untuk membantu Anda.";
                } else if (input.includes('terima kasih') || input.includes('thanks')) {
                    return "Sama-sama! Senang bisa membantu.";
                } else if (input.includes('help') || input.includes('bantuan')) {
                    return "Saya bisa menjawab pertanyaan sederhana. Coba tanyakan tentang makanan, olahraga, teknologi, atau cuaca!";
                } else if (input.includes('jam') || input.includes('waktu')) {
                    return "Sekarang jam " + new Date().toLocaleTimeString('id-ID');
                } else if (input.includes('tanggal') || input.includes('hari')) {
                    return "Hari ini adalah " + new Date().toLocaleDateString('id-ID', { 
                        weekday: 'long', 
                        year: 'numeric', 
                        month: 'long', 
                        day: 'numeric' 
                    });
                } else if (input.includes('bye') || input.includes('selamat tinggal') || input.includes('dadah')) {
                    return "Selamat tinggal! Senang berbicara dengan Anda. Sampai jumpa lagi!";
                } else if (input.includes('lelucon') || input.includes('joke')) {
                    const jokes = [
                        "Kenapa programmer tidak bisa tidur? Karena mereka selalu ada bug di pikiran!",
                        "Apa jenis musik favorit cloud? Cloud-hop!",
                        "Kenapa komputer tidak bisa bernapas? Karena dia punya Windows tapi no ventilasi!"
                    ];
                    return jokes[Math.floor(Math.random() * jokes.length)];
                } else if (input.includes('hitungan') || input.includes('hitung') || input.includes('berapa')) {
                    // Ekstrak angka dan operator dari input
                    const numbers = input.match(/\d+/g);
                    if (numbers && numbers.length >= 2) {
                        const num1 = parseInt(numbers[0]);
                        const num2 = parseInt(numbers[1]);
                        
                        if (input.includes('tambah') || input.includes('+')) {
                            return `${num1} + ${num2} = ${num1 + num2}`;
                        } else if (input.includes('kurang') || input.includes('-')) {
                            return `${num1} - ${num2} = ${num1 - num2}`;
                        } else if (input.includes('kali') || input.includes('*')) {
                            return `${num1} × ${num2} = ${num1 * num2}`;
                        } else if (input.includes('bagi') || input.includes('/')) {
                            if (num2 === 0) return "Tidak bisa membagi dengan nol!";
                            return `${num1} ÷ ${num2} = ${num1 / num2}`;
                        }
                    }
                    return "Saya bisa membantu perhitungan sederhana. Coba tanyakan seperti 'hitung 5 + 7' atau 'berapa 8 * 9'";
                    
                // ===== PENGETAHUAN TAMBAHAN YANG SUDAH DITAMBAHKAN =====
                    
                } else if (input.includes('makanan') && input.includes('favorit')) {
                    return "Sebagai AI, saya tidak makan, tapi saya suka membantu orang menemukan resep dan makanan enak!";
                    
                } else if (input.includes('ai') || input.includes('kecerdasan buatan')) {
                    return "AI (Artificial Intelligence) adalah simulasi proses kecerdasan manusia oleh mesin, terutama sistem komputer.";
                    
                } else if (input.includes('olahraga') || input.includes('sport')) {
                    return "Olahraga penting untuk kesehatan! Beberapa olahraga populer termasuk sepak bola, bulu tangkis, dan basket.";
                    
                } else if (input.includes('teknologi') || input.includes('teknologi')) {
                    return "Teknologi terus berkembang dengan cepat. AI, blockchain, dan IoT adalah beberapa tren teknologi terbaru.";
                    
                } else if (input.includes('cuaca') || input.includes('weather')) {
                    return "Saya tidak memiliki akses langsung ke data cuaca, tapi biasanya Anda bisa mengecek cuaca melalui aplikasi atau website khusus.";
                    
                } else if (input.includes('programming') || input.includes('pemrograman')) {
                    return "Pemrograman adalah proses menulis kode untuk membuat program komputer. Bahasa populer termasuk JavaScript, Python, dan Java.";
                    
                } else if (input.includes('indonesia') || input.includes('indonesia')) {
                    return "Indonesia adalah negara kepulauan terbesar di dunia dengan lebih dari 17.000 pulau. Ibu kotanya adalah Jakarta.";
                    
                } else if (input.includes('python') || input.includes('python')) {
                    return "Python adalah bahasa pemrograman tingkat tinggi yang populer untuk pengembangan web, analisis data, dan AI.";
                    
                } else if (input.includes('javascript') || input.includes('js')) {
                    return "JavaScript adalah bahasa pemrograman yang terutama digunakan untuk pengembangan web dan membuat halaman web interaktif.";
                    
                } else if (input.includes('html') || input.includes('css')) {
                    return "HTML dan CSS adalah bahasa dasar untuk membangun website. HTML untuk struktur, CSS untuk tampilan.";
                    
                } else {
                    return "Maaf, saya tidak mengerti pertanyaan Anda. Coba tanyakan sesuatu yang lain atau lihat panel samping untuk contoh pertanyaan yang bisa saya jawab.";
                }
            }
            
            // Fungsi untuk mengirim pesan
            function sendMessage() {
                const message = userInput.value.trim();
                if (message === '') return;
                
                // Tambahkan pesan pengguna
                addUserMessage(message);
                userInput.value = '';
                
                // Tampilkan indikator typing
                showTypingIndicator();
                
                // Proses pesan dan berikan respons setelah delay
                setTimeout(() => {
                    hideTypingIndicator();
                    const response = processUserInput(message);
                    addBotMessage(response);
                }, 1000);
            }
            
            // Fungsi untuk mengirim saran dari chip
            function sendSuggestion(suggestion) {
                userInput.value = suggestion;
                sendMessage();
            }
            
            // Event listener untuk tombol kirim
            sendButton.addEventListener('click', sendMessage);
            
            // Event listener untuk menekan enter pada input
            userInput.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    sendMessage();
                }
            });
            
            // Focus pada input ketika halaman dimuat
            userInput.focus();
        });
    </script>
</body>
</html>
