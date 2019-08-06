คู่มือการติดตั้งโปรแกรม RDX Gateway Client
================

1. ติดตั้งโปรแกรม RDX Gateway Client 
1.1	ติดตั้ง Microsoft SQL Server ManageMent Studio
   	ให้กำหนด Security Logins เป็น User: ecssystem Password: ecssystem
1.2	Copy Floder ECS_RD ไว้ Drive C:\
1.3	ติดตั้ง Database ECS_DATA_RD
ติดตั้ง Database จาก ECS_DATA_RD.bak โดยวิธีการ Restore Files and File Groups 
     > Destination to restore ระบุ To database เป็น “ECS_DATA_RD” 
     > From Device เลือกไฟล์ ECS_DATA_RD.bak ที่อยู่ใน Folder C:\ECS_RD\DATA
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjM0MDcwNzA3LDEzMTkzODEzMDgsLTE2OD
QzMjUxMDBdfQ==
-->