# weatherapp
from flask import Flask, request, render_template, url_for
import requests
from flask_bootstrap import Bootstrap
from flask_sqlalchemy import SQLAlchemy
app = Flask(__name__)
Bootstrap(app)
app.config["SQLALCHEMY_DATABASE_URI"]="sqlite:///weatherx.db"
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"]=False
db = SQLAlchemy(app)
class City(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable= False)
    def __init__(self,name):
        self.name = name

@app.route("/", methods = ["POST", "GET"])
def index():
    url = "http://api.openweathermap.org/data/2.5/weather?q={}&units=imperial&APPID=your openweatherapp.org api key"
    cities = City.query.all()
    weather_data = []
    if request.method == "POST":
        #city = "lagos"
        name = request.form["city"]
        data = City(name)
        db.session.add(data)
        db.session.commit()
        for city in cities:
            r = requests.get(url.format(city.name)).json()
            #print(r)
            weather = {
                'city':city.name,
                'temperature': r['main']['temp'],
                'description': r['weather'][0]['description'],
                'icon': r['weather'][0]['icon']
            }
            weather_data.append(weather)
            print(weather_data)
        return render_template("weather.html", weather_data = weather_data)
    return render_template("weather.html")
@app.route("/weather2")
def wet():
    return render_template("weather2.html")
if __name__ == "__main__":
    app.run(debug =  True)
