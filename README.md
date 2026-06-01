<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RAW Vault</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            background: #0a0a14;
            font-family: 'Segoe UI', sans-serif;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .container {
            width: 100%;
            max-width: 900px;
            background: rgba(12, 12, 30, 0.95);
            border-radius: 24px;
            padding: 40px 30px;
            border: 2px solid rgba(0, 170, 255, 0.4);
            box-shadow: 0 0 80px rgba(0, 100, 255, 0.2);
        }

        .title {
            text-align: center;
            font-size: 2.5em;
            font-weight: 900;
            background: linear-gradient(135deg, #00aaff, #0066ff, #00ccff);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 30px;
            letter-spacing: 3px;
        }

        .subtitle {
            text-align: center;
            color: #6699cc;
            margin-bottom: 25px;
            font-size: 0.9em;
        }

        textarea {
            width: 100%;
            height: 350px;
            background: #050510;
            border: 2px solid rgba(0, 150, 255, 0.3);
            border-radius: 16px;
            padding: 20px;
            color: #00ffcc;
            font-family: 'Courier New', monospace;
            font-size: 14px;
            line-height: 1.6;
            resize: vertical;
            outline: none;
            direction: ltr;
            text-align: left;
        }

        textarea:focus {
            border-color: rgba(0, 200, 255, 0.8);
            box-shadow: 0 0 30px rgba(0, 120, 255, 0.3);
        }

        .buttons {
            display: flex;
            gap: 15px;
            margin-top: 25px;
            justify-content: center;
            flex-wrap: wrap;
        }

        button {
            padding: 15px 40px;
            border: none;
            border-radius: 50px;
            font-weight: bold;
            font-size: 1em;
            cursor: pointer;
            letter-spacing: 2px;
            transition: all 0.3s;
            color: white;
        }

        .btn-raw {
            background: linear-gradient(135deg, #003380, #0055cc);
            border: 2px solid rgba(0, 180, 255, 0.7);
            box-shadow: 0 0 30px rgba(0, 120, 255, 0.5);
        }

        .btn-raw:hover {
            box-shadow: 0 0 50px rgba(0, 160, 255, 0.8);
            transform: translateY(-3px);
        }

        .btn-clear {
            background: rgba(255, 255, 255, 0.05);
            border: 2px solid rgba(255, 255, 255, 0.2);
        }

        .btn-clear:hover {
            background: rgba(255, 255, 255, 0.1);
        }

        .file-area {
            margin-top: 15px;
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .file-label {
            background: rgba(0, 80, 180, 0.3);
            border: 1px solid rgba(0, 160, 255, 0.5);
            color: #88bbff;
            padding: 10px 20px;
            border-radius: 50px;
            cursor: pointer;
            font-size: 0.9em;
            transition: all 0.3s;
        }

        .file-label:hover {
            background: rgba(0, 100, 220, 0.5);
            box-shadow: 0 0 20px rgba(0, 150, 255, 0.5);
        }

        #fileInput { display: none; }

        #fileName {
            color: #5588bb;
            font-size: 0.85em;
        }

        .result {
            margin-top: 25px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 16px;
            padding: 20px;
            border: 1px solid rgba(0, 150, 255, 0.3);
            display: none;
        }

        .link-box {
            background: #050510;
            color: #00ccff;
            padding: 15px;
            border-radius: 10px;
            word-break: break-all;
            font-family: monospace;
            font-size: 0.85em;
            border: 1px dashed rgba(0, 180, 255, 0.5);
            margin-bottom: 10px;
        }

        .copy-btn {
            background: rgba(0, 120, 200, 0.4);
            border: 1px solid rgba(0, 180, 255, 0.7);
            color: #cceeff;
            padding: 10px 20px;
            border-radius: 30px;
            cursor: pointer;
            font-weight: bold;
        }

        .copy-btn:hover {
            background: rgba(0, 150, 240, 0.7);
        }
    </style>
</head>
<body>

    <div class="container" id="mainApp">
        <div class="title">RAW VAULT</div>
        <div class="subtitle">حماية كاملة • تشفير • RAW حقيقي</div>

        <textarea id="codeInput" placeholder="// الصق الكود هنا..." spellcheck="false"></textarea>

        <div class="file-area">
            <label for="fileInput" class="file-label">📁 رفع ملف</label>
            <input type="file" id="fileInput">
            <span id="fileName">لم يتم اختيار ملف</span>
        </div>

        <div class="buttons">
            <button class="btn-raw" id="generateBtn">⚡ توليد RAW</button>
            <button class="btn-clear" id="clearBtn">🗑️ مسح</button>
        </div>

        <div class="result" id="resultBox">
            <div class="link-box" id="linkDisplay"></div>
            <button class="copy-btn" id="copyBtn">📋 نسخ الرابط</button>
        </div>
    </div>

    <script>
        // ========== تشفير بسيط ==========
        function simpleEncode(text) {
            try {
                var b64 = btoa(unescape(encodeURIComponent(text)));
                var reversed = b64.split('').reverse().join('');
                var salt = Math.random().toString(36).substring(2, 10);
                return salt + ':::' + reversed;
            } catch (e) {
                return null;
            }
        }

        function simpleDecode(data) {
            try {
                var parts = data.split(':::');
                if (parts.length < 2) return null;
                var reversed = parts[1];
                var b64 = reversed.split('').reverse().join('');
                return decodeURIComponent(escape(atob(b64)));
            } catch (e) {
                return null;
            }
        }

        // ========== تخزين معدل باستخدام localStorage لضمان عدم ضياع البيانات عند التحديث ==========
        function saveCode(code) {
            var id = 'raw_' + Math.random().toString(36).substr(2, 9);
            var encoded = simpleEncode(code);
            if (!encoded) return null;
            
            // حفظ في ذاكرة المتصفح الدائمة
            localStorage.setItem(id, encoded);
            return id;
        }

        function getCode(id) {
            // جلب من ذاكرة المتصفح الدائمة
            var data = localStorage.getItem(id);
            if (!data) return null;
            return simpleDecode(data);
        }

        // ========== الواجهة ==========
        var codeInput = document.getElementById('codeInput');
        var fileInput = document.getElementById('fileInput');
        var fileNameSpan = document.getElementById('fileName');
        var generateBtn = document.getElementById('generateBtn');
        var clearBtn = document.getElementById('clearBtn');
        var resultBox = document.getElementById('resultBox');
        var linkDisplay = document.getElementById('linkDisplay');
        var copyBtn = document.getElementById('copyBtn');

        // رفع ملف
        fileInput.onchange = function(e) {
            var file = e.target.files[0];
            if (!file) {
                fileNameSpan.textContent = 'لم يتم اختيار ملف';
                return;
            }
            fileNameSpan.textContent = '📄 ' + file.name;
            var reader = new FileReader();
            reader.onload = function(ev) {
                codeInput.value = ev.target.result;
            };
            reader.readAsText(file);
        };

        // توليد RAW
        generateBtn.onclick = function() {
            var code = codeInput.value.trim();
            if (!code) {
                alert('⚠️ الرجاء إدخال الكود أولاً!');
                return;
            }

            var id = saveCode(code);
            if (!id) {
                alert('❌ خطأ في التشفير');
                return;
            }

            var baseUrl = window.location.href.split('?')[0];
            var rawLink = baseUrl + '?raw=' + id;

            linkDisplay.textContent = rawLink;
            resultBox.style.display = 'block';
        };

        // نسخ
        copyBtn.onclick = function() {
            var text = linkDisplay.textContent;
            navigator.clipboard.writeText(text).then(function() {
                copyBtn.textContent = '✅ تم النسخ!';
                setTimeout(function() {
                    copyBtn.textContent = '📋 نسخ الرابط';
                }, 2000);
            }).catch(function() {
                alert('الرجاء نسخ الرابط يدوياً');
            });
        };

        // مسح
        clearBtn.onclick = function() {
            codeInput.value = '';
            fileInput.value = '';
            fileNameSpan.textContent = 'لم يتم اختيار ملف';
            resultBox.style.display = 'none';
            linkDisplay.textContent = '';
        };

        // ========== معالجة رابط RAW عند قراءة الصفحة ==========
        (function checkRaw() {
            var params = new URLSearchParams(window.location.search);
            var rawId = params.get('raw');

            if (rawId) {
                var code = getCode(rawId);
                if (code !== null) {
                    // عرض الكود الصافي مباشرة لتتمكن المنفذات والسكربتات من قراءته
                    document.body.innerHTML = '';
                    document.body.style.margin = '0';
                    document.body.style.padding = '0';
                    document.body.style.background = '#0a0a0f';
                    document.body.style.color = '#d0d0d0';
                    document.body.style.fontFamily = 'monospace';
                    document.body.style.whiteSpace = 'pre-wrap';
                    document.body.style.direction = 'ltr';
                    document.body.style.textAlign = 'left';
                    document.body.style.fontSize = '13px';
                    document.body.style.padding = '20px';
                    document.body.textContent = code;
                    document.title = 'RAW Source Code';
                } else {
                    document.body.innerHTML = '<h1 style="color:#ff3333;text-align:center;padding:100px;font-family:sans-serif;">404 - لم يتم العثور على الكود أو انتهت صلاحيته</h1>';
                    document.title = '404 - Not Found';
                }
            }
        })();
    </script>
</body>
</html>
