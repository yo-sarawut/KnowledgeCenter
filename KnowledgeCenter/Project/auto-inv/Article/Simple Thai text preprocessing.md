
Simple Thai text preprocessing
===

ขั้นตอนการเตรียมข้อมูลประเภท Text ภาษาไทย แบบง่ายๆ โดยใช้ Python (Simple Thai text preprocessing using Python)

![enter image description here](https://miro.medium.com/max/5115/1*Lb-J1bTOpR7NeyaxUrUkFA.jpeg)

หลายๆคนอาจจะสงสัยว่าการทำโมเดลจากข้อมูลประเภท text เริ่มยังไง เพราะข้อมูลประเภทนี้ไม่ได้มาเป็นตัวเลข (numerical) ให้เราพร้อมใช้งาน จึงต้องมีขั้นตอนเพิ่มเติมในการแปลงข้อมูลประเภทนี้ (preprocessing) ให้กลายเป็นตัวเลขก่อน ถึงจะนำไปเข้าโมเดลได้ ซึ่งวันนี้ทางเพจ  **Datawiz**  จะมาอธิบายขั้นตอนการเตรียมข้อมูลประเภทนี้อย่างง่าย ก่อนนำไปเข้าโมเดลครับ

สำหรับ code ผมแปะ link ไว้ให้ที่ท้ายบทความนี้ครับ

# **ขั้นตอนการเตรียมข้อมูล (preprocessing)**

![](https://miro.medium.com/max/60/1*s3sP13AP-1EscHAp3sEdVQ.png?q=20)

![](https://miro.medium.com/max/2498/1*s3sP13AP-1EscHAp3sEdVQ.png)

ภาพ 1 — ขั้นตอนการเตรียมข้อมูลประเภท text แบบง่ายๆ

ขั้นตอนในการเตรียมข้อมูลประเภท text โดยย่อ จะเริ่มต้นจาก

1.  ทำ text cleaning เพราะโดยส่วนใหญ่ข้อมูล text ที่เราดึงมาจาก social จะเต็มไปด้วยสิ่งที่เราต้องตัดออกพอสมควร เช่น html tag ต่างๆ, url, หรือเครื่องหมายคำพูด (punctuation)
2.  ตัดคำแต่ละคำในข้อความ (tokenize)
3.  นำเอาคำศัพท์มาสร้างเป็น คลังคำศัพท์ (bag of words)โดยแต่ละคำก็จะมี ID สำหรับแทนตัวมันครับ
4.  สร้าง feature สำหรับแต่ละคำศัพท์ ด้วยวิธีการนับคำ หรือ tfidf

ฟังแล้วดูไม่​ยากใช่ไหมครับ… งั้นมาเริ่มลงรายละเอียดกันเลยครับ😎

**ขั้นตอนที่ 1 — ทำความสะอาดข้อความ (Text cleaning)**

บางครั้ง text ที่เราได้มามันจะมีความ messy เบาๆ เช่น มี html tag (กรณี scarpe มาจาก web) หรือ มีเครื่องหมายต่างๆ ที่เราไม่ต้องการ (!€@%#*&~) ติดมาด้วย จึงจำเป็นต้อง clean text ก่อน โดยใช้  _regular expression_

หน้าตาของ text ก่อน clean จะเป็นประมาณที่แสดงในภาพ 2 ครับ


![](https://miro.medium.com/max/2252/1*Xpq4W7y4IU39EaJ4bE1oaA.png)

ภาพ 2 — ตัวอย่างข้อความจากโพสต์ในเฟสบุ๊ค

หน้าตา text หลังผ่านการ clean


![](https://miro.medium.com/max/2284/1*gycIRRRJcEWFQG-bNFcCcQ.png)

ภาพ 3— ตัวอย่างข้อความหลังจากผ่านการ clean

**ขั้นตอนที่ 2— ตัดคำ (Tokenize)**

ขั้นตอนถัดมา คือ การตัดคำ (Tokenize) โดย library สำหรับตัดคำภาษาไทยปัจจุบัน ผมใช้  _thainlp_  ซึ่งจะมีตัว  _module word_tokenize_  ให้ใช้ โดยหลักๆ ผมจะใช้ engine ชื่อ “_newmm_” ครับ หรือถ้าอยากจะตัดคำเนียนๆ ขึ้น ก็แนะนำ  _deepcut_  แต่อาจจะช้าหน่อยเพราะ library นี้ใช้ neural network  เป็น backend อยู่เบื้องหลังครับ

โดยในขั้นตอนนี้ยังมีเพิ่มเติมเรื่องการตัด  **stop words** หรือ คำที่เจอบ่อยๆ แต่ไม่สื่อความหมายอะไรมาก เช่น “การ”, “ความ”, “คือ”, “ที่”, “ซึ่ง” เป็นต้น การตัด  _stop words_ จะมีข้อดี คือ ทำให้คลังคำศัพท์  _(bag of words)_ไม่ใหญ่เกินโดยไม่จำเป็น โดยเฉพาะถ้าเรามี dataset ใหญ่ๆ แต่การตัดคำออกไปบางส่วนอาจจะทำให้ความแม่นยำของ model ลดลงไม่มากก็น้อยครับ

เทคนิคอีกอย่าง คือ การแปลงคำศัพท์ให้เป็นรากศัพท์ของคำนั้นๆ เช่น  _[ “ปักษา”, “วิหค”, “นก” ] — > “นก”_  เพื่อลดจำนวนคำศัพท์ให้น้อยลง… แต่เทคนิคนี้ก็มีข้อเสีย คือ บางคำมีหลายความหมาย ต้องดูบริบทประกอบถึงจะหารากศัพท์ได้ถูก เช่น คำว่า “ตา” อาจจะหมายถึง อวัยวะ หรือ คุณตาก็ได้

![](https://miro.medium.com/max/60/1*1xmyfv3qmRg95ZwSQbokMg.png?q=20)

![](https://miro.medium.com/max/2280/1*1xmyfv3qmRg95ZwSQbokMg.png)

ภาพ 4 — ตัวอย่าง token หลังผ่านการ ตัดคำ

**ขั้นตอนที่ 3— สร้างคลังคำศัพท์ (Bag of words)**

ขั้นตอนนี้ง่ายมาก คือ การแปลงคำแต่ละคำ (ที่ไม่ซ้ำกัน) เป็น ID ตามภาพด้านล่างทั้งหมด 74 unique ID  ซึ่งขั้นตอนนี้ ใน  _library sklearn_  จะรวมไว้กับ ขั้นตอนที่ 4a หรือ 4b เลย แต่สำหรับ  _library gensim_ จะต้องทำขั้นตอนนี้ต่างหาก



![](https://miro.medium.com/max/778/1*IibX606rpNVq9bFP89oabQ.png)

ภาพ 5 — คลังคำศัพท์ที่สร้างจาก token ของทั้ง 2 ข้อความ

**ขั้นตอนที่ 4a— สร้าง feature จากการนับคำ (Count words)**

ขั้นตอนสุดท้ายในการสร้าง feature จากการนับคำ คือ การนับจำนวนครั้งที่แต่ละ word id จาก  word id ทั้งหมด ปรากฏในแต่ละ text (ไม่มี คือ 0) ซึ่งยิ่งข้อมูลใน dataset เรายิ่งเยอะ จำนวน feature ก็จะยิ่งเยอะตามจำนวน unique word ID และทำให้ matrix ของ feature เต็มไปด้วย 0 ซะส่วนใหญ่ (เรียกว่าเป็น  _sparse matrix_ ครับ)


![](https://miro.medium.com/max/1474/1*rn1bNa1ReQgQlRLYrFgvig.png)

ภาพ 6 — ตัวอย่าง feature ที่สร้างจากการนับคำ (แสดงแค่ 20 word id แรก)

**ขั้นตอนที่ 4b — สร้าง feature ด้วย TFIDF (TFIDF)**

ขั้นตอนสุดท้ายในการสร้าง feature ด้วย tfidf จะคล้ายกับแบบ word count  แต่แทนที่จะนับคำ ก็คำนวณค่า tfidf ของแต่ละ word id แทน



![](https://miro.medium.com/max/1276/1*3KY4mgX3joFgw3hGmoAN9g.png)

ภาพ 7— ตัวอย่าง feature ที่สร้างจาก tfidf (แสดงแค่ 20 word id แรก)

**TFIDF คือ อะไร?** 🤯🤯🤯

_TFIDF_  ย่อมาจาก term frequency-inverse document frequency ซึ่งหมายถึงการเอา  _term frequency_  หรือ จำนวนครั้งที่แต่ละ word id ปรากฏ ในแต่ละ text หารด้วยจำนวน word ทั้งหมดใน text นั้น (เลยเป็น frequency แทนที่จะเป็น count) แล้วจึงนำมาคูณกับ  _inverse document frequency_  หรือ จำนวน document  ทั้งหมด หารด้วย จำนวน  _document_  (หรือ text ที่ผมหมายถึงในบทความนี้) ที่แต่ละ word id  ปรากฏอยู่ แล้ว take log เข้าไป

สำหรับ  _term frequency_  ยิ่ง word id  ไหนอยู่ใน text มากๆ ก็จะมีค่ามากตาม แต่ถ้า word id นั้น ไปปรากฏอยู่ในเกือบทุกๆ text ก็จะทำให้ค่า  _inverse document frequency_  ต่ำถ่วงดุลกันไป

โดยเฉพาะ  _stop words_  หรือคำที่เจอบ่อยๆ แต่ไม่มีนัยสำคัญ​ คำประเภทนี้จะมี  _term frequency_  สูง แต่เนื่องมันไปโผล่อยู่ในทุก text ก็จะทำให้ค่า  _tfidf_  โดยรวมของมันต่ำกว่าคำที่ปรากฏเฉพาะ text ใด text หนึ่ง และมี  _term frequency_  ใกล้ๆกัน

ดังนั้น  _tfidf_  มันจะช่วย highlight คำเด่นๆ ออกมานั่นเองครับ

**สรุป**

blog นี้สอนขั้นตอนการ preprocess ข้อมูลประเภท text อย่างง่ายโดยใช้วิธีการ count word และ tfidf โดย  _ทั้งสองวิธีนี้ จะไม่สนใจลำดับของคำที่เรียงกันในแต่ละ text_  ดังนั้นการใช้งานในการนำไปสร้าง model ต่อ อาจจะจำกัดอยู่กับ dataset ที่ไม่ใหญ่มาก แต่ก็ถือว่าเป็นท่ามาตรฐานในการทำ NLP ควรเรียนรู้ไว้ครับ

หากอยากไปต่อยอดการสร้าง feature ที่ซับซ้อนขึ้นมา หรือมี dataset ใหญ่ๆ ก็แนะนำให้ไปดูเรื่อง word embedding หรือ word vector อะไรพวกนี้ ซึ่งถ้ามีโอกาสจะมา share ให้ได้อ่านกันครับ  [**Source code in colab:**](https://colab.research.google.com/drive/1uaOsotWWgHGGceXkoE75xzSdlYsMZ4RK)


> [Source : ](https://medium.com/@witchapongdaroontham/%E0%B8%82%E0%B8%B1%E0%B9%89%E0%B8%99%E0%B8%95%E0%B8%AD%E0%B8%99%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B9%80%E0%B8%95%E0%B8%A3%E0%B8%B5%E0%B8%A2%E0%B8%A1%E0%B8%82%E0%B9%89%E0%B8%AD%E0%B8%A1%E0%B8%B9%E0%B8%A5%E0%B8%9B%E0%B8%A3%E0%B8%B0%E0%B9%80%E0%B8%A0%E0%B8%97-text-%E0%B8%A0%E0%B8%B2%E0%B8%A9%E0%B8%B2%E0%B9%84%E0%B8%97%E0%B8%A2-%E0%B9%81%E0%B8%9A%E0%B8%9A%E0%B8%87%E0%B9%88%E0%B8%B2%E0%B8%A2%E0%B9%86-%E0%B9%82%E0%B8%94%E0%B8%A2%E0%B9%83%E0%B8%8A%E0%B9%89-python-simple-thai-text-preprocessing-c8c46ca3ce46).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MzQ2MDUyMjldfQ==
-->