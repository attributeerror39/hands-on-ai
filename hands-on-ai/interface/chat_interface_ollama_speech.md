# Chat Interface for Ollama with Text to Speech 

We will add the functionality to speak the chat response.

Example:


> can you add functionality to speak the chat response?

Output:

Qwen/Qwen3-VL-30B-A3B-Instruct-GGUF

Absolutely! Here's an updated version of the Ollama Chat interface that includes text-to-speech (TTS) functionality — it will speak the AI's response aloud when it finishes.

We'll use the Web Speech API, which is built into modern browsers (works in Chrome, Edge, Firefox, etc.).
✅ Updated Features:

- Text-to-Speech: Speak the bot’s response
- Button to toggle speech on/off
- Voice selection and speed control (optional)
- "Listen" button next to each message (or auto-speak)


```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Ollama Chat with Speech</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      max-width: 800px;
      margin: 0 auto;
      padding: 20px;
      background-color: #f5f7fa;
      color: #333;
    }
    h1 {
      text-align: center;
      color: #2c3e50;
    }
    .chat-container {
      display: flex;
      flex-direction: column;
      gap: 15px;
      margin-top: 20px;
    }
    .message {
      padding: 12px 16px;
      border-radius: 8px;
      max-width: 80%;
      word-wrap: break-word;
      font-size: 16px;
    }
    .user {
      background-color: #007acc;
      color: white;
      align-self: flex-end;
      margin-left: auto;
      border-bottom-right-radius: 0;
    }
    .bot {
      background-color: #e8e8e8;
      color: #333;
      align-self: flex-start;
      border-bottom-left-radius: 0;
    }
    .input-container {
      display: flex;
      gap: 10px;
      margin-top: 20px;
    }
    input[type="text"] {
      flex: 1;
      padding: 12px;
      border: 1px solid #ccc;
      border-radius: 6px;
      font-size: 16px;
    }
    button {
      padding: 12px 20px;
      background-color: #007acc;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-size: 16px;
      transition: background-color 0.2s;
    }
    button:hover {
      background-color: #0056a3;
    }
    .loading {
      color: #666;
      font-style: italic;
    }
    .controls {
      margin-top: 15px;
      display: flex;
      gap: 10px;
      align-items: center;
    }
    .voice-control {
      display: flex;
      align-items: center;
      gap: 5px;
    }
    .speak-btn {
      background-color: #4CAF50;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 14px;
      margin-left: 5px;
    }
    .speak-btn:hover {
      background-color: #388E3C;
    }
  </style>
</head>
<body>
  <h1>Ollama Chat with Speech</h1>
  <div class="chat-container" id="chatContainer"></div>
  <div class="input-container">
    <input type="text" id="messageInput" placeholder="Type your message..." />
    <button onclick="sendMessage()">Send</button>
  </div>

  <!-- Controls -->
  <div class="controls">
    <label class="voice-control">
      <input type="checkbox" id="autoSpeak" checked> Auto-speak responses
    </label>
    <label class="voice-control">
      <span>Rate:</span>
      <input type="range" id="rate" min="0.5" max="2" step="0.1" value="1">
    </label>
  </div>

  <script>
    const chatContainer = document.getElementById('chatContainer');
    const messageInput = document.getElementById('messageInput');
    const autoSpeakCheckbox = document.getElementById('autoSpeak');
    const rateSlider = document.getElementById('rate');

    // Ollama API endpoint
    const OLLAMA_API_URL = 'http://localhost:11434/api/chat';

    // Speech Synthesis
    let speechSynth = window.speechSynthesis;
    let utterance = new SpeechSynthesisUtterance();
    utterance.rate = parseFloat(rateSlider.value);

    rateSlider.addEventListener('input', () => {
      utterance.rate = parseFloat(rateSlider.value);
    });

    function speak(text, lang = 'en-US') {
      if (!speechSynth) return;
      utterance.text = text;
      utterance.lang = lang;
      utterance.rate = parseFloat(rateSlider.value);
      speechSynth.cancel(); // Cancel any ongoing speech
      speechSynth.speak(utterance);
    }

    async function sendMessage() {
      const userMessage = messageInput.value.trim();
      if (!userMessage) return;

      // Add user message to UI
      addMessage(userMessage, 'user');
      messageInput.value = '';

      // Show loading indicator
      const loadingMsg = document.createElement('div');
      loadingMsg.className = 'message bot loading';
      loadingMsg.textContent = 'Thinking...';
      chatContainer.appendChild(loadingMsg);

      try {
        const response = await fetch(OLLAMA_API_URL, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            model: 'llama3', // Change this to your preferred model
            messages: [
              { role: 'user', content: userMessage }
            ],
            stream: true,
          }),
        });

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const reader = response.body.getReader();
        const decoder = new TextDecoder('utf-8');
        let botResponse = '';

        while (true) {
          const { done, value } = await reader.read();
          if (done) break;

          const chunk = decoder.decode(value, { stream: true });
          const lines = chunk.split('\n');

          for (const line of lines) {
            if (line.trim()) {
              try {
                const json = JSON.parse(line);
                if (json.message?.content) {
                  botResponse += json.message.content;
                  // Update the loading message with new content
                  loadingMsg.textContent = botResponse;
                }
              } catch (e) {
                console.error('Failed to parse JSON:', e);
              }
            }
          }
        }

        // Remove loading indicator and show final response
        chatContainer.removeChild(loadingMsg);
        addMessage(botResponse, 'bot');

        // Auto-speak if enabled
        if (autoSpeakCheckbox.checked) {
          speak(botResponse);
        }

      } catch (error) {
        console.error('Error:', error);
        chatContainer.removeChild(loadingMsg);
        addMessage(`Error: ${error.message}`, 'bot');
      }
    }

    function addMessage(text, sender) {
      const messageDiv = document.createElement('div');
      messageDiv.className = `message ${sender}`;
      messageDiv.textContent = text;

      // Add speak button for bot messages
      if (sender === 'bot') {
        const speakBtn = document.createElement('button');
        speakBtn.className = 'speak-btn';
        speakBtn.textContent = '🔊';
        speakBtn.title = 'Speak this response';
        speakBtn.onclick = () => speak(text);
        messageDiv.appendChild(speakBtn);
      }

      chatContainer.appendChild(messageDiv);
      chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    // Allow pressing Enter to send
    messageInput.addEventListener('keypress', (e) => {
      if (e.key === 'Enter') sendMessage();
    });
  </script>
</body>
</html>
```