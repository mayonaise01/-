# Mini Project: "เขื่อนเพื่อนรัก ช่วยเราด้วย"

## Research Question
1. ข้อมูลน้ำของเขื่อนในประเทศไทยจากปี 2014-2024 สามารถบอกการเกิดน้ำท่วมได้หรือไม่
2. ข้อมูลน้ำของเขื่อนในประเทศไทยจากปี 2014-2024 มีสัญญาณใดบ่งบอกตวามเป็นไปได้ของการเกิดน้ำท่วมบ้าง
   * 2.1. ทดสอบข้อมูลน้ำภาคเหนือปี 2017-2018 เพื่อหาสัญญาณว่าจะเกิดน้ำท่วมหรือไม่
   * 2.2. ทดสอบข้อมูลน้ำภาคเหนือปี 2020 เพื่อหาสัญญาณว่าจะเกิดน้ำท่วมหรือไม่
3. ข้อมูลน้ำภาคเหนือปี 2024 มีสัญญาณอะไรบ่งบอกว่าจะเกิดน้ำท่วมในช่วงปลายปี

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
  
เมื่อรวมข้อมูลรายวันของทุกเขื่อนตั้งแต่ปี 2014-2024 และภูมิภาคประเทศไทยจะได้ attribute ทั้งหมด 124635 rows/ 12 columns
* figure 1
  
![image](https://github.com/user-attachments/assets/2b96ec44-aef4-4e3a-afd3-6d533a3460e6)

สังเกตว่าจาก figure 1, volume percent_storage inflow และoutflow หายไปบางส่วนจึงคาดว่าอาจมีความผิดพลาดในการอ่านค่า sensor ในวันนั้นๆ จึงทำการ drop ดาต้าขอเขื่อนนั้นนะวันนั้น เนื่องด้วยเหตุผลว่าหากเขื่อนนั้นมีปริมาณน้ำล้น/ท่วมบ่อย การนำค่าเฉลี่ยมาแทนค่าที่หายไปอาจจะส่งผลให้วันนั้นมีค่าสูงผิดจากวันก่อนหน้าและวันถัดไป

จากนั้นยังพบข้อมูลบางส่วนที่เป็น outlier เช่น มีvolumeและ percent_storage มากกว่าค่าเฉลี่ยของเขื่อนนั้นๆถึง 500%, ข้อมูลบางส่วนที่มี inflow > 100k และข้อมูล outflow > 200 ทำให้ต้อง drop ข้อมูลส่วนนี้ ดังนั้นจะเหลือข้อมูลที่ใช้ได้ 79751 rows/ 12 columns

![image](https://github.com/user-attachments/assets/f24eb39b-4c1f-4bb7-b210-fbbbf5268e65)


### 2. ดูข้อมูลภาพรวมเที่มีนัยสำคัญ เพื่อนำมาตั้งเป็นเป้าหมายการวิเคราะห์
ทำการนำข้อมูลตลอด10ปี(2014-2024) มาดูความแตกต่างระหว่าง inflow และ outflow เพื่อหาช่วงเวลาที่มีนัยสำคัญดังต่อไปนี้
* ข้อมูลทั่วประเทศ
  
![image](https://github.com/user-attachments/assets/d5077d96-a8ae-4300-beda-a12d1bcb4efc)

เมื่อแยกข้อมูลทั่วประเทศออกมาเป็นแต่ละภูมิภาค ทำให้เห็นว่าปริมาณน้ำในภาคเหนือช่วงปี2017-2018 และปี2021-2022มีมากกว่าปีอื่นอย่างมีนัยสำคัญ เพื่อที่จะตอบได้ว่าปี2024 มีน้ำท่วมจริงโดยไม่ให้มี bias มาเกี่ยวข้องเราจึงจะโฟกัสไปที่ 2017-2018 จากการหาข้อมูลเพิ่มเติมพบว่าช่วงปีดังกล่าวมีพายุฝนใหญ่พัดผ่านภาคเหนือหรือตะวันออกเฉียงเหนือถึง 5ลูกด้วยกัน
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
จากการสรุปภาพรวมของข้อมูลทำให้เห็นว่าโซนที่น่าสนใจภาคเหนือ ส่วนช่วงที่น่าสนใจคือ 
* ปี2017-2018 เป็นตัวอย่างของปี่ที่มีน้ำท่วมใหญ่ในหลายพื้นที่
* ปี2020 เป็นตัวอย่างของปีที่ไม่มีน้ำท่วมใหญ่ๆ
เราจึงจะมาทำการหา signal ที่อาจบงชี้สัญญาณที่อาจจะมีน้ำท่วมจากขอมูลตัวอย่างสองกลุ่มนี้

### Signal 1 : Inflow and Outflow Over Time
นำข้อมูลของปริมาณน้ำเข้าเขื่อน(Inflow) และน้ำที่ระบายออกจากเขื่อน(Outflow) ตลอดทั้งปีของปี 2017,2018,2020 มาดูสัดส่วนกันเพื่อเปรียบเทียบระหว่างปีที่น้ำท่วมและปีที่น้ำไม่ท่วมตามภาพต่อไปนี้

* เขื่อนภาคเหนือปี 2017

![Signal1 2017](https://github.com/user-attachments/assets/0f40d88f-af54-479b-81ce-92b2f2bc3ba9)

* เขื่อนภาคเหนือปี 2018

![Signal1 2018](https://github.com/user-attachments/assets/ee944b5c-0a6a-478d-bbc3-96f9ec3700fe)

* เขื่อนภาคเหนือปี 2020

![Signal1 2020](https://github.com/user-attachments/assets/8b8226f7-8968-4314-8e35-feffd0a89f65)

* เขื่อนภาคเหนือปี 2024

![Signal1 2024](https://github.com/user-attachments/assets/7a394102-1395-4328-a5b1-bcff1307480d)


### Signal 2: Storage Percentage Over Time
นำข้อมูลเปอร์เซนต์ของปริมาณน้ำในเขื่อนตลอดปี 2017,2018,2020 มาเปรียบเทียบรูปแบบของปริมาณคงเหลือระหว่างปีที่น้ำท่วมและไม่ท่วมดังรูปต่อไปนี้

* เขื่อนภาคเหนือปี 2017

![Signal2 2017](https://github.com/user-attachments/assets/aa67cc4d-96ea-4959-ab1d-9bf3d2c45e80)

* เขื่อนภาคเหนือปี 2018

![Signal2 2018](https://github.com/user-attachments/assets/bbdfca9a-2534-49a8-8e4f-8f54912195b5)

* เขื่อนภาคเหนือปี 2020

![Signal2 2020](https://github.com/user-attachments/assets/6c8a8195-5908-42ef-ae77-c92050b335ac)

* เขื่อนภาคเหนือปี 2024

![Signal2 2024](https://github.com/user-attachments/assets/81c8f376-f60b-4b06-a199-81c7859a8ff6)


## บทสรุป

ตอบคำถามจาก research questions

1. ข้อมูลน้ำของเขื่อนในประเทศไทยจากปี 2014-2024 สามารถบอกการเกิดน้ำท่วมได้หรือไม่
*Ans - สามารถบอกถึงแนวโน้มได้ว่าจะเกิดน้ำท่วมหรือไม่เกิดจากข้อมูลที่ให้มา
2. ข้อมูลน้ำของเขื่อนในประเทศไทยจากปี 2014-2024 มีสัญญาณใดบ่งบอกตวามเป็นไปได้ของการเกิดน้ำท่วมบ้าง
Ans - ค้นพบสองสัญญาณที่จะสามารถบอกถึงแนวโน้มการเกิดน้ำท่วมได้ คือ Inflow and Outflow Over Time และ Storage Percentage Over Time
   * 2.1. ทดสอบข้อมูลน้ำภาคเหนือปี 2017-2018 เพื่อหาสัญญาณว่าจะเกิดน้ำท่วมหรือไม่
     *Ans - มีสัญญาณว่ามีโอกาสเกิดน้ำท่วมเนื่องจาก ปริมาณน้ำที่ไหลออกจากเขื่อนตั้งแต่ต้นปีมีปริมาณมากกว่า 10 ล้าน ลบ.เมตร (Inflow and Outflow Over Time) ปกติเขื่อนภาคเหนือมีค่า outflow เฉลี่ยอยู่ที่ประมาณ 3.9 ล้าน ลบ.เมตร และค่า std อยู่ที่         ประมาณ 6.6 ล้าน ลบ.เมตร ประกอบกับ Storage Percentage Over Time อยู่พี่ประมาน 100% ตั้งแต่ต้นปี แม้ว่าจะพยายามระบายช่วงหน้าแล้งแล้ว Storage Percentage ก็ยังอยู่ที่ประมาณ 60% ประกอบกับการที่ inflow ยกระดับขึ้นมาอยู่เหนือ outflow       (Inflow and Outflow Over Time) ในช่วงก่อนเดือนสิงหาคม และคงอยู่แบบนั้นเป็นเวลานานไปจนถึงประมาณปลายเดือนพฤศจิกายน
   * 2.2. ทดสอบข้อมูลน้ำภาคเหนือปี 2020 เพื่อหาสัญญาณว่าจะเกิดน้ำท่วมหรือไม่
     *Ans - มีสัญญาณว่ามีโอกาสไม่เกิดน้ำท่วมเนื่องจาก ปริมาณน้ำที่ไหลออกจากเขื่อนตั้งแต่ต้นปีมีปริมาณคร่าวๆอยู่ที่ 10 ล้าน ลบ.เมตร (Inflow and Outflow Over Time) ประกอบกับ Storage Percentage Over Time อยู่พี่ประมาน 60% ตอนต้นปี ประกอบกับ             การที่ inflow ยกระดับขึ้นมาอยู่เหนือ outflow (Inflow and Outflow Over Time) ในช่วงต้นเดือนสิงหาคมไปจนถึงกลางเดือนพฤศจิกายนเท่านั้น
ข้อมูลประกอบคำตอบข้อ 2
![Screenshot 2567-10-17 at 03 36 45](https://github.com/user-attachments/assets/0c835d8d-d2a3-4081-a568-61ddd9eefb38)

3. ข้อมูลน้ำภาคเหนือปี 2024 มีสัญญาณอะไรบ่งบอกว่าจะเกิดน้ำท่วมในช่วงปลายปี
*Ans - จากการทดสอบหาสัญญาณน้ำท่วมในปี 2017, 2018, และ 2020 ได้นำสัญญาณทั้ง 2 สัญญาณมาปรับใช้กับปี 2024 พบว่า มีโอกาสเกิดน้ำท่วมเนื่องจากปริมาณน้ำที่ไหลออกจากเขื่อนตั้งแต่ต้นปีมีปริมาณมากกว่า 10 ล้าน ลบ.เมตร (Inflow and Outflow Over Time) ประกอบกับ Storage Percentage Over Time อยู่พี่ประมาน 100% ตั้งแต่ต้นปี แม้ว่าจะพยายามระบายช่วงหน้าแล้งแล้ว Storage Percentage ก็ยังอยู่ที่ประมาณ 55%-60% ประกอบกับการที่ inflow ยกระดับขึ้นมาอยู่เหนือ outflow (Inflow and Outflow Over Time) ในช่วงก่อนเดือนสิงหาคม

## แนวทางศึกษาเพิ่มเติมต่อในอนาคต
1. ศึกษาเรื่องความชื้นในมหาสมุทรทั้ง 2 ฝั่งทะเล (มหาสมทรอินเดียและทะเลจีนใต้)เพื่อมาประกอบเพิ่มในปัจจัยการวิเคราะห์
2. อุณหภูมิในมหาสมุทรที่เปลี่ยนแปลง
3. จุดพักน้ำที่เปลี่ยนแปลงไปในภาคเหนือเพื่อจะได้ทำการวิเคราห์ต่อได้ในระดับจังหวัด

## ภาพประกอบตำแหน่งเขื่อนโดยสังเขป
![image](https://github.com/user-attachments/assets/013eb204-f31a-49a0-a1c6-e120b85e60cf)
reference site:https://www.dailynews.co.th/news/3792837/
