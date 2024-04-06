# app.py
from flask import Flask, render_template, url_for, redirect, request, flash
from werkzeug.security import generate_password_hash, check_password_hash
from flask_login import LoginManager, UserMixin, login_user, logout_user, login_required, current_user

app = Flask(__name__)
app.secret_key = "super_secret_key"

users = {}

class User(UserMixin):
    pass

@app.route("/signup", methods=["GET", "POST"])
def signup():
    if request.method == "POST":
        username = request.form.get("username")
        password = request.form.get("password")

        if username in users:
            flash("Username already exists!")
            return redirect(url_for("signup"))

        hashed_password = generate_password_hash(password)
        users[username] = hashed_password

        return redirect(url_for("login"))

    return render_template("signup.html")

@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        username = request.form.get("username")
        password = request.form.get("password")

        if username not in users:
            flash("Invalid username or password!")
            return redirect(url_for("login"))

        if not check_password_hash(users[username], password):
            flash("Invalid username or password!")
            return redirect(url_for("login"))

        user = User()
        user.id = username
        login_user(user)

        return redirect(url_for("dashboard"))

    return render_template("login.html")

@app.route("/logout")
@login_required
def logout():
    logout_user()
    return redirect(url_for("login"))

@app.route("/dashboard")
@login_required
def dashboard():
    passwords = {}

    for key, value in users.items():
        if key == current_user.id:
            passwords = value

    return render_template("dashboard.html", passwords=passwords)

@app.route("/add_password", methods=["POST"])
@login_required
def add_password():
    service = request.form.get("service")
    password = request.form.get("password")

    users[current_user.id][service] = password

    return redirect(url_for("dashboard"))

@app.route("/delete_password/<string:service>")
@login_required
def delete_password(service):
    del users[current_user.id][service]

    return redirect(url_for("dashboard"))

login_manager = LoginManager()
login_manager.init_app(app)

@login_manager.user_loader
def user_loader(username):
    if username not in users:
        return

    user = User()
    user.id = username

    return user
<!-- templates/signup.html -->
<html>
  <head>
    <title>Sign Up</title>
  </head>
  <body>
    <form method="POST">
      <label for="username">Username:</label>
      <input type="text" name="username" required><br>
      <label for="password">Password:</label>
      <input type="password" name="password" required><br>
      <button type="submit">Sign Up</button>
    </form>
  </body>
</html>
<!-- templates/login.html -->
<html>
  <head>
    <title>Login</title>
  </head>
  <body>
    <form method="POST">
      <label for="username">Username:</label>
      <input type="text" name="username" required><br>
      <label for="password">Password:</label>
      <input type="password" name="password" required><br>
      <button type="submit">Login</button>
    </form>
  </body>
</html>
<!-- templates/dashboard.html -->
<html>
  <head>
    <title>Dashboard</title>
  </head>
  <body>
    <h1>Welcome, {{ current_user.id }}!</h1>
    <form method="POST" action="add_password">
      <label for="service">Service:</label>
      <input type="text" name="service" required><br>
      <label for="password">Password:</label>
      <input type="password" name="password" required><br>
      <button type="submit">Add Password</button>
    </form>
    <hr>
    {% for key, value in passwords.items() %}
      <p>{{ key }}: {{ value }}</p>
    {% endfor %}
    <hr>
    <form method="POST" action="logout">
      <button type="submit">Logout</button>
    </form>
    <form method="POST" action="delete_password/<string:service>">
      <label for="service">Service to delete:</label>
      <input type="text" name="service" required><br>
      <button type="submit">Delete Password</button>
    </form>
  </body>
</html>
