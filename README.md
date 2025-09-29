<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>SIA - Smart Interactive Assistant</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f9f9f9; padding: 20px; }
    #chatbox { height: 300px; overflow-y: scroll; background: #fff; padding: 10px; border: 1px solid #ccc; margin-bottom: 10px; }
    input, button { padding: 10px; width: 100%; margin-top: 5px; }
  </style>
</head>
<body>
  <h1>Welcome to SIA 🤖</h1>
  <div id="chatbox"></div>
  <input type="text" id="userInput" placeholder="Ask me anything...">
  <button onclick="sendMessage()">Send</button>
  <button onclick="startListening()">🎙️ Speak</button>

  <script>
    const apiKey = 'YOUR_OPENAI_API_KEY'; // Replace with your actual key

    async function sendMessage() {
      const input = document.getElementById('userInput').value;
      document.getElementById('chatbox').innerHTML += `<p><strong>You:</strong> ${input}</p>`;
      document.getElementById('userInput').value = '';

      const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
          model: 'gpt-3.5-turbo',
          messages: [
            {
              role: 'system',
              content: 'You are SIA, a friendly, philosophical assistant who explains things clearly and supports learners like Aliya.'
            },
            {
              role: 'user',
              content: input
            }
          ]
        })
      });

      const data = await response.json();
      const reply = data.choices[0].message.content;
      document.getElementById('chatbox').innerHTML += `<p><strong>SIA:</strong> ${reply}</p>`;
    }

    function startListening() {
      const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
      recognition.lang = 'en-IN';
      recognition.interimResults = false;
      recognition.maxAlternatives = 1;

      recognition.start();

      recognition.onresult = function(event) {
        const voiceInput = event.results[0][0].transcript;
        document.getElementById('userInput').value = voiceInput;
        sendMessage();
      };

      recognition.onerror = function(event) {
        alert('Voice recognition error: ' + event.error);
      };
    }
  </script>
</body>
</html>
