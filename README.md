<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>오늘의 미션</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: aliceblue;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }

        .container {
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            text-align: center;
            max-width: 800px;
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        h1 {
            color: #333;
        }

        #mission {
            font-size: 1.5em;
            color: #555;
            margin-top: 20px;
        }

        #checkbox-container {
            margin-top: 20px;
        }

        #text-input {
            display: none;
            margin-top: 10px;
            width: 100%;
            max-width: 400px;
        }

        #calendar-container {
            margin-top: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
            max-width: 400px;
        }

        #calendar {
            border: 1px solid #ccc;
            border-radius: 5px;
            width: 100%;
            max-width: 400px;
            margin-top: 10px;
            padding: 10px;
            box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
        }

        .completed {
            background-color: aliceblue;
            color: #007bff;
            padding: 5px;
            border-radius: 5px;
            display: inline-block;
            margin: 5px;
        }

        .calendar-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }

        .calendar-header button {
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            padding: 5px 10px;
            cursor: pointer;
        }

        .calendar-days {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 5px;
        }

        .calendar-day {
            padding: 10px;
            text-align: center;
            cursor: pointer;
        }

        .calendar-day.selected {
            background-color: #007bff;
            color: white;
            border-radius: 5px;
        }

        .calendar-day.completed {
            background-color: rgb(188, 225, 255);
            color: #007bff;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>오늘의 미션</h1>
        <p id="mission"></p>
        <div id="checkbox-container">
            <label>
                <input type="checkbox" id="mission-checkbox"> 미션 완료
            </label>
        </div>
        <textarea id="text-input" rows="4" placeholder="여기에 적어주세요..."></textarea>
        <div id="calendar-container">
            <div id="calendar">
                <div class="calendar-header">
                    <button id="prev-month">◀</button>
                    <span id="month-year"></span>
                    <button id="next-month">▶</button>
                </div>
                <div class="calendar-days" id="calendar-days"></div>
            </div>
        </div>
    </div>

    <script>
        const missions = [
            "하루 목표 세우기",
            "숙제 미루지 않고 하기",
            "부모님께 오늘 학교에서 있었던 일 이야기하기",
            "하루에 20분 독서하기",
            "하루에 20분 공부하기",
            "아침에 이불 정리하기",
            "불량식품 먹지 않기",
            "친구에게 칭찬하기",
            "하루에 10분 운동하기",
            "집에서 자기가 사용한 물건 제자리에 놓기",
            "하루 일과 계획 세우기",
            "부모님이나 선생님께 도움 요청하기",
            "학용품 정리하기",
            "물 충분히 마시기 (하루 6잔 이상)",
            "하루에 한 번 깊게 숨쉬기 연습하기",
            "학교 가기 전에 가방 미리 챙기기",
            "자기만의 공간 꾸미기",
            "좋아하는 노래 듣기",
            "하루에 새로운 단어 1개 배우기",
            "수업 시간에 적극적으로 손들기",
            "친구와 도움을 나누기",
            "스스로 용돈 관리하기",
            "음식 골고루 먹기",
            "부모님이나 동생 도와주기",
            "잠들기 전에 스트레칭하기",
            "자신의 강점과 약점 분석하기",
            "하루에 스마트폰 사용 시간 제한하기",
            "감사 편지 쓰기",
            "가족과 함께 저녁 식사 준비 돕기",
            "일기 쓰기",
            "친구와 긍정적인 대화 나누기",
            "매일 새로운 학습 목표 설정하기",
            "하루에 5분간 명상하기",
            "학교에서 일어난 좋은 일 기억하기",
            "새로운 취미 시도하기",
            "주변을 깨끗이 정리하기",
            "어른께 감사 인사 드리기",
            "하루에 양치 3번 하기",
            "적극적으로 질문하기",
            "자기 전 내일의 계획 세우기",
            "시간 맞춰 일어나기",
            "자기 전 전자기기 멀리하기",
            "친구와 학습 목표 공유하기",
            "가족과 시간을 보내기",
            "학교에서 새로운 친구 사귀기",
            "주말에 가족과 함께 야외 활동 계획하기",
            "감사한 일 3가지 적기",
            "오늘 한 일 중 가장 뿌듯한 일 적기",
        ];

        const calendarContainer = document.getElementById("calendar");
        const calendarDays = document.getElementById("calendar-days");
        const monthYearElem = document.getElementById("month-year");
        const prevMonthButton = document.getElementById("prev-month");
        const nextMonthButton = document.getElementById("next-month");

        let currentMonth = new Date().getMonth();
        let currentYear = new Date().getFullYear();
        let completedDates = JSON.parse(localStorage.getItem('completedDates')) || {};
        let selectedDate = getFormattedDate();

        function getFormattedDate() {
            const today = new Date();
            const year = today.getFullYear();
            const month = String(today.getMonth() + 1).padStart(2, '0');
            const day = String(today.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        }

        function updateCalendar() {
            calendarDays.innerHTML = "";
            const firstDayOfMonth = new Date(currentYear, currentMonth, 1).getDay();
            const daysInMonth = new Date(currentYear, currentMonth + 1, 0).getDate();

            monthYearElem.textContent = `${currentYear}-${String(currentMonth + 1).padStart(2, '0')}`;

            for (let i = 0; i < firstDayOfMonth; i++) {
                calendarDays.innerHTML += "<div class='calendar-day'></div>";
            }

            for (let day = 1; day <= daysInMonth; day++) {
                const dateStr = `${currentYear}-${String(currentMonth + 1).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
                const isToday = dateStr === selectedDate;
                const dayElem = document.createElement("div");
                dayElem.classList.add("calendar-day");
                dayElem.textContent = day;

                // Mark completed dates
                if (completedDates[dateStr]) {
                    dayElem.classList.add("completed");
                }
                
                if (isToday) {
                    dayElem.classList.add("selected");
                }

                dayElem.addEventListener("click", () => {
                    document.querySelectorAll(".calendar-day").forEach(day => day.classList.remove("selected"));
                    dayElem.classList.add("selected");
                    selectedDate = dateStr;
                });

                calendarDays.appendChild(dayElem);
            }
        }

        function markDateAsCompleted(date) {
            completedDates[date] = true;
            localStorage.setItem('completedDates', JSON.stringify(completedDates));
            updateCalendar();
        }

        prevMonthButton.addEventListener("click", () => {
            currentMonth--;
            if (currentMonth < 0) {
                currentMonth = 11;
                currentYear--;
            }
            updateCalendar();
        });

        nextMonthButton.addEventListener("click", () => {
            currentMonth++;
            if (currentMonth > 11) {
                currentMonth = 0;
                currentYear++;
            }
            updateCalendar();
        });

        document.getElementById("mission-checkbox").addEventListener("change", (event) => {
            if (event.target.checked) {
                markDateAsCompleted(selectedDate);
            }
        });

        function setMission() {
            const now = new Date();
            // 현재 날짜를 기반으로 미션 인덱스 계산
            const missionIndex = Math.floor(now.getTime() / (1000 * 60 * 60 * 24)) % missions.length;
            const mission = missions[missionIndex];
            document.getElementById("mission").textContent = mission;
            document.getElementById("mission-checkbox").checked = false;
            document.getElementById("text-input").style.display = mission.includes("적기") ? "block" : "none";
        }

        // 페이지 로드 시 미션 설정
        setMission(); 
        updateCalendar(); // 달력 초기화
    </script>
</body>
</html>
