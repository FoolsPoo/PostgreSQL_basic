# PostgreSQL Anonymization

PostgreSQL Anonymization เป็น Extension ที่นำมาใช้ในด้าน Data Masking เพื่อปิดบังข้อมูลในฐานข้อมูล เช่นเดียวกันกับ Faker แต่ทั้งนี้การใช้งานคำสั่งของ Anonymization จะแตกต่างจาก Fake เป็นอย่างมากทีเดียว

## Prepare

เราไม่จำเป็นต้องติดตั้งอะไรเพิ่มเติม เพราะตัวคอนเทนเนอร์ที่เราใช้อยู่นั้นเป็นของ PostgreSQL Anonymization อยู่แล้ว ซึ่งแน่นอนว่าเราสามารถใช้ชุดคำสั่งของ Anonymization ได้เลย ส่วนถ้าอยากหาวิธีติดตั้งทางอื่น เราสามารถคลิกดูลิงค์นี้ได้เลย [LINK](https://postgresql-anonymizer.readthedocs.io/en/stable/INSTALL/)

--

## Declare Masking Rule

การใช้งานคำสั่งปิดบังข้อมูลของ Anonymization 

```bash 
CREATE TABLE player( id SERIAL, name TEXT, points INT);
```

--

## Dynamic Masking

ในที่นี้ จะเป็นการใช้งาน Data Masking ปิดบังข้อมูลกับผู้ใช้อื่นที่เราไม่ต้องการให้เขารู้ วิธีการนั้นมีดังนี้

1. เปิดใช้งาน Dynamic Data Masking Engine โดยใช้ 2 คำสั่งนี้

```bash 
CREATE EXTENSION IF NOT EXISTS anon CASCADE;
```
```bash 
SELECT anon.start_dynamic_masking();
```

2. สร้างผู้ใช้ใหม่ขึ้นมา จากนั้นเราสามารถใส่คำสั่งตามคำสั่งที่ 2 เพื่อให้ผู้ใช้คนนั้นเห็นข้อมูลในสภาพที่ถูกปิดบังได้

```bash 
CREATE ROLE skynet LOGIN;
```
```bash 
SECURITY LABEL FOR anon ON ROLE skynet IS 'MASKED';
```

3. ทดลองใช้คำสั่งปิดบังข้อมูล 

--