# Freight Capstone: Network and Base Selection

Notebook ภาษาไทยสำหรับเลือกฐานรับจ้างขนส่งเมื่อกำหนดจำนวนฐาน 2 หรือ 3 แห่ง และทดลองบทบาทของ community ในการเข้าถึงตลาด

## เริ่มต้น

เปิด `Freight_Capstone_TH.ipynb` เพื่อดูคำอธิบาย โค้ด และผลที่รันไว้แล้ว หากต้องการคำนวณใหม่ ให้วางข้อมูลใน `data/` ตามรายการด้านล่าง แล้ว Run All

```bash
python -m pip install pandas numpy scipy networkx matplotlib ipykernel
```

`requirements-tested.txt` บันทึกเวอร์ชันที่ใช้ทดสอบ ผล partition หรือคำตอบที่คะแนนเสมอกันอาจต่างกันเมื่อเปลี่ยนเวอร์ชัน จึงต้องตรวจผลจริงก่อนนำไปใส่สไลด์

## ข้อมูลที่ต้องใช้

- `data/otp_69_01_69.csv`: ข้อมูลหลัก ม.ค.–เม.ย. 2569
- `data/otp_69_01_68.csv`: ข้อมูลปี 2568 สำหรับเดือนร่วมและ duplicate sensitivity
- `data/province_coordinates_used.csv`: จุดแทนจังหวัดและที่มาของพิกัด
- `data/straight_line_distance_km.csv`: ระยะเส้นตรงระหว่างจุดแทนจังหวัด มีการตรวจเทียบ Haversine ใน Notebook

แหล่งข้อมูลขนส่ง: https://otp.gdcatalog.go.th/dataset/91038df9-854e-442d-8408-c54032daf506

ดูรายละเอียดต้นทางใน `data/แหล่งที่มา_SOURCES.txt` และ hash ของไฟล์ที่ใช้รันใน `results/input_sha256.json` ข้อมูลที่ขาดไม่ได้แปลว่าไม่มี demand จริง ตรวจสิทธิ์ของต้นทางก่อนเผยแพร่ข้อมูลต่อ

## ขั้นตอนวิเคราะห์

1. Audit ข้อมูลและเลือกพอร์ต 7 สินค้า ไม่รวมอ้อย
2. สร้างเครือข่ายจังหวัด น้ำหนักเป็นเที่ยวรวมสองทิศทาง
3. Louvain 100 seeds ต่อ resolution (0.5, 1, 1.5, 2)
4. Fixed-topology weight permutation 100 ครั้ง โดยใช้ optimization budget เท่ากัน
5. ตรวจ bridge calculation และรายงาน centrality ประกอบ
6. Model A เพิ่ม coverage รวม; Model B เพิ่มข้อกำหนด coverage ขั้นต่ำรายกลุ่ม
7. ตรึงฐานแล้วตรวจย้อนหลัง พร้อมกราฟและไฟล์ผลลัพธ์

## ผลและข้อจำกัดสำคัญ

กรณีหลัก 150 กม. เส้นตรง: A สองฐานเลือกพิษณุโลก–สระบุรี coverage 66.4%; A สามฐานเพิ่มมหาสารคาม coverage 82.3%; B สามฐาน floor 50% ได้ 80.1%

Null test p=0.9604 จึงยังไม่สนับสนุน modularity สูงกว่าการสุ่มน้ำหนักแบบที่ทดสอบ Model B เป็น exploratory ไม่ใช่กฎลงทุน ผลตรวจย้อนหลังไม่ใช่ unseen holdout และ benchmark ของ B เป็น Model A ที่ไม่มีข้อกำหนดรายกลุ่ม

Coverage ไม่ใช่ยอดขายหรือกำไร ไม่มีต้นทุน capacity ระยะถนนจริง หรือข้อมูลคู่แข่ง และยังไม่ได้หาจำนวนฐานน้อยที่สุดภายใต้ coverage target

## การใช้ AI

AI ช่วยสร้างโค้ด อธิบาย และจัดเอกสาร ทีมต้องตรวจสมมติฐาน สูตร แหล่งข้อมูล และข้อสรุปด้วยตนเอง ไม่ใช้ AI เป็นหลักฐานข้อเท็จจริงทางตลาด



## เปิดบน Colab

[Open in Colab](https://colab.research.google.com/github/Pornphrom/MADT7202_capstone_freight/blob/main/Freight_Capstone_TH.ipynb)

รันเซลล์เตรียมข้อมูลสำหรับ GitHub/Colab ก่อนเริ่มวิเคราะห์ ระบบดาวน์โหลด CSV สองปีจาก สนข. โดยตรงและตรวจ SHA-256 กับข้อมูลที่ใช้ทดสอบ ไม่เก็บ CSV ขนาดใหญ่ไว้ใน Git หากไฟล์ต้นทางเปลี่ยนให้ใช้สำเนาเดิมแทนการข้ามการตรวจ hash
