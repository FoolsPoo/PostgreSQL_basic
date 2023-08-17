# PostgreSQL Anonymization

PostgreSQL Anonymization เป็น Extension ที่นำมาใช้ในด้าน Data Masking เพื่อปิดบังข้อมูลในฐานข้อมูล เช่นเดียวกันกับ Faker แต่ทั้งนี้การใช้งานคำสั่งของ Anonymization จะแตกต่างจาก Fake เป็นอย่างมากทีเดียว

## Prepare

เราไม่จำเป็นต้องติดตั้งอะไรเพิ่มเติม เพราะตัวคอนเทนเนอร์ที่เราใช้อยู่นั้นเป็นของ PostgreSQL Anonymization อยู่แล้ว ซึ่งแน่นอนว่าเราสามารถใช้ชุดคำสั่งของ Anonymization ได้เลย ส่วนถ้าอยากหาวิธีติดตั้งทางอื่น เราสามารถคลิกดูลิงค์นี้ได้เลย [LINK](https://postgresql-anonymizer.readthedocs.io/en/stable/INSTALL/)

---

## Declare Masking Rule

การใช้งานคำสั่งปิดบังข้อมูลสามารถใช้ได้ด้วยชุดคำสั่ง Security Label ยกตัวอย่างคำสั่งตามด้านล่างเลย

```bash 
SECURITY LABEL FOR anon ON COLUMN player.name
  IS 'MASKED WITH FUNCTION anon.fake_last_name()';
```
```bash 
SECURITY LABEL FOR anon ON COLUMN player.id
  IS 'MASKED WITH VALUE NULL';
```

โดยการปิดบังข้อมูลใน Anonymization สามารถปิดบังข้อมูลได้หลายแบบ ที่ได้ลองทำไปมี

Destruction - การปิดบังข้อมูลโดยข้อมูลจะแสดงข้อความที่เรากำหนดไว้ 

```bash 
SECURITY LABEL FOR anon
  ON COLUMN users.address
  IS 'MASKED WITH VALUE ''CONFIDENTIAL'' ';
```

Adding Noise - เป็นการปรับค่าที่เป็น int หรือวันที่ให้ต่างจากของเดิมได้ อย่างเช่นการปรับค่า int ให้มากกว่าหรือน้อยกว่า 10%

คำสั่งที่ใช้จะเป็น 'anon.noise(original_value,ratio)' 

ส่วน original_value สามารถใส่ข้อมูลที่เป็น integer, a bigint, a double precision, เวลา, หรือวันที่ได้
ส่วน ratio จะเป็นการประกาศค่าว่าจะให้ค่าต่างจากเดิมเท่าไหร่ ถ้าค่าตัวเลขก็ใส่ตัวเลขไปได้เลย ส่วนถ้าวันที่ จำเป็นต้องประกาศหน่วยอย่างวัน เดือน หรือปีด้วย

Randomization - ส่วนนี้จะเป็นการที่ระบบจะสุ่มข้อมูลมาให้เลย โดยสามารถสุ่มได้หลายแบบตามที่กำหนด มีดังนี้

anon.random_date() - สุ่มวันที่
anon.random_date_between(d1,d2) - สุ่มวันที่ในระหว่าง d1 กับ d2
anon.random_int_between(i1,i2) - สุ่มตัวเลขในระหว่าง i1 กับ i2
anon.random_string(n) - สุ่มค่า Text โดยจะมีตัวอักษร n อยู่ด้วย
anon.random_zip() - สุ่มตัวเลข 5 หลัก
anon.random_phone(p) - สุ่มเบอร์มือถือ 8 หลัก โดยมี P เป็นคำนำหน้า

Faking - เป็นการปิดบังข้อมูลโดยใช้ข้อมูลปลอมที่อยู่ใน Container โดยสามารถปลอมแปลงข้อมูลประเภทดังกล่าวได้ มีดังนี้

anon.fake_email() - returns a valid email address
anon.fake_first_name() - returns a generic first name
anon.fake_last_name() - returns a generic last name

Partial Scrambling - เป็นการปิดบังข้อมูลบางส่วน โดยสามารถได้ 2 แบบ 

anon.partial('abcdefgh',1,'xxxx',3) จะแสดงออกมาเป็น 'axxxxfgh'
'abcdefgh' คือส่วนข้อความที่เราจะทำการปกปิดข้อมูล เราสามารถเขียนเป็นชื่อค
1 คือจำนวนของข้อมูลตัวหน้าที่เปิดเผย
'xxxx' คือตัวอักษรที่ใช้ปกปิดข้อมูล
3 คือจำนวนของข้อมูลตัวหลังที่เปิดเผย


anon.partial_email('daamien@gmail.com') จะแสดงออกมาเป็น 'da**@gm****.com'
ซึ่งคำสั่งนี้ไว้ใช้ปกปิดข้อมูลอีเมลโดยเฉพาะ

Generalization - เป็นการปกปิดข้อมูลข้อมูลโดยใช้ค่าประมาณ การใช้คำสั่งจะเป็นแบบนี้

```bash 
CREATE VIEW anonymized_patient AS
SELECT
    'REDACTED' AS lastname,
    anon.generalize_int4range(zipcode,100) AS zipcode,
    anon.generalize_tsrange(birth,'decade') AS birth
    disease
FROM patients;
```

โดยผลลัพธ์จะเป็นแบบนี้

```bash 
SELECT * FROM anonymized_patient;
 lastname |   zipcode     |           birth             |    disease
----------+---------------+-----------------------------+---------------
 REDACTED | [47600,47700) | ["1970-01-01","1980-01-01") | Heart Disease
 REDACTED | [47600,47700) | ["1950-01-01","1960-01-01") | Heart Disease
 REDACTED | [47600,47700) | ["1980-01-01","1990-01-01") | Heart Disease
 REDACTED | [47900,48000) | ["1990-01-01","2000-01-01") | Flu
 REDACTED | [47900,48000) | ["1990-01-01","2000-01-01") | Heart Disease
 REDACTED | [47900,48000) | ["1960-01-01","1970-01-01") | Cancer
 REDACTED | [47600,47700) | ["1970-01-01","1980-01-01") | Heart Disease
 REDACTED | [47600,47700) | ["1970-01-01","1980-01-01") | Cancer
 REDACTED | [47600,47700) | ["1990-01-01","2000-01-01") | Cancer
```

---

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

---