<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>우정 금융 - 실시간 이자 계산기</title>
    <style>
        :root {
            --primary: #4e73df;
            --danger: #e74a3b;
            --dark: #5a5c69;
            --light: #f8f9fc;
        }
        body {
            font-family: 'Apple SD Gothic Neo', 'Malgun Gothic', sans-serif;
            background-color: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            color: #333;
        }
        .card {
            background: white;
            width: 90%;
            max-width: 400px;
            padding: 30px;
            border-radius: 25px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.15);
            text-align: center;
        }
        .header { margin-bottom: 25px; }
        .header h1 { font-size: 1.6rem; margin: 0; color: var(--primary); }
        .header p { font-size: 0.9rem; color: #888; margin: 5px 0 0; }

        .display-box {
            background: var(--light);
            padding: 25px;
            border-radius: 20px;
            margin-bottom: 20px;
            border: 2px solid #eaecf4;
        }
        .label { font-size: 0.85rem; font-weight: bold; color: var(--dark); text-transform: uppercase; }
        .interest-value {
            font-size: 3rem;
            font-weight: 800;
            color: var(--danger);
            margin: 10px 0;
            font-variant-numeric: tabular-nums;
        }
        .total-value { font-size: 1.2rem; font-weight: 600; color: #444; }

        .btn {
            width: 100%;
            padding: 18px;
            font-size: 1.2rem;
            font-weight: bold;
            border: none;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            margin-bottom: 10px;
        }
        #startBtn { background: var(--primary); color: white; box-shadow: 0 5px 15px rgba(78, 115, 223, 0.3); }
        #startBtn:active { transform: scale(0.95); }
        #stopBtn { background: #adb5bd; color: white; display: none; }

        .footer-info {
            background: #fff9db;
            padding: 15px;
            border-radius: 12px;
            font-size: 0.85rem;
            text-align: left;
            line-height: 1.6;
        }
        .footer-info b { color: var(--danger); }
        .d-day { color: var(--primary); font-weight: bold; }
    </style>
</head>
<body>

<div class="card">
    <div class="header">
        <h1>🤝 우정 금융 (2.0)</h1>
        <p>4월 10일까지 안갚으면 1초당 1원으로 올라가니까 알아서 갚아라 ㅇㅇ</p>
    </div>

    <div class="display-box">
        <div class="label">실시간 누적 이자</div>
        <div class="interest-value" id="interestDisplay">0.000원</div>
        <div class="total-value" id="totalDisplay">합계: 20,000원</div>
    </div>

    <button id="startBtn" class="btn">대출 실행 (이자 카운트)</button>
    <button id="stopBtn" class="btn">상환 완료 (정지)</button>

    <div class="footer-info">
        📍 <b>원금:</b> 20,000원<br>
        📍 <b>이자율:</b> 초당 0.005원<br>
        📍 <b>상환일:</b> 2024년 4월 10일 (<span class="d-day" id="dDayDisplay">D-12</span>)<br>
        📍 <b>예상 최종 이자:</b> 약 5,184원
    </div>
</div>

<script>
    let timer = null;
    let startTime = null;
    const principal = 20000;
    const ratePerSec = 0.005; // 요청하신 초당 0.005원 설정

    const startBtn = document.getElementById('startBtn');
    const stopBtn = document.getElementById('stopBtn');
    const interestDisplay = document.getElementById('interestDisplay');
    const totalDisplay = document.getElementById('totalDisplay');

    // D-Day 계산 (4월 10일 기준)
    function updateDDay() {
        const today = new Date();
        // 달(month)은 0부터 시작하므로 3이 4월입니다.
        const target = new Date(today.getFullYear(), 3, 10); 
        const diff = target - today;
        const days = Math.ceil(diff / (1000 * 60 * 60 * 24));
        document.getElementById('dDayDisplay').innerText = `D-${days > 0 ? days : 0}`;
    }
    updateDDay();

    function update() {
        if (!startTime) return;
        
        const now = Date.now();
        const elapsedMs = now - startTime;
        const elapsedSec = elapsedMs / 1000;
        const currentInterest = elapsedSec * ratePerSec;
        const currentTotal = principal + currentInterest;

        // 화면 갱신 (소수점 3자리까지 표시)
        interestDisplay.innerText = currentInterest.toFixed(3) + "원";
        
        // 합계 표시 (천단위 콤마)
        totalDisplay.innerText = "합계: " + Math.floor(currentTotal).toLocaleString() + "원";
        
        timer = requestAnimationFrame(update);
    }

    startBtn.onclick = function() {
        startTime = Date.now();
        startBtn.style.display = 'none';
        stopBtn.style.display = 'block';
        update();
    };

    stopBtn.onclick = function() {
        if (timer) {
            cancelAnimationFrame(timer);
            const finalInterest = interestDisplay.innerText;
            alert("정산 완료!\n최종 이자: " + finalInterest + "\n총 상환액: " + totalDisplay.innerText);
        }
    };
</script>

</body>
</html>
