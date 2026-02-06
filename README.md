<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>Secure Login</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
<link rel="stylesheet" href="style.css">
</head>
<body class="d-flex justify-content-center align-items-center vh-100">

<div class="card p-4 shadow" style="width:360px;">
    <h3 class="text-center" id="title">Login</h3>

    <input class="form-control mb-2" id="username" placeholder="Username">
    <input class="form-control mb-2" id="password" type="password" placeholder="Password">

    <button class="btn btn-primary w-100" onclick="handle()">Login</button>

    <p class="text-center mt-2 text-primary" style="cursor:pointer;" onclick="toggle()">Create Account</p>
    <p class="text-center text-danger" id="msg"></p>
</div>

<script>
let login = true;

// تشفير SHA-256
async function hash(p) {
    const data = new TextEncoder().encode(p);
    const buf = await crypto.subtle.digest("SHA-256", data);
    return [...new Uint8Array(buf)].map(b=>b.toString(16).padStart(2,"0")).join("");
}

// تبديل بين تسجيل دخول وإنشاء حساب
function toggle() {
    login = !login;
    document.getElementById("title").innerText = login ? "Login" : "Register";
    document.querySelector("button").innerText = login ? "Login" : "Register";
}

// التعامل مع الضغط على الزر
async function handle() {
    const u = username.value.trim();
    const p = password.value.trim();
    if (!u || !p) return msg.innerText = "Fill all fields";

    let users = JSON.parse(localStorage.getItem("users")) || {};
    const hp = await hash(p);

    if (login) {
        if (users[u] === hp) {
            localStorage.setItem("currentUser", u);
            window.location.href = "welcome.html"; // تحويل للصفحة الجديدة
        } else msg.innerText = "Wrong login";
    } else {
        if (users[u]) msg.innerText = "Username exists";
        else {
            users[u] = hp;
            localStorage.setItem("users", JSON.stringify(users));
            msg.innerText = "Account created successfully 🎉";
        }
    }
}
</script>

</body>
</html>
