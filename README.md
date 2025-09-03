<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>RDBMS & PL/SQL Quiz App</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f4f9;
      padding: 20px;
      color: #333;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }
    .container {
      max-width: 850px;
      width: 100%;
      background: white;
      padding: 25px;
      border-radius: 10px;
      box-shadow: 0px 4px 10px rgba(0,0,0,0.2);
    }
    h2 {
      text-align: center;
      margin-bottom: 20px;
    }
    .login-container, .quiz-container, .result-container {
      display: none;
    }
    .active {
      display: block;
    }
    input {
      width: 90%;
      padding: 10px;
      margin: 8px 0;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    button {
      background: #4CAF50;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
      margin-top: 15px;
    }
    button:hover {
      background: #45a049;
    }
    .question {
      margin-bottom: 15px;
    }
    .options {
      margin-left: 20px;
    }
    .options label {
      display: block;
      margin: 5px 0;
      cursor: pointer;
    }
    .score {
      font-size: 20px;
      margin: 15px 0;
      font-weight: bold;
      text-align: center;
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Login Section -->
    <div id="loginSection" class="login-container active">
      <h2>Student Login</h2>
      <input type="text" id="username" placeholder="Enter Username">
      <button onclick="login()">Start Quiz</button>
    </div>

    <!-- Quiz Section -->
    <div id="quizSection" class="quiz-container">
      <h2>Take the Quiz</h2>
      <form id="quizForm"></form>
      <button type="button" onclick="submitQuiz()">Submit Quiz</button>
    </div>

    <!-- Result Section -->
    <div id="resultSection" class="result-container">
      <h2>🎉 Submit Successful!</h2>
      <p class="score" id="score"></p>
      <button onclick="restartQuiz()">🔄 Restart Quiz</button>
    </div>
  </div>

  <script>
    const questionBank = [
      {q:"Which query inside another query is called?", options:["Main query","Subquery","Join","View"], answer:"Subquery"},
      {q:"Subqueries in SQL are enclosed within?", options:["{}", "()", "[]", "<>"], answer:"()"},
      {q:"Which operator is often used with subqueries?", options:["IN","LIKE","BETWEEN","ALL"], answer:"IN"},
      {q:"Which SQL command is used to remove a table?", options:["DROP","DELETE","REMOVE","TRUNCATE"], answer:"DROP"},
      {q:"Which keyword is used to sort results?", options:["ORDER BY","SORT","GROUP BY","RANK"], answer:"ORDER BY"},
      {q:"What does DDL stand for?", options:["Data Definition Language","Data Development Language","Dynamic Data Logic","Data Description List"], answer:"Data Definition Language"},
      {q:"Which SQL clause is used to group rows?", options:["ORDER BY","GROUP BY","HAVING","WHERE"], answer:"GROUP BY"},
      {q:"Which SQL keyword is used to remove duplicate rows?", options:["UNIQUE","DISTINCT","ONLY","REMOVE"], answer:"DISTINCT"},
      {q:"Which command is used to change data in a table?", options:["UPDATE","CHANGE","MODIFY","ALTER"], answer:"UPDATE"},
      {q:"Which SQL function counts rows?", options:["SUM()","COUNT()","TOTAL()","ROWS()"], answer:"COUNT()"},
      {q:"Which of these is not a type of SQL join?", options:["INNER JOIN","OUTER JOIN","CROSS JOIN","TOP JOIN"], answer:"TOP JOIN"},
      {q:"Which PL/SQL block type is stored in the database?", options:["Anonymous block","Stored procedure","Temporary block","Script block"], answer:"Stored procedure"},
      {q:"Which keyword is used in PL/SQL to handle exceptions?", options:["TRY","CATCH","EXCEPTION","HANDLE"], answer:"EXCEPTION"},
      {q:"Which command is used to save changes in SQL?", options:["COMMIT","SAVE","APPLY","STORE"], answer:"COMMIT"},
      {q:"Which command is used to undo changes in SQL?", options:["ROLLBACK","UNDO","CANCEL","RESET"], answer:"ROLLBACK"},
      {q:"In PL/SQL, which keyword is used to exit a loop?", options:["BREAK","EXIT","STOP","LEAVE"], answer:"EXIT"},
      {q:"Which SQL statement is used to extract data from a database?", options:["GET","EXTRACT","SELECT","FETCH"], answer:"SELECT"},
      {q:"What does TCL stand for in SQL?", options:["Transaction Control Language","Table Creation Language","Total Control Logic","Temporary Command List"], answer:"Transaction Control Language"},
      {q:"Which SQL constraint ensures a column cannot have NULL values?", options:["UNIQUE","PRIMARY KEY","NOT NULL","CHECK"], answer:"NOT NULL"},
      {q:"Which SQL keyword is used to combine results of two queries?", options:["JOIN","MERGE","UNION","INTERSECT"], answer:"UNION"}
    ];

    let questions = [];

    function login() {
      const username = document.getElementById("username").value.trim();
      if(username === "") {
        alert("⚠️ Please enter a username!");
        return;
      }

      document.getElementById("loginSection").classList.remove("active");
      document.getElementById("quizSection").classList.add("active");
      loadQuiz();
    }

    function getRandomQuestions(bank, count) {
      let shuffled = [...bank].sort(() => 0.5 - Math.random());
      return shuffled.slice(0, count);
    }

    function loadQuiz() {
      questions = getRandomQuestions(questionBank, Math.min(20, questionBank.length));
      const quizForm = document.getElementById("quizForm");
      quizForm.innerHTML = "";
      questions.forEach((item, index) => {
        const qDiv = document.createElement("div");
        qDiv.classList.add("question");
        qDiv.innerHTML = `<p><b>Q${index+1}:</b> ${item.q}</p>`;
        item.options.forEach(opt => {
          qDiv.innerHTML += `
            <div class="options">
              <label>
                <input type="radio" name="q${index}" value="${opt}"> ${opt}
              </label>
            </div>`;
        });
        quizForm.appendChild(qDiv);
      });
    }

    function submitQuiz() {
      let score = 0;
      questions.forEach((item, index) => {
        const selected = document.querySelector(`input[name="q${index}"]:checked`);
        if(selected && selected.value === item.answer) {
          score++;
        }
      });

      document.getElementById("quizSection").classList.remove("active");
      document.getElementById("resultSection").classList.add("active");

      document.getElementById("score").innerText = "✅ You scored " + score + " out of " + questions.length;
    }

    function restartQuiz() {
      document.getElementById("resultSection").classList.remove("active");
      document.getElementById("loginSection").classList.add("active");
      document.getElementById("username").value = "";
    }
  </script>
</body>
</html>
