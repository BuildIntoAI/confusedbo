<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ConfusedBot</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
        }
        #chatbox {
            width: 80%;
            max-width: 600px;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            margin-top: 20px;
        }
        #messages {
            margin-bottom: 20px;
            max-height: 400px;
            overflow-y: auto;
        }
        .message {
            padding: 10px;
            margin: 5px;
            border-radius: 5px;
        }
        .user-msg {
            background-color: #d3f4ff;
            text-align: right;
        }
        .bot-msg {
            background-color: #f9f9f9;
        }
        #userInput {
            width: 100%;
            padding: 10px;
            margin-top: 10px;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
        #sendBtn {
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        #sendBtn:disabled {
            background-color: #ccc;
        }
    </style>
</head>
<body>

<h1>ConfusedBot</h1>
<div id="chatbox">
    <div id="messages"></div>
    <input type="text" id="userInput" placeholder="Ask me anything!" />
    <button id="sendBtn">Send</button>
</div>

<script src="app.js"></script>

</body>
</html>
const apiKey = 'YOUR_OPENAI_API_KEY';  // Replace with your OpenAI API key
const sendBtn = document.getElementById('sendBtn');
const userInput = document.getElementById('userInput');
const messagesDiv = document.getElementById('messages');

sendBtn.addEventListener('click', () => {
    const input = userInput.value;
    if (input.trim()) {
        displayMessage(input, 'user');
        userInput.value = '';
        sendBtn.disabled = true;

        getConfusedBotAnswer(input).then(response => {
            const wrongAnswer = intentionallyWrongAnswer(response);
            displayMessage(wrongAnswer, 'bot');
            sendBtn.disabled = false;
        });
    }
});

function displayMessage(text, sender) {
    const messageDiv = document.createElement('div');
    messageDiv.classList.add('message', sender === 'user' ? 'user-msg' : 'bot-msg');
    messageDiv.textContent = text;
    messagesDiv.appendChild(messageDiv);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
}

async function getConfusedBotAnswer(input) {
    const url = 'https://api.openai.com/v1/completions';
    const response = await fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
            model: "gpt-4", // or "gpt-3.5-turbo" for GPT-3
            prompt: input,
            max_tokens: 100,
            temperature: 0.7
        })
    });

    const data = await response.json();
    return data.choices[0].text.trim();
}

// Function to intentionally return wrong answers
function intentionallyWrongAnswer(correctAnswer) {
    // For simplicity, this function reverses the correct answer or makes it nonsensical
    return 'Wrong answer: ' + correctAnswer.split('').reverse().join('');
}
