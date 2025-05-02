<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>掛け算タイムアタック</title>
  <style>
    body { font-family: sans-serif; text-align: center; margin-top: 50px; }
    input { font-size: 24px; width: 100px; text-align: center; }
    button { font-size: 20px; padding: 5px 20px; }
    h1 { font-size: 32px; }
    #question { font-size: 28px; margin: 20px; }
    #timer { font-size: 24px; color: red; }
  </style>
</head>
<body>
  <h1>掛け算タイムアタック</h1>
  <p id="timer">残り: 30秒</p>
  <div id="question">クリックでスタート！</div>
  <input type="number" id="answer" disabled>
  <button onclick="startGame()">スタート</button>
  <p id="result"></p>

  <script>
    let startTime, interval, a, b, correctCount = 0, totalCount = 0;
    let questionNum = 0, questionStartTime;
    const answerTimes = [];

    function randomNumber(max) {
      return Math.floor(Math.random() * max) + 1;
    }

    function nextQuestion() {
      questionNum++;
      if (questionNum <= 15) {
        a = randomNumber(9);
        b = randomNumber(9);
      } else {
        a = randomNumber(50);
        b = randomNumber(50);
      }
      document.getElementById("question").textContent = `${a} × ${b} = ?`;
      document.getElementById("answer").value = '';
      document.getElementById("answer").focus();
      questionStartTime = Date.now();
    }

    function startGame() {
      correctCount = 0;
      totalCount = 0;
      questionNum = 0;
      answerTimes.length = 0;
      startTime = Date.now();
      document.getElementById("answer").disabled = false;
      document.getElementById("result").textContent = '';
      nextQuestion();

      interval = setInterval(() => {
        const timeLeft = 30 - Math.floor((Date.now() - startTime) / 1000);
        document.getElementById("timer").textContent = `残り: ${timeLeft}秒`;

        if (timeLeft <= 0) {
          clearInterval(interval);
          document.getElementById("answer").disabled = true;
          document.getElementById("question").textContent = "終了！";

          const correctRate = ((correctCount / totalCount) * 100).toFixed(1);
          const avgTime = (answerTimes.reduce((a, b) => a + b, 0) / answerTimes.length / 1000).toFixed(2);

          document.getElementById("result").innerHTML = `
            正解数：${correctCount}問 / ${totalCount}問<br>
            正答率：${isNaN(correctRate) ? 0 : correctRate}%<br>
            平均解答時間：${isNaN(avgTime) ? 0 : avgTime} 秒
          `;
        }
      }, 100);
    }

    document.getElementById("answer").addEventListener("keydown", function(e) {
      if (e.key === "Enter" && !this.disabled) {
        const userAns = parseInt(this.value);
        const timeTaken = Date.now() - questionStartTime;
        if (!isNaN(userAns)) {
          totalCount++;
          if (userAns === a * b) correctCount++;
          answerTimes.push(timeTaken);
        }
        nextQuestion();
      }
    });
  </script>
</body>
</html>