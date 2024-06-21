# src-apps
apps
const express = require('express');
const { exec } = require('child_process');
const app = express();
const port = 3001;

app.use(express.json());

app.post('/run', (req, res) => {
  const { code, language } = req.body;

  let command;
  if (language === 'javascript') {
    command = `node -e "${code.replace(/"/g, '\\"')}"`;
  } else if (language === 'python') {
    command = `python -c "${code.replace(/"/g, '\\"')}"`;
  } else if (language === 'java') {
    command = `javac MyClass.java && java MyClass`; // Adjust according to your Java setup
  }

  exec(command, (error, stdout, stderr) => {
    if (error) {
      res.json({ output: stderr });
    } else {
      res.json({ output: stdout });
    }
  });
});

app.listen(port, () => {
  console.log(`IDE backend listening at http://localhost:${port}`);
});
cd ..
npx create-react-app ide-frontend
cd ide-frontend
cd ..
npm install @monaco-editor/react
// ide-frontend/src/App.js

import React, { useState } from 'react';
import MonacoEditor from '@monaco-editor/react';
import './App.css';

function App() {
  const [code, setCode] = useState('// Start coding...');
  const [output, setOutput] = useState('');

  const runCode = async () => {
    const response = await fetch('http://localhost:3001/run', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ code, language: 'javascript' }) // Adjust language as needed
    });

    const data = await response.json();
    setOutput(data.output);
  };

  return (
    <div className="App">
      <header className="App-header">
        <h1>My IDE</h1>
      </header>
      <main>
        <div className="editor">
          <MonacoEditor
            height="70vh"
            defaultLanguage="javascript"
            defaultValue={code}
            onChange={setCode}
          />
        </div>
        <button onClick={runCode}>Run Code</button>
        <div className="console">
          <pre>{output}</pre>
        </div>
      </main>
    </div>
  );
}

export default App;
npm start
cd my-ide
git init
git add .
git commit -m "Initial commit"
git remote add origin <https://github.com/vishwamadhuri-48/src-apps.git>
git push -u origin master

