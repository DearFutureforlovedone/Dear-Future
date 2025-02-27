
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Messaging Platform</title>
    <style>
        :root {
            --color-1: #FFE5EC;
            --color-2: #FFC2D1;
            --color-3: #FFB3C6;
            --color-4: #FF8FAB;
            --color-5: #FB6F92;
        }

  * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

    body {
            background-color: var(--color-1);
            min-height: 100vh;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

 .container {
            max-width: 600px;
            width: 100%;
            background-color: white;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            animation: fadeIn 0.5s ease-in-out;
        }

   @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

  .header {
            background: linear-gradient(135deg, var(--color-4), var(--color-5));
            color: white;
            padding: 20px;
            text-align: center;
        }

  .content {
            padding: 20px;
        }

  .message-type-selector {
            margin-bottom: 20px;
        }

  select {
            width: 100%;
            padding: 12px;
            border: 2px solid var(--color-3);
            border-radius: 8px;
            background-color: white;
            font-size: 16px;
            transition: all 0.3s ease;
        }

   select:focus {
            outline: none;
            border-color: var(--color-5);
            box-shadow: 0 0 0 3px rgba(251, 111, 146, 0.2);
        }

  .form-container {
            display: none;
            animation: slideDown 0.5s ease-in-out;
        }

   @keyframes slideDown {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }

  .form-group {
            margin-bottom: 15px;
        }

  label {
            display: block;
            margin-bottom: 5px;
            font-weight: 500;
            color: #333;
        }

  input, textarea {
            width: 100%;
            padding: 12px;
            border: 2px solid var(--color-3);
            border-radius: 8px;
            font-size: 16px;
            transition: all 0.3s ease;
        }

  input:focus, textarea:focus {
            outline: none;
            border-color: var(--color-5);
            box-shadow: 0 0 0 3px rgba(251, 111, 146, 0.2);
        }

  textarea {
            min-height: 100px;
            resize: vertical;
        }

  .media-controls {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-bottom: 15px;
        }

  .media-btn {
            padding: 12px;
            background-color: var(--color-4);
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            transition: all 0.3s ease;
        }

   .media-btn:hover {
            background-color: var(--color-5);
        }

  .media-preview {
            width: 100%;
            margin-top: 10px;
            display: none;
        }

   button {
            width: 100%;
            padding: 14px;
            background-color: var(--color-5);
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.3s ease;
        }

  button:hover {
            background-color: #e05a7c;
            transform: translateY(-2px);
        }

  .info-text {
            font-size: 14px;
            color: #666;
            margin-top: 5px;
        }

  .pricing-info {
            background-color: var(--color-2);
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
            font-size: 14px;
            color: #333;
        }

   .country-selector {
            margin-bottom: 10px;
        }

   .success-message {
            display: none;
            background-color: #d4edda;
            color: #155724;
            padding: 15px;
            border-radius: 8px;
            margin-top: 20px;
            text-align: center;
            animation: fadeIn 0.5s ease-in-out;
        }

   @media (max-width: 480px) {
            .container {
                border-radius: 10px;
            }
            
  .header {
                padding: 15px;
            }
            
 .content {
                padding: 15px;
            }
            
   input, textarea, select, button {
                padding: 10px;
                font-size: 14px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Messaging Platform</h1>
            <p>Send scheduled messages to your friends and family</p>
        </div>
        
   <div class="content">
            <div class="message-type-selector">
                <label for="message-type">Select Message Type</label>
                <select id="message-type">
                    <option value="">-- Select Message Type --</option>
                    <option value="text">Text Message</option>
                    <option value="audio">Audio Message</option>
                    <option value="video">Video Message</option>
                </select>
            </div>
            
  <div id="text-form" class="form-container">
                <div class="pricing-info">
                    <p><strong>Text Message Pricing:</strong></p>
                    <p>First 10 messages: Free</p>
                    <p>From 11th message: ₹1 per message (India) / $0.30 per message (Other countries)</p>
                    <p>Your free messages remaining: <span id="text-free-count">10</span></p>
                </div>
                
   <div class="country-selector">
                    <label for="text-country">Select Country</label>
                    <select id="text-country">
                        <option value="india">India</option>
                        <option value="other">Other Countries</option>
                    </select>
                </div>
                
   <div class="form-group">
                    <label for="text-sender-name">Sender Name</label>
                    <input type="text" id="text-sender-name" required>
                </div>
                
  <div class="form-group">
                    <label for="text-sender-address">Sender Address with Pincode</label>
                    <input type="text" id="text-sender-address" required>
                </div>
                
 <div class="form-group">
                    <label for="text-sender-contact">Sender Email/Phone Number</label>
                    <input type="text" id="text-sender-contact" required>
                </div>
                
   <div class="form-group">
                    <label for="text-receiver-name">Receiver Name</label>
                    <input type="text" id="text-receiver-name" required>
                </div>
                
  <div class="form-group">
                    <label for="text-receiver-address">Receiver Address with Pincode</label>
                    <input type="text" id="text-receiver-address" required>
                </div>
                
  <div class="form-group">
                    <label for="text-receiver-contact">Receiver Email/Phone Number</label>
                    <input type="text" id="text-receiver-contact" required>
                </div>
                
 <div class="form-group">
                    <label for="text-message">Message (minimum 10 characters)</label>
                    <textarea id="text-message" minlength="10" required></textarea>
                    <p class="info-text">Characters: <span id="text-char-count">0</span> (minimum 10)</p>
                </div>
                
   <div class="form-group">
                    <label for="text-scheduled-date">Scheduled Date</label>
                    <input type="date" id="text-scheduled-date" required>
                </div>
                
  <button type="button" id="text-submit">Send Text Message</button>
            </div>
            
 <div id="audio-form" class="form-container">
                <div class="pricing-info">
                    <p><strong>Audio Message Pricing:</strong></p>
                    <p>First message: Free</p>
                    <p>From 2nd message: ₹2 per message (India) / $0.50 per message (Other countries)</p>
                    <p>Your free messages remaining: <span id="audio-free-count">1</span></p>
                </div>
                
  <div class="country-selector">
                    <label for="audio-country">Select Country</label>
                    <select id="audio-country">
                        <option value="india">India</option>
                        <option value="other">Other Countries</option>
                    </select>
                </div>
                
  <div class="form-group">
                    <label for="audio-sender-name">Sender Name</label>
                    <input type="text" id="audio-sender-name" required>
                </div>
                
  <div class="form-group">
                    <label for="audio-sender-address">Sender Address with Pincode</label>
                    <input type="text" id="audio-sender-address" required>
                </div>
                
  <div class="form-group">
                    <label for="audio-sender-contact">Sender Email/Phone Number</label>
                    <input type="text" id="audio-sender-contact" required>
                </div>
                
  <div class="form-group">
                    <label for="audio-receiver-name">Receiver Name</label>
                    <input type="text" id="audio-receiver-name" required>
                </div>
                
   <div class="form-group">
                    <label for="audio-receiver-address">Receiver Address with Pincode</label>
                    <input type="text" id="audio-receiver-address" required>
                </div>
                
   <div class="form-group">
                    <label for="audio-receiver-contact">Receiver Email/Phone Number</label>
                    <input type="text" id="audio-receiver-contact" required>
                </div>
                
  <div class="form-group">
                    <label>Audio Message</label>
                    <div class="media-controls">
                        <button type="button" class="media-btn" id="audio-record">
                            <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <circle cx="8" cy="8" r="7" stroke="white" stroke-width="2" fill="white"/>
                            </svg>
                            Record Audio
                        </button>
                        <button type="button" class="media-btn" id="audio-stop" style="display: none;">
                            <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <rect x="3" y="3" width="10" height="10" fill="white"/>
                            </svg>
                            Stop Recording
                        </button>
                        <input type="file" id="audio-upload" accept="audio/*" style="display: none;">
                        <button type="button" class="media-btn" id="audio-upload-btn">
                            <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <path d="M8 3V13M8 3L4 7M8 3L12 7" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                            </svg>
                            Upload Audio
                        </button>
                    </div>
                    <audio id="audio-preview" class="media-preview" controls></audio>
                </div>
                
  <div class="form-group">
                    <label for="audio-scheduled-date">Scheduled Date</label>
                    <input type="date" id="audio-scheduled-date" required>
                </div>
                
  <button type="button" id="audio-submit">Send Audio Message</button>
            </div>
            
 <div id="video-form" class="form-container">
                <div class="pricing-info">
                    <p><strong>Video Message Pricing:</strong></p>
                    <p>First message: Free</p>
                    <p>From 2nd message: ₹5 per message (India) / $0.70 per message (Other countries)</p>
                    <p>Your free messages remaining: <span id="video-free-count">1</span></p>
                </div>
                
   <div class="country-selector">
                    <label for="video-country">Select Country</label>
                    <select id="video-country">
                        <option value="india">India</option>
                        <option value="other">Other Countries</option>
                    </select>
                </div>
                
  <div class="form-group">
                    <label for="video-sender-name">Sender Name</label>
                    <input type="text" id="video-sender-name" required>
                </div>
                
   <div class="form-group">
                    <label for="video-sender-address">Sender Address with Pincode</label>
                    <input type="text" id="video-sender-address" required>
                </div>
                
   <div class="form-group">
                    <label for="video-sender-contact">Sender Email/Phone Number</label>
                    <input type="text" id="video-sender-contact" required>
                </div>
                
  <div class="form-group">
                    <label for="video-receiver-name">Receiver Name</label>
                    <input type="text" id="video-receiver-name" required>
                </div>
                
  <div class="form-group">
                    <label for="video-receiver-address">Receiver Address with Pincode</label>
                    <input type="text" id="video-receiver-address" required>
                </div>
                
  <div class="form-group">
                    <label for="video-receiver-contact">Receiver Email/Phone Number</label>
                    <input type="text" id="video-receiver-contact" required>
                </div>
                
   <div class="form-group">
                    <label>Video Message</label>
                    <div class="media-controls">
                        <button type="button" class="media-btn" id="video-record">
                            <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <circle cx="8" cy="8" r="7" stroke="white" stroke-width="2" fill="white"/>
                            </svg>
                            Record Video
                        </button>
                        <button type="button" class="media-btn" id="video-stop" style="display: none;">
                            <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <rect x="3" y="3" width="10" height="10" fill="white"/>
                            </svg>
                            Stop Recording
                        </button>
                        <input type="file" id="video-upload" accept="video/*" style="display: none;">
                        <button type="button" class="media-btn" id="video-upload-btn">
                            <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <path d="M8 3V13M8 3L4 7M8 3L12 7" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                            </svg>
                            Upload Video
                        </button>
                    </div>
                    <video id="video-preview" class="media-preview" controls></video>
                </div>
                
   <div class="form-group">
                    <label for="video-scheduled-date">Scheduled Date</label>
                    <input type="date" id="video-scheduled-date" required>
                </div>
                
   <button type="button" id="video-submit">Send Video Message</button>
            </div>
            
  <div id="success-message" class="success-message">
                <h3>Message scheduled successfully!</h3>
                <p>Your message will be delivered to the recipient on the scheduled date.</p>
            </div>
        </div>
    </div>

  <script>
        // Message counters
        let textMessageCount = 0;
        let audioMessageCount = 0;
        let videoMessageCount = 0;
        
        // DOM Elements
        const messageTypeSelector = document.getElementById('message-type');
        const textForm = document.getElementById('text-form');
        const audioForm = document.getElementById('audio-form');
        const videoForm = document.getElementById('video-form');
        const successMessage = document.getElementById('success-message');
        
        // Text message elements
        const textMessage = document.getElementById('text-message');
        const textCharCount = document.getElementById('text-char-count');
        const textSubmitBtn = document.getElementById('text-submit');
        const textFreeCount = document.getElementById('text-free-count');
        
        // Audio message elements
        const audioRecordBtn = document.getElementById('audio-record');
        const audioStopBtn = document.getElementById('audio-stop');
        const audioUploadBtn = document.getElementById('audio-upload-btn');
        const audioUpload = document.getElementById('audio-upload');
        const audioPreview = document.getElementById('audio-preview');
        const audioSubmitBtn = document.getElementById('audio-submit');
        const audioFreeCount = document.getElementById('audio-free-count');
        
        // Video message elements
        const videoRecordBtn = document.getElementById('video-record');
        const videoStopBtn = document.getElementById('video-stop');
        const videoUploadBtn = document.getElementById('video-upload-btn');
        const videoUpload = document.getElementById('video-upload');
        const videoPreview = document.getElementById('video-preview');
        const videoSubmitBtn = document.getElementById('video-submit');
        const videoFreeCount = document.getElementById('video-free-count');
        
        // Set minimum date to today for all date pickers
        const today = new Date().toISOString().split('T')[0];
        document.getElementById('text-scheduled-date').min = today;
        document.getElementById('audio-scheduled-date').min = today;
        document.getElementById('video-scheduled-date').min = today;
        
        // Message Type Selection
        messageTypeSelector.addEventListener('change', function() {
            // Hide all forms
            textForm.style.display = 'none';
            audioForm.style.display = 'none';
            videoForm.style.display = 'none';
            successMessage.style.display = 'none';
            
            // Show selected form
            if (this.value === 'text') {
                textForm.style.display = 'block';
            } else if (this.value === 'audio') {
                audioForm.style.display = 'block';
            } else if (this.value === 'video') {
                videoForm.style.display = 'block';
            }
        });
        
        // Text Message Character Counter
        textMessage.addEventListener('input', function() {
            textCharCount.textContent = this.value.length;
        });
        
        // Audio Upload
        audioUploadBtn.addEventListener('click', function() {
            audioUpload.click();
        });
        
        audioUpload.addEventListener('change', function() {
            if (this.files && this.files[0]) {
                const file = this.files[0];
                const url = URL.createObjectURL(file);
                audioPreview.src = url;
                audioPreview.style.display = 'block';
            }
        });
        
        // Video Upload
        videoUploadBtn.addEventListener('click', function() {
            videoUpload.click();
        });
        
        videoUpload.addEventListener('change', function() {
            if (this.files && this.files[0]) {
                const file = this.files[0];
                const url = URL.createObjectURL(file);
                videoPreview.src = url;
                videoPreview.style.display = 'block';
            }
        });
        
        // Audio Recording Simulation
        let audioRecording = false;
        audioRecordBtn.addEventListener('click', function() {
            if (!audioRecording) {
                audioRecording = true;
                audioRecordBtn.style.display = 'none';
                audioStopBtn.style.display = 'block';
                // Simulate recording (in a real app, this would use the MediaRecorder API)
                console.log('Audio recording started...');
            }
        });
        
        audioStopBtn.addEventListener('click', function() {
            audioRecording = false;
            audioRecordBtn.style.display = 'block';
            audioStopBtn.style.display = 'none';
            // Simulate recording end
            console.log('Audio recording stopped');
            // Show a simulated preview
            audioPreview.src = 'data:audio/mp3;base64,AAAAFGZ0eXBNNEEgAAAAAE00QSBtcDQyAAAAAA==';
            audioPreview.style.display = 'block';
        });
        
        // Video Recording Simulation
        let videoRecording = false;
        videoRecordBtn.addEventListener('click', function() {
            if (!videoRecording) {
                videoRecording = true;
                videoRecordBtn.style.display = 'none';
                videoStopBtn.style.display = 'block';
                // Simulate recording
                console.log('Video recording started...');
            }
        });
        
        videoStopBtn.addEventListener('click', function() {
            videoRecording = false;
            videoRecordBtn.style.display = 'block';
            videoStopBtn.style.display = 'none';
            // Simulate recording end
            console.log('Video recording stopped');
            // Show a simulated preview (in a real app this would be the recorded video)
            videoPreview.style.display = 'block';
            videoPreview.poster = '#';
        });
        
        // Form submissions
        textSubmitBtn.addEventListener('click', function() {
            // Validate text form
            const textSenderName = document.getElementById('text-sender-name').value;
            const textSenderAddress = document.getElementById('text-sender-address').value;
            const textSenderContact = document.getElementById('text-sender-contact').value;
            const textReceiverName = document.getElementById('text-receiver-name').value;
            const textReceiverAddress = document.getElementById('text-receiver-address').value;
            const textReceiverContact = document.getElementById('text-receiver-contact').value;
            const message = textMessage.value;
            const scheduledDate = document.getElementById('text-scheduled-date').value;
            
            if (!textSenderName || !textSenderAddress || !textSenderContact || 
                !textReceiverName || !textReceiverAddress || !textReceiverContact || 
                !message || !scheduledDate) {
                alert('Please fill in all required fields');
                return;
            }
            
            if (message.length < 10) {
                alert('Message must be at least 10 characters long');
                return;
            }
            
            // Increment text message count
            textMessageCount++;
            textFreeCount.textContent = Math.max(0, 10 - textMessageCount);
            
            // Handle pricing logic
            const country = document.getElementById('text-country').value;
            if (textMessageCount > 10) {
                const price = country === 'india' ? '₹1' : '$0.30';
                const confirmSend = confirm(`This message will cost ${price}. Do you want to continue?`);
                if (!confirmSend) {
                    return;
                }
            }
            
            // Process form (in a real app, this would send data to a server)
            console.log('Scheduling text message for', scheduledDate);
            console.log('From:', textSenderName);
            console.log('To:', textReceiverName);
            console.log('Message:', message);
            
            // Reset form and show success message
            document.getElementById('text-sender-name').value = '';
            document.getElementById('text-sender-address').value = '';
            document.getElementById('text-sender-contact').value = '';
            document.getElementById('text-receiver-name').value = '';
            document.getElementById('text-receiver-address').value = '';
            document.getElementById('text-receiver-contact').value = '';
            textMessage.value = '';
            document.getElementById('text-scheduled-date').value = '';
            textCharCount.textContent = '0';
            
            // Hide form and show success message
            textForm.style.display = 'none';
            successMessage.style.display = 'block';
        });
        
        audioSubmitBtn.addEventListener('click', function() {
            // Validate audio form
            const audioSenderName = document.getElementById('audio-sender-name').value;
            const audioSenderAddress = document.getElementById('audio-sender-address').value;
            const audioSenderContact = document.getElementById('audio-sender-contact').value;
            const audioReceiverName = document.getElementById('audio-receiver-name').value;
            const audioReceiverAddress = document.getElementById('audio-receiver-address').value;
            const audioReceiverContact = document.getElementById('audio-receiver-contact').value;
            const scheduledDate = document.getElementById('audio-scheduled-date').value;
            
            if (!audioSenderName || !audioSenderAddress || !audioSenderContact || 
                !audioReceiverName || !audioReceiverAddress || !audioReceiverContact || 
                !scheduledDate) {
                alert('Please fill in all required fields');
                return;
            }
            
            if (audioPreview.style.display !== 'block') {
                alert('Please record or upload an audio message');
                return;
            }
            
            // Increment audio message count
            audioMessageCount++;
            audioFreeCount.textContent = Math.max(0, 1 - audioMessageCount);
            
            // Handle pricing logic
            const country = document.getElementById('audio-country').value;
            if (audioMessageCount > 1) {
                const price = country === 'india' ? '₹2' : '$0.50';
                const confirmSend = confirm(`This message will cost ${price}. Do you want to continue?`);
                if (!confirmSend) {
                    return;
                }
            }
            
            // Process form
            console.log('Scheduling audio message for', scheduledDate);
            console.log('From:', audioSenderName);
            console.log('To:', audioReceiverName);
            
            // Reset form and show success message
            document.getElementById('audio-sender-name').value = '';
            document.getElementById('audio-sender-address').value = '';
            document.getElementById('audio-sender-contact').value = '';
            document.getElementById('audio-receiver-name').value = '';
            document.getElementById('audio-receiver-address').value = '';
            document.getElementById('audio-receiver-contact').value = '';
            document.getElementById('audio-scheduled-date').value = '';
            audioPreview.style.display = 'none';
            audioPreview.src = '';
            
            // Hide form and show success message
            audioForm.style.display = 'none';
            successMessage.style.display = 'block';
        });
        
        videoSubmitBtn.addEventListener('click', function() {
            // Validate video form
            const videoSenderName = document.getElementById('video-sender-name').value;
            const videoSenderAddress = document.getElementById('video-sender-address').value;
            const videoSenderContact = document.getElementById('video-sender-contact').value;
            const videoReceiverName = document.getElementById('video-receiver-name').value;
            const videoReceiverAddress = document.getElementById('video-receiver-address').value;
            const videoReceiverContact = document.getElementById('video-receiver-contact').value;
            const scheduledDate = document.getElementById('video-scheduled-date').value;
            
            if (!videoSenderName || !videoSenderAddress || !videoSenderContact || 
                !videoReceiverName || !videoReceiverAddress || !videoReceiverContact || 
                !scheduledDate) {
                alert('Please fill in all required fields');
                return;
            }
            
            if (videoPreview.style.display !== 'block') {
                alert('Please record or upload a video message');
                return;
            }
            
            // Increment video message count
            videoMessageCount++;
            videoFreeCount.textContent = Math.max(0, 1 - videoMessageCount);
            
            // Handle pricing logic
            const country = document.getElementById('video-country').value;
            if (videoMessageCount > 1) {
                const price = country === 'india' ? '₹5' : '$0.70';
                const confirmSend = confirm(`This message will cost ${price}. Do you want to continue?`);
                if (!confirmSend) {
                    return;
                }
            }
            
            // Process form
            console.log('Scheduling video message for', scheduledDate);
            console.log('From:', videoSenderName);
            console.log('To:', videoReceiverName);
            
            // Reset form and show success message
            document.getElementById('video-sender-name').value = '';
            document.getElementById('video-sender-address').value = '';
            document.getElementById('video-sender-contact').value = '';
            document.getElementById('video-receiver-name').value = '';
            document.getElementById('video-receiver-address').value = '';
            document.getElementById('video-receiver-contact').value = '';
            document.getElementById('video-scheduled-date').value = '';
            videoPreview.style.display = 'none';
            videoPreview.src = '';
            
            // Hide form and show success message
            videoForm.style.display = 'none';
            successMessage.
