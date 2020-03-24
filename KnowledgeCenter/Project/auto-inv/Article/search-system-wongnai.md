
# พัฒนาระบบ Search ของ Wongnai ด้วยตัวตัดคำจาก Machine learning (AI)

![enter image description here](https://miro.medium.com/max/800/1*CzgUHKFhV6G5x7h2IxtlgQ.jpeg)

สวัสดีครับ ผมหนุ่ม เป็น Data Scientist ที่ Wongnai วันนี้อยากจะแชร์ประสบการณ์การทำงานที่นี่ โดยขอยกโปรเจคหลักที่ทำอยู่ตอนนี้คือ การพัฒนาระบบ Search

โดยผมจะเริ่มต้นจาก ที่มาว่าทำไมระบบ Search จึงต้องการตัวตัดคำ ก่อนลงรายละเอียดขั้นตอนในการพัฒนา ตั้งแต่การเตรียมข้อมูล, การพัฒนา model, การวัดผล และการนำไปใช้งานจริงบน production โดยในบทความนี้จะเน้นไปที่ไอเดียในการนำข้อมูลที่มีอยู่ มาประยุกต์ใช้ให้เกิดประโยชน์สูงสุด

![](https://miro.medium.com/max/30/1*Uw-f8Yw8IYdmfL050qkvCg.png?q=20)

![](https://miro.medium.com/max/2048/1*Uw-f8Yw8IYdmfL050qkvCg.png)

ระบบ Search นั้นถือว่าเป็น feature หลักของ Wongnai เพราะเป็น feature ที่มีคนใช้เยอะที่สุด แต่เราพบว่าก่อนหน้านี้ มีการค้นหามากกว่า 25% ที่ค้นหาแล้วไม่เจอผลลัพธ์ (zero search results) จากการค้นหาทั้งหมด หรือระบบไม่สามารถหาร้านที่ผู้ใช้ต้องการได้ ซึ่งส่งผลต่อการใช้งานโดยตรง

![](https://miro.medium.com/max/30/1*55BYZx8CiAWL4yxCcF46sg.png?q=20)

![](https://miro.medium.com/max/1012/1*55BYZx8CiAWL4yxCcF46sg.png)

ภาพตัวอย่าง เมื่อระบบไม่พบสิ่งที่ผู้ใช้กำลังค้นหา ระบบจะเปิดให้ผู้ใช้สามารถเพิ่มร้านใหม่ได้

เนื่องจาก ผู้ใช้จะพบหน้าจอตามภาพด้านบน จึงทำให้เกิด user experience ที่ไม่ดีหรือเกิดความผิดพลาดได้ เพราะผู้ใช้อาจจะทำการเพิ่มร้านเข้ามาในระบบ ทั้งๆ ที่ร้านนั้นมีอยู่แล้ว แต่ระบบหาไม่เจอเอง

## สาเหตุของปัญหา Zero search results

เราพยายามทำความเข้าใจปัญหาว่า การค้นหาเหล่านี้ มีรูปแบบในการพิมพ์อย่างไร จึงทำให้ระบบ Search ไม่สามารถหาของที่ผู้ใช้ต้องการได้ โดยนำตัวอย่าง Keyword ที่ผู้ใช้พิมพ์แล้วไม่เจอผลลัพธ์ ภายใน 1 วัน มาให้ทีมตรวจสอบ

![](https://miro.medium.com/max/30/0*GyFlOLQcY4-MAhDa.?q=20)

![](https://miro.medium.com/max/1206/0*GyFlOLQcY4-MAhDa.)

เราพบว่า 50% ของการค้นหา หรือครึ่งนึงของโดนัทด้านขวาในภาพ คือปัญหาที่เรายังสามารถแก้ไขได้ โดยใช้เทคนิคต่างๆเข้ามาช่วยได้ เช่น

-   ปัญหาการสะกดผิด ถือว่าเป็นสัดส่วนที่เยอะที่สุด เราใช้ตัว Spell correction ที่พยายามแก้คำ โดยเทียบจากข้อมูลที่ระบบเก็บไว้ (index) เช่นชื่อร้านอาหาร เมนูอาหาร ที่ระบบรู้จัก แต่บทความนี้จะไม่ได้ลงรายละเอียด
-   ปัญหาการพิมพ์ติดกัน และ ปัญหาใส่ Keyword เยอะเกินไป นั้นสามารถใช้ตัวตัดคำในการแก้ไขได้ ซึ่งจะอธิบายในหัวข้อถัดไป

## ทำไมคำพิมพ์ติดกัน จึงเป็นปัญหา

ปัจจุบันระบบ Search ทำงานโดยเปรียบเทียบระหว่างสิ่งที่ผู้ใช้พิมพ์เข้ามา (Query) กับข้อมูลที่ระบบ Search จัดเรียงไว้ (Index) ตามกลุ่มหลักๆดังนี้

1.  ชื่อร้านอาหาร
2.  ประเภทร้านอาหาร
3.  สถานที่
4.  เมนูอาหาร

![](https://miro.medium.com/max/30/1*Oxd8iV2AeklmJm2d-76cOg.png?q=20)

![](https://miro.medium.com/max/1758/1*Oxd8iV2AeklmJm2d-76cOg.png)

ตัวอย่างการค้นหาแบบปกติ

จากภาพ สิ่งที่พิมพ์เข้ามา จะถูกนำไปเทียบกับข้อมูลของแต่ละร้านอาหาร ถ้ามีร้านอาหารใดๆ ที่มีคำนี้ใน 4 หมวดหมู่ข้างต้น ร้านนั้นก็จะขึ้นในผลลัพธ์

แต่ถ้าลองพิมพ์คำติดกันเช่น ราเมงทองหล่อ ก็จะเกิดปัญหา เนื่องจากคำเหล่านี้ไม่ตรงกับสิ่งเราจัดเก็บไว้ ซึ่งสามารถแก้ไขได้ หากเปลี่ยนเป็นพิมพ์แยกกัน เราจึงเกิดไอเดียที่จะหา model ตัดคำมาแก้ปัญหาตรงนี้

![](https://miro.medium.com/max/30/1*nu3SGWarxaTA0cVbn2aGSw.png?q=20)

![](https://miro.medium.com/max/1720/1*nu3SGWarxaTA0cVbn2aGSw.png)

ตัวอย่างการค้นหาแบบพิมพ์ติดกัน

![](https://miro.medium.com/max/30/1*76MZiqYzTLl6j72wHyUDPA.png?q=20)

![](https://miro.medium.com/max/1710/1*76MZiqYzTLl6j72wHyUDPA.png)

ตัวอย่างการค้นหาเมื่อมีตัวตัดคำมาช่วย

_หมายเหตุ เป็นการอธิบายการทำงานระบบ Search เพื่อให้เข้าใจเหตุผลถึงการใช้ตัวตัดคำ เท่านั้น รายละเอียดการทำงานของระบบ Search ในเชิงลึก สามารถศึกษาต่อได้จากหัวข้อ_ [_information retrieval_](https://en.wikipedia.org/wiki/Information_retrieval)

ส่วนปัญหาการใส่ Keyword เยอะเกินไปนั้นเกิดจาก การพิมพ์คำที่นอกเหนือ จากสิ่งที่เราจัดเก็บไว้ เช่น “ร้านราเมงอร่อยแถวทองหล่อ” มี keyword ที่เป็นปัญหาคือคำว่า “ร้าน”, “อร่อย”, “แถว” ซึ่งจำเป็นต้องลบออกไป เพื่อทำให้ค้นหาเจอ โดยต้องอาศัยการตัดคำที่ถูกต้องก่อน จึงจะสามารถคัดแยกคำเหล่านี้

## เริ่มตัดสินใจใช้ตัวตัดคำ

การแก้ปัญหาการพิมพ์ติดกัน ในระบบ Search นั้นจำเป็นต้องมี model ที่สามารถตัดคำได้อย่างเหมาะสม ไม่ละเอียดจนเกินไป เพราะมีโอกาสทำให้ผลการค้นหาแย่ลง ทั้งในเรื่องของ precision ลดลง(เจอผลลัพธ์ที่ไม่เกี่ยวข้อง) หรือ อาจจะไม่เจอผลลัพธ์เหมือนเดิม

ยกตัวอย่างการค้นหา “ก๋วยเตี๋ยวต้มยำทองหล่อ ” การตัดคำต่างกัน จะส่งผลให้การค้นหา เจอผลลัพธ์แตกต่างกัน (ใช้สัญลักษณ์ “|” แทนการแบ่งคำ) เช่น

1.  ก๋วยเตี๋ยว|ต้มยำ|ทองหล่อ = เจอต้มยำอื่นๆ ที่ไม่เกี่ยวกับเมนูก๋วยเตี๋ยว
2.  ก๋วย|เตี๋ยว|ต้ม|ยำ|ทองหล่อ = ไม่เจอผลลัพธ์เหมือนเดิม เพราะแบ่งละเอียดไป

ด้วยเหตุนี้ เราจึงไม่สามารถใช้ model ตัดคำสำเร็จรูปได้ ถึงแม้จะเป็น model ใหม่ๆที่พัฒนาด้วย AI เพราะ model เหล่านั้น ส่วนใหญ่ถูกฝึกสอนจากคลังข้อมูลของ NECTEC ที่เกี่ยวกับความรู้ใน domain อื่นๆ ไม่เกี่ยวกับ เมนูอาหาร เช่น ข่าว และ สารคดี ทำให้ model เหล่านั้นไม่รู้จักคำศัพท์ หรือบริบทของคำที่เกี่ยวกับอาหาร

# พัฒนาตัวตัดคำบน Domain ของตัวเอง

เมื่อไม่สามารถใช้ตัวตัดคำทั่วไป เราจึงตัดสินใจที่จะสร้าง model ตัดคำที่เรียนรู้จากข้อมูลของเราเอง เพื่อแก้ปัญหาให้ตรงกับโจทย์ที่เราเจอ

# 1. Data preparation

ในการสร้าง model ตัดคำขึ้นมานั้น สิ่งแรกที่ต้องทำคือหาข้อมูล เพื่อใช้ฝึกสอน machine ซึ่งเราดึงตัวอย่างคำที่ผู้ใช้พิมพ์ (keyword) จากประวัติการค้นหาย้อนหลัง ตั้งแต่ปี 2017 จนถึงปัจจุบัน มาเป็นข้อมูลฝึกสอน โดยข้อมูลเหล่านี้ถูกดึงมาจากหลายที่ เช่น access log ใน AWS และ Google Bigquery โดยรวบรวมได้มาทั้งหมดประมาณ 800,000 คำที่แตกต่างกัน

![](https://miro.medium.com/max/30/1*C9h1Z6q4h_ByXWF_4qy2WQ.png?q=20)

![](https://miro.medium.com/max/563/1*C9h1Z6q4h_ByXWF_4qy2WQ.png)

ส่วนถัดมา คือการ label ข้อมูล หรือการระบุว่าเราจะตัดคำตรงไหนบ้าง เพื่อให้ machine เรียนรู้การตัดคำที่ถูกต้อง โดยเราพยายามสร้างให้อยู่ในรูปแบบเดียวกับที่ NECTEC สร้างไว้ เพื่อสามารถนำไปใช้งานต่อกับ model อื่นๆได้สะดวก

![](https://miro.medium.com/max/30/1*dyFk0or3FJOv4dz3OUe9kw.png?q=20)

![](https://miro.medium.com/max/1268/1*dyFk0or3FJOv4dz3OUe9kw.png)

ตัวอย่างข้อมูล news จาก  [BEST corpus ของ NECTEC](https://www.nectec.or.th/corpus/index.php?league=pm)

โดยสังเกตว่าข้อมูลตัวอย่างนั้น จะใช้ “|” ในการระบุว่าควรจะตัดคำตรงไหน ส่วน tag <NE> นั้นใช้สำหรับการระบุ entity หรือชื่อเฉพาะ ซึ่งเรายังไม่ใช้ในงานนี้

แต่เนื่องจากเราไม่มีคนมากพอที่จะช่วย label ข้อมูลเหล่านี้ เราจึงมีไอเดียที่จะสร้างวิธี label ขึ้นมา โดยอาศัยคลังคำศัพท์เมนูอาหาร และชื่อร้านอาหาร ที่เรารวบรวมมาจากข้อมูลรีวิวจากผู้ใช้

![](https://miro.medium.com/max/25/1*kH-9XfLNYy0i06TKOH-tHA.png?q=20)

![](https://miro.medium.com/max/1346/1*kH-9XfLNYy0i06TKOH-tHA.png)

ตัวอย่างคำศัพท์เมนูอาหาร ที่ได้จากข้อมูลรีวิวผู้ใช้

เราใช้คำศัพท์เหล่านี้มา label ข้อมูล ด้วยวิธี longest matching หรือเลือกคำที่ยาวที่สุดก่อน ยกตัวอย่าง เช่น เมื่อมีข้อมูล keyword คือ “ก๋วยเตี๋ยวต้มยำแถวทองหล่อ” และในคำศัพท์ของเรามีคำว่า “ก๋วยเตี๋ยว” กับ “ก๋วยเตี๋ยวต้มยำ” ตัว label จะต้องเลือก ก๋วยเตี๋ยวต้มยำ และ label โดยใส่ tag ดังนี้

> <mark>ก๋วยเตี๋ยวต้มยำ</mark>แถวทองหล่อ

ในการพัฒนา เราสามารถใช้ regex จดจำคำศัพท์ทั้งหมด และใส่ tag เมื่อพบคำศัพท์ใดๆใน keyword ได้ดังนี้
```py
food_dict = ['กะเพรา','กะเพราหมูสับ','ก๋วยเตี๋ยวต้มยำ','ต้มยำ']  
food_dict = sorted(food_dict, key=len)  
food_dict.reverse()import req = "ก๋วยเตี๋ยวต้มยำทองหล่อ"  
regex = re.compile(r'(' + '|'.join(food_dict) + ')', re.I)  
tag_q = regex.sub(r'<mark>\1</mark>', q)  
print(tag_q)
```
โดยเริ่มต้นจาก เรียงคำศัพท์ตามความยาวมากที่สุดก่อน เพื่อให้ regex สามารถทำงานแบบ longest matching (เนื่องจาก regex จะเลือกคำศัพท์ตามลำดับจากซ้ายไปขวา) ก่อนจะใช้ re.complie เพื่อให้ regex จดจำคำศัพท์ทั้งหมด และ replace text เมื่อพบคำศัพท์ใดๆ ใน keyword ด้วย regex.sub

แต่การใช้ regex แบบปกติ เพื่อทำการ find and replace แบบนี้ใช้เวลาประมวลผลนานมาก เพราะคำศัพท์เรามีมากกว่า 7 แสนคำ เราจึงลองหาวิธีใหม่ๆ มาช่วย นั่นคือ การทำให้ regex ค้นหาคำศัพท์เร็วขึ้นโดยการแปลง regex pattern ให้อยู่ในรูปแบบ  [prefix tree หรือ trie](https://en.wikipedia.org/wiki/Trie)

ยกตัวอย่างในกรณีมีคำศัพท์ 5 คำดังนี้

['foobar', 'foobah', 'fooxar', 'foozap', 'fooza']

![](https://miro.medium.com/max/30/0*4WQbnjoGXtUq764H.png?q=20)

![](https://miro.medium.com/max/298/0*4WQbnjoGXtUq764H.png)

ภาพจาก [stackoverflow.com](https://stackoverflow.com/questions/42742810/speed-up-millions-of-regex-replacements-in-python-3/42789508#42789508)

โดยปกติสิ่งที่ regex เห็นคือ 5 คำที่แยกกัน ในแต่ละครั้งที่สแกน keyword จำเป็นต้องไล่ดูคำศัพท์ทีละคำคือ 5 ครั้ง แต่เราสามารถแปลงเป็น prefix tree เพื่อย่อ prefix ตัวอักษรที่เหมือนกัน ได้ดังนี้

![](https://miro.medium.com/max/30/0*P4iIM7l1Lsye_4bI.png?q=20)

![](https://miro.medium.com/max/340/0*P4iIM7l1Lsye_4bI.png)

ภาพจาก [stackoverflow.com](https://stackoverflow.com/questions/42742810/speed-up-millions-of-regex-replacements-in-python-3/42789508#42789508)

จากภาพ คำศัพท์ที่ขึ้นต้นด้วย foo สามารถ group เป็นกลุ่มใหญ่ และแยกกลุ่มย่อยไปได้ อีก 3 กลุ่ม ซึ่งช่วยลดเวลาในการค้นหาได้ เช่น เมื่อ keyword คือ “fooza” จากแต่ก่อนต้องไล่ดู 5 ครั้ง แต่หลังจากสร้าง tree เราจะรู้ว่าต้องค้นหาแค่ path ด้านล่าง ไม่ต้องเสียเวลาไปดู path อื่นๆด้านบนที่เหลืออีก 3 คำ (fooxar, foobah, foobar)

โดยจากการทดสอบ prefix tree สามารถลดเวลาขั้นตอนการ label ได้มากกว่า 2–3 เท่า ขึ้นอยู่กับรูปแบบ และปริมาณของข้อมูล

ตัวอย่างการใช้ trie กับ regex
```py
import re  
from trie import Triedef trie_regex_from_words(words):  
    trie = Trie()  
    for word in words:  
        trie.add(word)  
    return re.compile(r'(' + trie.pattern() + ')', re.I)trie_regex = trie_regex_from_words(food_dict)q = "ก๋วยเตี๋ยวต้มยำทองหล่อ"  
tag_q = trie_regex.sub(r'<mark>\1</mark>', q)  
print(tag_q)
```
-   import library  [trie.py](https://stackoverflow.com/questions/42742810/speed-up-millions-of-regex-replacements-in-python-3/42789508#42789508)
-   trie.add(word) : เพื่อเพิ่มคำศัพท์ไปใน trie จนครบ
-   trie.pattern() : แปลง trie ให้อยู่ในรูปแบบที่ regex เข้าใจก่อน compile

หลังจากนั้น เราจะนำคำที่ label แล้วมาผ่านตัวตัดคำมาตรฐานอื่นๆ เช่น Deepcut เพื่อตัดคำที่อยู่นอก tag <mark> หรือคำอื่นๆที่นอกเหนือจากคลังคำศัพท์ของเราให้อยู่ในรูปแบบ

> <mark>ก๋วยเตี๋ยวต้มยำ</mark>แถว|ทองหล่อ

สุดท้ายจึงแปลงเป็นรูปแบบเดียวกับ NECTEC คือ

> ก๋วยเตี๋ยวต้มยำ|แถว|ทองหล่อ

# 2. Model

หลังจากที่เตรียมข้อมูล เรียบร้อยแล้ว ก็ถึงขั้นตอนการนำข้อมูลไป train model โดยเราเลือกใช้ model ชื่อว่า  [Bi-directional RNN](https://en.wikipedia.org/wiki/Bidirectional_recurrent_neural_networks)  ที่กำลังเป็นที่นิยมสำหรับงานวิเคราะห์ข้อมูลด้านภาษา (NLP) โดยเฉพาะการตัดคำ

[Bi-directional RNN](https://en.wikipedia.org/wiki/Bidirectional_recurrent_neural_networks)  นั้นมีต้นกำเนิดมาจาก  [Recurrent Neural Networks (RNN)](https://en.wikipedia.org/wiki/Recurrent_neural_network)  ซึ่งเป็น neural network ประเภทหนึ่ง ที่เหมาะกับข้อมูลที่มีลำดับต่อเนื่องกัน (sequence data) เช่น เสียง หรือข้อความ เนื่องจาก RNN มีหน่วยความจำในตัวเองที่สามารถใช้ในการจดจำ context ในอดีต หรืออนาคตเพื่อช่วยในการวิเคราะห์และทำนายได้

![](https://miro.medium.com/max/30/1*mt7eg99nmCukkXWJRVBCSA.png?q=20)

![](https://miro.medium.com/max/2852/1*mt7eg99nmCukkXWJRVBCSA.png)

ตัวอย่าง application ของ sequence data ที่ใช้ RNN, ภาพจาก  [deeplearning.ai](https://www.coursera.org/learn/nlp-sequence-models/lecture/0h7gT/why-sequence-models)

ถ้าเรากลับมามองในโจทย์การตัดคำ model จะพิจารณาว่าคำที่พิมพ์เข้ามา คือ ชุดของตัวอักษร sequence of characters เช่น จากคำว่า “กาแฟ” จะพิจารณาเป็น
```py
t = 0 : ก
t = 1: า
t = 2: แ
t = 3: ฟ
```
ตัวอักษรในแต่ละช่วงเวลา t หรือ x<t> จะถูกใส่เข้าไปใน model ที่แทนที่ด้วยกล่องสี่เหลี่ยม เรียกว่า cell หรือ memory cell ทีละตัวอักษร เพื่อทำนายออกมาเป็นค่า y <t>ในแต่ละตำแหน่ง ว่าควรจะตัดคำที่ตำแหน่งนี้หรือไม่ (0 = ไม่ตัด, 1 = ตัด)

![](https://miro.medium.com/max/30/1*cI6n6C1uUZy24UasWgci_Q.png?q=20)

![](https://miro.medium.com/max/2108/1*cI6n6C1uUZy24UasWgci_Q.png)

RNN model (Many-to-many) ภาพจาก  [deeplearning.ai](https://www.coursera.org/learn/nlp-sequence-models/lecture/ftkzt/recurrent-neural-network-model)

สังเกตว่า ตัวอักษรตัวท้ายๆ หรือที่ x<Tx> นั้นจะทำนาย โดยอาศัย context จาก ตัวอักษรก่อนหน้า เนื่องจากได้รับข้อมูลจาก a<Tx-1> ของ cell ก่อนหน้า เปรียบเสมือนกับคนที่เวลาเราจะตัดสินใจว่าตัดคำตรงไหน ก็ต้องอ่านทีละตัวอักษรจนจบประโยคก่อน

มาถึงจุดนี้อาจจะเกิดคำถามว่า แบบนี้ตัวอักษรแรกๆ อาจจะทำนายได้ไม่ดีหรือเปล่า เพราะไม่มี context มาช่วยเหมือนตัวท้ายๆ ด้วยข้อด้อยตรงนี้จึงเกิด  [Bi-directional RNN](https://en.wikipedia.org/wiki/Bidirectional_recurrent_neural_networks)  เพื่อให้ model สามารถอ่านประโยคไปข้างหน้าและหลังก่อนตัดสินใจได้

![](https://miro.medium.com/max/30/1*q9wgkoyS6Nl7gJBjZG3TsA.png?q=20)

![](https://miro.medium.com/max/1052/1*q9wgkoyS6Nl7gJBjZG3TsA.png)

Bi-directional RNN (LSTM) character level ดัดแปลงภาพมาจาก  [guillaumegenthial.github.io](https://guillaumegenthial.github.io/sequence-tagging-with-tensorflow.html)

จากภาพ memory cell จะอ่านและจดจำตัวอักษรทั้ง forward และ backward ทำให้การตัดสินใจ ตัวอักษรแรก คือ “ก” นั้นสามารถนำ context จากตัวอักษรข้างหลัง เข้ามาประกอบการตัดสินใจได้

ภายใน memory cell นั้นยังประกอบไปด้วย gate ต่างๆเพื่อใช้ตัดสินใจว่าข้อมูลที่ผ่านเข้ามาควรจะจำหรือไม่ ปัจจุบันที่กำลังเป็นที่นิยม คือ LSTM และ GRU ซึ่งเราเลือกใช้ GRU เนื่องจากมีความซับซ้อนน้อยกว่า และให้ผลลัพธ์พอๆกับ LSTM

![](https://miro.medium.com/max/30/1*zFYUTNl9SJwD8_xjQadbYA.png?q=20)

![](https://miro.medium.com/max/1498/1*zFYUTNl9SJwD8_xjQadbYA.png)

การทำงานภายใน cell แต่ละแบบ ภาพจาก  [colah.github.io](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)

สำหรับขั้นตอนการพัฒนา model นั้น เนื่องจากปัจจุบันมีคนพัฒนา  [model ตัดคำภาษาไทย](https://github.com/kobkrit/nlp_thai_resources/blob/master/README.md)  มาหลากหลายรูปแบบ เราสามารถนำมาพัฒนาต่อยอดได้ โดยไม่ต้องเริ่มจากศูนย์ ซึ่งเราตัดสินใจเลือก model Bidirectional RNN ที่พัฒนาโดย  [Sertis](https://sertiscorp.com/thai-word-segmentation-with-bi-directional_rnn/)  มาต่อยอด ด้วยการนำมาใช้ฝึกสอนกับข้อมูลของ Wongnai

ในขั้นแรกเราทดลองใช้ default parameter ที่มีมาให้ ซึ่งได้ผลลัพธ์ออกมาเป็นที่น่าพอใจ ได้คะแนน F1 score ประมาณ  **93.21**  ที่ word level บน test set

อย่างไรก็ตามเราได้ลองปรับ parameters ต่างๆเพิ่มเติม เพื่อเพิ่ม complexity ให้ model เช่น เพิ่มขนาด state size (ขนาดของ cell) และ layer ของ Bi-directional RNN จนสามารถเพิ่มคะแนน F1 ได้ถึง **95.27**

ตัวอย่างการ เพิ่ม Bi-directional RNN layer
```py
#Original bidirectional RNN layer
cell = tf.nn.rnn_cell.GRUCell(state_size)
cell = tf.nn.rnn_cell.DropoutWrapper(cell, output_keep_prob=1-dropout)
(forward_output, backward_output), _ = \
    tf.nn.bidirectional_dynamic_rnn(cell, cell, inputs=embedding_vectors,
                                    sequence_length=lengths, dtype=tf.float32,scope='BRNN_Layer_1')
outputs = tf.concat([forward_output, backward_output], axis=2)
#Add new bidirectional RNN layer
cell2 = tf.nn.rnn_cell.GRUCell(state_size)
cell2 = tf.nn.rnn_cell.DropoutWrapper(cell2, output_keep_prob=1-dropout)
(forward_output, backward_output), _ = \
    tf.nn.bidirectional_dynamic_rnn(cell2, cell2, inputs=outputs,
                                    sequence_length=lengths, dtype=tf.float32,scope='BRNN_Layer_2')
outputs = tf.concat([forward_output, backward_output], axis=2)
```
ในส่วนของการเพิ่ม layer นั้น จำเป็นต้องปรับ code จากของเก่า โดยกำหนด scope name เพิ่ม เพื่อให้ model สามารถแยกความแตกต่างระหว่าง layer

และส่วน layer ที่สอง ที่เพิ่มเข้ามานั้นจะคล้ายของเก่า เพียงแต่นำ output ที่ได้จาก layer ก่อนหน้า มาใส่ใน input ของ layer ใหม่เพื่อ stack กันระหว่างสอง layer(ที่ highlight ไว้)

# 3. Evaluation

สำหรับการวัดผลนั้นเราใช้ คะแนน F1 ที่คำนวณจากจำนวน word ที่ตัดได้ถูกต้อง เพื่อเป็นการวัดผลในระดับ word level ตามที่อธิบายในภาพด้านล่าง

![](https://miro.medium.com/max/30/1*G1l5HYMf5z54XpL8gYJNUg.png?q=20)

![](https://miro.medium.com/max/1011/1*G1l5HYMf5z54XpL8gYJNUg.png)

วิธีการวัดผลตัวตัดคำ ภาพจาก slide ของดร.ปรัชญา บุญขวัญ ใน course  [Deep learning for text](https://www.skooldio.com/courses/gde-tensorflow-03)

อย่างไรก็ตาม F1 score ก็ยังเป็นเพียงคะแนนที่วัดผลเทียบกับตัว label ซึ่งอาจจะไม่ตรงกับโจทย์ของจริง 100% เราจึงพยายามสร้าง Gold standard ที่มีความถูกต้องในระดับเดียวกับมนุษย์ โดยการลงทุนเอาคนมาช่วย label

โชคดีที่ Wongnai มีทีม data ที่ทำงานโดยตรงและคุ้นเคยกับข้อมูลอยู่แล้ว จึงเป็นกำลังสำคัญในการสร้าง Gold standard ซึ่งหลังจากที่สร้างเสร็จแล้ว เราสามารถนำมาใช้ชี้วัดประสิทธิภาพที่แท้จริง รวมถึง อาจจะใช้เป็น training data ในอนาคตเมื่อมีข้อมูลมากพอ

# **4.Integration**

เนื่องจาก Model ได้ถูกพัฒนาบน Tensorflow ซึ่งรองรับการ export model เพื่อเรียกใช้งานในภายหลังได้จากหลายภาษา ซึ่งในกรณีระบบ Search ที่พัฒนาบน Java นั้นก็สามารถเรียกใช้งานได้ทันที โดยไม่จำเป็นต้องแยก server สำหรับรัน model ตัดคำ ซึ่งทำให้การดูแลระบบง่ายขึ้น

ตัวอย่าง code การเรียกใช้ model บน Java  [https://gist.github.com/tanapoln/2b800d8adf14584e3f4587cb1f88491d](https://gist.github.com/tanapoln/2b800d8adf14584e3f4587cb1f88491d)

# 5. Result

จากการพัฒนาระบบ Search อย่างต่อเนื่องด้วยเทคนิคหลากหลายรูปแบบ เช่น การใช้ตัวตัดคำ และ Spell correction เราสามารถลดจำนวน Weekly zero search result rate ไปได้ถึงหนึ่งในสาม ซึ่งถือว่าเป็นจุดเริ่มต้นที่ดีในการพัฒนา เพราะเรายังมีอีกหลายเทคนิคที่จะมาแก้ไขปัญหานี้

![](https://miro.medium.com/max/30/1*bsZ0wuu0NFKlTW5Hu_lRIw.png?q=20)

![](https://miro.medium.com/max/719/1*bsZ0wuu0NFKlTW5Hu_lRIw.png)

Weekly zero result rate

# Next step

ปัญหาเรื่อง zero search results ยังสามารถแก้ไขได้ด้วยอีกหลายวิธี เช่น การพยายามทำความเข้าใจ query หรือ  [query understanding](https://queryunderstanding.com/)  โดยปรับเปลี่ยนคำให้เหมาะสม ก่อนส่งต่อให้ระบบ Search ด้วยสองวิธีดังนี้

-   [Query expansion](https://queryunderstanding.com/query-expansion-2d68d47cf9c8)  หรือการหาของที่กว้างขึ้น ให้ผู้ใช้ เช่น ถ้าค้นหา “ร้านก๋วยเตี๋ยวปลา” ไม่เจอ อาจจะเปลี่ยนเป็นหา “ร้าน ก๋วยเตี๋ยว” ให้ผู้ใช้แทน
-   [Query relaxation](https://queryunderstanding.com/query-relaxation-342bc37ad425)  หรือการลบบางคำที่ไม่จำเป็นออกไป เช่น อร่อย แถว ย่าน น่ากิน เพื่อแก้ไขปัญหา ใส่ keyword เยอะเกินไป

การแก้ไขปัญหาเหล่านี้ สามารถเพิ่ม  [recall](https://en.wikipedia.org/wiki/Precision_and_recall)  ให้กับการ Search หรือการเพิ่มโอกาสในการดึงผลลัพธ์ที่เกี่ยวข้องออกมามากขึ้น ซึ่งหลังจากที่ได้ผลลัพธ์ที่เกี่ยวข้องมากพอ โจทย์ถัดไปก็คือ การเพิ่ม  [precision](https://en.wikipedia.org/wiki/Precision_and_recall)  ของผลลัพธ์ หรือการ ranking ผลลัพธ์ให้ดีนั้นเอง

สุดท้ายนี้ ผมหวังว่าบทความนี้จะเป็นไอเดีย ให้กับหลายๆท่านในการนำ Machine learning มาประยุกต์ใช้เแก้ไขปัญหาใน domain ของตัวเอง ในอนาคตเราคาดว่าจะมีบทความเกี่ยวกับ Data science มานำเสนอมากขึ้นครับ

> Source : [wongnai.com](https://life.wongnai.com/wongnai-search-improvement-using-machine-learning-part1-e0777b65979e).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3NDM3NTcyNzFdfQ==
-->