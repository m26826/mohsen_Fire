<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>محسن فاير</title>
  <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
    }
    body {
      font-family: 'Cairo', sans-serif;
      background: url('file-EC9kNvmM3TYhhiXCLH6Hve') no-repeat center center fixed;
      background-size: cover;
      color: #fff;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
    }
    .container {
      background-color: rgba(30, 42, 56, 0.85);
      padding: 30px;
      border-radius: 15px;
      box-shadow: 0 0 20px rgba(0,0,0,0.5);
      width: 90%;
      max-width: 400px;
      text-align: center;
    }
    h1 {
      margin-bottom: 20px;
      color: #ffcc00;
    }
    input, button {
      width: 100%;
      padding: 12px;
      margin: 10px 0;
      border: none;
      border-radius: 8px;
      font-size: 16px;
    }
    input {
      background-color: #2e3e50;
      color: white;
    }
    button {
      background-color: #ffcc00;
      color: #1e2a38;
      font-weight: bold;
      cursor: pointer;
      transition: background 0.3s ease;
    }
    button:hover {
      background-color: #e6b800;
    }
    .points, .level, .status {
      font-size: 1.2em;
      margin-top: 10px;
    }
    .task {
      background: #2a3c4f;
      padding: 15px;
      margin: 10px 0;
      border-radius: 10px;
    }
    .task button {
      margin-top: 10px;
    }
    .completed {
      background-color: #4caf50 !important;
    }
    .link-button {
      background-color: #2196F3;
      margin-top: 10px;
    }
  </style>
</head>
<body>
<audio id="successSound" src="https://assets.mixkit.co/sfx/preview/mixkit-achievement-bell-600.mp3"></audio>
<audio id="levelUpSound" src="https://assets.mixkit.co/sfx/preview/mixkit-video-game-win-2016.mp3"></audio>
<div class="container" id="app">
  <h1>محسن فاير</h1>
  <div id="login">
    <input type="text" id="username" placeholder="ادخل اسم المستخدم">
    <button onclick="login()">دخول</button>
  </div>
  <div id="main" style="display:none;">
    <div class="points">نقاطك: <span id="points">0</span></div>
    <div class="level">مستواك: <span id="level">مبتدئ</span></div>
    <div id="tasks"></div>
    <button onclick="claimDaily()">تحصيل المكافأة اليومية</button>
    <button onclick="buyFirePass()">شراء فاير باس - 500 نقطة</button>
    <div class="status" id="firePassStatus"></div>
    <button onclick="redeem()">استبدال النقاط</button>
    <button onclick="logout()">تسجيل خروج</button>
    <button onclick="showInfo()">حول التطبيق</button>
  </div>
</div>
<script>
  let points = 0;
  let firePass = false;
  let level = "مبتدئ";
  const taskList = [
    { name: 'شاهد فيديو', url: 'https://www.youtube.com', points: 50 },
    { name: 'زر موقع mohsen331', url: 'https://mohsen331.blogspot.com', points: 50 },
    { name: 'شارك التطبيق', url: 'https://wa.me/?text=جرب%20تطبيق%20محسن%20فاير!', points: 50 }
  ];

  function login() {
    const username = document.getElementById('username').value;
    if (!username) return alert('يرجى إدخال اسم المستخدم');
    localStorage.setItem('username', username);
    checkReset();
    loadApp();
  }

  function checkReset() {
    const lastReset = parseInt(localStorage.getItem('lastReset') || '0');
    const now = Date.now();
    const oneDay = 24 * 60 * 60 * 1000;
    if (now - lastReset > oneDay) {
      localStorage.setItem('completedTasks', JSON.stringify([]));
      localStorage.setItem('lastReset', now);
    }
  }

  function claimDaily() {
    const lastDaily = parseInt(localStorage.getItem('lastDaily') || '0');
    const now = Date.now();
    const oneDay = 24 * 60 * 60 * 1000;
    if (now - lastDaily > oneDay) {
      points += 25;
      localStorage.setItem('lastDaily', now);
      updatePoints();
      alert('تم تحصيل 25 نقطة!');
    } else {
      alert('لقد حصلت على مكافأتك اليومية بالفعل. عُد لاحقًا.');
    }
  }

  function loadApp() {
    document.getElementById('login').style.display = 'none';
    document.getElementById('main').style.display = 'block';
    points = parseInt(localStorage.getItem('points') || 0);
    firePass = JSON.parse(localStorage.getItem('firePass') || 'false');
    updatePoints();
    updateTasks();
    updateFirePassStatus();
  }

  function updatePoints() {
    document.getElementById('points').innerText = points;
    updateLevel();
    localStorage.setItem('points', points);
  }

  function updateLevel() {
    const levelSpan = document.getElementById('level');
    if (points >= 1000) level = "أسطورة";
    else if (points >= 500) level = "محترف";
    else if (points >= 200) level = "متقدم";
    else level = "مبتدئ";
    levelSpan.innerText = level;
  }

  function updateTasks() {
    const tasksDiv = document.getElementById('tasks');
    tasksDiv.innerHTML = '';
    const completed = JSON.parse(localStorage.getItem('completedTasks') || '[]');
    taskList.forEach((task, i) => {
      const div = document.createElement('div');
      div.className = 'task';
      div.id = `task-${i}`;
      let button = `<button onclick="doTask(${i})">ابدأ المهمة</button>`;
      if (completed.includes(i)) {
        div.classList.add('completed');
        button = `<button disabled>تمت</button>`;
      }
      div.innerHTML = `<div>${task.name}</div>${button}`;
      tasksDiv.appendChild(div);
    });
  }

  function doTask(index) {
    const task = taskList[index];
    window.open(task.url, '_blank');
    setTimeout(() => {
      points += task.points;
      updatePoints();
      document.getElementById('successSound').play();
      const taskDiv = document.getElementById(`task-${index}`);
      taskDiv.classList.add('completed');
      taskDiv.querySelector('button').disabled = true;
      const completed = JSON.parse(localStorage.getItem('completedTasks') || '[]');
      completed.push(index);
      localStorage.setItem('completedTasks', JSON.stringify(completed));
      alert(`تم إنهاء المهمة وكسب ${task.points} نقطة!`);
    }, 1000);
  }

  function buyFirePass() {
    if (!confirm('هل أنت متأكد من شراء الفاير باس مقابل 500 نقطة؟')) return;
    if (points >= 500) {
      points -= 500;
      firePass = true;
      localStorage.setItem('firePass', 'true');
      updatePoints();
      updateFirePassStatus();
      document.getElementById('levelUpSound').play();
      alert('تم تفعيل فاير باس بنجاح');
    } else {
      alert('لا توجد نقاط كافية');
    }
  }

  function updateFirePassStatus() {
    document.getElementById('firePassStatus').innerText = firePass ? 'فاير باس مفعل' : 'فاير باس غير مفعل';
  }

  function redeem() {
    if (points >= 50) {
      if (confirm('هل تريد الاستبدال الآن؟')) {
        window.location.href = 'https://forms.gle/9yEDcSS6cAXefvTE9';
      }
    } else {
      alert('تحتاج 50 نقطة على الأقل للاستبدال');
    }
  }

  function showInfo() {
    alert("تطبيق محسن فاير | نسخة تجريبية 2025\nصمم خصيصًا للتحديات والمكافآت اليومية.");
  }

  function logout() {
    localStorage.clear();
    location.reload();
  }

  if (localStorage.getItem('username')) {
    checkReset();
    loadApp();
  }
</script>
</body>
</html>
