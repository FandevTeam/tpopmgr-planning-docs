# แนวทางการสร้างระบบ Persistent Manager สำหรับ TPop Manager (ฉบับเบื้องต้น)

เอกสารนี้จัดทำขึ้นเพื่อเป็นแนวทางเบื้องต้นในการทำความเข้าใจและพัฒนาระบบ **Persistent Manager** สำหรับเกม TPop Manager โดยมีเป้าหมายให้ทั้งทีมเข้าใจภาพรวมและหลักการทำงานที่สำคัญ

-----

## 1\. Persistent Manager คืออะไร?

ลองนึกภาพว่าเกม TPop Manager ของเราเหมือนกับ **"บริษัทค่ายเพลง"** แห่งหนึ่ง

  * **Persistent Manager** ก็เปรียบเสมือน **"สมองส่วนกลางของบริษัท"** หรือ **"สมุดจดบันทึกเล่มใหญ่"** ที่คอยเก็บข้อมูลสำคัญทุกอย่างของบริษัทไว้ตลอดเวลา ไม่ว่าเราจะย้ายไปห้องไหน (เปลี่ยนฉากในเกม) หรือทำกิจกรรมอะไร ข้อมูลในสมุดเล่มนี้ก็ยังอยู่ครบ ไม่หายไปไหน

  * **หน้าที่หลัก:** เก็บค่าตัวแปรสำคัญๆ ของเกมที่ต้องคงอยู่ตลอดการเล่น เช่น จำนวนเงินในบริษัท, ชื่อเสียงค่าย, รายชื่อศิลปิน, วันที่ในเกม, หรือการตั้งค่าต่างๆ ของผู้เล่น

  * **ทำไมต้องมี?**

      * **ข้อมูลไม่หาย:** ทำให้ข้อมูลสำคัญของเกมอยู่รอดข้ามฉาก (Scene) ได้ เช่น เมื่อผู้เล่นออกจากหน้าเมนูไปหน้าเล่นเกม ข้อมูลเงินและศิลปินก็ยังอยู่ครบ

      * **จัดการง่าย:** เป็นแหล่งรวมข้อมูลหลัก ทำให้การเข้าถึงและจัดการข้อมูลเป็นระบบ

-----

## 2\. ทำไมต้องมี Manager หลายตัว? (แนวคิด "หลาย Manager เป็นอิสระ")

ก่อนอื่น มาทำความเข้าใจกันก่อนว่า **"Manager"** ในที่นี้หมายถึงอะไร?

  * **Manager (ผู้จัดการ) ในระบบเกม:** เปรียบเสมือน **"ผู้เชี่ยวชาญเฉพาะด้าน"** หรือ **"กล่องเครื่องมือเฉพาะทาง"** ที่รับผิดชอบงานใดงานหนึ่งโดยเฉพาะ เช่น กล่องเครื่องมือสำหรับจัดการเงิน, กล่องเครื่องมือสำหรับจัดการข้อมูลศิลปิน

ในบริษัทค่ายเพลงของเรา เราคงไม่อยากให้คนๆ เดียวทำทุกอย่างใช่ไหม? มันจะยุ่งเหยิงและทำงานได้ไม่ดี

  * **การมี Manager หลายตัว** ก็เหมือนกับการมี **"แผนกต่างๆ ในบริษัท"** นั่นแหละจ้ะ\!

      * มี **แผนกบัญชี** (`EnterpriseManager`) ดูแลเรื่องเงินและรายได้

      * มี **แผนกศิลปิน** (`ArtistProfileManager`) ดูแลข้อมูลและพัฒนาศิลปิน

      * มี **แผนกจัดตารางงาน** (`ArtistScheduleManager`) ดูแลตารางกิจกรรมของศิลปิน

      * มี **แผนกสำนักงาน** (`OfficeManager`) ดูแลสิ่งปลูกสร้างในบริษัท

  * **ข้อดีของการแบ่งเป็นหลายแผนก (Manager):**

      * **จัดระเบียบง่าย:** แต่ละแผนกรับผิดชอบงานของตัวเองชัดเจน โค้ดไม่ปนกัน ทำให้ดูแลรักษา แก้ไข และทำความเข้าใจได้ง่าย

      * **ยืดหยุ่นสูง:** ถ้าจะเปลี่ยนอะไรในส่วนของแผนกบัญชี ก็แก้แค่ `EnterpriseManager` ไม่ต้องไปยุ่งกับแผนกอื่น

      * **ทำงานเป็นทีมง่าย:** แต่ละคนรับผิดชอบ Manager ตัวเองได้ ไม่ต้องกลัวโค้ดทับกัน

      * **ดูเป็นมืออาชีพ:** โครงสร้างโค้ดเป็นระเบียบและ scalable (ขยายได้ง่าย)

-----

## 3\. หลักการสำคัญทางเทคนิค (สำหรับโค้ดเดอร์)

### 3.0 เกร็ดความรู้ C\# เบื้องต้น (สำหรับทุกคนในทีม)

ก่อนจะไปดูหลักการซับซ้อนๆ มาทำความรู้จักคำศัพท์พื้นฐานในภาษา C\# (ภาษาที่เราใช้เขียนโค้ดเกม) กันก่อนนะจ๊ะ\! ลองนึกภาพแบบง่ายๆ ในชีวิตประจำวันดู:

  * **Class (คลาส):**

      * **เปรียบเทียบ:** เหมือนกับ **"พิมพ์เขียว"** หรือ **"สูตรอาหาร"**
      * **ในโค้ด:** มันคือแม่แบบสำหรับสร้างสิ่งต่างๆ เช่น เรามี `Class Artist` (พิมพ์เขียวศิลปิน) ที่บอกว่าศิลปินทุกคนต้องมีชื่อ, สกิลร้อง, สกิลเต้น พอเราจะสร้างศิลปินจริงๆ (เช่น ลิซ่า, เจนนี่) เราก็ใช้พิมพ์เขียวนี้แหละ\!

  * **Object (ออบเจกต์) / Instance (อินสแตนซ์):**

      * **เปรียบเทียบ:** คือ **"สิ่งของจริง"** ที่สร้างขึ้นมาจากพิมพ์เขียว หรือ **"อาหารที่ปรุงสำเร็จแล้ว"** จากสูตร
      * **ในโค้ด:** เมื่อเราใช้ `Class Artist` สร้าง `ลิซ่า` หรือ `เจนนี่` ขึ้นมา ลิซ่ากับเจนนี่ก็คือ `Object` หรือ `Instance` ของ `Class Artist` นั่นเอง

  * **Method (เมธอด):**

      * **เปรียบเทียบ:** คือ **"การกระทำ"** หรือ **"ขั้นตอนการทำ"** ในสูตรอาหาร
      * **ในโค้ด:** เป็นชุดคำสั่งที่บอกว่า Object นั้นๆ ทำอะไรได้บ้าง เช่น `Artist.TrainSkill()` (ศิลปินฝึกสกิล), `EnterpriseManager.AddMoney()` (แผนกบัญชีเพิ่มเงิน)

  * **Interface (อินเทอร์เฟซ):**

      * **เปรียบเทียบ:** คือ **"สัญญา"** หรือ **"รายการเมนู"** ในร้านอาหาร
      * **ในโค้ด:** มันคือชุดของ "ความสามารถ" ที่เราคาดหวังว่าคลาสใดๆ จะต้องมี เช่น `IArtistDataProvider` (สัญญาว่าใครก็ตามที่ทำตามสัญญานี้ จะต้อง "ให้ข้อมูลศิลปินได้") คลาสที่ทำตามสัญญานี้ก็จะต้องมีเมธอดตามที่ Interface กำหนดไว้ (เช่น `GetArtistName()`)

-----

### 3.1 Singleton Pattern (รูปแบบ Singleton)

  * **เปรียบเทียบ:** ลองนึกถึง **"CEO ของบริษัท"** หรือ **"หัวหน้าแผนก"** ที่มีคนเดียวในบริษัท และทุกคนต้องไปติดต่อกับคนนี้โดยตรง

  * **หลักการในโค้ด:** เป็นรูปแบบการออกแบบที่รับประกันว่า **คลาสหนึ่งๆ จะมี Object (Instance) ได้เพียงตัวเดียวเท่านั้น** ในระบบของเรา Manager หลักๆ (เช่น `GameCoreManager`, `EnterpriseManager`) จะใช้รูปแบบนี้ เพื่อให้โค้ดส่วนไหนของเกมก็สามารถเข้าถึง Manager ตัวนั้นได้ง่ายๆ

  * **ข้อดี:**

      * **เข้าถึงง่าย:** โค้ดส่วนไหนก็เรียกใช้ได้ทันที เช่น `EnterpriseManager.Instance.AddMoney(100);`

      * **มั่นใจว่ามีอยู่เสมอ:** ไม่ต้องกลัวว่าโหลดฉากใหม่แล้ว Manager จะหายไป เพราะเราจะทำให้มัน `DontDestroyOnLoad`

  * **ข้อควรระวัง (Over-Singleton-ization):**

      * **เปรียบเทียบ:** ถ้าทุกแผนกมี "หัวหน้า" ที่เป็น Singleton หมด และทุกหัวหน้าก็พยายามจะไปดูแลทุกเรื่องเอง หรือทุกคนก็ไปติดต่อแต่ CEO คนเดียวทุกเรื่อง มันก็จะกลายเป็น **"คอขวด"** หรือ **"ความวุ่นวาย"** ได้ง่ายๆ

      * **ในโค้ด:** ถ้าเราใช้ Singleton กับทุกๆ คลาสมากเกินไป หรือทำให้ Singleton ตัวใดตัวหนึ่งทำหน้าที่เยอะเกินไป (กลายเป็น "God Object") จะทำให้โค้ดดูยุ่งเหยิง ทดสอบยาก และแก้ไขยากขึ้นได้ ควรใช้กับส่วนที่ "จำเป็นต้องมีแค่ตัวเดียวและเข้าถึงได้ง่ายจากทุกที่" เท่านั้น

### 3.2 Dependency Injection (DI) (การยื่นของให้)

  * **เปรียบเทียบ:** ลองนึกถึง **"การจัดหาอุปกรณ์ให้พนักงาน"** แทนที่จะให้พนักงานไปหาซื้ออุปกรณ์เอง บริษัทก็จัดหาอุปกรณ์ที่จำเป็นให้พนักงานเลย

      * **สถานการณ์ปกติ (ไม่ใช้ DI):** คุณเป็นพนักงาน (คลาส) ที่ต้องใช้ "ปากกา" (Dependency) คุณก็เดินไปที่ "ห้องเก็บอุปกรณ์กลาง" (Manager.Instance) แล้วหยิบปากกามาใช้เอง

      * **สถานการณ์ใช้ DI:** หัวหน้า (ตัวกลาง) รู้ว่าคุณต้องใช้ปากกา ก็เลย "ยื่นปากกาให้" คุณตั้งแต่ต้น คุณไม่ต้องไปเดินหาเองเลย

  * **หลักการในโค้ด:** แทนที่จะให้ Manager หนึ่งๆ (เช่น `ArtistScheduleManager`) ไป "หา" ข้อมูลจากอีก Manager หนึ่ง (เช่น `ArtistProfileManager.Instance`) โดยตรง เราจะ **"ยื่น"** หรือ **"ส่ง"** สิ่งที่ Manager นั้นต้องการให้มันตั้งแต่แรก

      * **Interface (สัญญา):** เราจะสร้าง **"สัญญา" (Interface)** ขึ้นมา (เช่น `IArtistDataProvider`) ที่บอกว่าใครก็ตามที่ทำตามสัญญานี้ จะต้องมีความสามารถอะไรบ้าง (เช่น `GetArtistName()`, `TrainArtist()`) มันคือ **"คุณสมบัติ"** ที่เราคาดหวังจาก "ของ" ที่จะถูกยื่นให้

      * **การยื่นของ (Injection):** Manager ที่ต้องการข้อมูล (เช่น `ArtistScheduleManager`) จะรับ "สัญญา" นี้เข้ามาแทนที่จะรับคลาสจริง ทำให้มันไม่ผูกติดกับคลาสใดคลาสหนึ่ง

      * **ตัวอย่างง่ายๆ:**

        ```csharp
        // นี่คือ "สัญญา" ว่าใครก็ตามที่ทำตามสัญญานี้ จะให้ข้อมูลศิลปินได้
        public interface IArtistDataProvider { /* ... */ }

        // ArtistProfileManager ทำตามสัญญานี้ และเป็น Singleton
        public class ArtistProfileManager : Singleton<ArtistProfileManager>, IArtistDataProvider { /* ... */ }

        // ArtistScheduleManager ไม่ได้ไปหา ArtistProfileManager.Instance ตรงๆ
        // แต่มีช่องทาง "รับของขวัญ" (provider) ที่เป็นไปตามสัญญา IArtistDataProvider
        public class ArtistScheduleManager : Singleton<ArtistScheduleManager> {
            private IArtistDataProvider _artistDataProvider;
            public void Initialize(IArtistDataProvider provider) { // นี่คือการ "ยื่นของให้"
                _artistDataProvider = provider;
            }
            // ... แล้วค่อยใช้ _artistDataProvider ในเมธอดต่างๆ
        }

        // GameCoreManager เป็น "คนกลาง" ที่รู้ว่าใครต้องการอะไร แล้วก็ "ยื่นของ" ให้
        public class GameCoreManager : Singleton<GameCoreManager> {
            private void InitializeManagers() {
                // ยื่น ArtistProfileManager.Instance (ซึ่งทำตามสัญญา IArtistDataProvider)
                // ให้กับ ArtistScheduleManager.Instance
                ArtistScheduleManager.Instance.Initialize(ArtistProfileManager.Instance);
            }
        }
        ```

  * **ข้อดีของ Dependency Injection (DI):**

      * **ลดการผูกติดกัน (Loose Coupling):**

          * **เปรียบเทียบ:** เหมือนกับการออกแบบรถยนต์ที่ **"เปลี่ยนเครื่องยนต์ได้ง่ายๆ"** ไม่ว่าจะเป็นเครื่องยนต์ดีเซล, เบนซิน, หรือไฟฟ้า ขอแค่มี "ช่องเสียบ" (Interface) ที่เหมือนกัน คุณก็เอาเครื่องยนต์ไหนมาใส่ก็ได้หมดเลย\!

          * **ในโค้ด:** Manager แต่ละตัวจะพึ่งพากันแบบหลวมๆ ทำให้แก้ไขส่วนหนึ่งได้โดยไม่กระทบส่วนอื่นมากนัก ถ้าเราเปลี่ยน `ArtistProfileManager` ไปใช้ `NewArtistProfileManager` (ที่ทำตามสัญญา `IArtistDataProvider` เหมือนเดิม) `ArtistScheduleManager` ก็ยังทำงานได้ปกติ ไม่ต้องแก้โค้ดเลย

      * **ง่ายต่อการทดสอบ (Easier Unit Testing):**

          * **เปรียบเทียบ:** ถ้าคุณเป็นพนักงานที่ต้องใช้ปากกา คุณสามารถทดสอบการเขียนของคุณได้โดยใช้ "ปากกาปลอม" ที่คุณสร้างขึ้นมาเอง (Mock Object) โดยไม่ต้องไปยุ่งกับห้องเก็บอุปกรณ์จริงๆ

          * **ในโค้ด:** เราสามารถ "จำลอง" (Mock) ข้อมูลปลอมที่ทำตามสัญญา `IArtistDataProvider` ขึ้นมา เพื่อทดสอบแค่ `ArtistScheduleManager` อย่างเดียวได้ง่ายๆ ทำให้การทดสอบเร็วและแม่นยำขึ้น

      * **เพิ่มความแข็งแกร่งและเสถียร (Robustness & Stability):**

          * เมื่อโค้ดแต่ละส่วนพึ่งพากันน้อยลง โอกาสเกิด Bug ที่เกิดจากการเปลี่ยนแปลงที่ไม่คาดคิดก็ลดลง ทำให้ระบบโดยรวมมีความเสถียรและยืดหยุ่นต่อการเปลี่ยนแปลงในอนาคตได้ดีกว่า

  * **ข้อสังเกตสำหรับ `GameCoreManager`:**

      * `GameCoreManager` ซึ่งเป็นแกนหลักของเกม **ไม่จำเป็นต้องมี Interface** เพราะมันเป็นตัวควบคุมหลักที่ Manager อื่นๆ พึ่งพาอยู่แล้ว และไม่ค่อยมีการเปลี่ยนแปลง Logic บ่อย

### 3.3 Events / Observer Pattern (การประกาศข่าวสาร)

  * **เปรียบเทียบ:** เหมือนกับการ **"ประกาศข่าวสาร"** หรือ **"แจ้งเตือน"** ในบริษัท เมื่อมีเรื่องสำคัญเกิดขึ้น (เช่น เงินในบริษัทเปลี่ยน, ศิลปินฝึกเสร็จ) แผนกที่เกี่ยวข้องก็จะรับรู้และดำเนินการต่อเอง โดยไม่ต้องมีใครมาคอย "ถาม" ตลอดเวลา

  * **หลักการในโค้ด:** Manager ที่มีการเปลี่ยนแปลงข้อมูลจะ **"ส่ง Event"** ออกไป (เช่น `EnterpriseManager.OnMoneyChanged`) แล้ว Manager อื่นๆ ที่สนใจก็จะ **"สมัครรับ Event"** นั้นๆ พอมี Event เกิดขึ้น Manager ที่สนใจก็จะถูกเรียกให้ทำงานเองโดยไม่จำเป็นต้องรู้ว่าใครส่ง Event มา

  * **ข้อดี:**

      * **ลดการผูกติดกัน (Loose Coupling):** Manager ไม่ต้องรู้ว่าใครสนใจข้อมูลของมัน ทำให้โค้ดสะอาดขึ้น

      * **อัปเดตข้อมูลอัตโนมัติ:** เมื่อข้อมูลเปลี่ยน UI หรือส่วนอื่นๆ ที่สนใจก็จะอัปเดตตัวเองได้ทันที

### 3.4 แนวทางการสร้างความสัมพันธ์การพึ่งพาระหว่างแต่ละ Manager (สำหรับโค้ดเดอร์)

การที่ Manager แต่ละตัวจะ "คุย" กันยังไงนั้นสำคัญมาก เพื่อให้ระบบของเราเป็นระเบียบและยืดหยุ่น:

  * **หลักการ "Tell, Don't Ask" (บอก ไม่ใช่ถาม):**

      * **เปรียบเทียบ:** แทนที่ CEO จะต้องคอยเดินไป "ถาม" ทุกแผนกตลอดเวลาว่า "งานเสร็จหรือยัง?" "มีปัญหาอะไรไหม?" ให้แผนกต่างๆ **"บอก"** หรือ **"รายงาน"** CEO เองเมื่อมีเรื่องสำคัญเกิดขึ้น หรือเมื่อทำงานเสร็จ

      * **ในโค้ด:** แทนที่ `ManagerA` จะไป "ถาม" ข้อมูลจาก `ManagerB` โดยตรงบ่อยๆ ให้ `ManagerB` เป็นคน "ส่ง Event" ออกไปเมื่อข้อมูลของมันมีการเปลี่ยนแปลง แล้ว `ManagerA` ที่สนใจก็แค่ "รับฟัง" Event นั้นๆ

  * **ลำดับชั้นการพึ่งพา (Hierarchy of Dependencies):**

      * **เปรียบเทียบ:** ในบริษัทมักจะมีโครงสร้างการบริหารงานเป็นลำดับชั้น เช่น CEO อยู่บนสุด, รองลงมาเป็นหัวหน้าแผนก, และพนักงาน

      * **ในโค้ด:**

          * **`GameCoreManager`:** ควรเป็นตัวที่อยู่บนสุด เป็นแกนกลางที่ควบคุมภาพรวม ไม่ควรพึ่งพา Manager อื่นๆ มากนัก แต่จะเป็นตัวที่ "ยื่นของ" (DI) ให้ Manager อื่นๆ และคอย "รับฟัง" Event สำคัญจาก Manager อื่นๆ

          * **Manager หลักๆ (เช่น `EnterpriseManager`, `ArtistProfileManager`):** อาจจะพึ่งพา Manager อื่นๆ น้อย หรือเป็นแหล่งข้อมูลหลักที่ Manager อื่นๆ มาดึงไปใช้

          * **Manager ย่อยๆ (เช่น `ArtistScheduleManager`, `GameUIManager`):** มักจะพึ่งพา Manager หลักๆ หลายตัว เพื่อดึงข้อมูลมาแสดงผล หรือส่งคำสั่งกลับไปให้ Manager หลักทำงาน

  * **เน้นการใช้ Events/Callbacks:** นี่คือวิธีที่ดีที่สุดในการลดการผูกติดกัน\! ใช้ `System.Action` หรือ `System.Action<T>` เพื่อให้ Manager สื่อสารกันแบบ "ประกาศ" (Broadcast) และ "ฟัง" (Listen) แทนที่จะ "เรียกตรงๆ" (Direct Call)

-----

## 4\. การทำงานใน Unity Editor (สำหรับโค้ดเดอร์)

1.  **สร้าง GameObject เปล่าๆ:** ใน Scene แรกสุดของเกม (เช่น `Splash Scene` หรือ `Main Menu Scene`) ให้สร้าง GameObject เปล่าๆ ขึ้นมา 1 ตัว ตั้งชื่อว่า `_GameManagers`

2.  **Attach Script:** ลาก Script ของ Manager แต่ละตัว (เช่น `GameCoreManager.cs`, `EnterpriseManager.cs`, `ArtistProfileManager.cs`) ไปใส่ใน GameObject `_GameManagers` นี้

3.  **`DontDestroyOnLoad`:** ใน Script ของ Manager ที่สืบทอดมาจาก `Singleton<T>` จะมีโค้ด `DontDestroyOnLoad(gameObject);` อยู่แล้ว ซึ่งจะทำให้ GameObject `_GameManagers` และ Manager ทั้งหมดที่อยู่ภายใต้ GameObject นี้อยู่รอดข้าม Scene ได้

-----

## 5\. ตัวอย่างโครงสร้างโค้ด (สำหรับโค้ดเดอร์)

### 5.1 Base Class สำหรับ Singleton Manager

นี่คือคลาสพื้นฐานที่ทำให้ Manager ของเราเป็น Singleton ได้ง่ายๆ

```csharp
using UnityEngine;

// T คือประเภทของ Manager ที่เราต้องการให้เป็น Singleton
// where T : MonoBehaviour คือข้อจำกัดว่า T ต้องเป็นคลาสที่สืบทอดมาจาก MonoBehaviour
public abstract class Singleton<T> : MonoBehaviour where T : MonoBehaviour
{
    private static T _instance; // ตัวแปรสำหรับเก็บ Instance เดียวของ Manager

    public static T Instance // Property สำหรับเข้าถึง Instance ของ Manager
    {
        get
        {
            // ถ้า _instance ยังเป็น null (ยังไม่เคยมี Manager ตัวนี้ถูกสร้าง)
            if (_instance == null)
            {
                // ลองหาใน Scene ก่อนว่ามี Manager ตัวนี้อยู่แล้วไหม
                _instance = FindObjectOfType<T>();

                // ถ้ายังหาไม่เจอ ก็สร้าง GameObject ใหม่แล้ว Add Component Manager นั้นเข้าไป
                if (_instance == null)
                {
                    GameObject singletonObject = new GameObject();
                    _instance = singletonObject.AddComponent<T>();
                    singletonObject.name = typeof(T).ToString() + " (Singleton)";
                }
            }
            return _instance;
        }
    }

    protected virtual void Awake()
    {
        // ตรวจสอบว่าตัวนี้คือ Instance แรกที่ถูกสร้างขึ้นมาหรือไม่
        if (_instance == null)
        {
            _instance = this as T; // กำหนดให้ตัวเองเป็น Instance
            DontDestroyOnLoad(gameObject); // ทำให้ GameObject นี้ไม่ถูกทำลายเมื่อโหลด Scene ใหม่
        }
        else if (_instance != this) // ถ้ามี Instance อื่นอยู่แล้ว และไม่ใช่ตัวเราเอง
        {
            Destroy(gameObject); // ทำลายตัวเองทิ้งซะ (ป้องกันมี Manager ซ้ำซ้อน)
        }
    }
}
```

### 5.2 ตัวอย่าง Manager ที่ Implement Interface และรับ DI

```csharp
using UnityEngine;
using System;
using System.Collections.Generic; // สำหรับ List

// -------------------------------------------------------------
// IArtistDataProvider.cs (Interface/สัญญา)
// -------------------------------------------------------------
// สัญญาสำหรับ Manager ที่ให้ข้อมูลศิลปิน
public interface IArtistDataProvider
{
    string GetArtistName(int artistId);
    void TrainArtist(int artistId);
    // อาจจะมีเมธอดอื่นๆ ที่จำเป็นสำหรับดึงข้อมูลศิลปิน
}

// -------------------------------------------------------------
// ArtistProfileManager.cs (ตัวจัดการโปรไฟล์ศิลปิน)
// -------------------------------------------------------------
// เป็น Singleton และทำตามสัญญา IArtistDataProvider
public class ArtistProfileManager : Singleton<ArtistProfileManager>, IArtistDataProvider
{
    // ข้อมูลศิลปิน (ตัวอย่าง)
    public List<ArtistData> Artists { get; private set; } = new List<ArtistData>();

    protected override void Awake()
    {
        base.Awake();
        Debug.Log("ArtistProfileManager พร้อมใช้งานแล้ว!");
        // เพิ่มข้อมูลศิลปินเริ่มต้น (ตัวอย่าง)
        if (Artists.Count == 0)
        {
            Artists.Add(new ArtistData { id = 1, artistName = "ลิซ่า", vocalSkill = 80, danceSkill = 90 });
            Artists.Add(new ArtistData { id = 2, artistName = "เจนนี่", vocalSkill = 85, danceSkill = 85 });
        }
    }

    public string GetArtistName(int artistId)
    {
        ArtistData artist = Artists.Find(a => a.id == artistId);
        return artist != null ? artist.artistName : "ไม่พบศิลปิน";
    }

    public void TrainArtist(int artistId)
    {
        ArtistData artist = Artists.Find(a => a.id == artistId);
        if (artist != null)
        {
            artist.vocalSkill += 5; // เพิ่มสกิลร้อง
            Debug.Log($"[ArtistProfileManager] ฝึกฝน {artist.artistName} แล้ว! สกิลร้อง: {artist.vocalSkill}");
            // อาจจะส่ง Event แจ้งเตือนว่าสกิลศิลปินเปลี่ยน
        }
    }
    // เมธอด GetSaveData() และ LoadData() จะถูกเพิ่มในเอกสาร Save/Load
}

// -------------------------------------------------------------
// ArtistScheduleManager.cs (ตัวจัดการตารางงานศิลปิน)
// -------------------------------------------------------------
// เป็น Singleton และรับ IArtistDataProvider ผ่าน DI
public class ArtistScheduleManager : Singleton<ArtistScheduleManager>
{
    private IArtistDataProvider _artistDataProvider; // ตัวแปรสำหรับเก็บสิ่งที่ถูกยื่นให้

    // เมธอดสำหรับ "รับของขวัญ" (Initialize/Inject)
    public void Initialize(IArtistDataProvider provider)
    {
        _artistDataProvider = provider;
        Debug.Log("ArtistScheduleManager ได้รับ Artist Data Provider แล้ว!");
    }

    public void ScheduleConcert(int artistId)
    {
        if (_artistDataProvider == null)
        {
            Debug.LogError("Artist Data Provider ไม่ถูกตั้งค่าใน ArtistScheduleManager! โปรดเรียก Initialize ก่อน.");
            return;
        }

        string artistName = _artistDataProvider.GetArtistName(artistId);
        Debug.Log($"จัดคอนเสิร์ตให้ {artistName} (ID: {artistId}) แล้ว!");

        // อาจจะมีการเรียกใช้เมธอดอื่นๆ ของ provider ด้วย
        _artistDataProvider.TrainArtist(artistId); // สมมติว่าการจัดคอนเสิร์ตมีการฝึกฝนด้วย
    }
    // เมธอด GetSaveData() และ LoadData() จะถูกเพิ่มในเอกสาร Save/Load
}

// -------------------------------------------------------------
// ArtistData.cs (Class สำหรับเก็บข้อมูลศิลปินแต่ละคน)
// -------------------------------------------------------------
[System.Serializable] // ต้องใส่ [Serializable] เพื่อให้สามารถเซฟ/โหลดได้
public class ArtistData
{
    public int id;
    public string artistName;
    public int vocalSkill;
    public int danceSkill;
    // ... เพิ่มข้อมูลอื่นๆ ของศิลปินที่จำเป็น ...
}

// -------------------------------------------------------------
// GameCoreManager.cs (คนกลางที่จัดการการเชื่อมต่อ)
// -------------------------------------------------------------
public class GameCoreManager : Singleton<GameCoreManager>
{
    protected override void Awake()
    {
        base.Awake();
        Debug.Log("GameCoreManager พร้อมใช้งานแล้ว!");
        InitializeManagers(); // เรียกเมธอดเชื่อมต่อ Manager
    }

    private void InitializeManagers()
    {
        // ตรวจสอบให้แน่ใจว่า ArtistProfileManager.Instance ถูกสร้างแล้ว
        // แล้ว "ยื่น" ArtistProfileManager.Instance (ซึ่งทำตามสัญญา IArtistDataProvider)
        // ให้กับ ArtistScheduleManager.Instance
        ArtistScheduleManager.Instance.Initialize(ArtistProfileManager.Instance);

        Debug.Log("Managers ทั้งหมดถูกเชื่อมต่อด้วย DI แล้ว!");
    }

    void Start()
    {
        // ตัวอย่างการใช้งาน: สั่งให้ ArtistScheduleManager จัดคอนเสิร์ต
        ArtistScheduleManager.Instance.ScheduleConcert(1); // จัดคอนเสิร์ตให้ลิซ่า
        ArtistScheduleManager.Instance.ScheduleConcert(2); // จัดคอนเสิร์ตให้เจนนี่
    }
}
```

### 5.3 ตัวอย่าง Manager ที่ใช้ Events/Observer Pattern

```csharp
using UnityEngine;
using System;
using TMPro; // ถ้าใช้ TextMeshPro

// -------------------------------------------------------------
// EnterpriseManager.cs (ตัวจัดการข้อมูลบริษัท)
// -------------------------------------------------------------
public class EnterpriseManager : Singleton<EnterpriseManager>
{
    public int Money { get; private set; } = 1000; // เงินในบริษัท
    public int FamePoints { get; private set; } = 0; // ชื่อเสียงค่าย

    // Event สำหรับแจ้งเตือนเมื่อเงินเปลี่ยน (แนบข้อมูลจำนวนเงินที่เปลี่ยนและเงินปัจจุบัน)
    public static event Action<int, int> OnMoneyChanged;
    // Event สำหรับแจ้งเตือนเมื่อชื่อเสียงเปลี่ยน (แนบข้อมูลชื่อเสียงปัจจุบัน)
    public static event Action<int> OnFamePointsChanged;

    protected override void Awake()
    {
        base.Awake();
        Debug.Log("EnterpriseManager พร้อมใช้งานแล้ว!");
    }

    public void AddMoney(int amount)
    {
        Money += amount;
        Debug.Log($"เงิน: {Money}");
        OnMoneyChanged?.Invoke(amount, Money); // ส่ง Event พร้อมข้อมูล
    }

    public void SpendMoney(int amount)
    {
        if (Money >= amount)
        {
            Money -= amount;
            Debug.Log($"เงิน: {Money}");
            OnMoneyChanged?.Invoke(-amount, Money); // ส่ง Event พร้อมข้อมูล (ติดลบคือใช้ไป)
            return;
        }
        Debug.Log("ไม่พอจ้า!");
    }

    public void AddFamePoints(int amount)
    {
        FamePoints += amount;
        Debug.Log($"ชื่อเสียง: {FamePoints}");
        OnFamePointsChanged?.Invoke(FamePoints); // ส่ง Event พร้อมข้อมูล
    }
    // เมธอด GetSaveData() และ LoadData() จะถูกเพิ่มในเอกสาร Save/Load
}

// -------------------------------------------------------------
// GameUIManager.cs (ตัวจัดการ UI)
// -------------------------------------------------------------
public class GameUIManager : Singleton<GameUIManager>
{
    public TextMeshProUGUI moneyText; // UI Text สำหรับแสดงเงิน
    public TextMeshProUGUI fameText; // UI Text สำหรับแสดงชื่อเสียง

    protected override void Awake()
    {
        base.Awake();
        // ตรวจสอบว่ามี Text UI อ้างอิงหรือไม่
        if (moneyText == null) Debug.LogError("Money Text (TMP) is not assigned in GameUIManager!");
        if (fameText == null) Debug.LogError("Fame Text (TMP) is not assigned in GameUIManager!");
    }

    void OnEnable()
    {
        // สมัครรับ Event เมื่อ GameObject ถูกเปิดใช้งาน
        EnterpriseManager.OnMoneyChanged += UpdateMoneyDisplay;
        EnterpriseManager.OnFamePointsChanged += UpdateFameDisplay;

        // อัปเดตค่าเริ่มต้นเมื่อเปิดใช้งาน (กรณีโหลดเกมหรือเริ่มเกมใหม่)
        if (EnterpriseManager.Instance != null)
        {
            UpdateMoneyDisplay(0, EnterpriseManager.Instance.Money);
            UpdateFameDisplay(EnterpriseManager.Instance.FamePoints);
        }
    }

    void OnDisable()
    {
        // ยกเลิกการรับ Event เมื่อ GameObject ถูกปิดใช้งาน (สำคัญมาก!)
        EnterpriseManager.OnMoneyChanged -= UpdateMoneyDisplay;
        EnterpriseManager.OnFamePointsChanged -= UpdateFameDisplay;
    }

    // เมธอดสำหรับอัปเดต UI เมื่อ Event ถูกเรียก
    private void UpdateMoneyDisplay(int amountChanged, int currentMoney)
    {
        moneyText.text = $"เงิน: {currentMoney} ({ (amountChanged >= 0 ? "+" : "") }{amountChanged})";
        Debug.Log($"UI แสดง: เงินเปลี่ยนไป {amountChanged}, ปัจจุบันคือ {currentMoney}");
    }

    private void UpdateFameDisplay(int currentFame)
    {
        fameText.text = $"ชื่อเสียง: {currentFame}";
        Debug.Log($"UI แสดง: ชื่อเสียงปัจจุบันคือ {currentFame}");
    }

    // ตัวอย่างการเรียกใช้ Manager จาก UI (เช่น ปุ่มกด)
    public void OnClickEarnMoneyButton()
    {
        EnterpriseManager.Instance.AddMoney(100);
    }

    public void OnClickSpendMoneyButton()
    {
        EnterpriseManager.Instance.SpendMoney(50);
    }
}
```

-----

## 6\. การสร้าง Manager เพิ่ม หรือเอาออกทีหลัง

ไม่ต้องกังวลเลยจ้ะ\! ด้วยโครงสร้างที่เราออกแบบมานี้ การเพิ่มหรือเอา Manager ออกทีหลังนั้น **ไม่ได้ยากเลย\!** เพราะเราเน้นการแบ่งงานเป็นส่วนๆ และลดการผูกติดกัน

  * **การสร้าง Manager เพิ่ม (Add New Manager):**

      * **เปรียบเทียบ:** เหมือนกับการ **"เปิดแผนกใหม่"** ในบริษัท เมื่อมีงานใหม่ๆ เพิ่มขึ้น

      * **ในโค้ด:**

        1.  สร้าง Script C\# ขึ้นมาใหม่ (เช่น `ArtistEventManager.cs`)

        2.  ให้มันสืบทอดจาก `Singleton<T>` (เช่น `public class ArtistEventManager : Singleton<ArtistEventManager> { ... }`)

        3.  ถ้า Manager ใหม่นี้ต้องให้ข้อมูลกับ Manager อื่นๆ ก็สร้าง **Interface (สัญญา)** ให้มัน Implement ด้วย (เช่น `IArtistEventProvider`)

        4.  ถ้า Manager ใหม่นี้ต้องใช้ข้อมูลจาก Manager อื่นๆ ก็เพิ่มเมธอด `Initialize()` เพื่อ **"รับของขวัญ" (DI)** เข้ามา

        5.  นำ Script นี้ไปใส่ใน GameObject `_GameManagers` ใน Unity Editor

        6.  ที่สำคัญคือ ไปแก้ไขใน `GameCoreManager.cs` ตรงเมธอด `InitializeManagers()` เพื่อ **"เชื่อมต่อ"** Manager ใหม่นี้เข้ากับระบบ (เช่น เรียก `ArtistEventManager.Instance.Initialize(...)`) แค่นี้ก็พร้อมใช้งานแล้ว\!

  * **การเอา Manager ออก (Remove Manager):**

      * **เปรียบเทียบ:** เหมือนกับการ **"ยุบแผนก"** ที่ไม่จำเป็นแล้ว

      * **ในโค้ด:**

        1.  ลบ Script ของ Manager นั้นออกจากโปรเจกต์

        2.  ลบ Component ของ Manager นั้นออกจาก GameObject `_GameManagers` ใน Unity Editor

        3.  ไปไล่ลบโค้ดที่เคยเรียกใช้ Manager ตัวนั้นๆ ใน Script อื่นๆ (โดยเฉพาะใน `GameCoreManager.cs` ตรง `InitializeManagers()`)

      * **ทำไมถึงง่าย?** เพราะเราออกแบบให้ Manager แต่ละตัวพึ่งพากันแบบหลวมๆ (Loose Coupling) ผ่าน Interface และ Events ทำให้การลบ Manager ตัวหนึ่งกระทบกับส่วนอื่นๆ น้อยที่สุด คุณจะแก้แค่โค้ดที่ "เชื่อมต่อ" หรือ "เรียกใช้" Manager นั้นโดยตรงเท่านั้นเอง

-----

## สรุป: Persistent Manager

ระบบ Persistent Manager เป็นรากฐานสำคัญของเกม TPop Manager ที่ช่วยให้ข้อมูลสำคัญคงอยู่ตลอดเวลา และถูกจัดระเบียบอย่างเป็นระบบผ่าน Manager หลายตัว การใช้ Singleton, Dependency Injection และ Events จะช่วยให้โค้ดของเรายืดหยุ่น แข็งแกร่ง และดูแลรักษาง่ายในระยะยาว

-----

**หมายเหตุ:** โค้ดตัวอย่างข้างต้นใช้ `UnityEngine.Debug.Log` สำหรับการแสดงผลใน Console ของ Unity เพื่อให้เห็นภาพการทำงานเบื้องต้น ในการพัฒนาจริง คุณอาจจะใช้ระบบ Log ที่ซับซ้อนกว่านี้
