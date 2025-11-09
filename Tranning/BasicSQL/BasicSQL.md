# 📝 SQL DB《初階》技術手冊

## 📋 文件說明

| 撰寫人員 | 文件版本 | 建立日期 |
| -------- | -------- | -------- |
| BoyaWu     | 1.0     | 2025/11/09     |


---

### 目的
本技術手冊旨在系統化整理 SQL Server 資料庫初階操作流程，提供學習者一套標準化的實作指南。內容涵蓋資料表建立、資料操作（INSERT/UPDATE/DELETE）、觸發器設定、查詢語法設計、自訂函數與預存程序等核心技能，協助使用者依循步驟完成各項資料庫操作任務，強化實務能力並支援後續考核需求。

### 適用對象
- SQL Server 資料庫初階學習者
- 需要進行資料庫作業練習的學員
- 資料庫管理系統（DBMS）課程參與者

### 使用範圍
本手冊涵蓋以下作業項目：
- SQL Server Management Studio (SSMS) 連線設定
- 資料庫初始化與資料準備
- 資料表建立與資料操作（INSERT/UPDATE/DELETE）
- 觸發器（Trigger）建立與驗證
- 資料表關聯查詢（JOIN）
- 群組查詢與條件篩選（GROUP BY/HAVING）
- 自訂函數建立（純量函數與資料表值函數）
- 預存程序（Stored Procedure）建立與執行
- SQL Server Agent 排程作業設定

### 注意事項
- 本手冊使用測試環境，請勿在正式環境中執行
- 執行前請確認已正確連線至指定資料庫伺服器
- 建議依序完成各題目，以確保資料完整性
- 完成所有作業後，請記得清理測試資料與物件

---

## 🔐 開啟 SSMS

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm207.png)

| 欄位 | 設定值 |
| --- | --- |
| **伺服器名稱 (S)** | `192.168.3.150` |
| **驗證 (A)** | SQL Server 驗證 |
| **登入 (L)** | `sa` |
| **密碼 (P)** | `1qaz@WSX` |

## 🦖 資料庫

於 `1test`  右鍵 → 點選 `新增查詢`

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm507.png)

將每個`程式碼區塊`的指令貼在右側後，執行 (F5) 即可

> **💡 操作提示**  
> 在 SSMS 中，您可以將 SQL 程式碼複製貼上到查詢視窗，然後按 `F5` 或點選「執行」按鈕來執行查詢。程式碼區塊中的註解（以 `--` 開頭）是說明文字，不會被執行。

```sql
-- 這是一個程式碼區塊的範例
```

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm177.png)


## 🦖 初始化

> **📚 教學說明**  
> 初始化步驟會先清理舊的資料表、預存程序和函數，然後重新建立所需的資料表結構，並插入測試資料。這個步驟確保每次練習都從乾淨的環境開始。
> 
> **重點概念：**
> - `DROP TABLE IF EXISTS`：安全地刪除資料表（如果存在）
> - `CREATE TABLE`：建立資料表結構，定義欄位名稱、資料型態和約束條件
> - `INSERT INTO`：將資料插入資料表中
> - `PRIMARY KEY`：主鍵，用於唯一識別每筆記錄

```sql
-- 移除
DROP TABLE IF EXISTS Material_Info_Temp;
DROP TABLE IF EXISTS UpdateLog;
DROP TABLE IF EXISTS CostTable;
DROP TABLE IF EXISTS UpdateLog;
DROP TABLE IF EXISTS Material_Info;
DROP TABLE IF EXISTS Machine_Part;
DROP PROCEDURE IF EXISTS [dbo].[sp_Insert_Gas_EndDate_Remind_edited];
DROP PROCEDURE IF EXISTS [dbo].[UpdateCostBasedOnTime];
DROP FUNCTION IF EXISTS [dbo].[GetMaterialInfoByPartNo];
DROP FUNCTION IF EXISTS [dbo].[CountMaterialUpdatesByTime];

-- 建表
CREATE TABLE dbo.CostTable (
    ID INT NOT NULL PRIMARY KEY,
    Name NVARCHAR(50) NULL,
    cost INT NULL
);

CREATE TABLE [dbo].[Machine_Part](
	[GUID] [uniqueidentifier] ROWGUIDCOL  NOT NULL,
	[Factory] [nvarchar](50) NOT NULL,
	[Phase] [nvarchar](50) NOT NULL,
	[Tag_Name] [nvarchar](50) NOT NULL,
	[Part_No] [nvarchar](50) NOT NULL,
	[Part_Name] [nvarchar](50) NULL,
	[Max_Day_Qty] [int] NULL,
	[Now_Qty] [int] NULL,
	[Drum_Now_Qty] [int] NULL,
	[Drum_Set_Qty] [int] NULL,
	[Rpm_Code] [int] NULL,
	[Run_Mins] [int] NULL,
	[Effective_Mins] [int] NULL,
	[Barcode_Check_Code] [nvarchar](50) NULL,
	[Update_User_No] [nvarchar](50) NULL,
	[Update_Time] [datetime] NULL,
	[Case_No] [nvarchar](20) NULL,
	[Case_Status] [nvarchar](10) NULL,
	[SJ_Guid] [uniqueidentifier] NULL,
	[SJ_Update_Time] [datetime] NULL
)

CREATE TABLE [dbo].[Material_Info](
	[GUID] [uniqueidentifier] NOT NULL,
	[Factory] [nvarchar](50) NULL,
	[Phase] [nvarchar](50) NULL,
	[Tag_Name] [nvarchar](50) NULL,
	[Pallet_No] [nvarchar](50) NULL,
	[Part_No] [nvarchar](50) NOT NULL,
	[Batch_No] [nvarchar](50) NOT NULL,
	[Seq_No] [nvarchar](50) NOT NULL,
	[End_Date] [datetime] NOT NULL,
	[MR_No] [nvarchar](50) NULL,
	[Status_Code] [nvarchar](10) NULL,
	[Update_User_No] [nvarchar](50) NOT NULL,
	[Update_Time] [datetime] NOT NULL,
	[Purge_Start_Time] [datetime] NULL,
	[Purge_End_Time] [datetime] NULL,
	[Supply_Time] [datetime] NULL,
	[Case_No] [nvarchar](20) NULL,
	[Case_Status] [nvarchar](10) NULL,
	[Update_User_No2] [nvarchar](50) NULL,
	[Vendor_Code] [nvarchar](50) NULL,
	[Storage_ID] [nvarchar](50) NULL,
	[Debit] [bit] NULL,
	[Pink] [bit] NULL,
	[Note] [nvarchar](50) NULL,
	[GiNo] [nvarchar](50) NULL,
	[GiItem] [nvarchar](50) NULL,
	[TEMP_Time] [datetime] NULL
)

-- 預設值
INSERT INTO dbo.CostTable (ID, Name, cost) 
SELECT 1, 'A', 10 UNION ALL
SELECT 2, 'B', 7 UNION ALL
SELECT 3, 'A', 15 UNION ALL
SELECT 4, 'A', 70 UNION ALL
SELECT 5, 'B', 35 UNION ALL
SELECT 6, 'C', 46 UNION ALL
SELECT 7, 'C', 22 UNION ALL
SELECT 8, 'D', 78 UNION ALL
SELECT 9, 'E', 321 UNION ALL
SELECT 10, 'A', 100 UNION ALL
SELECT 11, 'B', 100;

INSERT [dbo].[Machine_Part] ([GUID], [Factory], [Phase], [Tag_Name], [Part_No], [Part_Name], [Max_Day_Qty], [Now_Qty], [Drum_Now_Qty], [Drum_Set_Qty], [Rpm_Code], [Run_Mins], [Effective_Mins], [Barcode_Check_Code], [Update_User_No], [Update_Time], [Case_No], [Case_Status], [SJ_Guid], [SJ_Update_Time]) VALUES (N'42927f6a-52bf-4678-ae23-b5421a69866a', N'AP2C', N'AP2C', N'Tag001', N'1L111111', N'Part1', 100, 50, NULL, NULL, NULL, NULL, NULL, NULL, NULL, CAST(N'2025-11-08T19:24:36.743' AS DateTime), NULL, NULL, NULL, NULL)
INSERT [dbo].[Machine_Part] ([GUID], [Factory], [Phase], [Tag_Name], [Part_No], [Part_Name], [Max_Day_Qty], [Now_Qty], [Drum_Now_Qty], [Drum_Set_Qty], [Rpm_Code], [Run_Mins], [Effective_Mins], [Barcode_Check_Code], [Update_User_No], [Update_Time], [Case_No], [Case_Status], [SJ_Guid], [SJ_Update_Time]) VALUES (N'1758d7e6-d705-48ef-8a95-8558960d35b1', N'AP2C', N'AP2C', N'Tag002', N'1L111111', N'Part1', 100, 30, NULL, NULL, NULL, NULL, NULL, NULL, NULL, CAST(N'2025-11-08T19:24:36.743' AS DateTime), NULL, NULL, NULL, NULL)
INSERT [dbo].[Machine_Part] ([GUID], [Factory], [Phase], [Tag_Name], [Part_No], [Part_Name], [Max_Day_Qty], [Now_Qty], [Drum_Now_Qty], [Drum_Set_Qty], [Rpm_Code], [Run_Mins], [Effective_Mins], [Barcode_Check_Code], [Update_User_No], [Update_Time], [Case_No], [Case_Status], [SJ_Guid], [SJ_Update_Time]) VALUES (N'c9aefb80-ccda-4de7-83d6-2a2b4f27c541', N'AP2C', N'AP2C', N'Tag003', N'1L222222', N'Part2', 200, 100, NULL, NULL, NULL, NULL, NULL, NULL, NULL, CAST(N'2025-11-08T19:24:36.743' AS DateTime), NULL, NULL, NULL, NULL)
INSERT [dbo].[Machine_Part] ([GUID], [Factory], [Phase], [Tag_Name], [Part_No], [Part_Name], [Max_Day_Qty], [Now_Qty], [Drum_Now_Qty], [Drum_Set_Qty], [Rpm_Code], [Run_Mins], [Effective_Mins], [Barcode_Check_Code], [Update_User_No], [Update_Time], [Case_No], [Case_Status], [SJ_Guid], [SJ_Update_Time]) VALUES (N'389dff1d-bddd-4aa8-8a16-5d32b3adaa5d', N'AP2C', N'AP2C', N'Tag004', N'1L333333', N'Part3', 150, 75, NULL, NULL, NULL, NULL, NULL, NULL, NULL, CAST(N'2025-11-08T19:24:36.743' AS DateTime), NULL, NULL, NULL, NULL)
INSERT [dbo].[Machine_Part] ([GUID], [Factory], [Phase], [Tag_Name], [Part_No], [Part_Name], [Max_Day_Qty], [Now_Qty], [Drum_Now_Qty], [Drum_Set_Qty], [Rpm_Code], [Run_Mins], [Effective_Mins], [Barcode_Check_Code], [Update_User_No], [Update_Time], [Case_No], [Case_Status], [SJ_Guid], [SJ_Update_Time]) VALUES (N'dba9c50d-cf9f-463f-a266-a61f078dc3f1', N'AP2C', N'AP2C', N'Tag005', N'1L111111', N'Part1', 100, 20, NULL, NULL, NULL, NULL, NULL, NULL, NULL, CAST(N'2025-11-08T19:24:36.743' AS DateTime), NULL, NULL, NULL, NULL)

INSERT [dbo].[Material_Info] ([GUID], [Factory], [Phase], [Tag_Name], [Pallet_No], [Part_No], [Batch_No], [Seq_No], [End_Date], [MR_No], [Status_Code], [Update_User_No], [Update_Time], [Purge_Start_Time], [Purge_End_Time], [Supply_Time], [Case_No], [Case_Status], [Update_User_No2], [Vendor_Code], [Storage_ID], [Debit], [Pink], [Note], [GiNo], [GiItem], [TEMP_Time]) VALUES (N'e0354df9-845c-43b3-a47a-57da2fc73a35', N'AP2C', N'AP2C', N'Tag001', N'P001', N'1L111111', N'B001', N'S001', CAST(N'2024-12-31T00:00:00.000' AS DateTime), NULL, N'STK', N'User1', CAST(N'2019-10-15T10:00:00.000' AS DateTime), NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ST001', NULL, NULL, NULL, NULL, NULL, NULL)
INSERT [dbo].[Material_Info] ([GUID], [Factory], [Phase], [Tag_Name], [Pallet_No], [Part_No], [Batch_No], [Seq_No], [End_Date], [MR_No], [Status_Code], [Update_User_No], [Update_Time], [Purge_Start_Time], [Purge_End_Time], [Supply_Time], [Case_No], [Case_Status], [Update_User_No2], [Vendor_Code], [Storage_ID], [Debit], [Pink], [Note], [GiNo], [GiItem], [TEMP_Time]) VALUES (N'23133dd5-8e9c-4353-8a12-5b3867982605', N'AP2C', N'AP2C', N'Tag002', N'P002', N'1L111111', N'B002', N'S002', CAST(N'2024-11-30T00:00:00.000' AS DateTime), NULL, N'STK', N'User1', CAST(N'2019-11-20T14:30:00.000' AS DateTime), NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ST002', NULL, NULL, NULL, NULL, NULL, NULL)
INSERT [dbo].[Material_Info] ([GUID], [Factory], [Phase], [Tag_Name], [Pallet_No], [Part_No], [Batch_No], [Seq_No], [End_Date], [MR_No], [Status_Code], [Update_User_No], [Update_Time], [Purge_Start_Time], [Purge_End_Time], [Supply_Time], [Case_No], [Case_Status], [Update_User_No2], [Vendor_Code], [Storage_ID], [Debit], [Pink], [Note], [GiNo], [GiItem], [TEMP_Time]) VALUES (N'd1085772-86c2-4602-b319-8ee1b26cade7', N'AP2C', N'AP2C', N'Tag005', N'P005', N'1L111111', N'B005', N'S005', CAST(N'2024-08-31T00:00:00.000' AS DateTime), NULL, N'STK', N'User3', CAST(N'2019-10-25T11:20:00.000' AS DateTime), NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ST005', NULL, NULL, NULL, NULL, NULL, NULL)
INSERT [dbo].[Material_Info] ([GUID], [Factory], [Phase], [Tag_Name], [Pallet_No], [Part_No], [Batch_No], [Seq_No], [End_Date], [MR_No], [Status_Code], [Update_User_No], [Update_Time], [Purge_Start_Time], [Purge_End_Time], [Supply_Time], [Case_No], [Case_Status], [Update_User_No2], [Vendor_Code], [Storage_ID], [Debit], [Pink], [Note], [GiNo], [GiItem], [TEMP_Time]) VALUES (N'2f6cd1c1-3aca-4177-95cc-127b9aad4fdb', N'AP2C', N'AP2C', N'Tag003', N'P003', N'1L222222', N'B003', N'S003', CAST(N'2024-10-31T00:00:00.000' AS DateTime), NULL, N'REMAIN', N'User2', CAST(N'2019-09-15T09:00:00.000' AS DateTime), NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ST003', NULL, NULL, NULL, NULL, NULL, NULL)
INSERT [dbo].[Material_Info] ([GUID], [Factory], [Phase], [Tag_Name], [Pallet_No], [Part_No], [Batch_No], [Seq_No], [End_Date], [MR_No], [Status_Code], [Update_User_No], [Update_Time], [Purge_Start_Time], [Purge_End_Time], [Supply_Time], [Case_No], [Case_Status], [Update_User_No2], [Vendor_Code], [Storage_ID], [Debit], [Pink], [Note], [GiNo], [GiItem], [TEMP_Time]) VALUES (N'01fca921-8a4a-451e-824f-12034e4c07c0', N'AP2C', N'AP2C', N'Tag004', N'P004', N'1L333333', N'B004', N'S004', CAST(N'2024-09-30T00:00:00.000' AS DateTime), NULL, N'UNUSUAL', N'User2', CAST(N'2019-12-10T16:45:00.000' AS DateTime), NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ST004', NULL, NULL, NULL, NULL, NULL, NULL)
```

## 📌 題目一：建立 Material_Info_Temp Table

> **📚 教學說明**  
> 本題目學習如何建立一個新的資料表。`CREATE TABLE` 語法用於定義資料表的結構，包括欄位名稱、資料型態和約束條件。
> 
> **重點概念：**
> - `UNIQUEIDENTIFIER`：全域唯一識別碼（GUID）資料型態
> - `DEFAULT NEWID()`：自動產生新的 GUID 值作為預設值
> - `NOT NULL`：欄位不允許為空值
> - `PRIMARY KEY`：主鍵約束，可包含多個欄位（複合主鍵）
> - `SELECT * FROM`：查詢資料表的所有資料

```sql
--1. 建立 Material_Info_Temp Table
CREATE TABLE Material_Info_Temp (
    GUID UNIQUEIDENTIFIER DEFAULT NEWID() NOT NULL,
    Factory NVARCHAR(50) NOT NULL,
    Phase NVARCHAR(50) NOT NULL,
    Part_No NVARCHAR(50) NOT NULL,
    Batch_No NVARCHAR(50) NOT NULL,
    Seq_No NVARCHAR(50) NOT NULL,
    Tag_Name NVARCHAR(50) NULL,
    PRIMARY KEY (Part_No, Batch_No, Seq_No)
);

SELECT * FROM Material_Info_Temp;
```

## 📌 題目二：Insert / Update / Delete 操作

> **📚 教學說明**  
> 本題目學習資料操作語言（DML）的三個基本操作：新增、修改和刪除資料。
> 
> **重點概念：**
> - **INSERT**：新增資料到資料表
>   - `INSERT INTO ... SELECT`：從其他資料表查詢並插入資料
>   - `WHERE`：條件篩選，只插入符合條件的資料
> - **UPDATE**：修改現有資料
>   - `UPDATE TOP (n)`：只更新前 n 筆資料
>   - `SET`：指定要更新的欄位和新值
> - **DELETE**：刪除資料
>   - `DELETE TOP (n)`：只刪除前 n 筆資料
> - 執行後使用 `SELECT` 查詢結果，確認操作是否成功

```sql
-- 2-1. Insert 資料
INSERT INTO Material_Info_Temp (Factory, Phase, Part_No, Batch_No, Seq_No, Tag_Name)
SELECT Factory, Phase, Part_No, Batch_No, Seq_No, Tag_Name
FROM [AP2C_Barcode].[dbo].[Material_Info]
WHERE Part_No = '1L111111';

SELECT 'After Insert' AS Step, * FROM Material_Info_Temp;

-- 2-2. Update 任 3 筆資料
UPDATE TOP (3) Material_Info_Temp
SET Factory = 'UpdatedFactory',
    Phase = 'UpdatedPhase',
    Tag_Name = 'UpdatedTag';

SELECT 'After Update' AS Step, * FROM Material_Info_Temp;

-- 2-3. Delete 任 1 筆資料
DELETE TOP (1) FROM Material_Info_Temp;

SELECT 'After Delete' AS Step, * FROM Material_Info_Temp;
```

## 📌 題目三：Trigger-2（Insert / Update / Delete 三合一）

> **📚 教學說明**  
> 本題目學習如何建立觸發器（Trigger），用於自動記錄資料表的變更歷史。觸發器會在特定事件發生時自動執行。
> 
> **重點概念：**
> - **觸發器（Trigger）**：當資料表發生 INSERT、UPDATE 或 DELETE 時自動執行的程式碼
> - **AFTER INSERT, UPDATE, DELETE**：觸發器監聽的事件類型
> - **inserted 和 deleted 虛擬資料表**：
>   - `inserted`：存放新增或更新後的資料
>   - `deleted`：存放刪除或更新前的資料
> - **IDENTITY(1,1)**：自動遞增欄位，從 1 開始，每次增加 1
> - **GETDATE()**：取得當前系統時間

```sql
-- 3-1. 建立 UpdateLog Table
CREATE TABLE UpdateLog (
    GUID INT IDENTITY(1,1) PRIMARY KEY,
    Part_No NVARCHAR(50) NOT NULL,
    Batch_No NVARCHAR(50) NOT NULL,
    Seq_No NVARCHAR(50) NOT NULL,
    UpdateType NCHAR(10) NOT NULL,
    UpdateTime SMALLDATETIME DEFAULT GETDATE()
);

SELECT * FROM UpdateLog;
```

> **📚 教學說明**  
> 建立觸發器來監控三種資料操作事件。觸發器會判斷操作類型並記錄到 UpdateLog 資料表。
> 
> **重點概念：**
> - 使用 `NOT EXISTS` 判斷是新增還是更新
> - 如果 `inserted` 有資料但 `deleted` 沒有 → 這是 INSERT
> - 如果 `inserted` 和 `deleted` 都有資料 → 這是 UPDATE
> - 如果 `deleted` 有資料但 `inserted` 沒有 → 這是 DELETE
> - `INNER JOIN`：用於 UPDATE 操作，比對 inserted 和 deleted 的複合主鍵

```sql
-- 3-2. 建立 Trigger 監控三種事件
CREATE TRIGGER TR_MaterialInfoTemp_Log
ON Material_Info_Temp
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
		INSERT INTO UpdateLog (Part_No, Batch_No, Seq_No, UpdateType, UpdateTime)
    SELECT Part_No, Batch_No, Seq_No, 'Insert', GETDATE()
    FROM inserted
    -- 確認該值為新，而非 Update
    WHERE NOT EXISTS (
        SELECT 1 FROM deleted
        WHERE inserted.Part_No = deleted.Part_No
        AND inserted.Batch_No = deleted.Batch_No
        AND inserted.Seq_No = deleted.Seq_No
    );

    INSERT INTO UpdateLog (Part_No, Batch_No, Seq_No, UpdateType, UpdateTime)
    SELECT inserted.Part_No, inserted.Batch_No, inserted.Seq_No, 'Update', GETDATE()
    FROM inserted
    INNER JOIN deleted
    ON inserted.Part_No = deleted.Part_No
    AND inserted.Batch_No = deleted.Batch_No
    AND inserted.Seq_No = deleted.Seq_No;

    INSERT INTO UpdateLog (Part_No, Batch_No, Seq_No, UpdateType, UpdateTime)
    SELECT Part_No, Batch_No, Seq_No, 'Delete', GETDATE()
    FROM deleted
    WHERE NOT EXISTS (
        SELECT 1 FROM inserted
        WHERE inserted.Part_No = deleted.Part_No
        AND inserted.Batch_No = deleted.Batch_No
        AND inserted.Seq_No = deleted.Seq_No
    );
END;
GO
```

> **📚 教學說明**  
> 驗證觸發器是否正常運作。依序執行 INSERT、UPDATE、DELETE 操作，然後查詢 UpdateLog 確認是否有正確記錄。
> 
> **重點概念：**
> - `VALUES`：直接指定要插入的資料值
> - `ORDER BY GUID DESC`：依 GUID 降序排列，最新的記錄會在最前面
> - 驗證時應該會看到三筆記錄：Insert、Update、Delete

```sql
-- 3-3. 驗證 Trigger 是否生效
INSERT INTO Material_Info_Temp (Factory, Phase, Part_No, Batch_No, Seq_No, Tag_Name)
VALUES ('Factory_Test', 'Phase_Test', 'P_TEST_INS', 'B_TEST_INS', 'S_TEST_INS', 'Tag_Test_Ins');

UPDATE Material_Info_Temp
SET Tag_Name = 'Updated_Test'
WHERE Part_No = 'P_TEST_INS';

DELETE FROM Material_Info_Temp
WHERE Part_No = 'P_TEST_INS';

SELECT * FROM UpdateLog ORDER BY GUID DESC;
```

---

## 📌 題目四：Inner Join/Left Join/Right Join

> **📚 教學說明**  
> 本題目學習如何將多個資料表關聯查詢。JOIN 用於根據關聯條件合併兩個資料表的資料。
> 
> **重點概念：**
> - **INNER JOIN（內連接）**：只回傳兩個資料表都有符合關聯條件的記錄
> - **LEFT JOIN（左連接）**：回傳左側資料表的所有記錄，右側沒有對應的欄位會是 NULL
> - **RIGHT JOIN（右連接）**：回傳右側資料表的所有記錄，左側沒有對應的欄位會是 NULL
> - **ON**：指定兩個資料表的關聯條件（通常是欄位相等）
> - **別名（Alias）**：使用 `MI` 和 `MP` 作為資料表別名，簡化程式碼

```sql
-- 4. Inner Join/Left Join/Right Join
SELECT *
FROM dbo.Material_Info MI
INNER JOIN dbo.Machine_Part MP
    ON MI.Factory = MP.Factory AND MI.Phase = MP.Phase AND MI.Tag_Name = MP.Tag_Name
WHERE MI.Factory = 'AP2C' AND MI.Phase = 'AP2C';

SELECT *
FROM dbo.Material_Info MI
LEFT JOIN dbo.Machine_Part MP
    ON MI.Factory = MP.Factory AND MI.Phase = MP.Phase AND MI.Tag_Name = MP.Tag_Name
WHERE MI.Factory = 'AP2C' AND MI.Phase = 'AP2C';

SELECT *
FROM dbo.Material_Info MI
RIGHT JOIN dbo.Machine_Part MP
    ON MI.Factory = MP.Factory AND MI.Phase = MP.Phase AND MI.Tag_Name = MP.Tag_Name
WHERE MP.Factory = 'AP2C' AND MP.Phase = 'AP2C';
```

## 📌 題目五：GROUP BY 與 HAVING

> **📚 教學說明**  
> 本題目學習如何對資料進行分組統計。GROUP BY 用於將資料分組，HAVING 用於篩選分組後的結果。
> 
> **重點概念：**
> - **GROUP BY**：將資料依指定欄位分組，通常搭配聚合函數使用
> - **聚合函數**：`SUM()`（總和）、`COUNT()`（計數）、`AVG()`（平均）、`MAX()`（最大值）、`MIN()`（最小值）
> - **HAVING**：對分組後的結果進行條件篩選（類似 WHERE，但用於分組後）
> - **WHERE vs HAVING**：
>   - `WHERE`：在分組前篩選原始資料
>   - `HAVING`：在分組後篩選聚合結果

```sql
-- 5. GROUP BY 與 HAVING
SELECT
    Name,
    SUM(cost) AS '消費總數'
FROM
    dbo.CostTable
GROUP BY
    Name
HAVING
    SUM(cost) > 150;
```

## 📌 題目六：自定義 function return value

> **📚 教學說明**  
> 本題目學習如何建立純量函數（Scalar Function），這是一種會回傳單一值的自訂函數。
> 
> **重點概念：**
> - **使用者定義函數（UDF）**：可以重複使用的程式碼區塊
> - **純量函數**：回傳單一值（如 INT、NVARCHAR、DATETIME 等）
> - **參數**：函數可以接受輸入參數，用 `@` 開頭
> - **RETURNS**：指定函數回傳值的資料型態
> - **RETURN**：回傳計算結果
> - **DECLARE**：宣告區域變數
> - **COUNT(*)**：計算符合條件的記錄筆數
> - 呼叫函數時使用 `dbo.函數名稱(參數1, 參數2)`

```sql
-- 6. 自定義function return value
CREATE FUNCTION dbo.CountMaterialUpdatesByTime (
    -- 函數接受兩個 smalldatetime 參數作為時間範圍
    @startTime SMALLDATETIME,
    @endTime SMALLDATETIME
)
RETURNS INT
AS
BEGIN
    DECLARE @RecordCount INT;
    
    -- 核心查詢：計算 UpdateTime 介於 @startTime 和 @endTime 之間的記錄筆數
    SELECT 
        @RecordCount = COUNT(*)
    FROM
        [Material_Info]
    WHERE 
        Update_Time >= @startTime 
        AND Update_Time <= @endTime;
        
    -- 回傳計算結果
    RETURN @RecordCount;
END;
GO

-- 測試
DECLARE @start SMALLDATETIME = '2019-09-01';
DECLARE @end SMALLDATETIME = '2019-12-31';

SELECT 
    dbo.CountMaterialUpdatesByTime(@start, @end) AS RecordsInTargetRange;
```

## 📌 題目七：自定義function return table

> **📚 教學說明**  
> 本題目學習如何建立資料表值函數（Table-Valued Function, TVF），這是一種會回傳資料表的自訂函數。
> 
> **重點概念：**
> - **資料表值函數（TVF）**：回傳一個資料表結果集，可以像查詢資料表一樣使用
> - **RETURNS TABLE**：指定函數回傳資料表
> - **RETURN**：直接回傳 SELECT 查詢結果
> - 呼叫 TVF 時，可以像查詢資料表一樣使用 `SELECT * FROM dbo.函數名稱(參數)`
> - TVF 適合用於封裝複雜的查詢邏輯，提高程式碼重用性

```sql
-- 7. 自定義function return table

-- 建立資料表值函數 (TVF)
CREATE FUNCTION dbo.GetMaterialInfoByPartNo (
    -- 函數接受一個 nvarchar(50) 參數 P_No
    @P_No NVARCHAR(50)
)
RETURNS TABLE
AS
RETURN
(
    -- 函數的核心查詢，回傳一個表格
    SELECT *
    FROM
        dbo.Material_Info
    WHERE
        Part_No = @P_No
);
GO

-- 輸入變數值
DECLARE @TargetPartNo NVARCHAR(50) = '1L111111';

-- 呼叫函數並顯示結果
SELECT 
    * FROM 
    dbo.GetMaterialInfoByPartNo(@TargetPartNo);
```

## 📌 題目八：Procedure

> **📚 教學說明**  
> 本題目學習預存程序（Stored Procedure），這是一種預先編譯的 SQL 程式碼區塊，可以接受參數並執行複雜的資料庫操作。
> 
> **重點概念：**
> - **預存程序（Stored Procedure）**：預先編譯的 SQL 程式碼，執行效率較高
> - **EXEC 或 EXECUTE**：執行預存程序的指令
> - 預存程序可以包含複雜的業務邏輯、條件判斷、錯誤處理等
> - 可以接受輸入參數和輸出參數
> - 適合用於封裝複雜的資料庫操作，提高安全性和維護性

```sql
/* 8-1. Procedure-1
 說明：提醒用戶鋼瓶（Gas）過期的情況。程式會在過期 90 天前提醒，
 並將相關資訊 Insert 到 Alarm_Summary 表格中。*/
EXEC [AP2C_Barcode].[dbo].sp_Insert_Gas_EndDate_Remind_edited;
```

> **📚 教學說明**  
> 建立一個自訂的預存程序，示範如何處理參數、進行資料型態轉換、執行條件判斷和資料更新。
> 
> **重點概念：**
> - **CREATE PROCEDURE**：建立新的預存程序
> - **參數宣告**：在程序名稱後宣告輸入參數
> - **TRY_CONVERT**：安全地將資料型態轉換，失敗時回傳 NULL 而不會產生錯誤
> - **IF...BEGIN...END**：條件判斷語法
> - **RAISERROR**：產生錯誤訊息並中斷執行
> - **RETURN**：提前結束程序執行
> - **GETDATE()**：取得當前系統時間
> - 執行程序時使用 `EXEC 程序名稱 @參數1=值1, @參數2=值2`

```sql
-- 8-2. Procedure-2
CREATE PROCEDURE dbo.UpdateCostBasedOnTime (
    -- 輸入參數為 NVARCHAR，用於傳入時間字串
    @startTime NVARCHAR(50),
    @endTime NVARCHAR(50)
)
AS
BEGIN
    -- 宣告變數來儲存轉換後的日期時間
    DECLARE @StartDateTime DATETIME;
    DECLARE @EndDateTime DATETIME;
    DECLARE @CurrentDateTime DATETIME = GETDATE(); -- 取得當前系統時間
    
    -- 嘗試將輸入的 NVARCHAR 轉換為 DATETIME
    SET @StartDateTime = TRY_CONVERT(DATETIME, @startTime);
    SET @EndDateTime = TRY_CONVERT(DATETIME, @endTime);

    -- 檢查轉換是否成功，若失敗則提前退出或拋出錯誤
    IF @StartDateTime IS NULL OR @EndDateTime IS NULL
    BEGIN
        RAISERROR(N'錯誤：無法將輸入的時間字串轉換為有效的日期時間格式。', 16, 1);
        RETURN;
    END

    -- 核心條件判斷
    IF @CurrentDateTime >= @StartDateTime AND @CurrentDateTime <= @EndDateTime
    BEGIN
        -- 條件成立：執行更新操作
        
        UPDATE dbo.CostTable
        SET cost = cost * 2;
    END
END;
GO

SELECT * FROM CostTable

EXEC dbo.UpdateCostBasedOnTime 
    @startTime = '2025-11-01 00:00:00', 
    @endTime = '2025-12-31 23:59:59';

SELECT * FROM CostTable

```

## 📌 題目九：排程

> **📚 教學說明**  
> 本題目學習如何設定 SQL Server Agent 排程作業，讓預存程序可以自動定期執行。
> 
> **重點概念：**
> - **SQL Server Agent**：SQL Server 的排程服務，用於自動執行任務
> - **作業（Job）**：定義要執行的任務
> - **步驟（Step）**：作業中的執行單元
> - **排程（Schedule）**：定義作業執行的時間
> - **DATEADD**：日期時間運算函數，可以加減指定的時間單位
> - **CONVERT**：資料型態轉換函數
> - 動態計算時間範圍，確保當前時間落在範圍內，讓條件判斷成立

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm517.png)
![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm07.png)
![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm237.png)



將以下程式碼貼至`命令(M)`

```sql
-- 這是要在 SQL Server Agent Job Step 中使用的 T-SQL
DECLARE @DynamicStartTime NVARCHAR(50);
DECLARE @DynamicEndTime NVARCHAR(50);
DECLARE @CurrentTime DATETIME = GETDATE();

-- 計算一個涵蓋當前時間的範圍：
-- 起始時間：當前時間減 1 分鐘
SET @DynamicStartTime = CONVERT(NVARCHAR(50), DATEADD(MINUTE, -1, @CurrentTime), 120);

-- 結束時間：當前時間加 1 分鐘
SET @DynamicEndTime = CONVERT(NVARCHAR(50), DATEADD(MINUTE, 1, @CurrentTime), 120);

-- 執行預存程序，傳入動態計算出的參數
EXEC dbo.UpdateCostBasedOnTime 
    @startTime = @DynamicStartTime, 
    @endTime = @DynamicEndTime;
```

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm377.png)
![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm447.png)
![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm17.png)



左鍵雙擊`作業活動監視器`

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm117.png)
![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm197.png)


執行成功

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm317.png)



### 最後記得刪除

![](https://cdn.jsdelivr.net/gh/YCS-Boya/image-repo@main/Training/Basic%20SQL/2025_11_09_7pm567.png)
