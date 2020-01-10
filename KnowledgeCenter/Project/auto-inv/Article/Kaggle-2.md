
From LAB to Production – จาก Machine Learning Model สู่ Flask RESTful
===

จาก Kaggle – วิธีการใช้ Logistic Regression บนข้อมูล Iris เราได้ Model มาแล้ว แต่ จะนำสู่ Production ได้อย่างไร ?

ใน Python มี Object Serialization ทำให้สามารถเก็บ Object ที่สร้างขึ้น ไปไว้ในไฟล์ ซึ่ง มีให้ใช้หลายตัว ได้แก่

- pickle
- cpickle
- joblib

มีคนทำการทดสอบความเร็ว พบว่า cpickle เร็วสุด (https://stackoverflow.com/questions/12615525/what-are-the-different-use-cases-of-joblib-versus-pickle) แต่ในที่นี้ จะใช้ joblib เพราะน่าจะเหมาะกับงานที่ต้องมีการ Load Data ขนาดใหญ่ ใช้งานร่วมกันหลาย Process (เท่าที่เข้าใจครับ)

การสร้างไฟล์ .pkl บน kaggle ดังนี้

เพิ่มคำสั่งต่อไปนี้ แล้ว กดปุ่ม commit and run ด้านบนขวา
```py
from sklearn.externals import joblib
joblib.dump(model, 'myiris.pkl')
```
กดปุ่ม รูป << ด้าน ซ้ายบน เพื่อกลับไป หน้า Kernel ของเรา คลิกที่ Output จะเห็นไฟล์ ที่เพิ่งสร้าง ให้คลิก Download ไปเก็บไว้ใน Folder ที่จะใช้งาน Productioin

ต่อไป จะเป็นขั้นตอนการติดตั้ง และการใช้ Flask ซึ่งเป็น Python Microframework  และ ใช้ Flask RESTful เพื่อสร้าง REST API

ใช้คำสั่งต่อไปนี้ ติดตั้ง flask และ flask-resetful
```py
pip install flask flask-restful
```
จากนั้น เข้าไปใน folder ที่เราวางไฟล์ myiris.pkl ไว้ แล้ว สร้างไฟล์ iris.py มี Code ดังนี้
```py
from flask import Flask, request
from flask_restful import Resource, Api, reqparse
from sklearn.externals import joblib
import pandas as pd
#from sklearn.linear_model import LogisticRegression

app = Flask(__name__)
api = Api(app)
```
```py
# Model
model = joblib.load('myiris.pkl')
class Iris(Resource):
    def get(self):        
        return { "greeting":"Hello From IRIS Dataset"}
    def post(self):
        parser = reqparse.RequestParser()
        parser.add_argument('sl')
        parser.add_argument('sw')
        parser.add_argument('pl')
        parser.add_argument('pw')
        args = parser.parse_args()        
        x = pd.DataFrame([[ args['sl'],args['sw'], args['pl'],args['pw'] ]] ,\
            columns=['sepal_length', 'sepal_width', 'petal_length', 'petal_width'])
        result = model.predict(x)
        return {"result": result[0]}, 201
api.add_resource(Iris, "/iris")
app.run(debug=True)
```
จากนั้น ไปที่ Command Prompt พิมพ์คำสั่งต่อไปนี้ เพื่อเรียก Flask ขึ้นมาทำงาน โดยรับ Request ที่ Port 5000
```command
python iris.py
```
ได้ผลดังนี้


หากใช้ Web Browser ติดต่อไปยัง http://localhost:5000/iris จะได้ผลดังนี้

แต่ถ้าใช้ Postman ติดต่อไปยัง http://localhost:5000/iris แล้วส่งตัวแปร ความกว้าง ความยาว ของกลีบดอก ผ่าน POST ไป จะได้ผลการ Classification มาว่าเป็น Species อะไร ดังนี้

จากตัวอย่างนี้ แสดงให้เห็นว่า เราสามารถสร้าง Model จากข้อมูลขนาดใหญ่ แล้วนำออกมาเป็น Pickle แล้วใช้ Flask RESTFul เพื่อรับ Request แล้วตอบกลับเป็น ผลการ Classification ได้ หรือ Prediction ต่าง ๆ ได้
เดี๋ยวค่อยมาลงรายละเอียดเรื่อง วิธีการใช้งาน Flask และ การใช้ Machine Learning แบบต่าง ๆ กันKaggle – วิธีการใช้ Logistic Regression บนข้อมูล Iris
===




> Written with [StackEdit](https://sysadmin.psu.ac.th/2018/07/23/from-lab-to-production-with-flask-restful19/kaggle-logistic-regression-iris-dataset/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTYwODc4ODE5LDE0NzYwNjM5MThdfQ==
-->