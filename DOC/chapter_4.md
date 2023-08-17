# PostgreSQL Anonymization

PostgreSQL Anonymization เป็น Extension ที่นำมาใช้ในด้าน Data Masking เพื่อปิดบังข้อมูลในฐานข้อมูล เช่นเดียวกันกับ Faker แต่ทั้งนี้การใช้งานคำสั่งของ Anonymization จะแตกต่างจาก Faker อยู่หลายอย่าง

## Prepare

เราไม่จำเป็นต้องติดตั้งอะไรเพิ่มเติม เพราะตัวคอนเทนเนอร์ที่เราใช้อยู่นั้นเป็นของ PostgreSQL Anonymization อยู่แล้ว ซึ่งแน่นอนว่าเราสามารถใช้ชุดคำสั่งของ Anonymization ได้เลย ส่วนถ้าอยากหาวิธีติดตั้งทางอื่น เราสามารถคลิกดูลิงค์นี้ได้เลย [LINK](https://postgresql-anonymizer.readthedocs.io/en/stable/INSTALL/)

---

## Declare Masking Rule

การใช้งานคำสั่งปิดบังข้อมูลสามารถใช้ได้ด้วยชุดคำสั่ง Security Label 

ตัวอย่าง

```bash 
SECURITY LABEL FOR anon ON COLUMN player.name
  IS 'MASKED WITH FUNCTION anon.fake_last_name()';
```
```bash 
SECURITY LABEL FOR anon ON COLUMN player.id
  IS 'MASKED WITH VALUE NULL';
```

โดยการปิดบังข้อมูลใน Anonymization สามารถปิดบังข้อมูลได้หลายแบบ 

เช่น

Destruction - การปิดบังข้อมูลโดยข้อมูลจะแสดงข้อความที่ถูกกำหนดไว้ 

```bash 
SECURITY LABEL FOR anon
  ON COLUMN users.address
  IS 'MASKED WITH VALUE ''CONFIDENTIAL'' ';
```

Adding Noise - เป็นการปรับค่าที่เป็น int หรือวันที่ให้ต่างจากของเดิม เช่นการปรับค่า int ให้มากกว่าหรือน้อยกว่า 10%

คำสั่งที่ใช้จะเป็น 'anon.noise(original_value,ratio)' 

original_value สามารถใส่ข้อมูลที่เป็น integer, a bigint, a double precision, เวลา, หรือวันที่ 

ratio เป็นการประกาศค่าว่าจะให้ค่าต่างจากเดิมเท่าไหร่ ค่าเป็นตัวเลขให้ใส่ตัวเลขที่ต้องการ ส่วนวันที่ จำเป็นต้องประกาศหน่วย วัน เดือน หรือปี

Randomization - จะเป็นการสุ่มข้อมูลจากระบบ โดยสามารถสุ่มได้ตามที่กำหนด 

เช่น
anon.random_date() - สุ่มวันที่

anon.random_date_between(d1,d2) - สุ่มวันที่ในระหว่าง d1 กับ d2

anon.random_int_between(i1,i2) - สุ่มตัวเลขในระหว่าง i1 กับ i2

anon.random_string(n) - สุ่มค่า Text โดยจะมีตัวอักษร n อยู่ด้วย

anon.random_zip() - สุ่มตัวเลข 5 หลัก

anon.random_phone(p) - สุ่มเบอร์มือถือ 8 หลัก โดยมี P เป็นคำนำหน้า

Faking - เป็นการปิดบังข้อมูลโดยใช้ข้อมูลปลอมที่อยู่ใน Container โดยสามารถปลอมแปลงข้อมูลประเภทดังกล่าวได้ 

เช่น

anon.fake_email() - returns a valid email address

anon.fake_first_name() - returns a generic first name

anon.fake_last_name() - returns a generic last name

Generic Hashing - เป็นการปิดบังข้อมูลโดยใช้อัลกอริทึ่มสร้างข้อมูลที่ยาวและซับซ้อนขึ้น

```bash 
anon.hash(value)
```

หลังจากใช้แล้ว ข้อมูลจะแสดงออกมาดังนี้

```bash
SELECT anon.hash('bob');
                                  hash
----------------------------------------------------------------------------------------------------------------------------------
95b6accef02c5a725a8c9abf19ab5575f99ca3d9997984181e4b3f81d96cbca4d0977d694ac490350e01d0d213639909987ef52de8e44d6258d536c55e427397
```
ref: https://postgresql-anonymizer.readthedocs.io/en/stable/masking_functions/

---

Partial Scrambling - เป็นการปิดบังข้อมูลบางส่วน โดยสามารถได้ 2 แบบ 

anon.partial('abcdefgh',1,'xxxx',3) จะแสดงผลเป็น 'axxxxfgh'

'abcdefgh' คือส่วนข้อความที่ต้องการปกปิดข้อมูล สามารถเขียนเป็นชื่อ column เพื่อปกปิดข้อมูลทั้งหมดภายใน column

1 คือจำนวนของข้อมูลตัวหน้าที่เปิดเผย

'xxxx' คือตัวอักษรที่ใช้ปกปิดข้อมูล

3 คือจำนวนของข้อมูลตัวหลังที่เปิดเผย

anon.partial_email('daamien@gmail.com') จะแสดงผลเป็น 'da**@gm****.com' คำสั่งนี้ไว้ใช้ปกปิดข้อมูลอีเมลโดยเฉพาะ

---
Generalization - เป็นการปกปิดข้อมูลข้อมูลโดยใช้ค่าประมาณ 

anon.generalize_int4range(value, step) ใช้สำหรับข้อมูล integer

anon.generalize_int8range(value, step) ใช้สำหรับข้อมูล bigint

anon.generalize_numrange(value, step) ใช้สำหรับข้อมูล numeric

value คือค่าที่เราจะปกปิดข้อมูล ส่วน step คือความห่างของข้อมูล


ตัวอย่างคำสั่ง

```bash 
CREATE VIEW anonymized_patient AS
SELECT
    'REDACTED' AS lastname,
    anon.generalize_int4range(zipcode,100) AS zipcode,
    anon.generalize_tsrange(birth,'decade') AS birth
    disease
FROM patients;
```
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
reference: https://postgresql-anonymizer.readthedocs.io/en/stable/masking_functions/

---

ยกเลิกการปกปิดข้อมูลที่ทำการ Masking เพื่อแสดงผลเป็นข้อมูลต้นฉบับ

```bash 
SECURITY LABEL FOR anon ON COLUMN player.name IS NULL;
```

สามารถลบข้อมูลทั้งหมด โดยคำสั่งด้านล่าง

```bash 
SELECT anon.remove_masks_for_all_columns();
```

---
## Static Masking

คือการปกปิดข้อมูลโดยการนำข้อมูลเข้าไปแทนที่ข้อมูลต้นฉบับ **โดยที่ข้อมูลต้นฉบับจะหายไปเลย เพราะงั้นวิธีการนี้ควรใช้อย่างระมัดระวัง**

โดยคำสั่งที่ใช้ในการทำ Static Masking คือanon.anonymize_database()

ยกตัวอย่างตารางข้อมูล customer ที่นำมาใช้

```bash 
 id  |   full_name      |   birth    |    employer   | zipcode | fk_shop
-----+------------------+------------+---------------+---------+---------
 911 | Chuck Norris     | 1940-03-10 | Texas Rangers | 75001   | 12
 112 | David Hasselhoff | 1952-07-17 | Baywatch      | 90001   | 423
```

1. เปิดใช้งาน Extension 

```bash 
CREATE EXTENSION IF NOT EXISTS anon CASCADE;
SELECT anon.init();
```

2. ใช้งานคำสั่งปกปิดข้อมูล

ปกปิดข้อมูลชื่อ นามสกุลโดยใช้ข้อมูลชื่อ นามสกุลปลอม
```bash 
SECURITY LABEL FOR anon ON COLUMN customer.full_name
IS 'MASKED WITH FUNCTION anon.fake_first_name() || '' '' || anon.fake_last_name()';
```
ปกปิดข้อมูลชื่อบริษัทโดยใช้ข้อมูลชื่อบริษัทปลอม
```bash 
SECURITY LABEL FOR anon ON COLUMN customer.employer
IS 'MASKED WITH FUNCTION anon.fake_company()';
```
ปกปิดข้อมูลรหัสไปรษณีย์โดยใช้ข้อมูลรหัสไปรษณีย์ปลอม
```bash 
SECURITY LABEL FOR anon ON COLUMN customer.zipcode
IS 'MASKED WITH FUNCTION anon.random_zip()';
```

3. แทนที่ข้อมูลต้นฉบับด้วยข้อมูลที่ถูกปกปิด

```bash
SELECT anon.anonymize_database();
```

5. เช็คข้อมูล

```bash
SELECT * FROM customer;
```

จะได้ค่าดังนี้

```bash 
 id  |  full_name  |   birth    |      employer       | zipcode | fk_shop
-----+-------------+------------+---------------------+---------+---------
 911 | jesse Kosel | 1940-03-10 | Marigold Properties | 62172   |      12
 312 | leolin Bose | 1952-07-17 | Inventure           | 20026   |     423
```

---

## Dynamic Masking

เป็นการใช้งาน Data Masking ปิดบังข้อมูลกับผู้ใช้อื่นที่เราไม่ต้องการให้รู้ 

```bash 
 id | firstname | lastname |   phone
----+----------+----------+------------
 T1 | Sarah    | Conor    | 0609110911
(1 row)
```

1. เปิดใช้งาน Dynamic Data Masking Engine 

```bash 
CREATE EXTENSION IF NOT EXISTS anon CASCADE;
```
```bash 
SELECT anon.start_dynamic_masking();
```

2. สร้างผู้ใช้ใหม่ และใส่คำสั่ง เพื่อให้ผู้ใช้นั้นเห็นข้อมูลในสภาพที่ถูกปิดบังแล้ว

```bash 
CREATE ROLE skynet LOGIN;
```
```bash 
SECURITY LABEL FOR anon ON ROLE skynet IS 'MASKED';
```

3. ทดลองใช้คำสั่งปิดบังข้อมูลตัวอย่าง

ทดลองการปกปิดข้อมูลชื่อโดยใช้ข้อมูลนามสกุลปลอมแบบสุ่ม
```bash 
SECURITY LABEL FOR anon ON COLUMN people.name
IS 'MASKED WITH FUNCTION anon.random_last_name()';
```
ด้านล่างเป็นการปกปิดข้อมูลเบอร์มือถือโดยใช้ตัว * ปิดข้อมูลให้เหลือ 2 ตัวหน้าและ 2 ตัวหลัง

```bash 
SECURITY LABEL FOR anon ON COLUMN people.phone
IS 'MASKED WITH FUNCTION anon.partial(phone,2,$$******$$,2)';
```

4. ดูข้อมูลในฐานะ skynet 

```bash 
\c - skynet
```
```bash 
SELECT * FROM people;
 id | firstname | lastname  |   phone
----+----------+-----------+------------
 T1 | Sarah    | Stranahan | 06******11
(1 row)
```

reference: [LINK](https://postgresql-anonymizer.readthedocs.io/en/stable/declare_masking_rules/#removing-a-masking-rule)
---

## Localized fake data

อันนี้เป็นกรณีที่เราอยากจะเปลี่ยนข้อมูลปลอมเดิมเพื่อนำมาใช้กับคำสั่ง anon.fake เพื่อปกปิดข้อมูลใน PostgreSQL เริ่มแรกเราเช็คว่ามีไฟล์ populate.py ใน container หรือไม่

โดยไฟล์ที่เก็บไว้อยู่ใน  /usr/share/postgresql/14/extension/anon/populate.py

กรณีที่ไม่มี สามารถดาวน์โหลดได้ในลิงค์นี้ [LINK](https://gitlab.com/dalibo/postgresql_anonymizer/-/tree/master/python?ref_type=heads) และนำไฟล์ดังกล่าวเข้าไปใน container
โดยแนะนำว่าให้นำไฟล์เข้าไปในตำแหน่งไฟล์นี้ '/usr/share/postgresql/14/extension/anon/populate.py'

ต่อไปจะมาทดลองการใช้งาน สมมุติว่าเราอยากจะสร้างไฟล์ข้อมูลที่มีชื่อจริงอยู่ 500 ชื่อ ให้ใช้คำสั่งนี้ใน container เลย

```bash 
$ python3 $(pg_config --sharedir)/extension/anon/populate.py --table first_name \
                                                             --locales th_TH \
                                                             --lines 500
```
table เป็นการประกาศว่าจะสร้างข้อมูลอะไร
locales เป็นการประกาศว่าต้องการข้อมูลภาษาอะไรและประเทศอะไร โดยถ้าใช้ภาษาไทย จะเป็น th_TH
lines เป็นการกำหนดว่าจะสร้างจำนวนข้อมูลในไฟล์เท่าไหร่

หลังใช้คำสั่งแล้ว ระบบจะแสดงข้อมูลชื่อจริง 500 ชื่อ โดยจะมีกำกับลำดับตัวเลขเริ่มตั้งแต่ 0 อันนี้คือเราสามารถสร้างข้อมูลปลอมได้สำเร็จแล้ว

ต่อไปก็มาลองใช้งานใน postgresql วิธีจะมีดังนี้

1. ลบข้อมูลเดิมภายในตาราง anon คอลัมน์ first_name ออกก่อน โดยใช้คำสั่งนี้

```bash 
TRUNCATE anon.first_name;
```

2. นำข้อมูลที่สร้างผ่านคำสั่ง python มาคัดลอกลงในตาราง anon คอลัมน์ first_name โดยใช้คำสั่งนี้

```bash
COPY anon.first_name
FROM
PROGRAM 'python3 /usr/share/postgresql/14/extension/anon/populate.py --table first_name --locales th_TH --lines 500';
```

3. ตั้งค่าลำดับของข้อมูลโดยใช้คำสั่งนี้

```bash
SELECT setval('anon.first_name_oid_seq', max(oid))
FROM anon.first_name;
```

4. จัดลำดับข้อมูลโดยจัดตามเลขลำดับที่อยู่ในข้อมูลโดยใช้คำสั่ง

```bash
CLUSTER anon.first_name;
```

ที่นี้เราสามารถทดลองใช้งานคำสั่งปกปิดชื่อจริงเพื่อดูว่าข้อมูลที่นำเข้าไปนั้นขึ้นมาปกปิดข้อมูลให้หรือไม่

```bash
SECURITY LABEL FOR anon ON COLUMN customer.first_name
IS 'MASKED WITH FUNCTION anon.fake_first_name()';
```

---