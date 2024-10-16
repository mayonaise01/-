# Mini Project: "เขื่อนเพื่อนรัก ช่วยเราด้วย"

## Research Question
1. ข้อมูลน้ำของเขื่อนในประเทศไทยจากปี 2014-2024 สามารถบอกการเกิดน้ำท่วมได้หรือไม่
2. ข้อมูลน้ำของเขื่อนในประเทศไทยจากปี 2014-2024 มีสัญญาณใดบ่งบอกตวามเป็นไปได้ของการเกิดน้ำท่วมบ้าง
    2.1. ทดสอบข้อมูลน้ำภาคเหนือปี 2017-2018 เพื่อหาสัญญาณว่าจะเกิดน้ำท่วมหรือไม่
    2.2. ทดสอบข้อมูลน้ำภาคเหนือปี 2020-2021 เพื่อหาสัญญาณว่าจะเกิดน้ำท่วมหรือไม่
3. ข้อมูลน้ำภาคเหนือปี 2024 มีสัญญาณอะไรบ่งบอกว่าจะเกิดน้ำท่วมในช่วงปลายปี
4. (จากข้อ2)ถ้ามีจะสามารถใช้วิธีใดเพื่อปรับปรุงการบริหารน้ำ เพื่อป้องกันการเกิดน้ำท่วมได้

## Data Jouney

### 1. การนำเข้าข้อมูลข้อมูลน้ำของเขื่อนในประเทศไทยจากปี 2014-2024
เก็บข้อมูลรายวันของเขื่อนจากเว็บไซต์ของกรมชลประทาน โดยการใช้ request GET ไปที่ REST APIlink: https://app.rid.go.th/reservoir/api/dam/public/{date}
จากนั้นจัด format ให้อยู่ในรูปที่ต้องการโดย 1 row ประกอบไปด้วย:
* id - รหัสของเขื่อน
* name - ชื่อของเขื่อน
* owner - หน่วยงานที่รับผิดชอบ
* capacity - ความจุที่สามารถรับได้ทั้งหมด
* storage - ความจุ ณ ขณะที่เก็บข้อมูล
* active_storeage - ปริมาณน้ำที่ใช้ได้จากความจุ ณ ขณะที่เก็บข้อมูล
* daed_storage - ปริมาณน้ำที่ไม่สามารถใช้ได้จากความจุ ณ ขณะที่เก็บข้อมูล
* volume - ปริมาตรของเขื่อน
* percent_storage - เปอร์เว็นความจุ ณ ขณะที่เก็บข้อมูล
* inflow - ปริมาณน้ำไหลเข้าเขื่อน
* outflow - ปริมาณน้ำไหลออกขื่อน
* date - วันที่เก็บข้อมูล


เพื่อให้สามารถดูภาพรวมได้ดียิ่งขึ้นจึงนำข้อมูลข้างต้นไปรวมกับภูมิภาคโดยลิ้งกันด้วยชื่อเขื่อน
* name - ชื่อของเขื่อน
* region - ภูมิภาคประเทไทย
  
เมื่อรวมข้อมูลรายวันของทุกเขื่อนตั้งแต่ปี 2014-2024 และภูมิภาคประเทศไทยจะได้ attribute ทั้งหมด 244292 rows/ 14 columns

![image](https://github.com/user-attachments/assets/8b6f7aad-4ac0-471c-8723-e074b975dbef)

สังเกตว่า volume และ percentage_storage หายไปบางส่วนจึงคาดว่าอาจมีความผิดพลาดในการอ่านค่า sensor ในวันนั้นๆ จึงทำการแทนค่าด้วยค่าเฉลี่ยของเขื่อนนั้นๆแทน

![image](https://github.com/user-attachments/assets/c95a9486-e6ad-424b-a69d-9eb40385b5af)

### 2. ดูข้อมูลภาพรวมเที่มีนัยสำคัญ เพื่อนำมาตั้งเป็นเป้าหมายการวิเคราะห์
ทำการนำข้อมูลตลอด10ปี(2014-2024) มาดูความแตกต่างระหว่าง inflow และ outflow เพื่อหาช่วงเวลาที่มีนัยสำคัญดังต่อไปนี้
* ข้อมูลทั่วประเทศ
  
![image](https://github.com/user-attachments/assets/e0b062f8-f043-4421-96b8-4abd1550da47)

* ข้อมูลภาคกลาง

![image](https://github.com/user-attachments/assets/d21eaff6-64db-4903-a277-3ce627ede74b)

* ข้อมูลภาคตะวันตก

![image](https://github.com/user-attachments/assets/e20f5498-b6a1-4273-ae44-bf528f6b252a)

* ข้อมูลภาคตะวันออก

![image](https://github.com/user-attachments/assets/fadce4a0-f032-4bc7-aed6-02485794d889)

* ข้อมูลภาคตะวันออกเฉียงเหนือ

![image](https://github.com/user-attachments/assets/c05e0b7c-1d86-46b1-b43b-e8b4038aa38a)

* ข้อมูลภาคเหนือ

![image](https://github.com/user-attachments/assets/0aa746a0-08a2-43b6-8e98-de11dab862a7)

เมื่อแยกข้อมูลทั่วประเทศออกมาเป็นแต่ละภูมิภาค ทำให้เห็นว่าปริมาณน้ำในภาคเหนือช่วงปี2017-2018 มีมากกว่าภาคอื่นๆอยากมีนัยสำคัญและ
จากการหาข้อมูลเพิ่มเติมพบว่าช่วงปีดังกล่าวมีพายุฝนใหญ่พัดผ่านภาคเหนือหรือตะวันออกเฉียงเหนือถึง 5ลูกด้วยกัน
### ปี2017/2560
* น้ำท่วมจากอิทธิพลพายุ "ตาลัส" (TALAS) 15-19 กรกฎาคม 2560
* น้ำท่วมจากอิทธิพลพายุ "เซินกา" (SONCA) 24-31 กรกฎาคม 2560
* น้ำท่วมจากอิทธิพลพายุ "ทกซูริ" (DOKSURI) 14-19 กันยายน 2560
### ปี2018/2561
* น้ำท่วมจากอิทธิพลพายุโซนร้อน "เซินติญ" (SON-TINH) และลมมรสุมตะวันตกเฉียงใต้ กรกฎาคม-สิงหาคม 2561
* น้ำท่วมจากอิทธิพลพายุโซนร้อน "เบบินคา" (BEBINCA) 16-18 สิงหาคม 2561

![image](https://github.com/user-attachments/assets/e71781a9-1c9a-481d-ae68-a6beae45a2ac)
![image](https://github.com/user-attachments/assets/c37b11bf-5c5c-4c90-9d1f-e2a29d95e2ea)

reference site: https://tiwrm.hii.or.th/web/index.php/flood-history.html

กลุ่มของเราจึงจะนำเอาเคสน้ำท่วมที่เกิดขึ้นมาศึกษาการบริหารจัดการน้ำของเขื่อนเหล่านี้ เพื่อหาคำตอบตาม research question ที่เราได้ตั้งไว้

### 3. ค้นหาสัญญาณต่างๆที่อาจบ่งบอกการเกืดน้ำท่วมล่วงหน้า

Signal 1 : Inflow and Outflow Over Time

เขื่อนภาคเหนือปี 2017

![Signal1 2017](https://github.com/user-attachments/assets/e0900f42-e061-4c2b-874a-36889e41c682)

เขื่อนภาคเหนือปี 2018

![Signal1 2018](https://github.com/user-attachments/assets/575a7806-7529-4121-8e50-5685a7d198ad)

เขื่อนภาคเหนือปี 2020

![Signal 2020](https://github.com/user-attachments/assets/359bd596-78e2-4bde-8d66-9ff2ceb7f073)

เขื่อนภาคเหนือปี 2021

![Signal 2021](https://github.com/user-attachments/assets/69d5d2bd-e7b8-4cc1-899b-2d14ca0203be)

เขื่อนภาคเหนือปี 2024

![Signal1 2024](https://github.com/user-attachments/assets/e86bf1dc-9b8b-4c13-a615-78eb1a1ad71b)



Signal 2: Storage Percentage Over Time

เขื่อนภาคเหนือปี 2017

![Signal2 2017](https://github.com/user-attachments/assets/aa67cc4d-96ea-4959-ab1d-9bf3d2c45e80)

เขื่อนภาคเหนือปี 2018

![Signal2 2018](https://github.com/user-attachments/assets/bbdfca9a-2534-49a8-8e4f-8f54912195b5)

เขื่อนภาคเหนือปี 2020

![Signal2 2020](https://github.com/user-attachments/assets/6c8a8195-5908-42ef-ae77-c92050b335ac)

เขื่อนภาคเหนือปี 2021

![Signal2 2021](https://github.com/user-attachments/assets/d53cb905-8df1-4813-9d65-a3894cef276f)

เขื่อนภาคเหนือปี 2024

![Signal2 2024](https://github.com/user-attachments/assets/81c8f376-f60b-4b06-a199-81c7859a8ff6)


## บทสรุป
