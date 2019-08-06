คู่มือการติดตั้งโปรแกรม RDX Gateway Client
================
## ติดตั้งโปรแกรม RDX Gateway Client 
1. ติดตั้ง Microsoft SQL Server ManageMent Studio
2. Copy Floder ECS_RD ไว้ Drive C:\
3. ติดตั้ง Database ECS_DATA_RD ติดตั้ง Database จาก ECS_DATA_RD.bak โดยวิธีการ Restore Files and File Groups 
     > Destination to restore ระบุ To database เป็น “ECS_DATA_RD” 
     > From Device เลือกไฟล์ ECS_DATA_RD.bak ที่อยู่ใน Folder C:\ECS_RD\DATA
 
 4. Config groupdb.ini
 
 >  ![image](image)

>C:\ECS_RD\DATA\groupdb.ini แก้ไขข้อมูลชื่อบริษัท , Member Code และ ConnectionString ตามรูป

## ติดตั้ง  Certificate
1. ติดตั้ง Cert ประเภท TOKEN จาก TDID
	- ติดตั้ง Driver Safenet ติดตั้ง Driver Safenet ที่ C:\ECS_RD\Utility\SafeNetAuthenticationClient-x64-8.0-SP2.msi หรือดาวน์โหลดได้ที่  http://www.thaidigitalid.com/hardware/

 >  ![image](image)

 >  ![image](image)


 >  ![image](image)

 >  ![image](image)

 >  ![image](image)


<!--stackedit_data:
eyJoaXN0b3J5IjpbMjUxMDQ2MDg3LDEzMTkzODEzMDgsLTE2OD
QzMjUxMDBdfQ==
-->