| Title/Initiative | Educational SaaS Platform |
| --- | --- |
| Date & Version | 25/10/2026 |
| Product - Point of Contact (POC) | Salahuddin-BA
Mahm0udNasr-TL |
| Design POC |  |
| Tech POC |  |
| Marketing POC |  |

### Why? (Objective)

1. For business:
- Expand Reach & Mirror Offline Experience: Empower teachers to seamlessly reach online students by replicating the offline center discipline (delivering DRM-protected VODs, scheduled homework, and time-bound exams)
- Internal Security & Financial Governance: Provide a robust digital infrastructure that protects content from leakage, while enforcing strict permissions to prevent internal fraud and secure offline cash operations

2. For users:
- Save Time & Effort: Students can watch all lessons and follow the full curriculum from home, without wasting time traveling to the center
- Easy Payments & Support: Students don't need credit cards. They can easily pay using scratch cards or Vodafone Cash, and get answers to their questions through the platform.

### How do we measure success?

1. Associated OKR/Goal
- Objective: Launching online educational platform MVP, Technically and Financially safe Organizes students and defends Teachers from content pirating 
- KR1: delivering VOD system and easy controlled payment method(Scratch code)
- KR2: Achieving a 0% discrepancy or financial variance between printed and shipped codes
- KR3: Keep the rate of incorrect account blocks (false positives) for students below 5%
2. Success Metrics
1- A 100% match between issued top-up codes and cash collected (zero discrepancies), demonstrating the elimination of manipulation by assistants
2- A 0% rate of illegal downloads or video leaks, demonstrating the effectiveness of the DRM system
3- 1- **Batch Cash Reconciliation:** 100% reconciliation between redeemed scratch codes per batch and physical cash settled by assistants via manual batch settlement logs (`batch_settlements`), eliminating unrecorded cash discrepancies.
3. Guardrail/Do not disturb metrics (These metrics shouldn’t be negatively affected)
1- False Positive Account Locks: The rate of legitimate student accounts automatically locked due to the 2-device limit security rule must remain below 5%

### Who are the users:

1. Persona
1- Student: High school(Secondary) Egyptian student (16-18 years old), already knows the student (attends his classes at offline center or follows him on social media), They are visiting the platform *specifically* for this teacher's content, not to browse for courses
2. Problems we are solving [Needs, Problems, Jobs to be done]
**Teacher Problems:**
1- Teachers lose significant revenue due to unauthorized account sharing and illegal downloading/distribution of VOD content
2- Manual cash collection and scratch card distribution in physical centers lack tracking, leading to financial discrepancies, lost revenue, and potential internal fraud by assistants
**Student Problems:**
1- The target demographic (high school students) typically lacks credit cards or bank accounts, making standard online payment gateways inaccessible and blocking them from purchasing content
2- Students suffer from a scattered, distracting online learning process (e.g., receiving video links on WhatsApp, exams on Google Forms, and PDFs on Telegram) instead of a unified, center-like digital environment
3. How do we know these problems exist (research)
1- First-hand User Experience: Direct lived experience and deep empathy with the target demographic (Egyptian high school students), having personally faced these exact payment and accessibility frictions
2- Market & Field Observation: Continuous monitoring of existing local educational platforms and physical centers, revealing clear patterns of financial leakage for teachers and fragmented user journeys for students.

### Solution:

1. Brief of the solution
- For Teachers (The Digital Right-Hand): A secure, all-in-one platform that eliminates geographical barriers. It automates operational workflows, secures cash flow via a closed-loop scratch card system, and protects premium content with strict anti-piracy (DRM) measures
- For Students (The Virtual Center): A seamless, distance-free learning portal that mirrors the physical center experience. It enables students to access VODs, submit homework, and take exams from home, saving commute time and costs through accessible, non-banking payment methods

2. Other alternatives are considered with prioritization metrics
**Automated Payment Gateways (Bank Transfers / E-Wallets via APIs):**
  - High integration complexity. It significantly scales up the scope and delays the MVP launch. Additionally, it misaligns with our unbanked target audience who prefer cash-equivalent methods. We prioritized the "Offline Scratch Card" system to maintain a lean scope and ensure immediate market fit
**Free Video Hosting (Unlisted YouTube / Google Drive)**
  - Zero content security. These links can be easily shared or downloaded, directly violating our core business objective (stopping revenue leakage). We prioritized "DRM-Secured Servers" despite the higher infrastructure setup to guarantee intellectual property protection

### Product flow (Details of the feature/product):

1. Customer Journey
**Step One:**
Onboarding & Registration: Ahmed enters the platform and creates an account using essential data (Name, Phone Number, Parent's Phone Number, and Grade)
Step Two:
Discovery: Ahmed browses the platform, finds Mr. M.A’s Physics class, and selects the required package (VOD Lecture + Homework + Workshop) which costs a specific $$$ amount
**Step Three:**
The Manual Top-Up (Payment workaround): Since Ahmed is in a different governorate and cannot buy a physical card, he contacts the center's assistant (via a WhatsApp button on the platform). He transfers the money via Vodafone Cash and sends the receipt screenshot
**Step Four:**
Code Redemption: The assistant manually sends Ahmed a picture of a valid scratch code. Ahmed clicks the "Wallet" button on the platform, enters the code, and his virtual balance is instantly updated
**Step Five:**
Enrollment: Ahmed uses his new wallet balance to purchase the Physics package. The content is now unlocked
**Step Six:**
Consumption & Retention: Ahmed watches the DRM-protected video, submits his homework, attends the workshop, and receives his grades. The class remains accessible to him securely without the ability to download it.

2. Wireframes and Flow diagrams —————> DELAYED
3. User Stories
**EPIC ONE:**
**US1:-
As an** unverified student
**I want to** register for new account using my Phone number instead of using Email
**So that** I can easly onboard without the friction of remembering or verifying an email
**Acceptance Criteria:**
- Given the student enters a phone number that already exists in the database, When they click 'Register', Then the system shows an error message: "This phone number is already registered"
****- Given the student enters a valid, unregistered phone number and required details, When they click 'Register', Then the account is successfully created
**US2:-
As a** system administrator
**I want to** restrict student accounts to a maximum of 2 active devices
**So that I** prevent students from sharing their accounts with others to bypass purchasing
**Acceptance Criteria:**
- Given a student has logged in from fewer than 2 devices, When they log in from a new device, Then the login is successful and the device is registered to their account
- Given a student already has 2 registered devices, When they attempt to log in from a 3rd device, Then the system denies access and prompts them to "Log out from another device first."

**EPIC TWO:**
**US3:-
As a** remote student
**I want** to enter a 14-digit scratch code in my digital wallet
**So that** I can top up my balance safely without a credit card
4. **Acceptance Criteria:**
- Given the student enters a valid, unused code, When they click 'Redeem', Then their balance is updated immediately by the code's value and the code status changes to 'Used'
- Given the student enters an invalid or used code, When they click 'Redeem', Then an error message is displayed
- Given the student enters an invalid code 5 consecutive times, When they attempt a 6th time, Then the wallet feature is temporarily locked for 15 minutes to prevent brute-force guessing
**US4:-**
**As a** student with funded wallet
**I want** click "Purchase" on a specific class package
**So that** the class cost is deducted from my balance and the content unlocks immediately
**Acceptance Criteria:**
- Given the wallet balance is $\ge$ the class price, When the student confirms purchase, Then the cost is deducted, and the VOD/Homework links become active
- Given the wallet balance is $<$ the class price, When the student clicks purchase, Then the transaction is denied with a prompt to "Recharge Wallet"

**EPIC3:
US5:-
As a** legitimate enrolled student
**I want to** play the class video smoothly on the platform
**So that** I can study while the teacher's content remains secure from piracy
**Acceptance Criteria:**
- Given the student has unlocked the class, When they click play, Then the video streams successfully within the platform's custom player
- Given the student tries to use a browser download extension (like IDM) or inspects the network tab, When they attempt to download the video, Then the action is blocked or the downloaded file is completely encrypted and unplayable
**US6:-
As a** enrolled student
**I want** access the attached PDFs and homework links on the same page as the video
**So that** I have a unified learning experience just like the offline center
**Acceptance Criteria:
- Given** the student has purchased the class, **When** they scroll below the video player, **Then** they can view/download the attached PDF notes and click the link to the homework form.
**As a** Center assistant(Admin)
**I want to** generate batches of unique 14-digit scratch codes with a specific monetary value
**So that** I can sell them to students physically or send them as images via WhatsApp
**Acceptance Criteria:
-** Given the admin selects a monetary value (e.g., 100 EGP) and quantity (e.g., 50 codes), When they click 'Generate', Then the system creates 50 unique codes and allows exporting them as a CSV or PDF file
**US8:-**
**As a** center assistant
**I want** to search for a student using their phone number
**So that** I can check their wallet balance and purchased classes to resolve support tickets quickly

5. Edge cases:-
**Edge Case 1: The "New Phone" Dilemma:
- Scenario:** A student buys a new mobile phone or laptop and sells the old one. When they try to log in, the system blocks them because they hit the "2-device limit" (User Story 1.2) from their old devices
- **System Behavior / Mitigation:** The system will prompt the user to contact support. The Admin dashboard must include a "Reset Devices" button for each student profile to clear their login history manually after verifying their identity.

**Edge Case 2: Double-Sent Scratch Code:
- Scenario:** The teacher's assistant accidentally sends the exact same scratch code image on WhatsApp to two different students. Student A redeems it first. Student B tries to redeem it and gets an error
- **System Behavior / Mitigation:** The system operates correctly by blocking Student B (User Story 2.1). However, to resolve the dispute, the Admin dashboard's "Student Lookup" (User Story 4.2) must show a log of which student account redeemed which specific code and at what timestamp

**Edge Case 3: Phone Number Change:
- Scenario:** A student loses their SIM card and gets a new phone number. Since their phone number is their primary identifier (User Story 1.1), they cannot access their wallet or purchased classes
- **System Behavior / Mitigation:** Students cannot change their phone number autonomously to prevent selling accounts. They must contact support. The Admin can manually update the phone number in the database after verifying the student's identity (e.g., via the parent's Manual phone call)
6. **Event tracking sheet:**

| **Event Name** | **Trigger (When it happens)** | **Properties/Data Passed** | **Why are we tracking this?** |
| --- | --- | --- | --- |
| `user_registered` | Student successfully creates an account. | `user_id`, `grade_level` | To measure daily sign-up volume. |
| `wallet_topped_up` | Student successfully redeems a scratch code. | `user_id`, `amount_added` | To track daily cash flow and code usage. |
| `invalid_code_attempt` | Student enters a wrong or used code. | `user_id`, `error_type` | To detect potential fraud or guessing attempts. |
| `class_purchased` | Student buys a class or a chapter. | `user_id`, `class_name`, `price` | To identify the best-selling lessons. |
| `video_started` | Student clicks play on the DRM video. | `user_id`, `video_id` | To track actual engagement with content. |
1. **Out of scope:**
To maintain a lean scope and ensure a fast time-to-market (5 weeks), the following features are explicitly excluded from this phase and deferred to Phase 2:
- **Automated Payment Gateways:** Integration with Stripe, Paymob, or Fawry APIs (Relying fully on the manual Scratch Code system)
- **Native Mobile Applications:** Building iOS or Android apps (The MVP will be a fully responsive Mobile-First Web Application)
- **Live Streaming Sessions:** Real-time broadcasting capabilities (Focusing strictly on Pre-recorded DRM-protected VODs)
- **In-Platform Chat/Forums:** Community features or in-app messaging (Support will be handled externally via the WhatsApp button)

2. **Dependencies & Risks:
1. Open Questions**
- Will the 15-minute temporary wallet lock be sufficient to deter brute-force code guessing without overwhelming the assistant with support tickets?
**2. Infrastructure requirements (Risk):**
- **DRM-Secured Hosting:** The platform relies heavily on specialized video hosting providers (e.g., VdoCipher or AWS MediaConvert) that support Widevine/FairPlay encryption. Any    downtime from these providers directly stops content consumption
**3. Budget approvals:**
- Upfront budget allocation is required for the DRM video hosting servers, as they are significantly more expensive than standard cloud storage (like AWS S3 or Google Drive)
**4. Partner Support (APIs, Partnerships) (Risk):**
- **Vodafone Cash Daily/Monthly Limits:** Since the teacher's assistant will receive money manually via Vodafone Cash, we are dependent on the telecom's wallet limits. If the transaction volume exceeds the limit, cash flow will be temporarily blocked. (Mitigation: Using multiple merchant numbers)
**5. Internal Dependencies (Tech, Design, Other Teams)
- Operations & Training:** The Tech team must thoroughly train the Center Assistants (Admins) on how to securely generate, export, and distribute the Scratch Codes without duplicating them or sending the wrong codes to students
**6. Legal & Regulatory Compliance (Egypt PDPL 151/2020):**
- Since the platform serves minor students (ages 16–18), Egyptian Personal Data Protection Law (Law No. 151/2020) requires verifiable parental consent. Legal counsel review is mandatory prior to production launch, and the system must record `consent_version` and `consent_at` timestamps upon registration.
