# พื้นฐานภาษา C\# สำหรับ TPop Manager (ฉบับเข้าใจง่ายที่สุด)

เอกสารนี้จัดทำขึ้นเพื่อช่วยให้ทุกคนในทีม โดยเฉพาะผู้ที่ไม่มีพื้นฐานด้านการเขียนโปรแกรม สามารถเข้าใจหลักการทำงานพื้นฐานของภาษา C\# และมองเห็นภาพว่าโค้ดที่เราเขียนไปนั้น กำลังบอกอะไรกับคอมพิวเตอร์ และส่งผลต่อเกม TPop Manager ของเราอย่างไรบ้าง

-----

## 1\. มองเห็นภาพการทำงานจากโค้ด (สำหรับคนไม่รู้โค้ดเลย)

  * **1.1 โค้ดคืออะไร?**

      * **เปรียบเทียบ:** ลองนึกว่าโค้ดคือ **"ชุดคำสั่ง"** หรือ **"สูตรอาหาร"** ที่เราเขียนขึ้นมาเพื่อบอกให้ **"เชฟ (คอมพิวเตอร์)"** ทำตาม
      * **ในเกม:** โค้ด C\# คือชุดคำสั่งที่เราเขียนขึ้นมา เพื่อบอกให้ Unity (และคอมพิวเตอร์) สร้างโลกในเกมขึ้นมา ควบคุมศิลปิน จัดการเงิน แสดงผลบนหน้าจอ และทำทุกอย่างในเกมของเรานั่นแหละจ้ะ

  * **1.2 วิธีการ "อ่าน" โค้ด (แบบคนทั่วไป):**

      * ถึงแม้จะไม่รู้ภาษา C\# แต่เราสามารถมองหา **"คำสำคัญ"** และ **"รูปแบบ"** บางอย่างได้ เพื่อให้พอเข้าใจว่าโค้ดกำลังทำอะไรอยู่
      * **เปรียบเทียบ:** เวลาเราเห็นสูตรอาหารที่ไม่รู้จัก เราอาจจะยังทำไม่ได้ แต่ถ้าเราเห็นคำว่า "หั่น", "ผัด", "ต้ม", "น้ำตาล", "เกลือ" เราก็พอจะเดาได้ว่ามันเป็นเมนูอาหารอะไร และมีส่วนผสมอะไรบ้างใช่ไหม?
      * **ในโค้ด:**
          * **ชื่อ Class/ชื่อไฟล์:** มักจะบอกว่าโค้ดนี้ **"เกี่ยวกับอะไร"** เช่น `EnterpriseManager` ก็คือ "ตัวจัดการเรื่องบริษัท/ธุรกิจ"
          * **ชื่อ Method/ชื่อฟังก์ชัน (การกระทำ):** มักจะบอกว่าโค้ดส่วนนี้ **"ทำอะไร"** เช่น `AddMoney()` ก็คือ "เพิ่มเงิน", `SaveGame()` ก็คือ "บันทึกเกม"
          * **ชื่อ Variable (ข้อมูล):** มักจะบอกว่าโค้ดส่วนนี้ **"เก็บข้อมูลอะไร"** เช่น `currentMoney` ก็คือ "เงินปัจจุบัน"
          * **ตัวเลข/ข้อความ:** เราจะเห็นตัวเลข (เช่น `100`, `500`) หรือข้อความ (เช่น `"สวัสดี"`, `"ลิซ่า"`) ซึ่งเป็นค่าที่เราใช้ในเกม
          * **วงเล็บ `{}` (บล็อกคำสั่ง):** เหมือนการบอกว่า "นี่คือกลุ่มคำสั่งที่ทำหน้าที่เดียวกัน"
          * **วงเล็บ `()` (การเรียกใช้/ข้อมูลที่ส่งไป):** ถ้าอยู่หลังชื่อฟังก์ชัน (Method) ก็คือการบอกว่า "เรียกฟังก์ชันนี้" และข้อมูลที่อยู่ข้างในคือสิ่งที่เรา **"ส่งให้"** ฟังก์ชันนั้นๆ เช่น `AddMoney(100)` คือ "เพิ่มเงินไป 100 บาท"
          * **เครื่องหมาย `;` (จบประโยค):** ทุกคำสั่งใน C\# จะจบด้วยเซมิโคลอน เหมือนจุดจบประโยคในภาษาไทย

  * **1.3 ตัวอย่างการ "มองภาพ" โค้ด:**

    ```csharp
    // นี่คือคลาสชื่อ EnterpriseManager (ตัวจัดการธุรกิจ)
    public class EnterpriseManager
    {
        // นี่คือกล่องเก็บข้อมูลเงินปัจจุบัน มีค่าเริ่มต้นคือ 1000
        public int Money = 1000;

        // นี่คือฟังก์ชันชื่อ AddMoney (เพิ่มเงิน)
        // มันจะรับค่า "amount" (จำนวนเงิน) เข้ามา
        public void AddMoney(int amount)
        {
            // เอาเงินปัจจุบัน (Money) ไปบวกกับ "amount" (จำนวนเงินที่รับเข้ามา)
            // แล้วผลลัพธ์ที่ได้ ก็เก็บกลับไปในกล่อง Money
            Money = Money + amount; // หรือเขียนย่อๆ เป็น Money += amount;

            // Debug.Log คือการบอกให้ Unity แสดงข้อความในหน้าต่าง Console (สำหรับคนเขียนโค้ดดู)
            Debug.Log("ตอนนี้เงินของบริษัทคือ: " + Money);
        }
    }
    ```

      * **สำหรับคนไม่รู้โค้ด:**

          * เห็น `EnterpriseManager` ก็รู้ว่าเกี่ยวกับบริษัท
          * เห็น `int Money = 1000;` ก็รู้ว่ามี **กล่องเก็บเงิน** ชื่อ `Money` เริ่มต้นที่ 1000
          * เห็น `AddMoney(int amount)` ก็รู้ว่ามี **ฟังก์ชัน (การกระทำ)** ชื่อ `AddMoney` ที่รับตัวเลข (amount) เข้ามา
          * เห็น `Money = Money + amount;` ก็รู้ว่า **เอาเงินไปบวกเพิ่ม**
          * เห็น `Debug.Log(...)` ก็รู้ว่า **แสดงข้อความ** ในส่วนที่คนเขียนโค้ดดู

      * แค่นี้ก็พอจะเดาได้แล้วว่าโค้ดนี้ทำอะไรเกี่ยวกับเงินใช่ไหมล่ะ?

-----

## 2\. ประเภทตัวแปรของ C\# (Data Types)

  * **เปรียบเทียบ:** ลองนึกว่าเรากำลังจัดเก็บ **"สิ่งของ"** ประเภทต่างๆ ในชีวิตประจำวัน เรามีกล่องสำหรับเก็บ "ตัวเลข" (เช่น เงิน), กล่องสำหรับเก็บ "ตัวอักษร" (เช่น ชื่อคน), กล่องสำหรับเก็บ "ใช่/ไม่ใช่" (เช่น ไฟเปิดอยู่ไหม) เราคงไม่เอาตัวเลขไปใส่ในกล่องที่เขียนว่า "ชื่อคน" ใช่ไหม?

  * **ใน C\#:** เราต้องบอกคอมพิวเตอร์ว่า **"กล่องเก็บข้อมูล"** (หรือที่เรียกว่า **"ตัวแปร" - Variable**) ที่เราจะใช้เก็บข้อมูลนั้น มันจะเก็บข้อมูลประเภทไหน เพื่อให้คอมพิวเตอร์จัดสรรพื้นที่และจัดการข้อมูลได้อย่างถูกต้อง

  * **2.1 ประเภทข้อมูลพื้นฐานที่ใช้บ่อยในโปรเจกต์ TPop Manager:**

      * **`int` (Integer): ตัวเลขจำนวนเต็ม**

          * **เปรียบเทียบ:** เหมือน **"จำนวนเงิน"** ที่ไม่มีเศษสตางค์ (100 บาท, 500 บาท), **"จำนวนศิลปิน"**, **"จำนวนวัน"** ในเกม
          * **เก็บอะไร:** ตัวเลขจำนวนเต็ม ไม่มีทศนิยม เช่น `5`, `100`, `-20`, `0`
          * **ตัวอย่างโค้ด:**
            ```csharp
            int playerMoney = 1000;         // กล่องเก็บเงินผู้เล่น เริ่มต้นที่ 1000
            int numberOfArtists = 5;        // กล่องเก็บจำนวนศิลปิน เริ่มต้นที่ 5
            int currentDay = 1;             // กล่องเก็บวันปัจจุบันในเกม เริ่มต้นที่ 1
            ```
            **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
              * `int` คือบอกว่า "กล่องนี้เก็บแค่ **ตัวเลขเต็มๆ** นะ"

      * **`float` (Floating-Point Number): ตัวเลขทศนิยม**

          * **เปรียบเทียบ:** เหมือน **"ค่าพลังงาน"** ของศิลปินที่อาจจะมีเศษ (85.5%), **"เปอร์เซ็นต์"** ต่างๆ ในเกม (0.75, 1.25)
          * **เก็บอะไร:** ตัวเลขที่มีจุดทศนิยม เช่น `3.14`, `0.5`, `-10.25` (ต้องมี `f` ต่อท้ายตัวเลขด้วย เพื่อบอกว่าเป็น `float`)
          * **ตัวอย่างโค้ด:**
            ```csharp
            float artistEnergy = 75.5f;     // กล่องเก็บพลังงานศิลปิน เริ่มต้นที่ 75.5
            float successRate = 0.8f;       // กล่องเก็บโอกาสสำเร็จ (80%) เริ่มต้นที่ 0.8
            ```
            **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
              * `float` คือบอกว่า "กล่องนี้เก็บ **ตัวเลขที่มีจุดทศนิยม** นะ"

      * **`string` (String): ข้อความ**

          * **เปรียบเทียบ:** เหมือน **"ชื่อศิลปิน"**, **"ชื่อเพลง"**, **"ข้อความในเกม"** (เช่น "คุณชนะ\!"), **"ชื่อไฟล์เซฟ"**
          * **เก็บอะไร:** ตัวอักษร, คำ, ประโยค ที่อยู่ภายในเครื่องหมายคำพูดสองชั้น (`" "`)
          * **ตัวอย่างโค้ด:**
            ```csharp
            string artistName = "ลิซ่า";        // กล่องเก็บชื่อศิลปิน
            string gameTitle = "TPop Manager"; // กล่องเก็บชื่อเกม
            string saveFileName = "game_save_slot1.json"; // กล่องเก็บชื่อไฟล์เซฟ
            ```
            **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
              * `string` คือบอกว่า "กล่องนี้เก็บ **ตัวอักษร ข้อความ** นะ"

      * **`bool` (Boolean): ค่าความจริง (ใช่/ไม่ใช่)**

          * **เปรียบเทียบ:** เหมือนการตอบคำถามว่า **"ไฟเปิดอยู่ไหม?"** (ใช่/ไม่ใช่), **"ผู้เล่นชนะหรือยัง?"** (ใช่/ไม่ใช่), **"มีไฟล์เซฟอยู่ไหม?"** (ใช่/ไม่ใช่)
          * **เก็บอะไร:** เก็บได้แค่ 2 ค่า คือ `true` (จริง/ใช่) หรือ `false` (เท็จ/ไม่ใช่)
          * **ตัวอย่างโค้ด:**
            ```csharp
            bool isGamePaused = false;      // กล่องเก็บสถานะว่าเกมหยุดอยู่ไหม (เริ่มต้นคือ "ไม่ใช่")
            bool hasSaveFile = true;        // กล่องเก็บสถานะว่ามีไฟล์เซฟอยู่ไหม (เริ่มต้นคือ "ใช่")
            ```
            **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
              * `bool` คือบอกว่า "กล่องนี้เก็บแค่ **ใช่ หรือ ไม่ใช่** นะ"

      * **`List<T>` (List): รายการของสิ่งของ**

          * **เปรียบเทียบ:** เหมือน **"ถุงใส่ผลไม้รวม"** ที่มีผลไม้หลายๆ ชนิด หรือ **"รายชื่อนักเรียน"** ในห้อง ที่มีชื่อนักเรียนหลายๆ คน
          * **เก็บอะไร:** เป็นกล่องที่สามารถเก็บข้อมูลประเภทเดียวกันได้หลายๆ ชิ้นเรียงต่อกัน เช่น `List<ArtistData>` คือ "รายการของข้อมูลศิลปิน" หลายๆ คน
          * **ตัวอย่างโค้ด:**
            ```csharp
            List<string> songTitles = new List<string>(); // กล่องเก็บรายชื่อเพลง (ว่างเปล่า)
            songTitles.Add("เพลงใหม่ของลิซ่า");            // เพิ่มเพลงเข้าไปในกล่อง
            songTitles.Add("เพลงเดบิวต์ของเจนนี่");

            // List<ArtistData> allArtists; // นี่คือ List ที่เก็บข้อมูลศิลปินหลายๆ คน
            ```
            **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
              * `List<T>` คือบอกว่า "กล่องนี้เก็บ **รายการของสิ่งของ** ที่เป็นประเภท `T` นะ" (`T` จะเป็นอะไรก็ได้ เช่น `string`, `int`, หรือ `ArtistData`)

      * **`enum` (Enumeration): กลุ่มของตัวเลือกที่จำกัด**

          * **เปรียบเทียบ:** เหมือน **"ปุ่มเลือกโหมดเกม"** ในเครื่องซักผ้า ที่มีแค่ "ซักธรรมดา", "ซักด่วน", "ปั่นแห้ง" เท่านั้น ไม่สามารถเลือก "ซักไปกินข้าวไป" ได้
          * **เก็บอะไร:** เป็นการกำหนดกลุ่มของชื่อค่าคงที่ที่ชัดเจน เพื่อให้โค้ดอ่านง่ายและลดข้อผิดพลาดจากการพิมพ์ผิด
          * **ตัวอย่างโค้ด:**
            ```csharp
            // กำหนดโหมดการฝึกศิลปิน
            public enum TrainingMode
            {
                Vocal,  // โหมดฝึกร้อง
                Dance,  // โหมดฝึกเต้น
                Acting  // โหมดฝึกการแสดง
            }

            // ในโค้ด:
            TrainingMode currentTraining = TrainingMode.Vocal; // กำหนดว่าตอนนี้กำลังฝึกร้อง
            ```
            **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
              * `enum` คือบอกว่า "กล่องนี้เก็บได้แค่ **ค่าที่เรากำหนดไว้เท่านั้น** นะ" ทำให้เลือกได้ง่าย ไม่สับสน

-----

## 3\. ส่วนประกอบต่างๆ ของภาษา C\# สำหรับการใช้งานในโปรเจกต์นี้

  * **เปรียบเทียบ:** การจะสร้างบ้าน ต้องมีส่วนประกอบหลายอย่าง เช่น "แปลนบ้าน (Class)", "การกระทำ (Method)", "วัตถุดิบ (Variable)", "สัญญา (Interface)"

  * **ใน C\#:** เรามีส่วนประกอบพื้นฐานเหล่านี้ที่ใช้ในการสร้างโปรเจกต์ของเรา

  * **3.1 Class (คลาส) - "พิมพ์เขียว" หรือ "แม่พิมพ์"**

      * **เปรียบเทียบ:** เหมือน **"พิมพ์เขียว"** ของบ้าน ที่บอกว่าบ้านหลังนี้จะมีกี่ห้อง มีหน้าต่างตรงไหนบ้าง หรือเหมือน **"แม่พิมพ์"** สำหรับทำขนมคุ้กกี้ ที่บอกว่าคุ้กกี้จะมีรูปร่างแบบไหน
      * **ใน C\#:** `Class` คือ **"พิมพ์เขียว"** หรือ **"แม่แบบ"** สำหรับสร้าง Object (วัตถุ) ต่างๆ ในเกมของเรา มันจะบอกว่า Object นั้นๆ จะมีข้อมูลอะไรบ้าง (ตัวแปร) และทำอะไรได้บ้าง (ฟังก์ชัน/เมธอด)
      * **ตัวอย่างโค้ด:**
        ```csharp
        // นี่คือพิมพ์เขียวสำหรับ "ศิลปิน"
        public class ArtistData
        {
            // กล่องเก็บข้อมูลของศิลปินแต่ละคน
            public string artistName; // ชื่อศิลปิน
            public int vocalSkill;    // สกิลร้อง
            public int danceSkill;    // สกิลเต้น

            // ฟังก์ชัน (การกระทำ) ที่ศิลปินทำได้
            public void TrainVocal()
            {
                vocalSkill += 5; // เพิ่มสกิลร้อง 5 แต้ม
                Debug.Log(artistName + " ฝึกร้องเพลง! สกิลร้องตอนนี้: " + vocalSkill);
            }
        }
        ```
        **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
          * `public class ArtistData { ... }` คือการบอกว่า "เราจะสร้าง **พิมพ์เขียว** สำหรับ **ArtistData** นะ"
          * จากพิมพ์เขียวนี้ เราสามารถสร้าง "ศิลปินตัวจริง" ขึ้นมาได้หลายคน โดยแต่ละคนก็จะมีชื่อ สกิลร้อง สกิลเต้น และสามารถฝึกร้องเพลงได้

  * **3.2 Object (อ็อบเจกต์) - "ของจริง" ที่สร้างจากพิมพ์เขียว**

      * **เปรียบเทียบ:** ถ้า `Class` คือ "พิมพ์เขียวบ้าน" `Object` ก็คือ **"บ้านจริง"** ที่ถูกสร้างขึ้นมาตามพิมพ์เขียวนั้นๆ
      * **ใน C\#:** `Object` คือ **"สิ่งที่ถูกสร้างขึ้นมาจริง"** จาก `Class` เราสามารถสร้าง Object จาก Class เดียวกันได้หลายๆ ตัว แต่ละตัวก็จะมีข้อมูลของตัวเอง
      * **ตัวอย่างโค้ด:**
        ```csharp
        // สร้างศิลปิน "ลิซ่า" จากพิมพ์เขียว ArtistData
        ArtistData lisa = new ArtistData();
        lisa.artistName = "ลิซ่า";
        lisa.vocalSkill = 80;
        lisa.danceSkill = 90;

        // สร้างศิลปิน "เจนนี่" จากพิมพ์เขียว ArtistData
        ArtistData jennie = new ArtistData();
        jennie.artistName = "เจนนี่";
        jennie.vocalSkill = 85;
        jennie.danceSkill = 85;

        lisa.TrainVocal(); // บอกให้ลิซ่าฝึกร้องเพลง
        jennie.TrainVocal(); // บอกให้เจนนี่ฝึกร้องเพลง
        ```
        **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
          * `ArtistData lisa = new ArtistData();` คือการบอกว่า "สร้าง **ศิลปินตัวจริง** ชื่อ `lisa` โดยใช้ **พิมพ์เขียว ArtistData** นะ\!"

  * **3.3 Method (เมธอด) / Function (ฟังก์ชัน) - "การกระทำ"**

      * **เปรียบเทียบ:** เหมือน **"การกระทำ"** ที่เราทำได้ เช่น "กิน", "นอน", "วิ่ง" หรือสำหรับบ้านก็คือ "เปิดไฟ", "ปิดประตู"
      * **ใน C\#:** `Method` หรือ `Function` คือ **"ชุดคำสั่ง"** ที่ถูกจัดกลุ่มไว้เพื่อทำหน้าที่ใดหน้าที่หนึ่งโดยเฉพาะ
      * **ตัวอย่างโค้ด:** (ดูในส่วน `AddMoney()` หรือ `TrainVocal()` ในตัวอย่างด้านบน)

  * **3.4 Variable (ตัวแปร) - "กล่องเก็บข้อมูล"**

      * **เปรียบเทียบ:** เหมือน **"กล่อง"** ที่ใช้เก็บสิ่งของต่างๆ เราสามารถเปลี่ยนของในกล่องได้
      * **ใน C\#:** `Variable` คือ **"กล่องเก็บข้อมูล"** ที่มีชื่อเรียกเฉพาะ และสามารถเก็บข้อมูลประเภทใดประเภทหนึ่งที่เรากำหนดไว้ (เช่น `int` สำหรับตัวเลข, `string` สำหรับข้อความ) ข้อมูลในกล่องสามารถเปลี่ยนแปลงได้ตลอดเวลา
      * **ตัวอย่างโค้ด:** (ดูในส่วน `int Money = 1000;` หรือ `string artistName;` ในตัวอย่างด้านบน)

  * **3.5 Interface (อินเทอร์เฟซ) - "สัญญา"**

      * **เปรียบเทียบ:** เหมือน **"สัญญา"** หรือ **"ข้อตกลง"** ที่ระบุว่า "ถ้าใครอยากเป็น **Manager ที่ Save ได้** ต้องทำสิ่งเหล่านี้ให้ได้นะ\!"
      * **ใน C\#:** `Interface` คือ **"สัญญา"** ที่บังคับให้ Class ที่ Implement (ทำตาม) สัญญานั้นๆ ต้องมี Method (การกระทำ) บางอย่างตามที่สัญญากำหนด
      * **ประโยชน์:** ทำให้โค้ดของเรายืดหยุ่นและเป็นระเบียบมากขึ้น
      * **ตัวอย่างโค้ด:**
        ```csharp
        // ISavable.cs
        // นี่คือสัญญาว่า "ถ้าใครทำตามสัญญานี้ ต้องมี 2 ฟังก์ชันนี้"
        public interface ISavable
        {
            // ฟังก์ชันสำหรับ "ดึงข้อมูล Save ของตัวเองออกมา"
            object GetSaveData();

            // ฟังก์ชันสำหรับ "รับข้อมูลที่โหลดมา แล้วเอาไปอัปเดตตัวเอง"
            void LoadData(object data);
        }

        // ใน EnterpriseManager.cs
        // EnterpriseManager ทำตามสัญญา ISavable
        public class EnterpriseManager : Singleton<EnterpriseManager>, ISavable
        {
            // ... (โค้ดอื่นๆ) ...

            // ต้องมีฟังก์ชัน GetSaveData() ตามสัญญา
            public object GetSaveData() { /* ... */ return null; }

            // ต้องมีฟังก์ชัน LoadData() ตามสัญญา
            public void LoadData(object data) { /* ... */ }
        }
        ```
        **เกร็ดเล็กน้อยสำหรับคนไม่รู้โค้ด:**
          * `public interface ISavable { ... }` คือการบอกว่า "เราจะสร้าง **สัญญา** ชื่อ `ISavable` นะ"
          * `public class EnterpriseManager : Singleton<EnterpriseManager>, ISavable { ... }` คือการบอกว่า "**EnterpriseManager** เป็น **พิมพ์เขียว** ที่ **ทำตามสัญญา ISavable** ด้วยนะ" ทำให้เรารู้ว่า `EnterpriseManager` จะต้องมีฟังก์ชัน `GetSaveData()` และ `LoadData()` แน่นอน

  * **3.6 Singleton (ซิงเกิลตัน) - "ผู้จัดการคนเดียว"**

      * **เปรียบเทียบ:** เหมือน **"ผู้จัดการบริษัท"** หรือ **"หัวหน้างาน"** ใน TPop Manager ที่มีเพียงคนเดียวเท่านั้น ไม่สามารถมีผู้จัดการสองคนมาดูแลเรื่องเดียวกันพร้อมกันได้
      * **ใน C\#:** `Singleton` คือ **"รูปแบบการออกแบบ"** (Design Pattern) ที่ทำให้มั่นใจว่า `Class` นั้นๆ จะมี Object (ตัวจริง) แค่ **"เพียงอันเดียว"** ในเกมทั้งหมด
      * **ประโยชน์:** เหมาะสำหรับ Manager ต่างๆ ในเกมของเรา (เช่น `GameCoreManager`, `EnterpriseManager`, `ArtistProfileManager`) เพราะเราต้องการให้มีตัวจัดการเหล่านี้แค่คนเดียว เพื่อไม่ให้เกิดความสับสนในการทำงาน
      * **ตัวอย่างโค้ด:** (ดูในไฟล์ `Singleton.cs` และ `EnterpriseManager : Singleton<EnterpriseManager>` ในเอกสารก่อนหน้า)

-----

## สรุป: C\# คือกุญแจสู่โลกเกมของเรา

การทำความเข้าใจพื้นฐานของภาษา C\# แม้เพียงเล็กน้อย ก็จะช่วยให้เรามองเห็นภาพรวมของการทำงานในโปรเจกต์ TPop Manager ได้ชัดเจนขึ้นจ้ะ\! การรู้จัก **"กล่องเก็บข้อมูล" (Variable)** ประเภทต่างๆ, **"พิมพ์เขียว" (Class)** ที่ใช้สร้างของจริง, **"การกระทำ" (Method)** ที่สั่งให้ทำอะไร, และ **"สัญญา" (Interface)** ที่ทำให้งานเป็นระเบียบ จะทำให้การทำงานร่วมกันในทีมง่ายขึ้นเยอะ และเราก็จะเข้าใจว่าโค้ดที่เราเห็น กำลังสร้าง TPop Manager ของเราให้มีชีวิตขึ้นมาได้ยังไงนั่นเอง\!
