# webapp-url
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AI-Powered Education</title>

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Background Image Example</title>
  <link rel="stylesheet" href="app.css">
</head>
<body>
  <div class="content">
    <h1>Welcome to Your AI-Powered Education Platform</h1>
    <p>Personalized learning pathways for every student.</p>
  </div>
</body>
</html>


  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 0;
      padding: 0;
    }
    h1 {
      color: #333;
    }
    .section {
      margin: 20px auto;
    }
    select, button {
      padding: 10px;
      margin: 10px;
      font-size: 16px;
    }
  </style>
</head>
<body>
  <h1>AI-Powered Education Web App</h1>
  <div class="section">
    <label for="subject">Select a subject:</label>
    <select id="subject">
      <option value="Math">Math</option>
      <option value="Science">Science</option>
    </select>
    <button onclick="fetchLessons()">Get Lessons</button>
  </div>

  <div class="section" id="lessons-section">
    <h2>Lessons</h2>
    <ul id="lessons-list"></ul>
  </div>

  <div class="section">
    <button onclick="fetchQuiz()">Take Quiz</button>
    <div id="quiz-section">
      <h2>Quiz</h2>
      <p id="quiz-question"></p>
      <div id="quiz-options"></div>
      <button onclick="submitAnswer()">Submit Answer</button>
    </div>
  </div>

  <div class="section" id="progress-section">
    <h2>Progress</h2>
    <p id="progress-score"></p>
    <p id="progress-badges"></p>
  </div>

  <script>
    const fetchLessons = async () => {
      const subject = document.getElementById('subject').value;
      const response = await fetch(`/get_lessons?subject=${subject}`);
      const data = await response.json();
      const lessonsList = document.getElementById('lessons-list');
      lessonsList.innerHTML = data.lessons
        ? data.lessons.map(lesson => `<li>${lesson}</li>`).join('')
        : '<li>No lessons available</li>';
    };

    const fetchQuiz = async () => {
      const subject = document.getElementById('subject').value;
      const response = await fetch(`/get_quiz?subject=${subject}`);
      const data = await response.json();
      document.getElementById('quiz-question').innerText = data.quiz.question || '';
      document.getElementById('quiz-options').innerHTML = data.quiz.options
        ? data.quiz.options.map(option => `<button onclick="setAnswer('${option}')">${option}</button>`).join('')
        : 'No options available';
    };

    let selectedAnswer = '';
    const setAnswer = answer => {
      selectedAnswer = answer;
    };

    const submitAnswer = async () => {
      const subject = document.getElementById('subject').value;
      const response = await fetch('/submit_answer', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ subject, answer: selectedAnswer })
      });
      const data = await response.json();
      document.getElementById('progress-score').innerText = `Score: ${data.progress.score}`;
      document.getElementById('progress-badges').innerText = `Badges: ${data.progress.badges.join(', ')}`;
      alert(data.result);
    };
  </script>
</body>
</html>
.App {
    text-align: center;
    font-family: Arial, sans-serif;
  }
  
  button {
    margin: 5px;
    padding: 10px;
    font-size: 16px;
    cursor: pointer;
  }
  
  select {
    margin: 10px;
    padding: 10px;
    font-size: 16px;
  }
  
  h1, h2 {
    color: #333;
  }
  
  ul {
    list-style-type: none;
    padding: 0;
  }
  
  li {
    margin: 5px 0;
  }
  body {
    margin: 0;
    padding: 0;
    height: 100vh;
    background-image: url('myimage.jpg'); /* Add your image file path here */
    background-size: cover; /* Scales the image to cover the entire screen */
    background-position: center; /* Centers the image */
    background-repeat: no-repeat; /* Prevents the image from repeating */
  }
  
  .content {
    text-align: center;
    color: white;
    padding: 50px;
    background-color: rgba(0, 0, 0, 0.5); /* Adds a transparent overlay for readability */
    border-radius: 10px;
    margin: auto;
    width: 80%;
    max-width: 600px;
  }
  import React, { useState, useEffect } from "react";
import axios from "axios";
import "./App.css";

const App = () => {
  const [subject, setSubject] = useState("Math");
  const [lessons, setLessons] = useState([]);
  const [quiz, setQuiz] = useState(null);
  const [answer, setAnswer] = useState("");
  const [feedback, setFeedback] = useState("");
  const [progress, setProgress] = useState(null);

  // Fetch lessons when the subject changes
  useEffect(() => {
    fetchLessons(subject);
  }, [subject]);

  const fetchLessons = async (subject) => {
    try {
      const response = await axios.get(`http://localhost:5000/get_lessons?subject=${subject}`);
      setLessons(response.data.lessons);
    } catch (error) {
      console.error("Error fetching lessons:", error);
    }
  };

  const fetchQuiz = async () => {
    try {
      const response = await axios.get(`http://localhost:5000/get_quiz?subject=${subject}`);
      setQuiz(response.data.quiz);
    } catch (error) {
      console.error("Error fetching quiz:", error);
    }
  };

  const submitAnswer = async () => {
    try {
      const response = await axios.post("http://localhost:5000/submit_answer", {
        user_id: "student1",
        subject,
        question: quiz.question,
        answer,
      });
      setFeedback(response.data.result);
    } catch (error) {
      console.error("Error submitting answer:", error);
    }
  };

  const fetchProgress = async () => {
    try {
      const response = await axios.get("http://localhost:5000/get_progress?user_id=student1");
      setProgress(response.data.progress);
    } catch (error) {
      console.error("Error fetching progress:", error);
    }
  };

  return (
    <div className="App">
      <h1>AI-Powered Education</h1>
      <div>
        <label>Select Subject: </label>
        <select value={subject} onChange={(e) => setSubject(e.target.value)}>
          <option value="Math">Math</option>
          <option value="Science">Science</option>
        </select>
      </div>
      <div>
        <h2>Lessons</h2>
        {lessons.length > 0 ? (
          <ul>
            {lessons.map((lesson, index) => (
              <li key={index}>{lesson}</li>
            ))}
          </ul>
        ) : (
          <p>No lessons available.</p>
        )}
      </div>
      <div>
        <button onClick={fetchQuiz}>Get Quiz</button>
        {quiz && (
          <div>
            <h2>Quiz</h2>
            <p>{quiz.question}</p>
            <div>
              {quiz.options.map((opt, index) => (
                <button key={index} onClick={() => setAnswer(opt)}>
                  {opt}
                </button>
              ))}
            </div>
            <button onClick={submitAnswer}>Submit Answer</button>
          </div>
        )}
      </div>
      {feedback && <p>Feedback: {feedback}</p>}
      <div>
        <button onClick={fetchProgress}>Get Progress</button>
        {progress && (
          <div>
            <h2>Progress</h2>
            <p>Score: {progress.score}</p>
            <p>Badges: {progress.badges.join(", ")}</p>
          </div>
        )}
      </div>
    </div>
  );
};

export default App;
