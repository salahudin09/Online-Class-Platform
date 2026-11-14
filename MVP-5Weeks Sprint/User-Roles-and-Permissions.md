# **Users-Roles**

# **Student**

---

***Authentication:***  
 ***\-*** Register and log in securely using basic credentials (Phone Number / Password / Parent Phone Number)  
 **\-** Maximum 2 active devices per student

* Simultaneous multi-device login must be blocked

***Content access:***  
 ***\-*** Student can browse every visible course details on the platform  
 ***\-*** Student can access all free courses & the only purchased paid courses

* Student can write or reply on comments below the accessible videos  
* Student can’t save, share, screen record or download videos  
* Student can only download allowed data  
* Student can attend his time bound homework, workshops, or exams at the scheduled time  
* Every student has a dashboard has a complete view on his account  
* Wallet(Balance, bills, subscriptions)  
* Enrolled courses and lessons  
* scheduled homework and exams

***Financials:***  
 **Wallet Top-up & Payments:** Charging the personal wallet is done exclusively via two methods:

1. **Offline Cash Top-up:** Redeeming a pre-generated 14-digit Scratch Code purchased physically from the center.  
2. **Manual Admin Top-up:** Executed directly by the Assistant from the dashboard after verifying an external out-of-system transfer (e.g., manual Vodafone Cash transfer via WhatsApp) for online-only students.

# 

# **Assistant**

---

***Content management:***

* Assistant structure educational courses Monthly bundles,chapters, and individual lectures  
* Assistant uploads DRM-protected VOD lessons, attach supplementary PDF lecture notes  
* Assistant configures lesson-specific consumption rules (maximum view count per student, watch-time expiration quota)  
* Build and manage assessment modules (Homework & Prerequisite MCQ Exams) with automated grading logic and minimum passing score thresholds required to unlock subsequent lectures

***Student Lifecycle & Technical support:***

* Query student profiles using Student ID, registered phone number, or Parent's phone number  
* Student Tagging & Filtering(Manage student categorization tags (Enrollment Type: Center vs. Online-Only, Governorate, Academic Grade, and Gender))  
* Execute account recovery actions: trigger password resets and clear registered browser/device fingerprints (Reset Device Limit) when a student's account is auto-locked for exceeding the active devices limit 2-devices & one active(online) session
* View individual student logs (exam scores, video completion status) to handle parent inquiries, and respond to academic Q\&A comments under VODs

***Financials:***  
 Scratch Code Operations (Offline Center Distribution):

* Generate bulk batches of randomized 14-digit numeric Scratch Codes, assigned either to specific monetary wallet values or direct lecture unlocks  
* Export generated code batches as print-ready PDF/CSV manifests for physical cutting and cash distribution at offline educational center  
* Perform single-code status lookups (Code Inquiry) to verify if a specific code is Active, Redeemed (displaying the redeeming student's ID and timestamp), or Deactivated to resolve student support tickets  
* Deactivate specific unredeemed code ranges (e.g., unsold or lost physical cards returned from the center)  
* External Payment Handling (Online Students): Verify out-of-system digital transfers (e.g., Vodafone Cash receipts sent via external channels like WhatsApp), and fulfill the transaction by either performing a direct manual top-up to the student's wallet from the dashboard, or generating and sending a single-use Scratch Code to the student

***Strict Access Restrictions (Security & Anti-Fraud Constraints):***

* No Financial Visibility: Strictly blocked (HTTP 403 Forbidden / Hidden UI) from accessing the Financial Dashboard, total revenue metrics, aggregate wallet balances, or total activated vs. printed code ratios  
* No Destructive Actions (Soft-Delete Only): Prohibited from executing hard deletes (DELETE) on courses, videos, student accounts, or generated code batches to preserve data integrity  
* No Audit or System Access: Blocked from viewing, modifying, or exporting the System Audit Log, white-label branding settings, or other staff accounts

# 

# **Teacher / Super Admin**

---

***Inherited Operational Authority:***

* Possesses full read/write/delete (Full CRUD) privileges across all Assistant operational modules (Content, Assessments, Student Profiles, and Scratch Code Management), including permanent Hard-Delete capabilities

***Financial Dashboard & Revenue Reconciliation:***

* Access real-time financial analytics displaying gross revenue, total redeemed scratch code value, active student wallet balances, and course-by-course sales performance  
* View batch reconciliation reports comparing Total Generated/Printed Codes against Total Redeemed Codes per batch to audit physical cash collection from Assistants at the center

***Security & Anti-Fraud Audit Log (Immutable Read-Only):***

* Monitor a comprehensive, timestamped Audit Log tracking all high-risk Assistant actions to prevent internal fraud or unauthorized black-market code distribution  
* Tracked events strictly include: Batch Code Generation (who generated, quantity, value, timestamp), Manual Course Unlock / Balance Adjustment, Device Fingerprint Resets, and Code Deactivations

***Staff & Role-Based Access Control (RBAC):***

* Create, edit, suspend, or permanently revoke Assistant accounts  
* Configure granular permission toggles per Assistant (e.g., restricting a specific Assistant to *Academic Q\&A & Exam Grading* while revoking *Scratch Code Generation* privileges)

***White-Label & Configuration:***

* Manage global platform branding variables via the Admin Settings UI: upload primary/secondary logos, configure theme color palettes (Primary, Secondary, Accent hex codes), update landing page hero assets, and set official support/social media links (WhatsApp, Telegram, Facebook, YouTube)  
* Configure global DRM and security parameters (e.g., adjusting the global maximum device limit per student, customizing dynamic watermark opacity/speed, and toggling maintenance mode)

