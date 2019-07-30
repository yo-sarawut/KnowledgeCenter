RDX Platform  
============  
  
# What is the RDX Platform?  
![what-RDX-Platform.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/what-RDX-Platform.jpg)  
  
# RDX Work Flow  
![RDX-work-flow.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/RDX-work-flow.jpg)  
  
# How to interface with RDX Platform?  
![interface-with-RDX.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/interface-with-RDX.jpg)  
  
# RDX Gateway Client Module Type  
![RDX-Gateway-Client-Module.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/RDX-Gateway-Client-Module.jpg)  
  
# RDX Secure Docs Model  
![RDX-Secure-Docs-Model.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/RDX-Secure-Docs-Model.jpg)  
  
# RDX Biz Exchange Services  
![RDX-Biz-Exchange-Services.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/RDX-Biz-Exchange-Services.jpg)  
  
# ลักษณะของการเชื่อมต่อข้อมูลกับ RDX Platform  
  
ระบบได้ออกแบบมาเพื่อไม่ให้เข้าไปยุ่งเกี่ยวกับระบบของ Partner ใด ๆ ทั้งสิ้น ไม่ว่าจะเป็น hardware หรือ software เพื่อป้องกันปัญหาการแทรกแซงข้อมูลของลูกค้า และ ผลข้างเคียงอันอาจจะเกิดจากการทำงานของระบบ ปัญหาหลักของการทำงานข้าม Platform คือ การเข้าถึงข้อมูลโดยตรงระหว่างกัน ซึ่งเมื่อมีข้อผิดพลาดเกิดขึ้นในการทำงาน การตรวจสอบข้อมูลถึงสาเหตุของปัญหา เป็นการตรวจสอบที่ใช้เวลาค่อนข้างมาก หากทั้ง 2 Platform เป็นคนละหน่วยงานกัน อาจจะเกิดปัญหาการโยนความผิดให้กัน ส่งผลกระทบไปกับทุก ๆ ฝ่าย  
  
**เราได้ออกแบบการเชื่อมต่อระบบไว้ 2 ลักษณะ ได้แก่**  
  
1. **File System** : เหมาะสำหรับระบบงานแบบเดิม ที่ไม่สามารถปรับเปลี่ยนอะไรได้มาก เป็นการเชื่อมต่อข้อมูลระหว่างระบบด้วย Text File ผ่าน Shared Folder ที่ตกลงกัน ทั้งการส่งข้อมูล การรับข้อมูล และ การตรวจสอบข้อผิดพลาดจากสาเหตุต่างๆ เพื่อไม่ให้ส่งผลกระทบกับการทำงานของทั้ง 2 Platform  
  
2. **Web Service** : เหมาะสำหรับระบบงานที่สามารถเรียกใช้ Web Service ได้ โดยการรับส่งข้อมูลผ่าน Web Service API ที่กำหนด โดยสามารถเปลี่ยนวิธีการจาก Call Web Service เปลี่ยนเป็น REST API แทนได้  
  
# กระบวนการส่งข้อมูล e-Tax Invoice ของกรมสรรพาการทางอิเล็กทรอนิกส์  
![data-tranmission-process.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/data-tranmission-process.jpg)  
  
# Method 1 : การส่งข้อมูลไปกรมสรรพากร โดยการเชื่อมต่อแบบ File System  
  
## RDX Platform File System Interface Concept  
  
![File-System-Interface-Concept.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/File-System-Interface-Concept.jpg)  
  
## Partner File System Interface  
![Partner-File-System-Interface.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/Partner-File-System-Interface.jpg)  
  
## ขั้นตอนการทำงาน (File System)  
  
1. Shared Folder ‘D:\ECS-SYS\AutoSend’ ที่เครื่อง RDX Gateway Client ภายใน Folder นั้นจะมี Folder ย่อยคือ Inbox, Outbox  
2. Map Network Drive ให้ไปชี้ที่ Shared Folder ในข้อ 1 ของเครื่องของ RDX Gateway Client  
3. สร้างข้อมูลเอกสารตาม Flat File Layout ที่ RDX Platform กำหนด  
4. Copy File ไปยัง Folder ‘Outbox’ ที่ Map ไว้ กรณีที่มี PDF File ให้ Copy PDF File ส่งมาให้ด้วย โดยตั้งชื่อไฟล์ PDF เป็น TAXNO_DOCTYPE_DOCNO.PDF เช่น 1052903456_ETAXINVOICE_0199123.PDF เป็นต้น (ห้ามมีเครื่องหมายสัญลักษณ์ใดๆ ในชื่อไฟล์)  
5. ระบบ RDX Gateway Client จะทำการตรวจสอบ File ใน Folder ‘Outbox’ แล้วจะทำการ Import ข้อมูลลงใน Database ของระบบ  
6. ระบบจะส่ง Log File กลับมาที่ Folder Inbox เพื่อให้โปรแกรมได้ตรวจสอบว่า ข้อมูลที่ส่งเข้ามา ถูกต้องหรือไม่ รวมถึง Response ต่างๆ  
7. ระบบ RDX Gateway Client จะ Scan ข้อมูลใน Database ของระบบเพื่อตรวจสอบว่ามีข้อมูลเข้ามาใหม่หรือไม่ ถ้ามีข้อมูลใหม่ ระบบจะทำการสร้างข้อมูล XML จากเอกสารชุดนั้น พร้อมลงลายมือชื่ออิเล็กทรอนิกส์  
8. ระบบ จะส่งข้อมูล ต่อไปให้กับ RDX RD Gateway เพื่อส่งข้อมูลให้กรมสรรพากรต่อไป  
9. เมื่อกรมสรรพากรได้รับข้อมูลแล้ว กรมสรรพากร จะส่งข้อมูลสถานะการส่งข้อมูลกลับมาให้ ว่าส่งผ่าน (Accept) หรือ ไม่ผ่าน (Reject)  
10. ระบบ RDX Gateway Client จะทำการสร้าง Log File มาไว้ที่ Folder Inbox เพื่อให้โปรแกรมได้ตรวจสอบว่า สถานะส่งข้อมูล ไปที่กรมสรรพากร ถูกต้องหรือไม่  
11. เมื่อข้อมูลที่ส่งให้กรมได้รับ Accept จากกรมสรรพากร ระบบลงลายมือชื่ออิเล็กทรอนิกส์ใน PDF File จากข้อ 4  
12. ระบบจะ Zip เอกสาร XML File และ PDF File พร้อมเข้ารหัส ไปเก็บไว้ใน RDX Secure Doc Server และ ส่งต่อให้ RDX Biz Exchange ต่อไป  
  
* **ข้อแนะนำ :** ควรสร้างหน้าจอสำหรับรับส่งข้อมูล และ หน้าจอสถานะข้อมูล ให้กับลูกค้าได้ทราบ ถึงรายการที่เกิดขึ้นในขั้นตอนต่าง ๆ  
  
## การตรวจสอบข้อมูล Response จากระบบ (File System)  
  
1. RDX Gateway Client จะทำการตรวจสอบ Response ต่างๆจาก RDX Platform ถ้ามี Response ใดๆกลับมา ไม่ว่าจะเป็น RDX RD Gateway / RDX Secure Docs / RDX Biz Exchange ระบบจะสร้าง Log Record ทุกครั้ง  
2. ระบบจะทำการสร้างไฟล์ต่างๆ ที่เกี่ยวข้อง ลงใน Inbox Folder  
- สร้าง Log File ตั้งชื่อเป็น LOGyyyyMMdd_0000000_TaxNo_Branch.log ( JSON String )  
- สร้าง Text File ตั้งชื่อเป็น TXT_TaxNo_Branch_RefNo.txt ( Text Pipe )  
- สร้าง PDF File ตั้งชื่อเป็น PDF_TaxNo_Branch_RefNo.pdf ( PDF File )  
3. ให้ระบบของ Partner เข้ามาตรวจสอบไฟล์ใน Inbox Folder ว่ามีไฟล์ใดบ้าง  
4. ทำการ Loop Log File ที่ได้ โดยการตรวจสอบค่า Log.ResType Field  
- **'ACCEPT'** : ผลการส่งข้อมูลไปที่กรมสรรพากรสำเร็จ กรมฯ ยืนยันการรับข้อมูล ตรวจสอบเลขรับข้อมูลที่ Log.PayLoad Field  
- **'REJECT'** : ผลการส่งข้อมูลไปที่กรมสรรพากรสำเร็จ กรมฯ แจ้งปัญหากลับมา ตรวจสอบ สาเหตุที่ REJECT จากข้อมูลที่ Log.PayLoad  
- **'ERROR'** : มี Error จากการทำรายการ ตรวจสอบ สาเหตุที่ Error จากข้อมูลที่ Log.PayLoad  
- **'KeepDoc'** : เก็บข้อมูลลง RDX Secure Docs เรียบร้อย  
- **'BizX'** : ได้รับข้อมูลจากคู่ค้าผ่าน RDX Biz Exchange Server  
- ให้ใช้ Text File โดยอ้างอิงจากชื่อไฟล์ TXT_TaxNo_Branch_RefNo.txt เพื่อใช้ในการนำเข้าข้อมูลเอกสารต่อไป  
- ให้ใช้ PDF File (ถ้ามี) โดยอ้างอิงจากชื่อไฟล์ PDF_TaxNo_Branch_RefNo.pdf เพื่อใช้ในการเก็บเอกสารต่อไป (RefNo จะได้จาก Log.RefNo Field)  
  
5. ทำการ DELETE File ที่อ่านมาแล้ว ทั้ง Log, Text และ PDF จากใน Inbox  
  
# Method 2 : การส่งข้อมูลไปกรมสรรพากร โดยการเชื่อมต่อแบบ Web Service  
  
## RDX Platform Web Service Interface Concept  
  
![web-service-Interface-Concept.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/web-service-Interface-Concept.jpg)  
  
## Partner Web Service Interface  
  
![Partner-web-service-Interface.jpg](https://github.com/yosarawut/WorkingArea/raw/master/e-tax-invoice/img/Partner-web-service-Interface.jpg)  
  
## ขั้นตอนการทำงาน (Web Service)  
  
1. ตรวจสอบ IP Address ของ RDX Gateway Client ว่าใช้ IP ใด  
2. ทดสอบการเรียก Web Service โดยการเปิด Web Browser แล้ว ใส่ URL: https://xxx.xxx.xxx.xxx/AUTOSENDWS/AutoSend_Services.asmx (โดยที่ xxx.xxx.xxx.xxx คือ IP ของเครื่อง RDX Gateway Client)  
2. หน้าจอ Browser จะแสดงชื่อ Service Function ออกมาให้เห็นถ้าเปิดได้ปกติ  
3. ในส่วนของ Application จะต้องระบุ End Point ไปที่ URL ตามข้อ 2  
4. สร้างข้อมูลเอกสารตาม Flat File Layout ที่ RDX Platform กำหนด  
6. เรียก Web Service ‘SendData’ ตาม parameter ที่กำหนด เพื่อส่งข้อมูลเข้า RDX Gateway Client  
5. ระบบ RDX Gateway Client จะ Scan ข้อมูลใน Database ของระบบเพื่อตรวจสอบว่ามีข้อมูลเข้ามาใหม่หรือไม่ ถ้ามีข้อมูลใหม่ ระบบจะทำการสร้างข้อมูล XML จากเอกสารชุดนั้น พร้อมลงลายมือชื่ออิเล็กทรอนิกส์  
6. ระบบ จะส่งข้อมูล ต่อไปให้กับ RDX RD Gateway เพื่อส่งข้อมูลให้กรมสรรพากรต่อไป  
7. เมื่อกรมสรรพากรได้รับข้อมูลแล้ว กรมสรรพากร จะส่งข้อมูลสถานะการส่งข้อมูลกลับมาให้ ว่าส่งผ่าน (Accept) หรือ ไม่ผ่าน (Reject) ระบบจะเก็บข้อมูลไว้ที่ Log Table  
8. เรียก Web Service ‘GetLog’ ตาม parameter ที่กำหนด เพื่อตรวจสอบ Response ต่างๆ ที่ตอบกลับมาในระบบ และ ‘CommitLog’ เพื่อยืนยันว่าได้รับข้อมูลแล้ว  
9. เมื่อข้อมูลที่ส่งให้กรมได้รับ Accept จากกรมสรรพากร ระบบลงลายมือชื่ออิเล็กทรอนิกส์ใน PDF File จากข้อ 4  
10. ระบบจะ Zip เอกสาร XML File และ PDF File พร้อมเข้ารหัส ไปเก็บไว้ใน RDX Secure Doc Server และ ส่งต่อให้ RDX Biz Exchange ต่อไป  
  
- **ข้อแนะนำ :** ควรสร้างหน้าจอสำหรับรับส่งข้อมูล และ หน้าจอสถานะข้อมูล ให้กับลูกค้าได้ทราบ ถึงรายการที่เกิดขึ้นในขั้นตอนต่าง ๆ  
  
### การตรวจสอบข้อมูล Response จากระบบ (Web Service)  
  
1. RDX Gateway Client จะทำการตรวจสอบ Response ต่างๆจาก RDX Platform ถ้ามี Response ใดๆกลับมา ไม่ว่าจะเป็น RDX RD Gateway / RDX Secure Docs / RDX Biz Exchange ระบบจะสร้าง Log Record ทุกครั้ง  
2. ทำการ Call GetLog() เพื่ออ่านข้อมูล Log.StatusRead < 2 ( Function นี้จะทำให้ Log.StatusRead เปลี่ยนจาก 0 เป็น 1 )  
3. ทำการ Loop Log Record ที่ได้ โดยการตรวจสอบค่า Log.ResType Field  
- **'ACCEPT'** : ผลการส่งข้อมูลไปที่กรมสรรพากรสำเร็จ กรมฯ ยืนยันการรับข้อมูล ตรวจสอบเลขรับข้อมูลที่ Log.PayLoad Field  
- **'REJECT'** : ผลการส่งข้อมูลไปที่กรมสรรพากรสำเร็จ กรมฯ แจ้งปัญหากลับมา ตรวจสอบ สาเหตุที่ REJECT จากข้อมูลที่ Log.PayLoad  
- **'ERROR'** : มี Error จากการทำรายการ ตรวจสอบ สาเหตุที่ Error จากข้อมูลที่ Log.PayLoad  
-**‘KeepDoc’** : เก็บข้อมูลลง RDX Secure Docs เรียบร้อย  
- **'BizX'** : ได้รับข้อมูลจากคู่ค้าผ่าน RDX Biz Exchange Server  
- Call GetDocX (Log.TaxNumber, Log.BranchCode, Log.RefNo) เพื่ออ่านข้อมูลเอกสารที่ได้รับมาสามารถตรวจสอบข้อมูลเอกสาร จากค่าที่ตอบกลับมา (Text Pipe)  
- Call GetPDFX(Log.TaxNumber, Log.BranchCode, Log.RefNo) เพื่ออ่านข้อมูล PDF ที่ Seller ส่งมา ให้ทำการแปลง Bese64 เป็น Binary เพื่อสร้าง PDF File  
4. ทำการ Call CommitLog() เพื่อปรับสถานะ Log.StatusRead จาก 1 เป็น 2

> Written with [StackEdit](https://stackedit.io/).


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkzMjIwNzg2OF19
-->