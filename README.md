<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pomodoro Technique Tracker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background: linear-gradient(135deg, #ffefba, #ffffff);
            position: relative;
        }
        body::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-image: url('https://www.transparenttextures.com/patterns/white-paper.png');
            opacity: 0.1;
            z-index: 0;
        }
        h1, h2, h3 {
            color: #333;
            z-index: 1;
        }
        #clock, .session-count, .notification {
            text-align: center;
            color: #333;
            z-index: 1;
        }
        .settings, .timers, .todo, .habits, .calendar {
            margin: 20px 0;
            background-color: rgba(255, 255, 255, 0.9);
            padding: 10px;
            border-radius: 8px;
            z-index: 1;
        }
        .timer {
            margin: 10px 0;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .timer-display {
            font-size: 24px;
            margin: 10px 0;
        }
        .todo-zone {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
        }
        .todo-box {
            display: flex;
            flex-direction: column;
            background-color: white;
            border: 1px solid #ddd;
            border-radius: 8px;
            padding: 10px;
            width: 48%;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        textarea {
            width: 100%;
            height: 100px;
            padding: 10px;
            box-sizing: border-box;
        }
        .task-list {
            margin-top: 5px;
            list-style-type: none;
            padding: 0;
        }
        .task-list li {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 5px;
            border-bottom: 1px solid #ccc;
        }
        .completed {
            text-decoration: line-through;
            color: gray;
        }
        .calendar-grid {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 5px;
            margin-top: 10px;
        }
        .calendar-day {
            background-color: #ffffff;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            position: relative;
            cursor: pointer;
        }
        .today {
            background-color: #ffeb3b; /* Highlight color for today's date */
            font-weight: bold;
        }
        .modal-overlay, .event-modal {
            display: none;
        }
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 5;
        }
        .event-modal {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
            z-index: 10;
        }
    </style>
</head>
<body>
    <h1>Pomodoro Technique Tracker</h1>
    <div id="clock"></div>
    <div class="session-count">Sessions Completed: <span id="sessionCount">0</span></div>
    <div class="notification" id="notification"></div>

    <div class="settings">
        <label>
            Pomodoro Duration (min):
            <input type="number" id="pomodoroDuration" value="25" min="1" />
        </label>
        <label>
            Break Duration (min):
            <input type="number" id="breakDuration" value="5" min="1" />
        </label>
    </div>

    <div class="timers">
        <h2>Timers</h2>
        <div class="timer">
            <h3>Pomodoro Timer</h3>
            <div class="timer-display" id="pomodoroTimerDisplay">25:00</div>
            <button id="startPomodoro">Start Pomodoro</button>
            <button id="resetPomodoro">Reset Pomodoro</button>
        </div>
        <div class="timer">
            <h3>Break Timer</h3>
            <div class="timer-display" id="breakTimerDisplay">05:00</div>
            <button id="startBreak">Start Break</button>
            <button id="resetBreak">Reset Break</button>
        </div>
        <div class="timer">
            <h3>Custom Timer</h3>
            <input type="number" id="customTimerInput" placeholder="Enter minutes" />
            <div class="timer-display" id="customTimerDisplay">00:00</div>
            <button id="startCustomTimer">Start Custom Timer</button>
            <button id="resetCustomTimer">Reset Custom Timer</button>
        </div>
    </div>

    <div class="todo">
        <h2>To-Do List</h2>
        <div class="todo-zone">
            <div class="todo-box">
                <h3>重要且緊急</h3>
                <textarea placeholder="Enter important and urgent tasks..." id="urgentImportantInput"></textarea>
                <button id="addUrgentImportant">Add</button>
                <ul id="urgentImportantList" class="task-list"></ul>
            </div>
            <div class="todo-box">
                <h3>緊急不重要</h3>
                <textarea placeholder="Enter urgent but not important tasks..." id="urgentNotImportantInput"></textarea>
                <button id="addUrgentNotImportant">Add</button>
                <ul id="urgentNotImportantList" class="task-list"></ul>
            </div>
            <div class="todo-box">
                <h3>重要不緊急</h3>
                <textarea placeholder="Enter important but not urgent tasks..." id="importantNotUrgentInput"></textarea>
                <button id="addImportantNotUrgent">Add</button>
                <ul id="importantNotUrgentList" class="task-list"></ul>
            </div>
            <div class="todo-box">
                <h3>不重要不緊急</h3>
                <textarea placeholder="Enter not important and not urgent tasks..." id="notImportantNotUrgentInput"></textarea>
                <button id="addNotImportantNotUrgent">Add</button>
                <ul id="notImportantNotUrgentList" class="task-list"></ul>
            </div>
        </div>
    </div>

    <div class="habits">
        <h2>Habits to Achieve</h2>
        <textarea placeholder="Enter habits here..." id="habitsText"></textarea>
    </div>

    <div class="calendar">
        <h2>Calendar</h2>
        <div class="calendar-header">
            <button id="prevMonth">Previous</button>
            <h2 id="currentMonth">September 2024</h2>
            <button id="nextMonth">Next</button>
        </div>
        <div class="calendar-grid" id="calendarGrid"></div>
    </div>

    <audio id="timerEndSound" src="https://www.soundjay.com/button/beep-07.wav"></audio>

    <div class="modal-overlay" id="modalOverlay"></div>
    <div class="event-modal" id="eventModal">
        <h3>Create/Edit Event</h3>
        <input type="text" id="eventTitle" placeholder="Event Title" />
        <textarea id="eventDescription" placeholder="Event Description"></textarea>
        <button id="saveEvent">Save Event</button>
        <button id="cancelEvent">Cancel</button>
        <ul class="event-list" id="eventList"></ul>
    </div>

    <script>
        let currentDate = new Date();
        let sessionCount = 0;
        document.getElementById('sessionCount').innerText = sessionCount;

        // Timer variables
        let pomodoroTimer, breakTimer, customTimer;
        let pomodoroTime = 25 * 60; // 25 minutes
        let breakTime = 5 * 60; // 5 minutes
        let customTime = 0;

        let events = {};
        let completedTasks = [];

        function updateClock() {
            const now = new Date();
            const options = { timeZone: 'Asia/Hong_Kong', hour: '2-digit', minute: '2-digit', second: '2-digit', year: 'numeric', month: 'numeric', day: 'numeric' };
            const timeString = now.toLocaleTimeString('en-US', options);
            const dateString = now.toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' });
            document.getElementById('clock').innerHTML = `${timeString}<br>${dateString}`;
        }

        function renderCalendar() {
            const month = currentDate.getMonth();
            const year = currentDate.getFullYear();
            const firstDay = new Date(year, month, 1);
            const lastDay = new Date(year, month + 1, 0);
            const daysInMonth = lastDay.getDate();
            const calendarGrid = document.getElementById('calendarGrid');
            const currentMonthDisplay = document.getElementById('currentMonth');

            calendarGrid.innerHTML = '';
            currentMonthDisplay.innerText = `${firstDay.toLocaleString('default', { month: 'long' })} ${year}`;

            for (let i = 1; i < firstDay.getDay(); i++) {
                calendarGrid.innerHTML += '<div class="calendar-day"></div>'; // Empty cells for days before the first
            }
            for (let day = 1; day <= daysInMonth; day++) {
                const dateString = `${year}-${month + 1}-${day}`;
                const isToday = new Date().toDateString() === new Date(dateString).toDateString();
                calendarGrid.innerHTML += `
                    <div class="calendar-day ${isToday ? 'today' : ''}" data-date="${dateString}">
                        ${day}
                        <ul class="task-list" id="tasks-${dateString}"></ul>
                    </div>`;
            }

            document.querySelectorAll('.calendar-day').forEach(day => {
                day.addEventListener('click', () => {
                    const date = day.getAttribute('data-date');
                    showEventModal(date);
                });
            });
        }

        function addTask(listId, inputId) {
            const taskInput = document.getElementById(inputId);
            const taskText = taskInput.value.trim();
            if (taskText) {
                const taskList = document.getElementById(listId);
                const li = document.createElement('li');
                li.innerHTML = `
                    <input type="checkbox" class="task-checkbox" />
                    <span>${taskText}</span>
                `;
                li.querySelector('.task-checkbox').onclick = () => toggleTaskCompletion(taskText, li);
                taskList.appendChild(li);
                taskInput.value = ''; // Clear the input
            }
        }

        document.getElementById('addUrgentImportant').addEventListener('click', () => addTask('urgentImportantList', 'urgentImportantInput'));
        document.getElementById('addUrgentNotImportant').addEventListener('click', () => addTask('urgentNotImportantList', 'urgentNotImportantInput'));
        document.getElementById('addImportantNotUrgent').addEventListener('click', () => addTask('importantNotUrgentList', 'importantNotUrgentInput'));
        document.getElementById('addNotImportantNotUrgent').addEventListener('click', () => addTask('notImportantNotUrgentList', 'notImportantNotUrgentInput'));

        function toggleTaskCompletion(task, li) {
            li.classList.toggle('completed');
            if (li.classList.contains('completed')) {
                completedTasks.push(task);
            } else {
                completedTasks = completedTasks.filter(t => t !== task);
            }
            updateCompletedTasksDisplay();
        }

        function updateCompletedTasksDisplay() {
            const completedTasksList = document.getElementById('completedTasks');
            completedTasksList.innerHTML = '';
            completedTasks.forEach(task => {
                const li = document.createElement('li');
                li.textContent = task;
                completedTasksList.appendChild(li);
            });
        }

        function showEventModal(date) {
            const modal = document.getElementById('eventModal');
            const overlay = document.getElementById('modalOverlay');
            const eventTitle = document.getElementById('eventTitle');
            const eventDescription = document.getElementById('eventDescription');
            const eventList = document.getElementById('eventList');

            eventTitle.value = '';
            eventDescription.value = '';

            eventList.innerHTML = '';
            if (events[date]) {
                events[date].forEach((event, index) => {
                    const li = document.createElement('li');
                    li.textContent = `${event.title}: ${event.description}`;
                    li.onclick = () => editEvent(date, index);
                    eventList.appendChild(li);
                });
            }

            modal.style.display = 'block';
            overlay.style.display = 'block';

            document.getElementById('saveEvent').onclick = () => saveEvent(date);
            document.getElementById('cancelEvent').onclick = closeEventModal;
        }

        function closeEventModal() {
            document.getElementById('eventModal').style.display = 'none';
            document.getElementById('modalOverlay').style.display = 'none';
        }

        function saveEvent(date) {
            const title = document.getElementById('eventTitle').value;
            const description = document.getElementById('eventDescription').value;

            if (!title) {
                alert('Event title is required!');
                return;
            }

            if (!events[date]) {
                events[date] = [];
            }

            events[date].push({ title, description });
            closeEventModal();
            renderCalendar();
        }

        function editEvent(date, index) {
            const event = events[date][index];
            document.getElementById('eventTitle').value = event.title;
            document.getElementById('eventDescription').value = event.description;

            events[date].splice(index, 1);
            showEventModal(date);
        }

        document.getElementById('prevMonth').addEventListener('click', () => {
            currentDate.setMonth(currentDate.getMonth() - 1);
            renderCalendar();
        });

        document.getElementById('nextMonth').addEventListener('click', () => {
            currentDate.setMonth(currentDate.getMonth() + 1);
            renderCalendar();
        });

        document.getElementById('startPomodoro').addEventListener('click', () => {
            startTimer('pomodoro', pomodoroTime);
        });

        document.getElementById('resetPomodoro').addEventListener('click', () => {
            resetTimer('pomodoro');
        });

        document.getElementById('startBreak').addEventListener('click', () => {
            startTimer('break', breakTime);
        });

        document.getElementById('resetBreak').addEventListener('click', () => {
            resetTimer('break');
        });

        document.getElementById('startCustomTimer').addEventListener('click', () => {
            const input = document.getElementById('customTimerInput').value;
            if (input) {
                customTime = input * 60; // Convert to seconds
                startTimer('custom', customTime);
            } else {
                alert('Please enter a valid number of minutes for the custom timer.');
            }
        });

        document.getElementById('resetCustomTimer').addEventListener('click', () => {
            resetTimer('custom');
        });

        function startTimer(type, duration) {
            let time = duration;
            const display = document.getElementById(`${type}TimerDisplay`);

            clearInterval(pomodoroTimer);
            clearInterval(breakTimer);
            clearInterval(customTimer);

            const timer = setInterval(() => {
                const minutes = Math.floor(time / 60);
                const seconds = time % 60;
                display.innerText = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
                time--;

                if (time < 0) {
                    clearInterval(timer);
                    document.getElementById('timerEndSound').play();
                    if (type === 'pomodoro') {
                        sessionCount++;
                        document.getElementById('sessionCount').innerText = sessionCount;
                    }
                    resetTimer(type);
                }
            }, 1000);

            if (type === 'pomodoro') {
                pomodoroTimer = timer;
            } else if (type === 'break') {
                breakTimer = timer;
            } else {
                customTimer = timer;
            }
        }

        function resetTimer(type) {
            clearInterval(pomodoroTimer);
            clearInterval(breakTimer);
            clearInterval(customTimer);
            if (type === 'pomodoro') {
                document.getElementById('pomodoroTimerDisplay').innerText = '25:00';
            } else if (type === 'break') {
                document.getElementById('breakTimerDisplay').innerText = '05:00';
            } else {
                document.getElementById('customTimerDisplay').innerText = '00:00';
            }
        }

        // Initial Render
        renderCalendar();
        updateClock();
        setInterval(updateClock, 1000);
    </script>
</body>
</html>
