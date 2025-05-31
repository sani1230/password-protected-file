|app.py
|----
from flask import Flask, render_template, request, send_file, session, redirect, url_for
import time

app = Flask(__name__)
app.secret_key = 'supersecretkey'  # Needed for session

@app.route('/', methods=['GET', 'POST'])
def index():
    secret_message = None
    show_image = False
    show_video = False
    message = ""
    sms = ""

    
    
    # Initialize attempt counter if not present

    if 'attempts' not in session:
        session['attempts'] = 3
        
        
    if request.method == 'POST':
        if session['attempts'] <= 1:
            sms = "❌ Acess Denied"
            
            return render_template("password.html", sms=sms)
       

        password = request.form.get('password')

        if password == "imagepass":
            message = "✅ Access granted: Showing image."
            time.sleep(1)
            show_image = True
            session['attempts'] = 3  # Reset attempts
        elif password == "videopass":
            message = "✅ Access granted: Playing video."
            time.sleep(1)
            show_video = True
            session['attempts'] = 3
        elif password == "secretmsg":
            message = "✅ Access granted: Secret message revealed."
            time.sleep(1)
            secret_message = "You’re my favorite person ❤️"
            session['attempts'] = 3
        elif password == "filepass":
            session['attempts'] = 3
            return send_file("static/rang dey.mp3", as_attachment=True)
        else:
            session['attempts'] -= 1
            message = f"❌ Wrong password. {session['attempts']} attempts left."
            

    return render_template("password.html", message=message, sms=sms, show_image=show_image,
                           show_video=show_video, secret_message=secret_message)

if __name__ == '__main__':
    app.run(debug=True)
