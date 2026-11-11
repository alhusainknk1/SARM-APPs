
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SARM V3.0</title>

<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial;background:#f1f5f9;color:#222}
header{background:#087443;color:white;padding:20px;text-align:center}
.container{max-width:650px;margin:20px auto;padding:15px}
.card{background:white;padding:20px;margin:15px 0;border-radius:12px;box-shadow:0 2px 8px #ddd}
h2{color:#087443}
input{width:100%;padding:13px;margin:8px 0;border:1px solid #ccc;border-radius:6px}
button{background:#087443;color:white;border:0;padding:13px 18px;border-radius:6px;margin:5px 0;cursor:pointer}
.secondary{background:#475569}
.danger{background:#b91c1c}
.hidden{display:none}
.menu{display:flex;flex-wrap:wrap;gap:8px}
.menu button{flex:1}
footer{text-align:center;padding:20px;color:#666}
a{color:#087443;cursor:pointer}
</style>
</head>

<body>

<header>
<h1>SARM</h1>
<p>School Attendance & Records Manager</p>
<p>Version 3.0</p>
</header>

<div class="container">

<!-- HOME -->
<div id="home">
<div class="card">
<h2>Welcome to SARM</h2>
<p>School Attendance and Records Management System.</p>
<button onclick="show('register')">Create New School Account</button>
<button class="secondary" onclick="show('adminLogin')">Admin Login</button>
<button class="secondary" onclick="show('staffLogin')">Staff Login</button>
</div>
</div>

<!-- REGISTRATION -->
<div id="register" class="hidden">
<div class="card">
<h2>School Registration</h2>
<form id="registerForm">
<input id="schoolName" placeholder="School Name" required>
<input id="schoolLga" placeholder="Local Government Area" required>
<input id="adminName" placeholder="Admin Full Name" required>
<input id="phone" type="tel" placeholder="Admin Phone Number" required>
<input id="newUsername" placeholder="Create Admin Username" required>
<input id="newPassword" type="password" placeholder="Create Admin Password" required>
<button type="submit">Register School</button>
</form>
<button class="secondary" onclick="show('home')">Back</button>
</div>
</div>

<!-- ADMIN LOGIN -->
<div id="adminLogin" class="hidden">
<div class="card">
<h2>Admin Login</h2>
<form id="adminLoginForm">
<input id="username" placeholder="Admin Username" required>
<input id="password" type="password" placeholder="Admin Password" required>
<button type="submit">Login as Admin</button>
</form>
<p><a onclick="show('forgotUsername')">Forgot Username?</a></p>
<p><a onclick="show('forgotPassword')">Forgot Password?</a></p>
<button class="secondary" onclick="show('home')">Back</button>
</div>
</div>

<!-- FORGOT USERNAME -->
<div id="forgotUsername" class="hidden">
<div class="card">
<h2>Recover Admin Username</h2>
<p>Enter your registered phone number.</p>
<input id="recoverPhone" type="tel" placeholder="Admin Phone Number">
<button onclick="recoverUsername()">Recover Username</button>
<div id="usernameResult"></div>
<button class="secondary" onclick="show('adminLogin')">Back to Login</button>
</div>
</div>

<!-- FORGOT PASSWORD -->
<div id="forgotPassword" class="hidden">
<div class="card">
<h2>Reset Admin Password</h2>
<input id="resetPhone" type="tel" placeholder="Registered Phone Number">
<input id="resetUsername" placeholder="Admin Username">
<input id="newResetPassword" type="password" placeholder="Create New Password">
<button onclick="resetPassword()">Reset Password</button>
<button class="secondary" onclick="show('adminLogin')">Back to Login</button>
</div>
</div>

<!-- STAFF LOGIN -->
<div id="staffLogin" class="hidden">
<div class="card">
<h2>Staff Login</h2>
<form id="staffLoginForm">
<input id="loginPin" type="password" placeholder="Enter Staff PIN" required>
<button type="submit">Login as Staff</button>
</form>
<button class="secondary" onclick="show('home')">Back</button>
</div>
</div>

<!-- ADMIN DASHBOARD -->
<div id="adminDashboard" class="hidden">

<div class="card">
<h2>Admin Dashboard</h2>
<h3 id="schoolDisplay"></h3>
<p id="adminDisplay"></p>
</div>

<div class="card">
<h2>School Management</h2>
<div class="menu">
<button onclick="section('staffManagement')">Manage Staff</button>
<button onclick="section('attendance')">Attendance</button>
<button onclick="section('studentRecords')">Student Records</button>
</div>
</div>

<div id="staffManagement" class="card hidden">
<h2>Add New Staff</h2>
<form id="staffForm">
<input id="staffName" placeholder="Staff Full Name" required>
<input id="staffPin" type="password" placeholder="Create Staff PIN" required>
<button type="submit">Add Staff</button>
</form>
<h3>Registered Staff</h3>
<ul id="staffList"></ul>
</div>

<div id="attendance" class="card hidden">
<h2>Attendance</h2>
<p>Attendance module will be developed next.</p>
</div>

<div id="studentRecords" class="card hidden">
<h2>Student Records</h2>
<p>Student registration module will be developed next.</p>
</div>

<div class="card">
<button class="danger" onclick="logout()">Logout</button>
</div>
</div>

<!-- STAFF DASHBOARD -->
<div id="staffDashboard" class="hidden">
<div class="card">
<h2>Staff Dashboard</h2>
<h3 id="staffWelcome"></h3>
<p id="staffSchool"></p>
<p>You are logged in as school staff.</p>
<button class="danger" onclick="logout()">Logout</button>
</div>
</div>

</div>

<footer>
SARM - School Attendance & Records Manager
</footer>

<script>

let currentSchool = null;
let currentStaff = null;

function getSchools(){
return JSON.parse(localStorage.getItem("sarm_schools") || "[]");
}

function saveSchools(data){
localStorage.setItem("sarm_schools",JSON.stringify(data));
}

function show(id){
["home","register","adminLogin","forgotUsername",
"forgotPassword","staffLogin","adminDashboard",
"staffDashboard"].forEach(x=>{
document.getElementById(x).classList.add("hidden");
});
document.getElementById(id).classList.remove("hidden");
}

function section(id){
["staffManagement","attendance","studentRecords"].forEach(x=>{
document.getElementById(x).classList.add("hidden");
});
document.getElementById(id).classList.remove("hidden");
}

// SCHOOL REGISTRATION
document.getElementById("registerForm").onsubmit=function(e){
e.preventDefault();

let schools=getSchools();

let username=newUsername.value.trim();

if(schools.some(s=>s.username.toLowerCase()===username.toLowerCase())){
alert("Username already exists.");
return;
}

let school={
id:Date.now().toString(),
name:schoolName.value.trim(),
lga:schoolLga.value.trim(),
admin:adminName.value.trim(),
phone:phone.value.trim(),
username:username,
password:newPassword.value,
staff:[]
};

schools.push(school);
saveSchools(schools);

alert("School registered successfully!");
this.reset();
show("adminLogin");
};

// ADMIN LOGIN
document.getElementById("adminLoginForm").onsubmit=function(e){
e.preventDefault();

let school=getSchools().find(s=>
s.username.toLowerCase()===username.value.trim().toLowerCase()
&& s.password===password.value
);

if(!school){
alert("Incorrect username or password.");
return;
}

currentSchool=school;
openAdmin();
};

function openAdmin(){
show("adminDashboard");
schoolDisplay.textContent=currentSchool.name;
adminDisplay.textContent="Admin: "+currentSchool.admin;
showStaff();
section("staffManagement");
}

// FORGOT USERNAME
function recoverUsername(){
let p=recoverPhone.value.trim();

let school=getSchools().find(s=>s.phone===p);

if(!school){
usernameResult.textContent="No account found with this phone number.";
return;
}

usernameResult.textContent="Your Admin Username: "+school.username;
}

// FORGOT PASSWORD
function resetPassword(){
let p=resetPhone.value.trim();
let u=resetUsername.value.trim();
let newPass=newResetPassword.value;

if(!newPass){
alert("Please enter a new password.");
return;
}

let schools=getSchools();

let school=schools.find(s=>
s.phone===p &&
s.username.toLowerCase()===u.toLowerCase()
);

if(!school){
alert("Account details do not match.");
return;
}

school.password=newPass;
saveSchools(schools);

alert("Password changed successfully. Please login.");
document.getElementById("resetPhone").value="";
document.getElementById("resetUsername").value="";
document.getElementById("newResetPassword").value="";
show("adminLogin");
}

// ADD STAFF
document.getElementById("staffForm").onsubmit=function(e){
e.preventDefault();

let name=staffName.value.trim();
let pin=staffPin.value.trim();

if(pin.length<4){
alert("Staff PIN must contain at least 4 characters.");
return;
}

if(currentSchool.staff.some(s=>s.pin===pin)){
alert("This PIN is already used in your school.");
return;
}

currentSchool.staff.push({name:name,pin:pin});
updateSchool();

this.reset();
showStaff();

alert("Staff added successfully!");
};

function updateSchool(){
let schools=getSchools();
let i=schools.findIndex(s=>s.id===currentSchool.id);
if(i!==-1){
schools[i]=currentSchool;
saveSchools(schools);
}
}

function showStaff(){
let list=document.getElementById("staffList");
list.innerHTML="";

currentSchool.staff.forEach((s,index)=>{
let li=document.createElement("li");
li.textContent=s.name+" - PIN: hidden ";
list.appendChild(li);
});
}

// STAFF LOGIN USING PIN ONLY
document.getElementById("staffLoginForm").onsubmit=function(e){
e.preventDefault();

let pin=loginPin.value.trim();
let schools=getSchools();

let matches=[];

schools.forEach(s=>{
s.staff.forEach(st=>{
if(st.pin===pin){
matches.push({school:s,staff:st});
}
});
});

if(matches.length===0){
alert("Incorrect Staff PIN.");
return;
}

if(matches.length>1){
alert("PIN matches more than one school. Contact your Admin to assign a unique PIN.");
return;
}

currentSchool=matches[0].school;
currentStaff=matches[0].staff;

staffWelcome.textContent="Welcome, "+currentStaff.name;
staffSchool.textContent="School: "+currentSchool.name;

show("staffDashboard");
this.reset();
};

function logout(){
currentSchool=null;
currentStaff=null;
show("home");
}

show("home");

</script>
</body>
</html>
