# ***Flow Diagrams***

### 1- Registration & Onboarding Flow:

```mermaid
flowchart TD
    Start([Student opens Registration Page]) --> InputForm
    InputForm[/Student enters Name, Phones, Grade, Password & Parent Consent/] --> ValidateFormat{Are all inputs & consent valid?}
    ValidateFormat -- No --> FormatErr[/Show Error: Missing fields or parental consent required/]
    FormatErr --> InputForm
    ValidateFormat -- Yes --> CheckMatch{Student Phone == Parent Phone?}
    CheckMatch -- Yes --> MatchErr[/Show Error: Phones must differ/]
    MatchErr --> InputForm
    CheckMatch -- No --> CheckDB{Does Student Phone exist in DB?}
    CheckDB -- Yes --> ExistErr[/Show Error: Phone already registered/]
    ExistErr --> InputForm
    CheckDB -- No --> CreateAcc[Create Student Account & Save consent_version + consent_at]
    CreateAcc --> InitWallet[Initialize Wallet Balance = 0 EGP]
    InitWallet --> InitSession[Create Authenticated Session]
    InitSession --> RegDevice[Register current Device ID as Device #1]
    RegDevice --> Redirect([Redirect to Student Dashboard])
```

---

### 2- Login & Anti-Sharing Device Limit Flow

```mermaid
flowchart TD
    %% Start and Input
    Start([Student opens Login Page]) --> InputForm[/Student enters Phone & Password/]
    
    %% Authentication & Security Lockout
    InputForm --> Auth{Are Credentials Correct?}
    Auth -- No --> IncFail[Increment Failed_Login_Attempts]
    IncFail --> CheckBrute{Failed_Attempts >= 5?}
    
    CheckBrute -- Yes --> LockAcc[Lock Account for 15 Mins]
    LockAcc --> LockErr[/Show Error: Account temporarily locked/]
    LockErr --> InputForm
    
    CheckBrute -- No --> AuthErr[/Show Error: Invalid phone or password/]
    AuthErr --> InputForm
    
    %% Account Status
    Auth -- Yes --> ResetFail[Reset Failed_Login_Attempts to 0]
    ResetFail --> CheckStatus{Is Account Active?}
    
    CheckStatus -- Suspended/Locked --> AccLock[/Show Error: Account suspended. Contact Support/]
    AccLock --> InputForm
    
    %% Device Limit Logic (The Core Anti-Sharing Rule)
    CheckStatus -- Active --> CheckDevice{Is current Device ID in Student's Active Registered Devices?}
    
    CheckDevice -- Yes --> GrantAccess[Update 'Last Login' & Initialize Session]
    GrantAccess --> Redirect([Redirect to Student Dashboard])
    
    CheckDevice -- No --> CountDev{Active Registered Devices < 2?}
    
    CountDev -- Yes --> SaveDev[Register Current Device to Student Profile]
    SaveDev --> GrantAccess
    
    CountDev -- No --> BlockAccess[/Deny Login: Max 2 devices reached. Use a registered device or contact support to remove old devices/]
    BlockAccess --> InputForm
```

---

### **3- Wallet Top-Up & Brute-Force Protection Flow**

```mermaid
flowchart TD
    Start([Student opens Wallet Page]) --> InputForm[/"Student enters 14-digit Code"/]
    
    %% Lock Check
    InputForm --> CheckLock{"Is Top-Up Operation Locked?"}
    CheckLock -- Yes --> ShowLockMsg[/"Show Error: Top-Up locked. Wait X mins"/]
    ShowLockMsg --> InputForm
    
    %% Code Validation
    CheckLock -- No --> QueryCode{"Does Code Exist, Active & Unused?"}
    
    %% Failed Attempt Path
    QueryCode -- No --> IncFail["Atomically Increment Failed_Attempts"]
    IncFail --> LogSecurity["Log Security Event: invalid_code_attempt"]
    LogSecurity --> CheckCounter{"Failed_Attempts >= 5?"}
    
    CheckCounter -- Yes --> LockTopUp["Lock Top-Up Operation for 15 Minutes"]
    LockTopUp --> ShowBlockError[/"Show Error: 5 failed attempts. Top-Up locked for 15 mins"/]
    ShowBlockError --> InputForm
    
    CheckCounter -- No --> ShowInvalidError[/"Show Error: Invalid/Used code. Attempts remaining: 5-X"/]
    ShowInvalidError --> InputForm
    
    %% Success & Concurrency Path
    QueryCode -- Yes --> DBTransaction["Start Atomic DB Transaction"]
    DBTransaction --> ClaimCode{"Can Code Be Claimed as Unused? (Row Lock)"}
    
    %% Prevent simultaneous requests (Double Spend)
    ClaimCode -- No --> Rollback["Rollback Transaction"]
    Rollback --> ShowInvalidError
    
    %% Atomic Updates
    ClaimCode -- Yes --> MarkUsed["Update Code Status = 'Used'"]
    MarkUsed --> UpdateBal["Credit Student Wallet Balance"]
    UpdateBal --> LogSuccess["Create Wallet Transaction Record"]
    LogSuccess --> ResetFail["Reset Failed_Attempts to 0"]
    ResetFail --> CommitTx["Commit DB Transaction"]
    
    CommitTx --> EndSuccess[/"Show Success: X EGP added to your wallet!"/]
    EndSuccess --> InputForm
```

---

### Class Purchase & DRM Playback Flow

```mermaid
flowchart TD
    Start([Student selects Class Package]) --> CheckAvail{"Is Class Available for Purchase?"}
    CheckAvail -- No --> UnavailableErr[/"Show Error: Class currently unavailable"/]
    
    %% Enrollment Check
    CheckAvail -- Yes --> CheckEnroll{"Has Active Enrollment?"}
    
    %% Purchase Path (If not enrolled)
    CheckEnroll -- No --> ClickBuy[/"Student clicks 'Purchase'"/]
    ClickBuy --> CheckBal{"Wallet Balance >= Class Price?"}
    
    CheckBal -- No --> LowBal[/"Deny Purchase: Insufficient Balance"/]
    CheckBal -- Yes --> PurchaseTx["Start Atomic DB Transaction"]
    
    PurchaseTx --> VerifyBalAgain{"Balance Still Sufficient? (Row Lock)"}
    VerifyBalAgain -- No --> Rollback["Rollback Transaction"]
    Rollback --> LowBal
    
    VerifyBalAgain -- Yes --> Deduct["Deduct Class Price from Balance"]
    Deduct --> LogWalletTx["Create Wallet Transaction Record"]
    LogWalletTx --> CreateEnroll["Create Enrollment Record"]
    CreateEnroll --> CommitTx["Commit Transaction"]
    CommitTx --> LogEvent["Log Event: class_purchased"]
    LogEvent --> GrantAccess
    
    %% Access Path
    CheckEnroll -- Yes --> GrantAccess["Grant Class Page Access"]
    GrantAccess --> StudentAction{"Student Action on Class Page"}
    
    %% DRM Video Playback Lifecycle
    StudentAction -- "Clicks Play Video" --> VerifyAuth{"Is Enrollment Active?"}
    VerifyAuth -- No --> DenyPlay[/"Deny Playback: Access Revoked"/]
    
    VerifyAuth -- Yes --> RequestDRM["Backend requests Playback Authorization from DRM API"]
    RequestDRM --> DRMAuth{"Authorization Successful?"}
    
    DRMAuth -- No --> PlayErr[/"Show Error: Failed to load secure stream"/]
    DRMAuth -- Yes --> LoadPlayer["Load Custom Player with Encrypted Stream"]
    LoadPlayer --> GetLicense["Player obtains DRM License"]
    GetLicense --> AddWatermark["Apply Dynamic Watermark (Masked Phone: 010****1234)"]
    AddWatermark --> PlayVideo[/"Start Video Playback"/]
    PlayVideo --> LogVideo["Log Event: video_started"]
    
    %% Materials & Assessments
    StudentAction -- "Scrolls to Materials" --> MatAuth{"Is Student Authorized?"}
    MatAuth -- No --> DenyMat[/"Deny Material Access"/]
    MatAuth -- Yes --> AllowMat[/"View/Download PDF & Open Homework Form"/]
```

---
### **Free Class Enrollment Flow**
```mermaid
flowchart TD
    Start([Student selects a Free Class]) --> CheckAvail{"Is Class Available & Published?"}
    CheckAvail -- No --> UnavailableErr[/"Show Error: Class currently unavailable"/]

    %% Check if already enrolled
    CheckAvail -- Yes --> CheckEnroll{"Has Active Enrollment?"}
    CheckEnroll -- Yes --> GrantAccess["Grant Class Page Access"]

    %% Free Enrollment Path (Price == 0)
    CheckEnroll -- No --> CheckPrice{"Is Class Price == 0 EGP?"}
    CheckPrice -- No --> PaidFlow(["Redirect to Paid Purchase Flow"])

    CheckPrice -- Yes --> ClickEnroll[/"Student clicks 'Enroll for Free'"/]
    ClickEnroll --> EnrollTx["Start Atomic DB Transaction"]
    
    EnrollTx --> CreateEnroll["Create Enrollment Record (status='ACTIVE', source='FREE', price_paid=0)"]
    CreateEnroll --> SkipWallet["Bypass Wallet Deduction & Ledger Entry (No Financial Tx)"]
    SkipWallet --> CommitTx["Commit DB Transaction"]
    
    CommitTx --> LogEvent["Log Event: free_class_enrolled (user_id, class_id)"]
    LogEvent --> GrantAccess
    GrantAccess --> EndFlow([Student Accesses DRM Video & Materials])
```
---

### **Admin & Assistant Operations Flow (RBAC & Audit-Ready)**

```mermaid
flowchart TD
    Start(["Admin / Assistant Logs In"]) --> RBAC{"Check Role (Super Admin vs Assistant)"}
    RBAC --> Dashboard["Load Dashboard with Role-Specific Permissions"]
    Dashboard --> SelectOp{"Select Operation"}

    %% 1. Generate Codes
    SelectOp -- "1. Generate Codes" --> CheckPerm1{"Has Code Generation Permission?"}
    CheckPerm1 -- No --> Deny1[/"Show Error: Unauthorized"/]
    CheckPerm1 -- Yes --> ValidateBatch{"Are Batch Inputs Valid?"}
    ValidateBatch -- No --> ErrBatch[/"Show Error: Invalid inputs"/]
    ValidateBatch -- Yes --> GenCodes["Generate Unique Cryptographic Codes"]
    GenCodes --> SaveCodes["Save Secure Hash to DB (Batch ID, Value)"]
    SaveCodes --> Audit1["Log Audit: batch_generated (Operator ID)"]
    Audit1 --> Export[/"Export Codes as PDF/CSV (One-time only)"/]

    %% 2. Upload Class
    SelectOp -- "2. Upload & Manage Class" --> CheckPerm2{"Has Upload Permission?"}
    CheckPerm2 -- No --> Deny2[/"Show Error: Unauthorized"/]
    CheckPerm2 -- Yes --> UploadAssets["Upload Video & Metadata"]
    UploadAssets --> DRMProcess["Send Video to DRM Provider (Processing State)"]
    DRMProcess --> CheckStatus{"Is DRM Processing Successful?"}
    CheckStatus -- No --> SetFail["Set Class Status = Failed"]
    CheckStatus -- Yes --> SetReady["Set Class Status = Ready"]
    SetReady --> CheckPub{"Has Explicit Publish Permission (class.publish)?"}
    CheckPub -- No --> KeepReady["Save as Draft / Await Super Admin Approval"]
    CheckPub -- Yes --> Publish["Publish Class"]
    Publish --> Audit2["Log Audit: class_published (Operator ID)"]

    %% 3. Student Support
    SelectOp -- "3. Student Support" --> Search["Search by Phone"]
    Search --> ShowProfile[/"Display Masked Data based on RBAC"/]
    ShowProfile --> SupportAction{"Select Support Action"}

    %% Reset Devices
    SupportAction -- "Reset Devices" --> CheckPerm3{"Has Reset Permission?"}
    CheckPerm3 -- No --> Deny3[/"Show Error: Unauthorized"/]
    CheckPerm3 -- Yes --> Revoke["Revoke DB Devices & Terminate Active Sessions"]
    Revoke --> Audit3["Log Audit: devices_reset (Operator ID, Student ID)[cite: 4]"]

    %% Change Phone
    SupportAction -- "Change Phone" --> VerifyID{"Is Parent Identity Verified Manually?"}
    VerifyID -- No --> Deny4[/"Show Error: Verification Failed"/]
    VerifyID -- Yes --> CheckNewPhone{"Is New Phone Available in DB?"}
    CheckNewPhone -- No --> ErrPhone[/"Show Error: Phone Already Exists"/]
    CheckNewPhone -- Yes --> UpdatePhone["Update Primary Phone & Terminate Sessions"]
    UpdatePhone --> Audit4["Log Audit: phone_changed (Operator ID, Old/New Phone)[cite: 4]"]
```

---

# ***WireFrames***

### Registration & Login Screen

```
+-------------------------------------------------------------------+
|  [Platform Logo]                                [Login] [Register]|
+-------------------------------------------------------------------+
|   CREATE NEW ACCOUNT (Registration Flow)                          |
|   -------------------------------------------------------------   |
|   Full Name *                                                     |
|   [ e.g., Ahmed Mohamed Ali                                   ]   |
|                                                                   |
|   Student Phone (Primary ID) *      Parent Phone *                |
|   [ 010XXXXXXXX              ]      [ 012XXXXXXXX             ]   |
|                                                                   |
|   Grade Level *                                                   |
|   [ Dropdown: 1st Sec / 2nd Sec / 3rd Sec v                   ]   |
|                                                                   |
|   Password *                                                      |
|   [ ****************                                          ]   |
|                                                                   |
|   [                      Create Account                       ]   |
|                                                                   |
|   ⚠️ Validation Messages:                                         |
|   - Err 1: "Student and Parent phones must differ."               |
|   - Err 2: "This phone number is already registered."             |
+-------------------------------------------------------------------+
```

---

### Student Dashboard & Class Catalog

```
+-------------------------------------------------------------------+
|  [Logo]   📚 My Classes   💳 Wallet (150 EGP)   [👤 Ahmed (3rd Sec)]|
+-------------------------------------------------------------------+
|  AVAILABLE CLASSES - MR. M.A (PHYSICS)                            |
|  Filter by Chapter: [ All Chapters v ]                            |
|                                                                   |
|  +-----------------------------+  +-----------------------------+ |
|  | [Thumbnail: Chapter 1]      |  | [Thumbnail: Chapter 2]      | |
|  | Lecture 1: Ohm's Law        |  | Lecture 2: Kirchhoff's Law  | |
|  | Status: ✅ ACTIVE ENROLLMENT|  | Status: 🛒 AVAILABLE        | |
|  |                             |  | Price: 80 EGP               | |
|  | [     ▶️ Watch Now      ]   |  | [   🔒 Purchase (80 EGP)  ] | |
|  +-----------------------------+  +-----------------------------+ |
|                                                                   |
|  +-----------------------------+                                  |
|  | [Thumbnail: Chapter 3]      |  ⚠️ Conditional Purchase Modal:  |
|  | Lecture 3: Magnetism        |  "Deduct 80 EGP? (Balance: 150)" |
|  | Status: ⏳ UNAVAILABLE      |  [ Cancel ] [ Confirm Purchase ] |
|  | (Coming Soon)               |  (Triggers Atomic DB Tx)         |
|  | [ 🚫 Purchase Disabled  ]   |                                  |
|  +-----------------------------+                                  |
+-------------------------------------------------------------------+
```

---

### Student Wallet & Top-Up

```
+-------------------------------------------------------------------+
|  [Logo]   📚 My Classes   💳 Wallet (150 EGP)   [👤 Ahmed]         |
+-------------------------------------------------------------------+
|   💰 CURRENT WALLET BALANCE: 150.00 EGP                           |
|                                                                   |
|   +-----------------------------------------------------------+   |
|   | REDEEM SCRATCH CODE (Top-Up Operation)                    |   |
|   | Enter the 14-digit code: [  XXXX-XXXX-XXXX-XX  ]          |   |
|   |                                                           |   |
|   | [     Redeem Code     ]                                   |   |
|   |                                                           |   |
|   | ⚠️ Top-Up States (BR-W05 / BR-W08):                       |   |
|   | - Success: "✅ 100 EGP added! (Failed attempts reset to 0)" |   |
|   | - Invalid: "❌ Invalid/Used code. (2/5 tries remaining)"    |   |
|   | - Locked:  "🔒 Top-Up operation is locked for 14:32 mins  |   |
|   |             due to multiple failed attempts."             |   |
|   +-----------------------------------------------------------+   |
|                                                                   |
|   📜 WALLET TRANSACTION RECORD (Financial Audit)                  |
|   25/10/2026 | Purchased: Lecture 1           | -80 EGP           |
|   25/10/2026 | Code Top-Up (Ref: ****4591)    | +100 EGP          |
+-------------------------------------------------------------------+
```

---

### DRM Video Player & Materials

```
+-------------------------------------------------------------------+
|  < Back to Dashboard                                     [👤 Ahmed] |
+-------------------------------------------------------------------+
|  Lecture 1: Ohm's Law & Electrical Resistance                     |
|                                                                   |
|  +-------------------------------------------------------------+  |
|  |  [🔒 DRM-Protected Stream (Active Auth Token)]              |  |
|  |                                                             |  |
|  |         (Dynamic Watermark: Student ID - 010****1234)       |  |
|  |                                                             |  |
|  |  [00:14:22 / 01:45:00]              [Speed: 1.5x] [1080p]   |  |
|  +-------------------------------------------------------------+  |
|  * If Authorization Fails: [🚫 Playback Access Denied]            |
|                                                                   |
|  📂 ATTACHED CLASS MATERIALS (Independent Entitlement Checks)     |
|  +-------------------------------------------------------------+  |
|  | 📄 Lecture 1 Summary (View Only)         [ 👁️ View PDF     ]|  |
|  | 📝 Lecture 1 Mandatory Homework          [ 🔗 Open Form    ]|  |
|  | 🧪 Weekly Timed Quiz (30 Mins)           [ ⏱️ Start Quiz   ]|  |
|  +-------------------------------------------------------------+  |
+-------------------------------------------------------------------+
```

---

### Admin & Assistant RBAC Portal

```
+-----------------------------------------------------------------------+
|  [Portal] Role: [Assistant_Omar] | Permissions: Restricted   [Logout] |
+-----------------------------------------------------------------------+
|                                                                       |
|  SECTION A: CLASS UPLOAD & PUBLISHING (Processing States)             |
|  Class: [ Lecture 2 ]   Price: [ 80 EGP ]   Grade: [ 3rd Sec ]        |
|  Video: [📤 Upload] --> State: 🔄 PROCESSING (Encrypting via DRM...)  |
|  [ 💾 Save as Draft (Ready) ]  [ 🚀 Publish (Super Admin Only) 🔒 ]   |
|                                                                       |
|  -------------------------------------------------------------------  |
|  SECTION B: SECURE CODE GENERATION (RBAC Applied)                     |
|  Value: [100 EGP] Quantity: [50]   [ ⚡ Generate Cryptographic Batch ]  |
|  Export Options: [ 📥 Export CSV (Allowed ONCE) ]                     |
|  * Super Admin Only: [ Revoke Unused Batch 🔒 ]                       |
|                                                                       |
|  -------------------------------------------------------------------  |
|  SECTION C: STUDENT SUPPORT & AUDIT-READY ACTIONS                     |
|  Lookup: [ 01012345678 ] [🔍 Search]                                  |
|                                                                       |
|  👤 PROFILE (Masked Data via Least Privilege):                        |
|  Name: Ahmed M. ***                 Parent Phone: 012****5432         |
|  Wallet: 70 EGP                     Status: ✅ Active                 |
|                                                                       |
|  🛠️ SUPPORT ACTIONS (Triggers Audit Logs):                            |
|  • Active Devices: 2/2                                                |
|    [ 🔄 Revoke Devices & Terminate Sessions ]                         |
|  • Phone Recovery (Requires Parent Verification):                     |
|    [ ✏️ Update Primary Phone (Super Admin Only) 🔒 ]                  |
|                                                                       |
|  -------------------------------------------------------------------  |
|  SECTION D: SYSTEM AUDIT LOGS (Append-Only / Viewable by Admin)       |
|  25/10 14:00 | batch_generated | Op_ID: Asst_Omar | Qty: 50           |
|  25/10 15:30 | devices_reset   | Op_ID: Admin_01  | Target: 010***12  |
|  25/10 16:00 | class_published | Op_ID: Admin_01  | Target: Class_02  |
+-----------------------------------------------------------------------+
```
