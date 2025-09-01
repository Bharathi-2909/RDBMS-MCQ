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
    .login-container, .quiz-container {
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
    #result {
      margin-top: 20px;
      font-weight: bold;
      text-align: center;
      font-size: 18px;
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Login Section -->
    <div id="loginSection" class="login-container active">
      <h2>Student Login</h2>
      <input type="text" id="username" placeholder="Enter Name or Email">
      <input type="password" id="password" placeholder="Enter Password">
      <button onclick="login()">Login</button>
    </div>

    <!-- Quiz Section -->
    <div id="quizSection" class="quiz-container">
      <h2>Welcome <span id="displayName"></span>! Take the Quiz</h2>
      <form id="quizForm"></form>
      <button type="button" onclick="submitQuiz()">Submit Quiz</button>
      <p id="result"></p>
    </div>
  </div>

  <script>
    // Full Question Bank (50+)
    const questionBank = [
      // Subqueries
      {q:"Which query inside another query is called?", options:["Main query","Subquery","Join","View"], answer:"Subquery"},
      {q:"Subqueries in SQL are enclosed within?", options:["{}", "()", "[]", "<>"], answer:"()"},
      {q:"Which operator is often used with subqueries?", options:["IN","LIKE","BETWEEN","ALL"], answer:"IN"},
      {q:"Subqueries can return?", options:["Single value","Multiple values","Table","All of these"], answer:"All of these"},
      {q:"Subquery used in WHERE clause is called?", options:["Correlated Subquery","Nested Subquery","Simple Subquery","Inline View"], answer:"Simple Subquery"},
      {q:"Which clause is mandatory in subquery?", options:["WHERE","FROM","SELECT","None"], answer:"SELECT"},
      {q:"Which keyword is used for comparing subquery output?", options:["EXISTS","IN","ANY","All of these"], answer:"All of these"},
      {q:"A subquery returning multiple columns is called?", options:["Scalar subquery","Row subquery","Table subquery","Nested subquery"], answer:"Table subquery"},
      {q:"Which subquery runs first?", options:["Inner","Outer","Both","Random"], answer:"Inner"},
      {q:"Subquery inside FROM clause is called?", options:["Inline view","Nested view","Virtual table","All"], answer:"Inline view"},
      // Correlated Subqueries
      {q:"A correlated subquery executes?", options:["Once only","For each row of outer query","Twice","Never"], answer:"For each row of outer query"},
      {q:"Correlated subqueries are generally ___ than simple subqueries?", options:["Faster","Slower","Same speed","None"], answer:"Slower"},
      {q:"Correlated subquery depends on?", options:["Outer query","Inner query","Database engine","None"], answer:"Outer query"},
      {q:"Which keyword is used often with correlated subqueries?", options:["EXISTS","GROUP BY","ORDER BY","UNION"], answer:"EXISTS"},
      {q:"Correlated subqueries are evaluated?", options:["Bottom to Top","Row by Row","Column by Column","All at once"], answer:"Row by Row"},
      {q:"Which is more efficient for large data?", options:["Join","Correlated Subquery","Union","Group By"], answer:"Join"},
      {q:"Correlated subqueries are used mainly for?", options:["Filtering rows","Sorting","Joins","Grouping"], answer:"Filtering rows"},
      {q:"Which clause is required in correlated subquery?", options:["WHERE","HAVING","GROUP BY","ORDER BY"], answer:"WHERE"},
      {q:"Correlated subqueries cannot be used in?", options:["UPDATE","DELETE","SELECT","None"], answer:"None"},
      {q:"Which subquery is executed once per row?", options:["Correlated Subquery","Nested Subquery","Scalar Subquery","Table Subquery"], answer:"Correlated Subquery"},
      // PL/SQL
      {q:"PL/SQL stands for?", options:["Programming Language/SQL","Procedural Language/SQL","Practical Language/SQL","Predefined Language/SQL"], answer:"Procedural Language/SQL"},
      {q:"Which keyword is used to start a PL/SQL block?", options:["BEGIN","START","DECLARE","OPEN"], answer:"DECLARE"},
      {q:"Which section of PL/SQL block is mandatory?", options:["DECLARE","EXCEPTION","BEGIN","CURSOR"], answer:"BEGIN"},
      {q:"Which statement is used to handle exceptions in PL/SQL?", options:["TRY","CATCH","EXCEPTION","HANDLE"], answer:"EXCEPTION"},
      {q:"Which loop is not available in PL/SQL?", options:["FOR LOOP","DO WHILE LOOP","WHILE LOOP","SIMPLE LOOP"], answer:"DO WHILE LOOP"},
      {q:"In PL/SQL, which symbol is used for assignment?", options:["=","<=",":=","->"], answer:":="},
      {q:"Which command is used to permanently save changes in PL/SQL?", options:["SAVEPOINT","ROLLBACK","COMMIT","END"], answer:"COMMIT"},
      {q:"Which is not a PL/SQL datatype?", options:["NUMBER","VARCHAR2","BOOLEAN","STRING"], answer:"STRING"},
      {q:"What is the default value of a variable in PL/SQL if not initialized?", options:["0","NULL","FALSE","Undefined"], answer:"NULL"},
      {q:"Which is a composite datatype in PL/SQL?", options:["ARRAY","RECORD","TABLE","Both B and C"], answer:"Both B and C"},
      {q:"Cursor in PL/SQL is used for?", options:["Looping","Row by row processing","Exception Handling","Sorting"], answer:"Row by row processing"},
      {q:"Which PL/SQL statement is used to stop a loop?", options:["EXIT","BREAK","STOP","RETURN"], answer:"EXIT"},
      {q:"PL/SQL is executed in?", options:["Client machine","Server","Both","None"], answer:"Server"},
      {q:"What is an anonymous block in PL/SQL?", options:["Without name","Without variables","Without exception","Without output"], answer:"Without name"},
      {q:"Which operator is used in PL/SQL to compare NULLs?", options:["=","IS NULL","==","!="], answer:"IS NULL"},
      {q:"Which keyword is used to declare an exception?", options:["DECLARE","EXCEPTION","DEFINE","CATCH"], answer:"EXCEPTION"},
      {q:"Which PL/SQL statement is used to undo changes?", options:["SAVEPOINT","COMMIT","ROLLBACK","EXIT"], answer:"ROLLBACK"},
      {q:"Which PL/SQL keyword defines a procedure?", options:["FUNCTION","PROC","PROCEDURE","SUB"], answer:"PROCEDURE"},
      {q:"Which section is optional in PL/SQL block?", options:["BEGIN","EXCEPTION","DECLARE","All"], answer:"All"},
      {q:"Which keyword defines a function in PL/SQL?", options:["FUNCTION","PROC","DEFINE","FUN"], answer:"FUNCTION"}
    ];

    // Login function
    function login() {
      const username = document.getElementById("username").value.trim();
      const password = document.getElementById("password").value.trim();

      if(username === "" || password === "") {
        alert("Please enter username and password!");
        return;
      }

      // Show quiz section
      document.getElementById("loginSection").classList.remove("active");
      document.getElementById("quizSection").classList.add("active");

      // Display student name
      document.getElementById("displayName").innerText = username;

      loadQuiz();
    }

    // Get 20 random questions
    function getRandomQuestions(bank, count) {
      let shuffled = [...bank].sort(() => 0.5 - Math.random());
      return shuffled.slice(0, count);
    }

    let questions = [];

    function loadQuiz() {
      questions = getRandomQuestions(questionBank, 20);
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
      document.getElementById("result").innerText =
        "✅ You scored " + score + " out of " + questions.length;
    }
  </script>
</body>
</html>
