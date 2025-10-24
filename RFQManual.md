# **RFQ Quotation Manual**

## การเข้าใช้งานระบบ RFQ Quotation
1. เข้าสู่ระบบ PJWSoft Core 
2. เลือกที่ปุ่ม __RFQ Quotation__ ระบบจะปรากฏหน้าต่างสำหรับระบบให้ทันที

>เมื่อระบบปรากฏหน้าต่างใหม่ ระบบจะแสดงรายการเอกสาร Cost Per Unit ที่มีสถานะ Approved มาให้ โดยจะแสดงเอกสารทุก ๆ Revision ตัวอย่าง Rev.000 โดยจะแสดงสถานะของเอกสารตามแทบสีของรายการนั้น ๆ 

>### แถบสีสถานะของเอกสาร
|สี   |🎨| รายละเอียด | สถานะเอกสาร | การทำงาน |
| ---- | ---- | ---- | ---- | --- |
| ฟ้า | 🩵 | รอสร้างเอกสาร Quotation| - | เมื่อคลิกระบบจะทำการ Generate Quotation ให้ |
| เทา | 🩶 | กำลังคำนวน Cost Per Unit หรือ Cost Per Unit กำลังดำเนินการ  | - |-|
| ดำ | 🖤 | เอกสาร Cost Per Unit ถูกยกเลิกแล้ว | - |-|
| ม่วง | 💜 | เอกสาร Quotation กำลังดำเนินการ | Requested |เมื่อคลิกจะไปที่หน้าจัดการ Quotation|
| ส้ม | 🧡 | เอกสาร Quotation รออนุมัติ | WaitApprove |เมื่อคลิกจะไปที่หน้าจัดการ Quotation|
| เขียว | 💚 | อกสาร Quotation ได้รับการอนุมัติแล้ว | Approved |เมื่อคลิกจะไปที่หน้าจัดการ Quotation|
| แดง | ❤️ | เอกสาร Quotation ถูก Reject | Rejected |เมื่อคลิกจะไปที่หน้าจัดการ Quotation|


## การ Generate Quotation
1. คลิกเลือกที่ที่เอกสารที่ต้องการ Generate (Revision ที่เป็นแถบสี่ฟ้า)
2. ระบบจะปรากฏหน้าต่างเพื่อให้ผู้ใช้งานยืนยันอีกครั้ง
3. เมื่อกดยืนยันแล้ว ระบบจะทำการ Generate ข้อมูลให้ทันที และเมื่อระบบ Generate เรียบร้อยแล้ว ระบบจะไปที่หน้าจัดการ Quotation ให้อัตโนมัติ

## หลักการ Generate และการคำนวน Quotation ของระบบ 
ระบบจะ generate ข้อมูลออกตาม Volume ของเอกสารนั้น ๆ และแบ่งเป็นทั้งหมด 7 หัวข้อ ได้แก่ 
1. **Raw Material Cost (A)**
    * **Raw Material A.1** : `ระบบจดึงข้อมูลจาก Estimate Material/Component เฉพาะประเภท Material ,Material Special ,Color Master Batch`
        >
        **สูตรการคำนวน Raw Material A.1**
        | Topic | สูตร | 
        | ---- | ---- | 
        | Actual Cost (Material และ Color Master Batch) | ราคาที่ Pricing Judge * Netweight(Kg) * Quantity * Percent Use material |
        | Actual Cost (Material Special) | ราคาที่ Pricing Judge * Grossweight(Kg) * Quantity * Percent Use material |
        | Loss | Actual Cost * Percent Loss |
        | Amount | Actual Cost + Loss |
    >
    * **Raw Material Paint A.2** : `ดึงข้อมูลมาจาก Estimate Painting ส่วนที่เป็น Material Painting Cost และ Paint Consumption`
        >
        **สูตรการคำนวน Raw Material Paint A.2**
        | Topic | สูตร | 
        | ---- | ---- | 
        | Actual Cost  | (ราคาที่ Pricing Judge * Paint Consumption ที่บวก markpu แล้ว) * Quantity |
        | Loss | Actual Cost * Percent Loss |
        | Amount | Actual Cost + Loss |
>
2. **Conponent Part Cost (B)**
    * `ระบบจะดึงข้อมูล Estimate Material/Component เฉพาะประเภท Component`
    >
        **สูตรการคำนวน Conponent Part Cost (B)**
        | Topic | สูตร | 
        | ---- | ---- | 
        | Amount | ราคาที่ Pricing Judge * Quantity |
>
3. **Processing Cost (C)**
    * `ระบบจะดึงข้อมูลการคำนวนหัวข้อ Topic Process (MC Rate) ,DL และ MC Robot`
        | Standard Rate | รายละเอียด | 
        | ---- | ---- | 
        | Process MC Rate | ได้มาจาก Rate เครื่องจักรที่ Esitemate เลือกเครื่งอจักรมา |
        | DL | 150 Baht/Hr. |
        | Big Booth | 200 THB(Hanger) |
        | Small Booth (โรง E) | 793 THB/Hr. |
        | Small Booth (โรง C) | 1,151 THB/Hr. |
        | Machine Robot | ((((ราคาเครื่องจักรที่ Sourcing ระบุ * 1.06)/5)/24)/280) + ค่าไฟ 35 Baht/Hr.บาท + ค่าแรง 150 Baht/Hr. |
        >
        **สูตรการคำนวน Processing Cost (C)**
        | Topic | สูตร | 
        | ---- | ---- | 
        | Amount : Process MC Rate | (Rate ที่ Pricing Judge * Quantity) * (CycleTime[PerShot] / 3600 / Cavity) |
        | Amount : DL | (Rate ที่ Pricing Judge * Man) * (CycleTime[PerShot] / 3600 / Cavity) |
        | Amount : DL Assembly | (CycleTime / 3600) * Rate ที่ Pricing Judge |
        | Amount : Big Booth (Painting Process) | Rate ที่ Pricing Judge /  Cavity(Hanger) |
        | Amount : Small Booth โรง C และ E (Painting Process) | ((CycleTime / Cavity) / 3600) x Rate ที่ Pricing Judge |
        | Amount : Machine Robot | Rate ที่ Pricing Judge * (CycleTime / 3600) |
>    
4. **Packing Cost (D)**
    * `ระบบจะดึงเฉพาะข้อมูล Packing Concept เฉพาะประเภท Packing FG เท่านั้น`
        >
         **สูตรการคำนวน Packing Cost (D)**
        | Topic | สูตร | Note. |
        | ---- | ---- | ---- |
        | Model Life | Model Life จาก Information –1Y | `กรณี Life 1-3 ปีไม่ต้อง -1Y` |
        | Amount | (Packing Qty * ราคาที่ Pricing Judge)/(Volume ต่อปี *  Model Life) | |
>
5. **Transportation Cost (E)**
    * `ระบบจะดึงข้อมูลมาจาก Cost Per Unit Transport ก็ต่อเมื่อ Information มีการระบุ Transport Option ว่า Milk Run`
    >
        **สูตรการคำนวน Transportation Cost (E)**
        | Topic | สูตร |
        | ---- | ---- |
        | Amount | ใช้ราคาที่ Pricing Judge |
>
6. **Mold,Tooling & Testing Cost [Lum-sum Payment] (F)**
    * **Mold/Tooling F.1** : `ระบบจะดึงข้อมูลมาจาก Estimate Machine/Tooling เฉพาะประเภท Tooling เท่านั้น`
    * **Testing F.2** : `ระบบจะดึงข้อมูลจาก RFQ Item Test รวม Test (DV. + PV.Test + Jig & Fixture + Test Period)`
    >
         **สูตรการคำนวน Mold,Tooling & Testing Cost [Lum-sum Payment] (F)**
        | Topic | สูตร |
        | ---- | ---- |
        | Amount | ราคาที่ Pricing Judge * Quantity |
>
7. **Mold,Tooling & Testing Cost [Amortize Payment] (G)**
    * **Mold/Tooling G.1** : `ระบบจะดึงข้อมูลมาจาก Estimate Machine/Tooling เฉพาะประเภท Tooling เท่านั้น`
    >
         **สูตรการคำนวน Mold/Tooling G.1**
        | Topic | สูตร |
        | ---- | ---- |
        | Total Price | ราคาที่ Pricing Judge + ดอกเบี้ย |
        | Volume Qty | Volume ต่อปี * Model Life |
        | Amount | Total Price * Volume Qty |
        >
        **ตัวอย่างการคำนวนดอกเบี้ย**
        | ราคาต้นทุนที่ Pricing Judge | จำนวนปี | ดอกเบี้ย| ดาวน์ |
        | ---- | ---- | ---- | ---- | 
        | 481,800.00 | 5 | 5% | 40% |

        | ปี | ยอดชำระ (Payment) | คงเหลือ (Balance) | ดอกเบี้ย (Interest) |
        | ---- | ---- | ---- | ---- | 
        | 0 | - | 481,800.00 | - |
        | 1 | 192,720.00 | 289,080.00	 |24,090.00  |
        | 2 | 72,270.00 | 216,810.00 | 14,454.00 |
        | 3 | 72,270.00 | 144,540.00	 | 10,840.50 |
        | 4 | 72,270.00 | 72,270.00	 |7,227.00  |
        | 5 | 72,270.00 | - | 3,613.50 |
        |  | | **รวมดอกเบี้ย** | **60,225.00** |
    >
    * **Testing G.2** :`ระบบจะดึงข้อมูลจาก RFQ Item Test รวม Test (DV. + PV.Test + Jig & Fixture + Test Period)`
     >
         **สูตรการคำนวน Mold/Tooling G.1**
        | Topic | สูตร |
        | ---- | ---- |
        | Volume Qty | Volume ต่อปี * Model Life |
        | Amount | ราคาที่ Pricing Judge * Volume Qty |
>        
## สิ่งที่ผู้ใช้งานสามารถแก้ไขได้
* ผู้ใช้งานสามารถแก้ไขข้อมูลได้และระบบจะคำนวนจากสิ่งที่แก้ไขให้อัตโนมัติ สิ่งที่แก้ไขได้มีดังนี้  Markup หรือ Profit ,Pricing Judge Cost ,Pricing Judge Rate ,Quantity ,Loss และระบบจะคำนวนให้อัตโนมัติ

## การส่งอนุมัติ
>### ลำดับการส่งอนุมัติ
ระบบจะส่งอนุมัติตาม Route ของผู้แก้ไขเอกสารล่าสุด
|ลำดับที่   |ตำแหน่ง|
| ---- | ---- | 
| 1 | Pricing Manager |
| 2 | Chief Administration Office | 
