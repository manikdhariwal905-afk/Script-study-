<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TalkTide AI Fix</title>
    <style>
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: #000; color: #fff; font-family: sans-serif; margin: 0; padding: 10px; }
        
        /* Screens */
        .screen { display: none; width: 100%; max-width: 400px; margin: 0 auto; }
        #scr-login { display: block; } /* Pehle login dikhega */

        /* Compact UI */
        .card { background: #111; padding: 15px; border-radius: 12px; border: 1px solid #333; margin-top: 20px; text-align: center; }
        
        input { 
            width: 100%; padding: 12px; margin: 8px 0; border-radius: 8px; border: 1px solid #444;
            background: #fff !important; color: #000 !important; font-size: 16px; position: relative; z-index: 100;
        }

        .btn { 
            width: 100%; padding: 12px; background: #00f2ff; color: #000; border: none; 
            border-radius: 8px; font-weight: bold; cursor: pointer; margin-top: 5px;
        }

        /* Chapter Grid */
        .grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 8px; margin-top: 15px; }
        .ch-item { background: #1a1a1a; padding: 10px; border-radius: 8px; border: 1px solid #333; font-size: 13px; }

        /* Quiz */
        .opt { width: 100%; padding: 10px; background: #222; color: #fff; border: 1px solid #444; border-radius: 8px; margin: 4px 0; text-align: left; }
        #feedback { margin-top: 10px; padding: 8px; border-radius: 5px; display: none; font-size: 14px; }
    </style>
</head>
<body>

    <div id="scr-login" class="screen">
        <div class="card">
            <h2 style="color:#00f2ff; margin:0 0 10px 0;">TalkTide</h2>
            <input type="text" id="user" placeholder="Naam">
            <input type="password" id="pass" placeholder="Password">
            <button class="btn" onclick="doLogin()">Login Karein</button>
        </div>
    </div>

    <div id="scr-dash" class="screen">
        <h3>Syllabus</h3>
        <div class="grid" id="chapters"></div>
    </div>

    <div id="scr-study" class="screen">
        <div style="text-align:right; font-size:12px; color:#888;">⏱️ <span id="timer">20:00</span></div>
        <div class="card">
            <h4 id="question" style="margin:0 0 15px 0;">Sawal load ho raha hai...</h4>
            <div id="ui"></div>
            <div id="feedback"></div>
            <button id="next" class="btn" style="display:none; background:#fff;" onclick="loadQ()">Agla Sawal</button>
        </div>
    </div>

    <script>
        let step = 0;
        const data = [
            { t: 'read', q: "Apple is a ___.", o: ["Fruit", "Toy"], a: "Fruit" },
            { t: 'write', q: "'Cow' ko Hindi mein likho:", a: "gaay" },
            { t: 'listen', q: "How are you", a: "How are you" }
        ];

        function doLogin() {
            let u = document.getElementById('user').value;
            let p = document.getElementById('pass').value;
            if(u && p) {
                document.getElementById('scr-login').style.display = 'none';
                document.getElementById('scr-dash').style.display = 'block';
                let html = '';
                for(let i=1; i<=10; i++) {
                    html += `<div class="ch-item">Day ${i}<button class="btn" style="padding:4px; font-size:11px;" onclick="startStudying()">Start</button></div>`;
                }
                document.getElementById('chapters').innerHTML = html;
            } else { alert("Naam aur Password bhariye"); }
        }

        function startStudying() {
            document.getElementById('scr-dash').style.display = 'none';
            document.getElementById('scr-study').style.display = 'block';
            loadQ();
        }

        function loadQ() {
            let q = data[step % data.length];
            step++;
            document.getElementById('question').innerText = q.q;
            document.getElementById('feedback').style.display = 'none';
            document.getElementById('next').style.display = 'none';
            let ui = document.getElementById('ui');
            ui.innerHTML = '';

            if(q.t === 'read') {
                q.o.forEach(opt => {
                    ui.innerHTML += `<button class="opt" onclick="check('${opt}','${q.a}')">${opt}</button>`;
                });
            } else {
                ui.innerHTML = `<input type="text" id="ans" placeholder="Jawab likhein">
                                <button class="btn" onclick="checkInput('${q.a}')">Check</button>`;
                if(q.t === 'listen') {
                    ui.insertAdjacentHTML('afterbegin', `<button class="btn" style="background:#fff;" onclick="speak('${q.a}')">🔊 Suniye</button>`);
                }
            }
        }

        function speak(txt) {
            let s = new SpeechSynthesisUtterance(txt);
            s.lang = 'en-US';
            window.speechSynthesis.speak(s);
        }

        function check(v, a) {
            let f = document.getElementById('feedback');
            f.style.display = 'block';
            let win = v.toLowerCase().trim() === a.toLowerCase().trim();
            f.style.color = win ? '#00ff88' : '#ff4b4b';
            f.innerHTML = win ? "Sahi!" : "Galat! Sahi: " + a;
            document.getElementById('next').style.display = 'block';
        }

        function checkInput(a) { check(document.getElementById('ans').value, a); }
    </script>
</body>
</html>
