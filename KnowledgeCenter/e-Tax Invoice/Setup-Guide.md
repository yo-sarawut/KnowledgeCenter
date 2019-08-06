คู่มือการติดตั้งโปรแกรม RDX Gateway Client
================

<![endif]-->

# 1. ติดตั้งโปรแกรม RDX Gateway Client

## <![if !supportLists]>1.1 <![endif]>ติดตั้ง Microsoft SQL Server ManageMent Studio

ให้กำหนด Security Logins เป็น User: ecssystem Password: ecssystem

## <![if !supportLists]>1.2 <![endif]>Copy Floder ECS_RD ไว้ Drive C:\

## <![if !supportLists]>1.3 <![endif]>ติดตั้ง Database ECS_DATA_RD

ติดตั้ง Database จาก ECS_DATA_RD.bak โดยวิธีการ Restore Files and File Groups

> Destination to restore ระบุ To database เป็น “ECS_DATA_RD”

> From Device เลือกไฟล์ ECS_DATA_RD.bak ที่อยู่ใน Folder C:\ECS_RD\DATA

## <![if !supportLists]>1.4 <![endif]>Config groupdb.ini

C:\ECS_RD\DATA\groupdb.ini แก้ไขข้อมูลชื่อบริษัท , Member Code และ ConnectionString ตามรูป

# 2. ติดตั้ง Certificate

## <![if !supportLists]>2.1 <![endif]>ติดตั้ง Cert ประเภท TOKEN จาก TDID

### 2.1.1 ติดตั้ง Driver Safenet

ติดตั้ง Driver Safenet ที่ C:\ECS_RD\Utility\SafeNetAuthenticationClient-x64-8.0-SP2.msi หรือดาวน์โหลดได้ที่ [http://www.thaidigitalid.com/hardware/](http://www.thaidigitalid.com/hardware/)


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjEwOTY0NTk5OSwtMTY4NDMyNTEwMF19
-->