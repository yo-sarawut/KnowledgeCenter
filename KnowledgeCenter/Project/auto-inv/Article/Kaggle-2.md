
Kaggle – วิธีการใช้ Logistic Regression บนข้อมูล Iris
===

ข้อมูล Iris Dataset มักจะใช้ในการเริ่มต้นศึกษาการใช้งาน เครื่องมือทาง Data Science โดยเฉพาะ Classification เพราะไม่ซับซ้อน มี 4 ฟิลด์ ที่ใช้เป็น Features และมี 1 ฟิลด์ ที่จะเป็น Class (มี 3 Categories)

1.  เริ่มจาก New Kernel  

![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-14_27_44-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-14_27_44-Window.png)
2.  ในที่นี้ เลือก Notebook  
    ![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-15_19_24-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-15_19_24-Window.png)
4.  จากนั้น เลือก Add Dataset จากที่เค้ามีให้ หรือ จะ Upload ขึ้นไปก็ได้  
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-15_50_54-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-15_50_54-Window.png)
5.  จากนั้น ข้อมูลของเราจะมาอยู่ที่ ../input/ ในกรณีเรามีไฟล์ ../input/iris.data  
    จาก Code ที่ให้มาในเบื้องต้น ให้กดปุ่ม Shift+Enter หรือ กดเครื่องหมาย Run ด้าน ซ้ายมือ ก็จะได้ผลดังนี้  
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-15_56_30-.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-15_56_30-.png)
6.  จากนั้น มาเขียน Code กัน เริ่มจาก Import Package ที่ต้องใช้
    
    import pandas as pd
    import numpy as np
    import seaborn as sns
    import matplotlib.pyplot as plt
    %matplotlib inline
    
7.  สร้างตัวแปร iris อ่านข้อมูลจากไฟล์
    
    iris = pd.read_csv('../input/iris.data')
    
8.  สำรวจข้อมูลเบื้องต้น  
    iris.head()  
    iris.info()  
    iris.describe()  
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-16_24_43-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-16_24_43-Window.png)
9.  ลองทำ Data Visualization เบื้องต้น ด้วย pairplot แยกตามสีของ species
    
    sns.pairplot(iris, hue='species')
    
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-16_31_19-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-16_31_19-Window.png)
    
    หรือ จะดูเป็น scatterplot
    
    plt.scatter(iris['sepal_length'], iris['sepal_width'], marker='.', color='r')
    plt.xlabel('Sepal Length')
    plt.ylabel('Sepal Width')
    
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-16_38_02-Clipboard.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-18-16_38_02-Clipboard.png)
    
10.  ต่อไป เป็นขั้นตอนการแบ่งข้อมูลออกเป็น 2 ส่วน สำหรับ Train และ Test
    
    ```
    from sklearn.model_selection import train_test_split
    ```
    
11.  จากนั้น Train Model
    
    from sklearn.linear_model import LogisticRegression
    model = LogisticRegression()
    model.fit(X_train, y_train)
    
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-19-09_59_55-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-19-09_59_55-Window.png)
    
12.  แล้วก็ ตรวจสอบความแม่นยำ Model Evaluation
    
    prediction = model.predict(X_test)
    from sklearn.metrics import confusion_matrix, classification_report, accuracy_score
    
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-19-10_03_38-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-19-10_03_38-Window.png)  
    [![](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-19-10_03_51-Window.png)](https://sysadmin.psu.ac.th/wp-content/uploads/2018/07/2561-07-19-10_03_51-Window.png)
    

ขั้นตอนไม่ยากครับ ส่วนว่าเราจะเลือกใช้ Model ไหน ทำอะไร อันนี้ต้องมาดูรายละเอียดกันต่อครับ

> [Source :](https://sysadmin.psu.ac.th/2018/07/19/kaggle-logistic-regression-iris-dataset/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDEwNDY5NTc2LDE0NzYwNjM5MThdfQ==
-->