Data Science for e-Customs Systems
===

# เป้าหมายที่จะนำมาแก้ปัญหาในด้านใดบ้าง

1. การนำเข้าข้อมูล ( import data ) ให้สะดวก ยืดหยุ่น มีความหลากหลาย 
3. ช่วยตรวจสอบการทำงาน ลดข้อผิดพลาด
4. ความรู้ข่าวสาร ประกาศกฎหมาย ข้อมูลที่แตกต่าง เพื่อความได้เปรียบทางธุรกิจ
5. Data Visualization ต่อยอดจาก Report ของเดิม เพื่อให้จบกระบวนการทำงาน อาจจะลองนำแนวคิด Privot table มาปรับใช้

 ==**Detail**==
 
## 1. การนำเข้าข้อมูล




## 2. ช่วยตรวจสอบการทำงาน ลดข้อผิดพลาด

- check digit 
	- Bank Account
	- ID Card
	- Container No.
- EXAM ใบอนุญาต ให้นำ excel เข้าในระบบเพื่อตรวจสอบ



## 3. ความรู้ข่าวสาร
- Permission แจ้งว่าเดือนนี้ มีพิกัดใดที่ตรวจสอบเพิ่มขึ้น / มีการเอาออก / มีการเปลี่ยนแปลง



## 4. Data Visualization 

### Example


- [**_esquisse_**](https://github.com/dreamRs/esquisse) 
- 
เป็น Interactive ที่มีความยืดหยุ่น ผู้ใช้งานสามารถเลือกข้อมูล กำหนดค่าต่าง ๆ ได้หลากหลายและเป็นอิสระ เพื่อให้รองรับกับ requirement ที่หลากหลาย 

This addin allows you to interactively explore your data by visualizing it with the  [ggplot2](https://github.com/tidyverse/ggplot2)  package. It allows you to draw bar plots, curves, scatter plots, histograms, boxplot and  [sf](https://github.com/r-spatial/sf)  objects, then export the graph or retrieve the code to reproduce the graph.

See online documentation :  [https://dreamrs.github.io/esquisse/index.html](https://dreamrs.github.io/esquisse/index.html)


![enter image description here](https://github.com/dreamRs/esquisse/raw/master/man/figures/esquisse.gif)


## ข้อมูลเพิ่มเติม

### Anatomy of Credit Card Number ความหมายของเลขบัตรเครดิต

เลขบัตรเครดิต/เดบิตกัน เลขบัตรเครดิตที่เห็นยาว ๆ กันนั้นประกอบด้วย 2 ส่วนใหญ่ ๆ ด้วยกันคือ BIN กับ PAN ครับ

"ส่วนแรก BIN"

. ตัวเลข 6 ตัวแรกเรียกว่า Issuer หรือ Bank Identification Number(IIN หรือ BIN) พัฒนาขึ้นมาโดย International Organization for Standardization (ISO) มี American Bankers Association เป็นหน่วยงานจัดสรรหมายเลข IIN

เลขตัวแรกของ IIN เรียกว่า Major Industry Identifier (MII) เป็นตัวบ่งบอก ประเภทของธุรกิจที่ออกบัตร โดย IIN ทั้ง 6 ตัวจะบอกถึง Card Brand, Issuing Bank, Card Type, Card Level, Country, Bank's website และ Customer Care Line

"ส่วนที่สอง PAN"

. ตัวเลขบัตรเครดิตที่เหลือเรียกว่า Primary Account Number (PAN) จะจำเพาะต่อผู้ถือบัตรแต่ละคน โดยตัวเลขสุดท้ายของ PAN เรียกว่า Check digit(?) เอาไว้ตรวจสอบว่าเลขบัตรเครดิตนั้นถูกต้องหรือไม่? ตัวเลขถัดมาทางซ้ายก็จะรัน 1, 2, 3,... ตามลำดับการอนุมัติบัตรครับ ดังนั้นถ้าเลข 8 ตัวท้ายของบัตรเป็น 0000 077x ก็คือบัตรอนุมัติเป็นคนที่ 77 นั่นเองครับ

แต่ถ้าเป็นสำนัก TMB, BBL, AEON จะไม่ใช้วิธีการเรียงลำดับดังกล่าว แต่จะกำหนดเลขบัตร 4 ตัวท้ายเป็นรูปแบบที่ตายตัว คือ

X00X หรือ X01X สำหรับ TMB  
X11X-X14X สำหรับ BBL และ  
XX0X สำหรับ AEON ครับ

ถ้าจะเรียงลำดับให้ดูที่หลัก 8-13 หรือ 8-14 แทนครับ ตัวอย่าง ของ TMB เป็น 4050 1617 0001 100X อันนี้จะเป็นการออกบัตรในลำดับที่ 11 ของ Series นั้นๆ (ตัดหลักที่ 14-16 ออกไป)

"Check digit"

. Check digit(?) ได้มาจากการใช้ Luhn algorithm (เทคนิคของผมคือจำว่า คูณสอง ลบเก้า คูณเก้า) ตัวอย่างเช่นบัตร MasterCard เลข 16 หลักคือ 5237 1645 0365 554(?) มาคำนวณ Luhn algorithm กันดูว่าเลขสุดท้ายควรเป็นเลขอะไร?

ขั้นแรก จาก Check digit(?) ไล่ย้อนมาทางซ้ายมือให้คูณที่ตัวเลขตัวเว้นตัวด้วย 2 ตัวที่เว้นไว้ไม่ต้องคูณอะไรดังนี้  
(5x2) 2 (3x2) 7 (1x2) 6 (4x2) 5 (0x2) 3 (6x2) 5 (5x2) 5 (4x2) ค่าที่คูณได้คือ (10) 2 (6) 7 (2) 6 (8) 5 (0) 3 (12) 5 (10) 5 (8)

ขั้นที่สอง ถ้าผลคูณในวงเล็บเกิน 9 ให้ลบออกด้วย 9 จะได้เป็น  
(1) 2 (6) 7 (2) 6 (8) 5 (0) 3 (3) 5 (1) 5 (8)

ขั้นที่สาม นำตัวเลขทั้งหมดบวกรวมกัน จะได้เป็น  
(1)+ 2+ (6)+ 7+ (2)+ 6+ (8)+ 5+ (0)+ 3+ (3)+ 5+ (1)+ 5+ (8) = 62

ขั้นสุดท้าย นำผลรวมที่ได้คูณด้วย 9 ได้ผลลัพธ์เท่าไหร่ ตัวเลขสุดท้ายคือ Check digit(?) นั่นคือ  
62x9 = 558 แสดงว่า check digit คือเลข 8 นั่นเอง (แสดงว่าเลขบัตรดังกล่าวคือ 5237 1645 0365 5548 นะคร้าบบบ)

หมายเหตุ เวลาซื้อของออนไลน์ถ้าเราพิมพ์เลขบัตรถูกต้องจะขึ้นเครื่องหมายถูกสีเขียวเพราะตรวจสอบด้วย Luhn algorithm นั่นเองครับ  
  
ดูภาพประกอบ>>> [https://bit.ly/2IuuzbK](https://goo.gl/aDWVoh)

> [Source:](http://thaicashlesssociety.blogspot.com/2018/03/anatomy-of-credit-card-number.html)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTMwMTA3MzYxMiwtMzA3NDkwNDE5LDkzOD
EwODIzOSwtMjUyNjA0MzcxLDIwNjc4NDA4MjRdfQ==
-->