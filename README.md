import os, sqlite3, zipfile

def create_file(path, content=""):
    os.makedirs(os.path.dirname(path), exist_ok=True)
    with open(path, "w", encoding="utf-8") as f:
        f.write(content)

print("Creating project files...")

# Desktop app (Tkinter)
desktop_code = """<PASTE YOUR ORIGINAL TKINTER CODE HERE FROM jewelry_job_tracker 1.py>"""
create_file("jewelry_job_tracker/desktop_app.py", desktop_code)

# Flask backend (app.py)
flask_code = """<PUT FLASK APP CODE HERE>"""
create_file("jewelry_job_tracker/app.py", flask_code)

# Templates with auto-loss calculation in job_form.html
job_form_html = """<!DOCTYPE html>
<html>
<head>
  <meta charset='utf-8'>
  <meta name='viewport' content='width=device-width, initial-scale=1'>
  <title>Job Form</title>
  <link rel='stylesheet' href='https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css'>
  <script>
    function calcLoss(section) {
      let issue = parseFloat(document.querySelector(`[name="${section}_Issue_Weight"]`)?.value) || 0;
      let receive = parseFloat(document.querySelector(`[name="${section}_Receive_Weight"]`)?.value) || 0;
      document.querySelector(`[name="${section}_Loss"]`).value = (issue - receive).toFixed(2);
    }
    function attachListeners(section) {
      document.querySelector(`[name="${section}_Issue_Weight"]`).addEventListener('input', ()=>calcLoss(section));
      document.querySelector(`[name="${section}_Receive_Weight"]`).addEventListener('input', ()=>calcLoss(section));
    }
    window.onload = () => {
      ['Ghat','Pinji','Setting'].forEach(s => attachListeners(s));
    }
  </script>
</head>
<body>
<div class='container mt-4'>
  <h3>{{ job_number or 'New Job' }}</h3>
  <form method='POST'>
    <div class='mb-3'>
      <label class='form-label'>Job Number</label>
      <input type='text' name='job_number' class='form-control' value='{{ job_number or '' }}' required>
    </div>

    <h5>Ghat</h5>
    <div class='row'>
      <div class='col'><input class='form-control' name='Ghat_Issue_Weight' placeholder='Issue Weight'></div>
      <div class='col'><input class='form-control' name='Ghat_Receive_Weight' placeholder='Receive Weight'></div>
      <div class='col'><input class='form-control' name='Ghat_Loss' placeholder='Loss' readonly></div>
    </div>

    <h5 class='mt-3'>Setting</h5>
    <div class='row'>
      <div class='col'><input class='form-control' name='Setting_Issue_Weight' placeholder='Issue Weight'></div>
      <div class='col'><input class='form-control' name='Setting_Receive_Weight' placeholder='Receive Weight'></div>
      <div class='col'><input class='form-control' name='Setting_Loss' placeholder='Loss' readonly></div>
    </div>

    <h5 class='mt-3'>Pinji</h5>
    <div class='row'>
      <div class='col'><input class='form-control' name='Pinji_Issue_Weight' placeholder='Issue Weight'></div>
      <div class='col'><input class='form-control' name='Pinji_Receive_Weight' placeholder='Receive Weight'></div>
      <div class='col'><input class='form-control' name='Pinji_Loss' placeholder='Loss' readonly></div>
    </div>

    <button class='btn btn-success mt-3'>Save</button>
    <a href='/dashboard' class='btn btn-secondary mt-3'>Back</a>
  </form>
</div>
</body>
</html>"""
create_file("jewelry_job_tracker/templates/job_form.html", job_form_html)

# Other templates
login_html = """<!DOCTYPE html><html><head><meta charset='utf-8'><meta name='viewport' content='width=device-width, initial-scale=1'><title>Login</title><link rel='stylesheet' href='https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css'></head><body class='bg-light d-flex justify-content-center align-items-center' style='height:100vh;'><div class='card p-4 shadow' style='width:350px;'><h3 class='text-center mb-3'>Jewelry Tracker</h3><form method='POST'><div class='mb-3'><label class='form-label'>Username</label><input type='text' name='username' class='form-control' required></div><div class='mb-3'><label class='form-label'>Password</label><input type='password' name='password' class='form-control' required></div><button class='btn btn-primary w-100'>Login</button></form></div></body></html>"""
create_file("jewelry_job_tracker/templates/login.html", login_html)

dashboard_html = """<!DOCTYPE html><html><head><meta charset='utf-8'><meta name='viewport' content='width=device-width, initial-scale=1'><title>Dashboard</title><link rel='stylesheet' href='https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css'></head><body><nav class='navbar navbar-dark bg-dark px-3'><span class='navbar-brand'>Jewelry Tracker</span><a href='/logout' class='btn btn-outline-light btn-sm'>Logout</a></nav><div class='container mt-4'><div class='d-flex justify-content-between mb-3'><h3>Jobs</h3><a href='/job/new' class='btn btn-success'>Add Job</a></div><table class='table table-bordered table-striped'><thead><tr><th>Job Number</th><th>Actions</th></tr></thead><tbody>{% for job in jobs %}<tr><td>{{ job }}</td><td><a href='/job/{{ job }}' class='btn btn-primary btn-sm'>Edit</a><a href='/job/delete/{{ job }}' class='btn btn-danger btn-sm'>Delete</a><a href='/export/{{ job }}' class='btn btn-warning btn-sm'>Export</a></td></tr>{% endfor %}</tbody></table></div></body></html>"""
create_file("jewelry_job_tracker/templates/dashboard.html", dashboard_html)

# Static files
create_file("jewelry_job_tracker/static/style.css", "body {font-family: Arial;}")
create_file("jewelry_job_tracker/static/theme.js", "console.log('Theme Toggle');")

# README
readme_content = """# Jewelry Job Tracker\n\n## How to Run Desktop App\n```bash\npython desktop_app.py\n```\n\n## How to Run Web App\n```bash\npip install flask reportlab\npython app.py\n```\nThen open http://127.0.0.1:5000\nLogin: admin/admin"""
create_file("jewelry_job_tracker/README.md", readme_content)

# Create empty SQLite DB
db_path = "jewelry_job_tracker/jewelry_jobs.db"
os.makedirs(os.path.dirname(db_path), exist_ok=True)
conn = sqlite3.connect(db_path)
c = conn.cursor()
c.execute("CREATE TABLE IF NOT EXISTS jobs (id INTEGER PRIMARY KEY AUTOINCREMENT, job_number TEXT, section TEXT, field TEXT, value TEXT)")
c.execute("CREATE TABLE IF NOT EXISTS users (username TEXT PRIMARY KEY, password TEXT)")
c.execute("INSERT OR IGNORE INTO users VALUES ('admin','admin')")
conn.commit()
conn.close()

# Zip project
zip_path = "jewelry_job_tracker.zip"
with zipfile.ZipFile(zip_path, 'w', zipfile.ZIP_DEFLATED) as zipf:
    for root, _, files in os.walk("jewelry_job_tracker"):
        for file in files:
            full_path = os.path.join(root, file)
            zipf.write(full_path, os.path.relpath(full_path, "jewelry_job_tracker"))

print(f"Project created and zipped as {zip_path}")
