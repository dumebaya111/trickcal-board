<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>SQLite Editor on GitHub Pages</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/sql.js/1.8.0/sql-wasm.js"></script>
</head>
<body>
<h1>SQLite 파일 업로드 & 수정</h1>

<input type="file" id="dbfile" accept=".sqlite,.db">
<br><br>

<button id="showTables">테이블 보기</button>
<button id="addData">샘플 데이터 추가</button>
<button id="downloadDB">수정된 DB 다운로드</button>

<pre id="output"></pre>

<script>
let db; // 전역 DB 객체

// SQLite 초기화
initSqlJs({ locateFile: file => `https://cdnjs.cloudflare.com/ajax/libs/sql.js/1.8.0/sql-wasm.wasm` })
.then(SQL => {
    document.getElementById('dbfile').addEventListener('change', async (event) => {
        const file = event.target.files[0];
        if (!file) return;
        const arrayBuffer = await file.arrayBuffer();
        db = new SQL.Database(new Uint8Array(arrayBuffer));
        document.getElementById('output').textContent = 'DB 파일 로드 완료!';
    });
});

// 테이블 보기
document.getElementById('showTables').addEventListener('click', () => {
    if (!db) return alert("DB를 먼저 업로드하세요!");
    const res = db.exec("SELECT name FROM sqlite_master WHERE type='table';");
    document.getElementById('output').textContent = JSON.stringify(res, null, 2);
});

// 샘플 데이터 추가
document.getElementById('addData').addEventListener('click', () => {
    if (!db) return alert("DB를 먼저 업로드하세요!");
    db.run("CREATE TABLE IF NOT EXISTS test (id INTEGER, name TEXT);");
    db.run("INSERT INTO test VALUES (1, 'Alice'), (2, 'Bob');");
    document.getElementById('output').textContent = "샘플 데이터 추가 완료!";
});

// 수정된 DB 다운로드
document.getElementById('downloadDB').addEventListener('click', () => {
    if (!db) return alert("DB를 먼저 업로드하세요!");
    const binaryArray = db.export();
    const blob = new Blob([binaryArray], { type: "application/octet-stream" });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'modified.sqlite';
    a.click();
});
</script>
</body>
</html>
