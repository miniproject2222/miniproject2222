from flask import Flask, render_template, request, redirect, url_for
import sqlite3

app = Flask(__name__)

# SQLite database initialization
conn = sqlite3.connect('grievances.db')
c = conn.cursor()
c.execute('''
    CREATE TABLE IF NOT EXISTS grievances (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        title TEXT NOT NULL,
        description TEXT NOT NULL
    )
''')
conn.commit()
conn.close()


@app.route('/')
def index():
    # Display all grievances
    conn = sqlite3.connect('grievances.db')
    c = conn.cursor()
    c.execute('SELECT * FROM grievances')
    grievances = c.fetchall()
    conn.close()
    return render_template('index.html', grievances=grievances)


@app.route('/add_grievance', methods=['POST'])
def add_grievance():
    # Add a new grievance to the database
    title = request.form['title']
    description = request.form['description']

    conn = sqlite3.connect('grievances.db')
    c = conn.cursor()
    c.execute('INSERT INTO grievances (title, description) VALUES (?, ?)', (title, description))
    conn.commit()
    conn.close()

    return redirect(url_for('index'))


if __name__ == '__main__':
    app.run(debug=True)

