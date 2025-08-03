# HACKATHON
from flask import Flask, render_template, request, redirect
import sqlite3

app = Flask(__name__)

# Initialize database
def init_db():
    conn = sqlite3.connect('database.db')
    conn.execute('''CREATE TABLE IF NOT EXISTS participants (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        name TEXT, email TEXT, team TEXT)''')
    conn.execute('''CREATE TABLE IF NOT EXISTS submissions (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        project_name TEXT, description TEXT, link TEXT, team TEXT)''')
    conn.close()

init_db()

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        name = request.form['name']
        email = request.form['email']
        team = request.form['team']
        conn = sqlite3.connect('database.db')
        conn.execute("INSERT INTO participants (name, email, team) VALUES (?, ?, ?)", (name, email, team))
        conn.commit()
        conn.close()
        return render_template('success.html', message="Registration successful!")
    return render_template('register.html')

@app.route('/submit', methods=['GET', 'POST'])
def submit():
    if request.method == 'POST':
        project = request.form['project']
        desc = request.form['description']
        link = request.form['link']
        team = request.form['team']
        conn = sqlite3.connect('database.db')
        conn.execute("INSERT INTO submissions (project_name, description, link, team) VALUES (?, ?, ?, ?)", (project, desc, link, team))
        conn.commit()
        conn.close()
        return render_template('success.html', message="Submission received!")
    return render_template('submit.html')

if __name__ == '__main__':
    app.run(debug=True)
