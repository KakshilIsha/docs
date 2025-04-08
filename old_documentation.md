# Overview

The primary goal behind building the Global PRS was to have all Isha programs under one system. To achieve this, we had to focus on two key factors, stability and flexibility.

The system should be stable enough for global systems to use it and provide consistent performance. The system should also be flexible enough to be able to handle all different features that are required across regions.

The system was designed in a highly modular fashion to ensure each component can be managed and scaled independently, while allowing programs to be fully customisable by the business teams.

  

**Primary Users:**

*   Participants: The participants who will register for the programs
*   Program setup team: The team which will setup the program. Can also be the Volunteers/Coordinators.
*   Volunteers/Coordinators: The team which is executing the program. Ground Volunteers, Ishangas, Checkin Volunteers.
*   Marketing, Finance & Legal Teams: The marketing finance and legal teams involved with the program.

  

**Key Points:**

*   **Layered Architecture:** The system separates concerns by dividing functionalities into distinct layers—API, frontend, Odoo backend, and database. This ensures that each layer can scale independently and leverage its unique strengths.
*   **Modular Design:** Components are designed to be generic and modular. This modularity allows for updates or changes in one part of the system without necessitating a complete overhaul, enhancing maintainability and efficiency.
*   **Scalability:** The architecture allows for horizontal scaling, particularly in the API layer, to handle increased load without stressing the Odoo systems.
*   **Decoupled Workers:** Odoo workers are decoupled from the Odoo server, providing the flexibility to integrate other platforms for backend jobs in the future.
*   **Integration Layer:** A separate external layer handles all integrations, allowing teams to build and manage necessary connections with other systems independently.
*   **Security:** Enhanced security measures are enforced at the API layer to ensure that only authorized systems can submit forms and access data.

  

**Guiding Principles:**

*   **Participant-First Approach:** Focus on participant needs when designing all aspects of the system. This includes things that the volunteers will need to help the participants.
*   **Simple and Clean Experience:** Ensure the system is intuitive and easy to navigate, requiring minimal orientation for new internal users.
*   **Stability & Scale:** The system should be able to scale up while staying stable and performing reliably.

  

**Internal Users:**

*   Tech Team (Development Team)
*   IPC - Tech Setup team (Core IPC team which sets up masters, templates, etc)
*   Program setup team (IPC team, centre coordinators or others who can create quick programs)
*   Program organizing team (Volunteers, Ishanga)

# User Types

## User Types & Journeys

The PRS system is used by three main internal user roles, each playing a distinct part in the lifecycle of program setup and participant engagement.

### 1\. **System Configurator**

_IPC & IT Team_

#### **Responsibilities**

*   Set up **Master Configurations**:
    *   Fields, sections, pages, and forms.
    *   Program Types, Centers, Venues.
*   Configure **Roles** for other users.
*   Define foundational data models to support templates and programs.

#### **Touchpoints**

**Odoo Backend – Masters Section**

*   Uses the Masters module extensively to establish system-wide definitions that are referenced in all program templates.

#### **User Journey**

1. Log into the Odoo backend with admin access.
2. Navigate to the **Masters** module.
3. Configure or update necessary master records (fields, roles, venues, etc.).
4. Review system integrity and update global settings as needed.

* * *

  

### 2\. **Template Designer**

_Business Analyst (BA)_

#### **Responsibilities**

*   Design and manage **Program Templates**, including:
    *   Form layouts, flow steps, participant journeys.
    *   Communication templates (email, SMS, WhatsApp).
*   Ensure reusability and standardization across programs.
*   Optimize flows for backend teams and participants.

#### **Touchpoints**

*   **Odoo Backend – Template Section**

#### **User Journey**

1. Log into the PRS Odoo backend with template creation access.
2. Navigate to the **Templates** module.
3. Create new or modify existing templates.
    *   Add flows, forms, and communication logic.
4. Test and validate templates.
5. Coordinate with the participant operations team to handover ready-to-use templates.

* * *

### 3\. **Program Operators**

_L1, L2 Volunteers, Regional Coordinators, Event Teams, etc._

#### **Responsibilities**

*   Launch programs from templates.
*   Manage participant registrations & actions.
*   Coordinate on-the-ground execution and backend operations.

#### **Touchpoints**

*   **Odoo Backend – Programs Section**
*   **React Admin Portal (Mobile/Web)**

#### **User Journey**

1. Access the system via Odoo or React Admin Portal.
2. Launch a new event using a predefined template.
3. Monitor incoming registrations.
4. Approve transfers, cancellations, upgrades.
5. View reports, mark attendance, and coordinate with volunteers.

* * *

### Summary Table

| Role | Primary Tasks | Primary Interface | Approx. Users |
| ---| ---| ---| --- |
| **System Configurator** | Define masters, roles, backend rules | Odoo Masters Section | 2–7 |
| **Template Designer** | Create templates, configure flows and communications | Odoo Templates Section | 5–20 |
| **Program Operators** | Launch and manage events, support participants | Odoo Programs / React Admin | 20–100+ |

# System Architecture

![](https://t37438690.p.clickup-attachments.com/t37438690/52343eb0-5620-43b2-bb35-327cae732e28/Global%20PRS.png)

**System Layers:**

*   **Odoo Layer**
    *   **Details:**
        *   **Program Setup:** Configures programs, including events, forms, pages, sections, and fields, with logic configuration managed through Odoo.
        *   **Registrations Viewing:** Handles registration submissions, where responses are stored as JSON objects with static fields. This allows for flexibility in managing dynamic form data.
        *   **Systems:**
            *   **Simplified Form Creation System:** Provides a templating system for forms and fields, enabling easy setup and reuse across programs.
            *   **Registrations Management System:** Manages participants, backend operations, and all aspects of program registrations and form submissions.

  

*   **Database Layer**
    *   **Technology:** PostgreSQL
    *   **Details:**
        *   The system's data is stored across multiple regions (DB Region 1, DB Region 2, DB Region 3, DB Region 4), with separate UAT databases for testing purposes (UAT DB: 1-4).
        *   Supports multi-region storage to improve resilience and availability.

  

*   **Cache Layer**
    *   **Technology:** Redis
    *   **Details:**
        *   **Storage Mechanism:** Stores key-value pairs where the key is a combination of ProgramID, FlowID, and FlowStepID.
        *   **Master Data:** Uses Redis lists to store cached master data, with backups and persistence options enabled to ensure data durability and recovery.

  

*   **Worker Layer**
    *   **Technology:** Celery
    *   **Details:**
        *   Handles asynchronous tasks, including delayed and scheduled tasks for updating the Odoo system and managing background processes.
        *   **Odoo RPC:** Uses remote procedure calls (RPC) to interact with the Odoo system, ensuring updates are processed efficiently in the background.

  

*   **API Layer**
    *   **Details:**
        *   Contains multiple layers, including:
            *   **Get API Layer:** Handles fetching data from the system for different forms and integrations.
            *   **Post API Layer:** Manages data submissions from participants or external systems.
            *   **Security Layer:** Ensures authentication and data protection.
            *   **Integrations:** Links external systems like ERP, VMS, and others with the core PRS platform, enabling seamless data flow.

  

*   **Frontend Layer**
    *   **Technology:** React with TypeScript (TS)
    *   **Details:**
        *   The frontend is powered by React, providing a dynamic and responsive user interface.
        *   Uses TypeScript to enhance code reliability and maintainability.
        *   **Rendering Engine:** Efficiently renders the forms and registration views, allowing users to interact with the system.
        *   **Theming:** Supports customizable theming to match the branding and user experience of the platform.

  

  

**Technology Stack:**

*   Management System: Odoo/Python
*   Database: PostgreSQL
*   Cache: Redis
*   API: Litestar/Python (Earlier Gin/Go)
*   Frontend: React/TypeScript
*   Workers: Celery/Python

  

#### Sample Data Flow:

*   **Program Setup in Odoo:** Business team configure programs (forms, flows, fields) in Odoo, and the data is stored in PostgreSQL.
*   **Pushing to Cache:** Once saved, the form configurations are cached in Redis, reducing database queries and speeding up access.
*   **Fetching Configurations via API:** When a user accesses the registration form, the API retrieves the configuration from Redis and sends it to the frontend.
*   **Rendering the Form:** The frontend dynamically renders the form using the JSON configuration, providing a flexible and responsive interface.
*   **Form Submission:** Users fill out the form and submit it. The submission is sent to the API, with security measures like authentication for private APIs and WAF for public ones.
*   **Data Saved to Database:** The API processes the submission, storing basic info in PostgreSQL tables and dynamic responses as JSON.
*   **View Registrations in Odoo:** Program teams can immediately access submissions in Odoo and perform necessary actions.
*   **Delayed Tasks via Workers:** Tasks like sending confirmation emails or updating external systems are handled by Celery workers asynchronously.
*   **Connected Apps:** Other apps (e.g., MyMedic) use the same API to render forms and manage registrations.

# Database Structure

### Database Structure

The database is laid out to efficiently manage events(programs), registrations, and forms, with relationships connecting the event-related data to the forms and flows required for participant registration and management.

![](https://t37438690.p.clickup-attachments.com/t37438690/ee9536eb-7d74-492f-9e17-715aed7af74f/Program.png)

### **Database Layout and Relationships**

*   **Event Model**: Central to the database, connected to multiple tables like EventFlow, EventTicket, EventCategory, etc., through one-to-many relationships.
*   **EventFlow and EventFlowStep**: Define the participant flow for an event, with each EventFlow containing multiple EventFlowStep records.
*   **Form Models**: Organize the structure of forms, with FormForm containing pages (FormPage), and pages containing sections (FormSection). Sections hold fields (FormField), and fields may have associated choices.
*   **Registration Data**: Captured via the EventRegistrationMixin, connecting participant details to events and flows.

  

![](https://t37438690.p.clickup-attachments.com/t37438690/1c8cbb65-12a4-4bdb-8a38-47e9555a0bd3/PRS.png)

[PRS.pdf](https://t37438690.p.clickup-attachments.com/t37438690/756232a3-2e71-4fe6-be9c-98774ffa12a1/PRS.pdf)

### Detailed document here:

[https://dbdocs.io/darshika.joshi/PRS](https://dbdocs.io/darshika.joshi/PRS)

General Password

###   

  

###

# API Structure

*   **API Summary**
    *   The API's will be used by the react frontend, mobile apps, ISO website, participant portal and other connected systems, like MyMedic or chatbots, to access programs, forms and registrations.
    *   API is fully capable of running in a headless configuration, meaning a system like Mobile Apps or MyMedic can use the entire PRS platform via these API's without interacting at all with any other systems.
    *   API's will be horizontally scalable and also will have necessary security protocols implemented.

  

*   **API Design Decisions**
    *   Extensive load tests have been performed to ensure that the selected stack can scale up at least 100x more than than the current odoo v1 systems. Tests and results can be found here: [https://github.com/KakshilIsha/load\_tests](https://github.com/KakshilIsha/load_tests)
    *   OpenAPI standards will be implemented to ensure we follow best practises and also it will ensure that we get auto generated documentation, test systems and even frameworks if needed.
    *   For testing, it is recommended to have at least 60% coverage with unit tests before we deploy to UAT.
    *   API's will run in a minimal configuration and will be load balanced behind a ECS service.

  

API Doc:

[

uat-prs-eu-api.sadhguru.org

https://uat-prs-eu-api.sadhguru.org/docs#/

](https://uat-prs-eu-api.sadhguru.org/docs#/)

  

* * *

  

### 📅 **Events & Flows**

*   Create event: `POST /events`
*   List Joomla events: `GET /events/joomla`
*   Get flows for an event: `GET /events/{event_id}/flows`
*   Get steps in a flow: `GET /flows/{flow_id}/steps`

* * *

  

### 📝 **Forms**

*   Get form step: `GET /flows/{flow_id}/steps/{step_key}/form`
*   Submit form: `POST /flows/{flow_id}/steps/{step_key}/form`
*   Refresh form (on field change): `POST /forms/{form_id}/refresh`

* * *

  

### 💳 **Payments**

*   Get payment step: `GET /flows/{flow_id}/steps/{step_key}/payment`
*   Submit payment: `POST /flows/{flow_id}/steps/{step_key}/payment`
*   eReceipt: `GET /payment/eReceipt`
*   Webhooks:
    *   Payment status: `POST /payment/status-webhook`
    *   Validate webhook: `POST /payment/validate-webhook`
    *   Refund: `POST /payment/refund`
    *   Refund webhook: `POST /payment/refund-webhook`

* * *

  

### 🙋 **Registrations**

*   Get next step: `GET /registrations/{booking_id}/get_next_step`
*   Reserve seats: `POST /registrations/reserve-seats`
*   List registrations:
    *   For event: `GET /query/registrations/{event_id}`
    *   For logged-in user: `GET /query/registrations`
*   Transfer registration: `POST /actions/{registration_id}/transfer`

* * *

  

### 🔐 **Access Checks**

*   Check field access: `POST /acl/field_check`
*   Check model access: `POST /acl/model_check`
*   Check record access: `POST /acl/record_check`

* * *

  

### 🛠️ **Admin**

*   Cancel registrations: `POST /admin/registrations/action_cancel`
*   List events: `GET /admin/list/events`
*   List registrations: `GET /admin/list/registrations`
*   Event details: `GET /admin/event/{event_id}` or `/lite`
*   Event registrations: `GET /admin/events/{event_id}/registrations`
*   Registration detail: `GET /admin/registration/{access_token}`

* * *

  

### 🔄 **Other**

*   Upload file URL: `GET /common/pre_signed_url`
*   Auth callback: `POST /auth/callback`
*   Latest T&C: `GET /tnc`
*   Health check: `GET /health`

* * *

# Features Requested - In progress

## Introduction

The Program Registration System (PRS) is a comprehensive platform designed to streamline the registration process for various programs and events. It caters to both end-users and backend operations, ensuring a seamless experience for participants and administrators alike. The following is a detailed compilation of suggested features to enhance the functionality and user experience of the PRS.

  

* * *

## 1\. End User Interface

### 1.1 Simplified Registration

*   **Mobile (OTP) Based Login and Autofilling**: Simplify the login process using One-Time Passwords (OTP) sent to mobile devices, with automatic fetching of participant profile details.
*   **Single Click Registrations**: Enable registrations with minimal steps post-login, creating a registration record instantly.

### 1.2 Personalized Program/Event Listing

*   **Geolocation and Profile-Based Recommendations**: Display programs/events based on the user's location and profile, considering factors like proximity, new user status, and program history.

### 1.3 User Account Features

*   **My Registrations Section**: Provide a dashboard listing all initiated and completed registrations, including status, e-receipts, program details, guidelines, e-passes, and support links.

### 1.4 Self-Service Functions

*   **Batch Change**: Allow users to change from one batch to another based on availability.
*   **Cancellation and Auto Refund**: Enable users to cancel registrations and receive refunds as per policy.
*   **Upgrades**: Allow users to upgrade from one category to another by paying the difference in fees.
*   **Transfer Registrations**: Facilitate transferring registrations to another participant or program.
*   **Re-registration**: Offer re-registration options to another instance of the program with a standard re-registration fee.

### 1.5 Integrated Support

*   **Tightly Coupled Support**: Integrate support options within the portal, allowing users to raise queries or access FAQs directly.

### 1.6 Mobile Application Integration

*   **Native Experience on Sadhguru App**: Provide an in-app registration experience without redirecting to a browser, including payment options like UPI, PayPal, etc.

### 1.7 Group and Gift Registrations

*   **Group Registrations**: Allow family and friends to register as a group with a single payment.
*   **Gifting a Program**: Distinguish between the booking person and participant, enabling users to gift programs to others.

### 1.8 Discounts and Coupons

*   **Discount Coupons**: Implement a system to apply coupons for fee discounts.
*   **Installment Payment Option**: Offer the ability to pay program fees in installments.

### 1.9 Multi-Lingual Support

*   **End-to-End Multi-Lingual Interface**: Provide a registration experience in multiple languages (e.g., English, Hindi, Tamil, Telugu, Kannada, Marathi).

### 1.10 Donations

*   **Simplified Donation Process**: Streamline donations into three steps: enter/choose amount, enter details, and pay.

### 1.11 Slot-Based Registrations

*   **Date Picker for Slot Selection**: Allow users to select dates for volunteering slots, rituals, etc.

### 1.12 Enhanced Form Features

*   **Address Field Validations and Autofilling**: Implement country-based dropdowns, pincode validations, and IP-based location autofill.
*   **Guardian Consent Form for Minors**: Include online consent forms for participants under legal age.
*   **Continue from Where User Left Off**: Enable users to save progress and return to incomplete forms.
*   **Aadhaar-Based Details Fetching**: Auto-populate form fields by uploading Aadhaar card images or PDFs (India-specific).
*   **Camera/Webcam Feature**: Allow users to capture and upload photos or ID proofs directly within the form.

### 1.13 Self Check-In and Attendance

*   **Self Check-In**: Provide mechanisms for users to mark their arrival or attendance, such as scanning QR codes.
*   **Feedback and Completion Surveys**: Collect feedback and conduct completion surveys with conditional questions and flows.

### 1.14 International Compliance

*   **Overseas Formalities Page for Ashram Visits**: Offer a simplified interface to collect additional details required for compliance from overseas nationals and Non-Resident Indians (NRIs).

### 1.15 Messaging Integration

*   **WhatsApp Bot Integration**: Utilize WhatsApp for promotional messages, collecting registration details, sharing payment links, and sending confirmations.

  

* * *

## 2\. Backend Operations

### 2.1 Secure Access

*   **Backend Login with Two-Factor Authentication**: Ensure secure access with mobile SSO-based login and additional authentication steps.

### 2.2 Registration Management

*   **View Registrations**: Access a list of records with detailed views, including filters, groupings, and search functionality.
*   **Spot Registrations**: Facilitate on-the-spot registrations by collecting details, marking payments, and issuing e-receipts.
*   **Backend Registrations**: Enable backend volunteers to create registrations, bypass capacity checks, and manage special cases like influencers or guests.

### 2.3 Communication and Support

*   **Trigger Communications**: Resend confirmation emails, WhatsApp messages, SMS individually or in bulk; send reminders.
*   **Participant Search Portal**: Provide search access to individual records for support teams without full access to all registrations.

### 2.4 Registration Adjustments

*   **Initiate Cancellation, Transfer, Upgrade, Re-Registration**: Manage these processes on behalf of participants.
*   **Complimentary Registrations**: Mark registrations as complimentary with built-in approval workflows.
*   **Enable Discounts**: Apply discounts for hardships, students, veterans, or medical professionals with an approval process.

### 2.5 Financial Reporting

*   **Fee Confirmation Report (FCR)**: Generate reports for financial reconciliation, tracking income against registrations, transfers, re-registrations, and discounts.

### 2.6 Attendance and Check-In

*   **Mark Attendance**: Record attendance day-wise or session-wise; handle final attendance.
*   **Check-In Features**: Scan e-passes, verify photos and uploaded proofs, and manually mark attendance.

### 2.7 Data and Analytics

*   **Reports and Dashboards**: Access live dashboards displaying registration trends by status, date, category, batch, and UTM parameters; includes pivot tables and graphs.
*   **Payment Failure Details**: View transaction statuses, error reasons, and payment modes to support participants with issues.

### 2.8 Access Control and Approvals

*   **Unblock Registrations**: Manage registrations blocked due to medical, age, or other considerations.
*   **Manual Confirmations/Approvals**: Evaluate applications requiring manual confirmation or approval.

### 2.9 Volunteer Management

*   **Event/Program Volunteering Backend**: Assign activities, areas, coordinators, and note feedback for volunteers.
*   **Call Campaigns**: Conduct campaigns for application evaluations, follow-ups on in-progress registrations, and post-program nurturing.

  

* * *

## 3\. Program Setup and Configurations - Standard Programs

### 3.1 Templates and Masters

*   **Standard Usable Templates**: Predefined templates for regional and ashram programs, including Hatha, Inner Engineering, and advanced programs.
*   **Fee Master**: Maintain fee structures for each program type, center, and language combination.
*   **Ebook and Subaccount Master**: Configure subaccounts and e-books for various permutations of program types, centers, and languages.
*   **Venue Master**: Maintain a database of venues used across regions.
*   **Ishangas Master**: List conducting and supporting Ishangas (teachers) for program mapping.

### 3.2 User-Friendly Program Creation

*   **Simplified Interface**: Create program instances by specifying key configurations such as program type, center, language, dates, batches, capacities, venue details, support contacts, guidelines, and fees.

  

* * *

## 4\. Program Setup and Configurations - New Programs/Templates

### 4.1 Form Customization

*   **Fields Selection**: Choose standard fields like name, phone, email, address, and languages; mark fields as mandatory.
*   **Additional Questions and Sections**: Add custom questions (multiple-choice, dropdown, date, numeric) and conditional logic; organize into sections.

### 4.2 Program Structure

*   **Batches, Categories/Bays**: Configure multiple batches or categories with specific capacities and details.
*   **Quotas**: Set overall registration limits, category-wise caps, batch-wise caps, and region-wise caps.

### 4.3 Pre-Requisite Checks

*   **Pre-Requisite Configurations**: Enable checks for prior completion of programs like Meditator status, Shoonya, BSP, Samyama, Inner Engineering Online, or any Hatha program.

### 4.4 Authentication and Blocks

*   **SSO Enabling**: Implement Single Sign-On where required.
*   **Blocks Setup**: Configure blocks for IMS (Inner Management System), medical, age restrictions, and sensitive country considerations.
*   **Duplicate Registration Checks**: Prevent duplicate registrations based on name, phone, email; configure allowances.

### 4.5 Field Validations and Integrations

*   **Mandatory Field Validation**: Define which fields are mandatory for each template.
*   **Integration with Program Delivery Platforms**: Sync with platforms like IPD and Zoom; push confirmed registrations and retrieve attendance data.

### 4.6 International Considerations

*   **Overseas Formalities Collection**: Enable additional data collection for compliance with overseas participants.
*   **Country-Based Restrictions**: Limit form availability to specific countries.

### 4.7 Check-In and Discounts

*   **Enable Check-In Configurations**: Set mandatory conditions for check-in processes.
*   **Discounts Configuration**: Configure early bird discounts, group discounts, coupon-based discounts, and discounts for specific groups (veterans, medical personnel).

### 4.8 Pricing and Enquiries

*   **Pricing Configurations**: Set country-based pricing, profile-based pricing variations, and category-based pricing.
*   **Collection of Enquiries/Expression of Interest**: Enable collection of enquiries before or during registration.

### 4.9 Waitlisting

*   **Waitlist Management**: Open waitlists after registration closure; automate notifications or seat allocations upon cancellations.

  

* * *

## 5\. Communication

### 5.1 Email Communication

*   **Automated Triggers**: Set up emails based on any data or time field.
*   **Campaigns**: Create one-time communications.
*   **List Management**: Create lists based on filters using any field.
*   **Tracking**: Monitor email sent status as part of communication workflows.

### 5.2 WhatsApp Integration

*   **Multiple Accounts Configuration**: Set up multiple accounts for different entities.
*   **Automated Triggers and Campaigns**: Similar functionalities as email, including tracking sent/opened/clicked statuses.

### 5.3 SMS Communication

*   **Bulk SMS Sending**: Configure multiple accounts for regional SMS providers.
*   **Automated Triggers and Campaigns**: Similar functionalities as email.

### 5.4 Outbound Calling

*   **Call Campaigns**: Set up call campaigns for various purposes like application evaluation and follow-ups.
*   **Call Portal for Volunteers**: Provide an easy-to-use interface for calling volunteers.

  

* * *

## 6\. CRM Integrations

### 6.1 Ishangam Integration

*   **Real-Time Sync**: Sync registration data with Ishangam for calling campaigns.
*   **Flag Setting and Program Tagging**: Update CRM with participant program relations and attendance.

  

* * *

## 7\. Access Control

### 7.1 Role-Based Access

*   **Program Center-Wise Access**: Limit user access to programs specific to their center.
*   **Team-Wise Access**: Restrict access based on teams (e.g., IPC Regions, Ashram Programs).
*   **Regional Scoped Access for Global Programs**: Allow users to see regional registrations for global programs.

### 7.2 Action-Based Access Control

*   **Permissions Management**: Control access to actions like program creation, template creation, viewing registrations, backend actions, issuing e-receipts, viewing payment transactions, and managing blocks.

  

* * *

## 8\. Additional Backend Operations Features

### 8.1 Customer Support Operations

*   **Participant Lookup**: Search for participant information by name or email.
*   **Payment Failure Management**: Detect failed or suspended payments; retrigger invoices; manage payment statuses.

### 8.2 Salesforce Sync

*   **Data Synchronization**: Sync participant program relations, attendance, and updates to Salesforce.

### 8.3 Program Operations

*   **Participant Lists and Reports**: Fetch participant lists, pre-requisite status reports, and attendance.

### 8.4 Hardship and Discount Applications

*   **Application Management**: Securely collect proof documents, manage workflows for pending/approved/denied applications, and send automated communications.

### 8.5 Recurring Payment Plans

*   **Payment Plan Support**: Implement fully automated payment plans; track failed or suspended payments.

### 8.6 Tax Deduction Letters

*   **Tax Letters**: Generate and send tax deduction letters to participants.

  

* * *

## 9\. Registration Form UI Enhancements

### 9.1 International Gifting

*   **Gifting to Other Countries**: Allow users to gift programs to participants in other countries.

### 9.2 Discounts and Special Offers

*   **Coupon Codes and Early Bird Discounts**: Implement discount mechanisms during registration.

### 9.3 Special Events

*   **Customized Registration Forms**: Offer flexibility to create forms for special events, combining different modules.

### 9.4 Credit Transfer Funds

*   **Display Transfer Credits**: Show existing transfer credit amounts on the registration form.

### 9.5 Registration Forms

*   **Flexible Forms**: Support single and group registrations, minors' registrations, city-specific forms, and external registration URLs.

### 9.6 Check-In Application

*   **Mobile Check-In App**: Provide an app for check-in and attendance marking, with features like session configurations and access controls.

  

* * *

## 10\. Program Setup Features

### 10.1 Webinar Support

*   **Online Program Workflow**: Support registration and tracking for online programs, including integration with platforms and sadhana support webinars.

### 10.2 General Flow Enhancements

*   **Program Configurations**: Set up dates, timings, venues, capacities, early bird discounts, and closing times with local timezone support.

### 10.3 Email Setup

*   **Customizable Confirmation Emails**: Allow configuration of email content per program and center; preview final emails.

### 10.4 Waitlist Feature

*   **Waitlisting**: Implement waitlists when program capacity is full.

### 10.5 Additional Features

*   **Integration with Shipping Platforms**: Integrate with platforms like ShipStation.
*   **Role-Based Access for Volunteers**: Assign roles and restrict access accordingly.
*   **Flexible Pricing Models**: Support pay-as-you-can registrations.
*   **Flag Checks**: Implement necessary checks and validations.

  

* * *

## 11\. Online Program Workflow

*   **Support for Various Online Programs**: Enable registration and tracking for programs like Inner Engineering Online, Ananda Alai, Garden of Peace, Shambhavi Mahamudra Kriya Refresher, and more.

  

* * *

## 12\. City Program Workflow

*   **End-to-End Workflow**: Manage the entire process from program opening to summary reporting, including teacher checks and medical verifications.

  

* * *

## 13\. Region-Specific Requirements

### 13.1 US-PRS Features

*   **Backend Operations**: Support team operations like participant lookup, cancellations, transfers, upgrades, and resending confirmations.
*   **Customer Support Enhancements**: Manage in-app purchases, handle data sync issues, and provide flexible forms.
*   **Program Setup**: Include features like group discounts, medical checks, and integration with third-party platforms.

### 13.2 APAC PRS Features

*   **Simplified UI/UX**: Provide an easy-to-use interface for volunteers.
*   **Multi-Session Support**: Create programs with multiple sessions and automatic schedule displays.
*   **Multi-Language Support**: Offer registration pages in multiple languages.
*   **Batch Support with Different Fees**: Allow different timings and fees per batch.
*   **Zoom Integration**: Integrate with Zoom for online programs.
*   **Waitlist and Payment Links**: Manage waitlists and provide payment links for completing registrations.

### 13.3 EU PRS Features

*   **Ease of Registration**: Implement one-click registrations without mandatory SSO for free offerings and first-time users.
*   **Customization**: Allow flexibility in modifying landing pages, thank-you pages, and email templates.
*   **Donation Integration**: Provide donation options post-registration.
*   **Compliance with GDPR**: Ensure strict adherence to GDPR regulations.
*   **Address and Phone Fields**: Customize fields based on country-specific formats.
*   **Program Setup Features**: Support different pricing based on geolocation and offer ride-share options for advanced programs.
*   **Support and Analytics**: Provide 24-hour support and advanced analytics for tracking user actions.

  

* * *

## 14\. Volunteer Program Registration System (Volunteer PRS)

*   **Integration with Participant PRS**: Develop a subsystem for volunteer registrations, integrated seamlessly with the participant PRS.
*   **Volunteer Management**: Handle volunteer applications, assignments, and communications efficiently.

  

**Other Systems:**

*   Datadog for Audit, logging
*   Mixpanel for analytics
*   Freshdesk for support
*   ClickUp for Project Management
*   OpenAPI based API docs by Litestar
*   Artifact storage - AWS S3
*   Jenkins for CI/CD

  

List:

| System | Location | Tech | Version | How |
| ---| ---| ---| ---| --- |
| Database | AWS RDS | Postgres | 16.4 | Managed |
| API | AWS ECS | Litestar | 2.21.1 | Docker |
| Backend | CloudPepper | Odoo | 17.0 | Managed |
| Frontend | Netlify | React | 18.3.1 | yarn |
| Worker | AWS ECS | Celery | 5.4.0 | Docker |
| Cache | AWS EC2 | Redis | 5.1.0 | Docker |
|  |  |  |  |  |

  

**Github Repos:**

Worker:

[

github.com

https://github.com/IshaFoundationIT/prs-worker.git

](https://github.com/IshaFoundationIT/prs-worker.git)

Facade:

[

github.com

https://github.com/IshaFoundationIT/prs-facade.git

](https://github.com/IshaFoundationIT/prs-facade.git)

# Security

### 1\. **Authentication**

*   **JWT (JSON Web Tokens):** Issue signed JWTs for secure, stateless authentication between clients and the API. Tokens should have expiration times and be signed using strong algorithms (e.g., RSA, HMAC).
*   **Role-based Access Control (RBAC):** Implement RBAC to restrict access based on the user’s role or permissions. This ensures users can only access the data and resources they are permitted to.

### 2\. **API Gateway**

*   **Rate Limiting and Throttling:** Use an API Gateway (e.g., AWS API Gateway, NGINX, Kong) to limit the number of requests from a client to prevent abuse (DDoS protection).
*   **Load Balancing:** Gateways can help distribute traffic across multiple API instances, ensuring stability and high availability.
*   **IP Whitelisting/Blacklisting:** Restrict access to the API based on IP addresses or ranges, allowing access from trusted networks or clients only.
*   **Logging and Monitoring:** Gateways often come with built-in logging and monitoring tools, which are essential for tracking API usage and detecting potential security threats.

### 3\. **Data Encryption (Optional)**

*   **Transport Layer Security (TLS/SSL):** Enforce HTTPS to ensure data is encrypted during transit. This protects against man-in-the-middle (MITM) attacks.
*   **Data Encryption at rest & transport(optional):** For sensitive data (e.g., personal data, program submissions), use encryption to protect data stored in databases or cache systems. - This can be used for things like Aadhaar card if needed.

### 4\. **Input Validation and Sanitization**

*   **Input Validation:** Strictly validate incoming API requests. Ensure input matches expected data types, lengths, and formats. Reject any invalid or malformed data.
*   **Cross-Site Scripting (XSS) Prevention:** Escape or sanitize data that might be reflected back to the user or rendered in the frontend.
*   **Cross-Site Request Forgery (CSRF) Protection:** Implement CSRF tokens for APIs that modify state (e.g., form submissions or updates) to prevent unauthorized actions from malicious sites.

### 5\. **API Rate Limiting and Quotas**

*   **Rate Limiting:** Limit the number of requests a user or system can make in a given timeframe to prevent abuse and resource exhaustion.
*   **Quotas:** Apply quotas per user, per application, or per endpoint to ensure that no user consumes more resources than allowed.

### 6\. **Audit Logging**

*   **Access Logs:** Log every API request with details like IP address, user-agent, user ID, endpoint accessed, and time of request.
*   **Audit Trails:** Keep logs of sensitive operations (e.g., data updates, form submissions) to ensure accountability and track any potential misuse or data leaks.
*   **Log Retention Policies:** Ensure logs are retained for an appropriate time for auditing and security incident investigation.

### 7\. **Secure API Design**

*   **Least Privilege:** Design APIs with the principle of least privilege, ensuring that each endpoint exposes only the data or operations necessary for a given role or system.
*   **Versioning:** Version your APIs to ensure that outdated or insecure APIs can be deprecated safely without disrupting service.

### 8\. **API Gateway with Security Features**

*   **Authentication Delegation:** Offload token validation and authentication to the API Gateway so that invalid requests are rejected before they hit your application.
*   **Data Masking:** Mask sensitive data in API responses if the entire dataset isn't necessary for the client.
*   **CORS (Cross-Origin Resource Sharing):** Properly configure CORS to allow only trusted domains to access your APIs.

### 9\. **Token Expiration and Refresh**

*   **Short-lived Tokens:** Use short-lived access tokens (e.g., 15-60 minutes) and issue refresh tokens to avoid long-lived tokens being misused.
*   **Refresh Tokens:** Implement secure refresh tokens for longer-lived sessions while keeping access tokens short-lived.

###   

### Example Workflow for Securing the API Layer:

1. **User Authentication:** Users authenticate and a JWT is issued and signed securely.
2. **Rate Limiting:** API Gateway imposes rate limits based on the user’s role or IP address to prevent abuse.
3. **Input Validation:** The API layer validates inputs (e.g., forms or registration data) using strong validation rules.
4. **CORS Rules:** Ensure that only specific domains can call the API to prevent unauthorized access from external origins.
5. **Data Encryption:** API traffic is secured with TLS, ensuring data-in-transit is encrypted.
6. **API Gateway Logs:** Logs and metrics from the API gateway are sent to a monitoring tool for visibility and anomaly detection.

  

### **Tools Suggested:**

*   Isha SSO for login & auth
*   Cloud-flare for WAF
*   DataDog for Audit, Logging & Monitoring

# Project Plan

### Larger Programs Plan & Ecosystem:

![](https://t37438690.p.clickup-attachments.com/t37438690/4220992c-81c9-411d-b4ed-0a06ee13f19e/project.png)

### Phase 1 Suggested Plan:

*   Plan the architecture for the entire larger programs ecosystem
*   Develop and deploy region wise. When a particular region is onboarded on Global PRS, all programs and connected systems are onboarded onto Global PRS
*   Phase 1 focus to launch in EU region. (Optionally add Africa & Middle-east)
*   **Phases**:
    *   Base system architecture plan (Complete)
    *   Base system architecture development (Complete)
    *   Specific EU Requirements finalisation (Complete)
    *   Design/screens finalisation (Complete)
    *   Development (In Progress)
    *   Internal testing
    *   IPC IT testing
    *   UAT testing with the EU team
    *   Launch

# Masters

[https://docs.google.com/spreadsheets/d/1aETTs4Ik3ku8noEDyH3\_qS1wEcnvtoB2QRM7bPpYzBg/edit?gid=185028216#gid=185028216](https://docs.google.com/spreadsheets/d/1aETTs4Ik3ku8noEDyH3_qS1wEcnvtoB2QRM7bPpYzBg/edit?gid=185028216#gid=185028216)[https://docs.google.com/spreadsheets/d/1DX1FXvbeFbwgYXml6ny-i24-Jk5AURw\_uac5MOD9\_eU/edit?gid=0#gid=0](https://docs.google.com/spreadsheets/d/1DX1FXvbeFbwgYXml6ny-i24-Jk5AURw_uac5MOD9_eU/edit?gid=0#gid=0)

# LLM Summary

The Global Program Registration System (Global PRS) by Isha Foundation is a centralized platform designed to manage registrations for programs like Inner Engineering, Shoonya, and Bhava Spandana. It ensures stability and flexibility to accommodate diverse regional requirements.

  

**Key Pointers:**

*   **Layered Architecture:** Divides functionalities into API, frontend, Odoo backend, and database layers, allowing independent scaling and optimized performance.
*   **Modular Design:** Generic, modular components enable updates without overhauling the entire system, enhancing maintainability.
*   **Scalability:** Supports horizontal scaling, especially in the API layer, to handle increased loads efficiently.
*   **Decoupled Workers:** Odoo workers operate separately from the server, allowing future integration with other platforms for backend tasks.
*   **Integration Layer:** Manages connections with external systems independently, facilitating seamless data flow.
*   **Security:** Enhanced measures at the API layer ensure that only authorized systems can submit forms and access data.

  

**Technology Stack:**

  

*   **Management System:** Odoo/Python
*   **Database:** PostgreSQL
*   **Cache:** Redis
*   **API:** Litestar/Python
*   **Frontend:** React/TypeScript
*   **Workers:** Celery/Python

  

**Sample Data Flow:**

  

1. **Program Setup in Odoo:** Configure programs in Odoo; data is stored in PostgreSQL.
2. **Pushing to Cache:** Form configurations are cached in Redis to enhance access speed.
3. **Fetching Configurations via API:** API retrieves configurations from Redis and sends them to the frontend.
4. **Rendering the Form:** Frontend dynamically renders the form using the JSON configuration.
5. **Form Submission:** Users submit forms; submissions are sent to the API with security measures like authentication.
6. **Data Saved to Database:** API processes submissions, storing data in PostgreSQL tables and dynamic responses as JSON.
7. **View Registrations in Odoo:** Program teams access submissions in Odoo and perform necessary actions.
8. **Delayed Tasks via Workers:** Tasks like sending confirmation emails are handled asynchronously by Celery workers.
9. **Connected Apps:** Other applications use the same API to render forms and manage registrations.

# Integration with Global PRS

GlobalPRS is a centralized, scalable platform for managing registrations for all Isha programs globally.

External systems such as MyMedic can seamlessly integrate with Global PRS through a robust set of APIs to launch events, manage forms, and handle registrations.

* * *

### **Integration Overview**

#### **1\. Templates Managed in PRS**

Program templates (flows, forms, configurations) are centrally created and managed by PRS admin teams, ensuring standardization across events.

#### **2\. Launch Events via API**

External systems can create and launch events using PRS APIs with minimal parameters, leveraging predefined templates for the rest of the configuration.

Key APIs: _Create Event_, _Fetch Flows for Event_.

#### **3\. Flexible Form Embedding Options**

Forms can be rendered through multiple integration methods:

*   Directly on the Global PRS frontend.
*   Embedded in third-party platforms using:
    *   _Iframe_.
    *   _Inline - using react Component Library by PRS._
    *   _Hybrid Flow_ – Start in the external system, continue in PRS.

#### **4\. Manage Registrations via API**

External systems can retrieve and act upon registration data efficiently.

Key APIs: _Fetch All Registrations_, _Transfer Registration_, _Cancel Registration_, and other registration actions.

* * *

### **Key Notes**

*   **Downstream Integrations** such as _Ishangam sync_, _ERP updates_, _e-Receipt generation_, and _confirmation emails_ are fully handled by PRS.
*   **Security & Access Control** are enforced at the API layer to ensure authorized access and data integrity.
*   Designed for **scalability and flexibility**, supporting both regional requirements and platform-level consistency.

* * *

### **Next Steps**

1. Choose preferred **form embedding method** (iframe, inline, hybrid).
2. Request access to **API credentials** and developer documentation.
3. Use the **sandbox environment** to test event creation and registration flows.
4. Move to production.

# Usage Guidelines

*   **Backend (UAT):**
    *   Backend link: [https://uat-prs-in-admin.sadhguru.org](https://uat-prs-in-admin.sadhguru.org)
    *   Login with SSO
    *   Odoo Admin Panel Link: [https://uat-prs-in-admin.sadhguru.org/web](https://uat-prs-in-admin.sadhguru.org/web)
    *   Main Sections:
        *   **Programs**: **(Main System)**
            *   All the running and draft programs and flow along with registrations
            *   This is the main section which will be shared with everyone. All other sections will be strongly access controlled.
        *   **Templates: (Semi-Internal)**
            *   All the templates creation menus.
            *   This will be shared with the template creation team
        *   **Masters: (Internal)**
            *   This will be strictly access controlled, for setting up masters for the system. Can come from other systems later if needed.
        *   **Data: (Internal)**
            *   All other internal data tables. Would only be mostly visible to internal users.
            *   Not needed for most use cases.
    *   **Primary notes/Issues:**
        *   Some drop-downs/pre-fills in template creation may not be updated unless the underling table is saved. e.g. Event needs to be saved for creating event category. Not doing this will cause issues. We haven't yet found a solution to do this easily.
    *   **Reference Videos:**
        *   Event Template Creation

[https://t37438690.p.clickup-attachments.com/t37438690/057325c3-7977-466b-9f42-e89788cb2acb/Event-Template-Creation.mp4?view=open](https://t37438690.p.clickup-attachments.com/t37438690/057325c3-7977-466b-9f42-e89788cb2acb/Event-Template-Creation.mp4?view=open)

*   Create Template Form

[https://t37438690.p.clickup-attachments.com/t37438690/a4c9d270-3588-480e-b495-731befb5f71c/Create%20Template%20Form.mp4?view=open](https://t37438690.p.clickup-attachments.com/t37438690/a4c9d270-3588-480e-b495-731befb5f71c/Create%20Template%20Form.mp4?view=open)

*   Create Flow Template

[https://t37438690.p.clickup-attachments.com/t37438690/e07ff7db-8b1a-43f6-992a-0f7a1c0b5b36/Create%20Flow%20Template.mp4?view=open](https://t37438690.p.clickup-attachments.com/t37438690/e07ff7db-8b1a-43f6-992a-0f7a1c0b5b36/Create%20Flow%20Template.mp4?view=open)

*   Create Event

[https://t37438690.p.clickup-attachments.com/t37438690/4e64a865-4f03-4fc1-a4ab-944f9680de46/Create%20Event.mp4?view=open](https://t37438690.p.clickup-attachments.com/t37438690/4e64a865-4f03-4fc1-a4ab-944f9680de46/Create%20Event.mp4?view=open)

# Odoo Guide

*   **Some General Pointers/Notes:**
    *   Images added to pre-section or anywhere else must be either S3 urls. If using odoo's uploader, the html needs to be edited to add full path.
    *   If first step is a global API ➝ needs a registration step before.
    *   For t&c checkboxes, keep the choices as the t&c content
    *   Payment Step should have a in-progress form.
    *   Reserve Seat and confirm seat needed with payments step

# Odoo - Field Masters

*   **Field Type:** Documented in Private ([https://app.clickup.com/37438690/docs/13ph72-100576/13ph72-40196](https://app.clickup.com/37438690/docs/13ph72-100576/13ph72-40196))
*   **Field Key:** Unique field key for internal database use → Try using format like my\_field\_key
*   **Field Name:** Backend name for the backend team to identify the field
*   **Field Label:** The label displayed to the participant
*   **Display Section:** Not used in field masters
*   **Display Order:** Not used in field masters
*   **Should Call API:** This should only be enabled for very specific fields. (Currently only 4-5 fields support this, will be configured by the tech team)
*   **Should Display:** If the field should be displayed or not. (e.g. hidden field)
*   **Should Display Label:** If the label of the field should be displayed to the participant
*   **Display Expression:** The JEXL expression, if evaluated to true, will display the field. Can use any other visible or hidden field. If JEXL expression throws error, the default fallback will be true. JEXL expressions documented here: You don't have access to this Doc
*   **IsMarketingTracked:** If true, the participants submission of this field will be sent to mixpanel.
*   **IsKeyLocked:** Not used for masters
*   **Placeholder Text:** The placeholder text displayed in the field (In a light color)
*   **Default Value:** Prefilled value in a field. This supports Jinaj2 Expressions documented here: Private ([https://app.clickup.com/37438690/docs/13ph72-100576/13ph72-45916](https://app.clickup.com/37438690/docs/13ph72-100576/13ph72-45916))
*   **Width:** Half or full. This is mainly for desktop view. If you want say first\_name and last\_name to appear in the same row, both will have width as half. On mobile, they will still show one below another
*   **Read only:** If this field is read only, participant wont be able to edit it.
*   **Disabled**: If the field is disabled. Similar to read only.
*   **Required:** If the field is required. This will display the \* mark next to the field and and ensure that the field is mandatory. No need to add validation for mandatory fields, this required flag handles it.
*   **Validation Logic:** The multi-line validation documented here: Private ([https://app.clickup.com/37438690/docs/13ph72-100576/13ph72-45936](https://app.clickup.com/37438690/docs/13ph72-100576/13ph72-45936)). Do not use this for simply marking the field as required or non empty. Use Required flag.

# Jinja2 Prefill Expressions

Jinja2 is a powerful templating engine that allows for dynamic content generation using placeholders and expressions. In the context of prefill expressions, Jinja2 templates can dynamically populate form fields or messages using predefined data such as event details, registration information, and flow data.

These are processed on the facade.

  

* * *

**Prompt to use with ChatGPT:**

  

**"Convert the following condition into a valid Jinja2 expression using the context:** **`event`****,** **`registration`****,** **`transactions`** **and** **`flow`****. Format the output clearly with code blocks and explain briefly if logic or filters are used.**

> **Condition: <your condition here>"**

  

**Example usage:**

  

Convert the following condition into a valid Jinja2 expression using the context: `event`, `registration`, and `flow`. Format the output clearly with code blocks and explain briefly if logic or filters are used.

> Condition: Display a thank-you message that includes the attendee’s name and event name, and show the event date in "Month Day, Year" format.

  

**Expected Output from ChatGPT:**

  

```plain
Thank you {{ registration.name }} for registering for {{ event.name }}.
The event is scheduled for {{ event.date | date("%B %d, %Y") }}.
```

  

### Context for Rendering

The Jinja2 `template.render` function uses the following dictionary as its context:

```bash
{
    'event': event_dict,
    'registration': registration_dict,
    'flow': flow_array,
    'transactions': transaction_array
}
```

*   **`event`**: A dictionary containing all event-related properties.
*   **`registration`**: A dictionary containing all registration-related properties.
*   **`flow`**: An array of flow objects, representing additional data associated with the process.

### How to Use Jinja2 for Prefilling

Using Jinja2, you can reference data from the context to dynamically generate content. Placeholders for data are defined using double curly braces (`{{ }}`), and logic or filters can be applied as needed.

### Examples of Prefill Expressions

#### Example 1: Prefill Amount

Condition: Prefill a field with the registration amount and currency.

```plain
{{ registration.currency }} : {{ registration.amount }}
```

**Output**:

```yaml
USD : 100
```

#### Example 2: Thank You Message

Condition: Generate a thank-you message for a registration.

```plain
Thank you {{ registration.name }} for registering for {{ event.name }}.
```

**Output**:

```rust
Thank you John Doe for registering for Yoga for Beginners.
```

#### Example 3: Flow Information

Condition: List all available flows.

```plain
The following flows are available:
{% for flow_item in flow %}
- {{ flow_item.name }}
{% endfor %}
```

**Output**:

```plain
The following flows are available:
- Registration Flow
- Payment Flow
- Confirmation Flow
```

#### Example 4: Personalized Welcome Message

Condition: Create a personalized message for an attendee based on their registration and event details.

```plain
Welcome {{ registration.name }}! We are excited to have you at {{ event.name }}.
The event will take place on {{ event.date }} at {{ event.location }}.
```

**Output**:

```plain
Welcome John Doe! We are excited to have you at Yoga for Beginners.
The event will take place on January 25, 2025, at The Wellness Center.
```

#### Example 5: Conditional Content

Condition: Show different messages based on the registration type.

```plain
{% if registration.type == 'VIP' %}
Welcome, VIP guest {{ registration.name }}!
{% else %}
Thank you for registering, {{ registration.name }}.
{% endif %}
```

**Output** (for a VIP registration):

```plain
Welcome, VIP guest John Doe!
```

**Output** (for a standard registration):

```erlang
Thank you for registering, John Doe.
```

### Using Filters in Jinja2

Jinja2 supports filters to format and manipulate data within templates. Here are a few examples:

#### Example 6: Formatting Dates

Condition: Display the event date in a specific format.

```plain
The event date is {{ event.date | date("%B %d, %Y") }}.
```

**Output**:

```plain
The event date is January 25, 2025.
```

#### Example 7: Uppercase Transformation

Condition: Display the event name in uppercase.

```plain
EVENT: {{ event.name | upper }}
```

**Output**:

```yaml
EVENT: YOGA FOR BEGINNERS
```

#### Example 8: Default Values

Condition: Show a default value if a field is not provided.

```plain
{{ registration.phone_number | default('Not Provided') }}
```

**Output** (if phone number is missing):

```plain
Not Provided
```

### Advanced Examples of Prefill Expressions

#### Example 9: Dynamic Iteration with Conditional Logic

Condition: Display different messages for each flow depending on its status.

```plain
{% for flow_item in flow %}
- {{ flow_item.name }}: 
  {% if flow_item.status == 'completed' %}
    Completed on {{ flow_item.completed_date | date("%B %d, %Y") }}
  {% else %}
    Status: {{ flow_item.status | capitalize }}
  {% endif %}
{% endfor %}
```

**Output**:

```yaml
- Registration Flow: Completed on January 20, 2025
- Payment Flow: Status: Pending
- Confirmation Flow: Status: In Progress
```

#### Example 10: Calculated Values

Condition: Display the total amount paid, including tax.

```plain
Total Amount: {{ registration.amount + (registration.amount * registration.tax_rate / 100) }} {{ registration.currency }}
```

**Output**:

```yaml
Total Amount: 110 USD
```

#### Example 11: Multi-Line Messages with Line Breaks

Condition: Prefill a custom email message.

```plain
Dear {{ registration.name }},

Thank you for registering for {{ event.name }}. Here are the details:
- Date: {{ event.date | date("%B %d, %Y") }}
- Location: {{ event.location }}

We look forward to seeing you there!

Best Regards,
The {{ event.organizer }} Team
```

**Output**:

```yaml
Dear John Doe,

Thank you for registering for Yoga for Beginners. Here are the details:
- Date: January 25, 2025
- Location: The Wellness Center

We look forward to seeing you there!

Best Regards,
The Wellness Team
```

#### Example 12: Nested Loops with Complex Logic

Condition: Display a summary of flows grouped by their types.

```plain
{% set flow_types = flow | groupby('type') %}
{% for type, grouped_flows in flow_types %}
### {{ type | capitalize }} Flows ###
{% for flow_item in grouped_flows %}
- {{ flow_item.name }}: {{ flow_item.status | capitalize }}
{% endfor %}
{% endfor %}
```

**Output**:

```plain
### Registration Flows ###
- Basic Registration: Completed
- Advanced Registration: In Progress

### Payment Flows ###
- Initial Payment: Pending
- Final Payment: Completed
```

### Tips for Writing Jinja2 Templates

1. **Use Meaningful Variable Names**: Make sure the context variables (like `event`, `registration`) are descriptive and easy to understand.
2. **Leverage Control Structures**: Use `if`, `for`, and other control structures to add dynamic behavior.
3. **Apply Filters for Formatting**: Use Jinja2's built-in filters to format and transform data as needed.
4. **Test Templates**: Validate your templates with sample data to ensure they render correctly.

### Available Variables

*   All registration variables entered by participant in the form, or as hidden fields.
*   All event variables

###

# JEXL Expressions

### **Prompt to Generate Valid Boolean JEXL Expressions**

  

**"Convert the following condition into a valid JEXL expression that uses field keys wrapped in curly braces (e.g.,** **`{email}`****,** **`{phone}`****). The expression must return a boolean value (****`true`** **or** **`false`****). Use functions like** **`.contains()`****,** **`.startsWith()`****,** **`.endsWith()`****,** **`.length()`****, or logical operators (****`==`****,** **`!=`****,** **`&&`****,** **`||`****, etc.) as needed. Format the result in a code block and explain any logic or functions used.**

> **Condition: <your condition here>"**

* * *

  

### 💡 **Example Usage**

  

Convert the following condition into a valid JEXL expression that uses field keys wrapped in curly braces (e.g., `{email}`, `{phone}`). The expression must return a boolean value (`true` or `false`). Use functions like `.contains()`, `.startsWith()`, `.endsWith()`, `.length()`, or logical operators (`==`, `!=`, `&&`, `||`, etc.) as needed.

> Condition: Verify if the email domain is '[example.com](http://example.com)'.

  

**Expected Output:**

  

```plain
{email}.endsWith('@example.com')
```

###   

### Example Expressions

Here are several expressions that evaluate to boolean values based on the sample data:

1. **Check if the gender is female:**

```java
{gender} == 'female'  // Evaluates to true
```

2. **Verify if the email domain is '**[**example.com**](http://example.com)**':**

```java
{email}.endsWith('@example.com')  // Evaluates to true
```

3. **Determine if the phone number starts with the country code '+1':**

```java
{phone}.startsWith('+1')  // Evaluates to true
```

4. **Check if the name contains the substring 'Ram':**

```java
{name}.contains('Ram')  // Evaluates to true
```

5. **Confirm if the country is either 'USA' or 'Canada':**

```java
{country} == 'USA' || {country} == 'Canada'  // Evaluates to true
```

6. **Verify if the state is not 'Tamil Nadu':**

```java
{state} != 'Tamil Nadu'  // Evaluates to true
```

7. **Check if the city name length is greater than 5 characters:**

```java
{city}.length() > 5  // Evaluates to true
```

8. **Determine if the email contains a period before the '@' symbol:**

```java
{email}.split('@')[0].contains('.')  // Evaluates to false
```

9. **Verify if the phone number has 12 digits (including the '+' sign):**

```java
{phone}.length() == 12  // Evaluates to true
```

10. **Check if all fields are non-empty:**

```java
{gender} && {email} && {phone} // Evaluates to true
```

* * *

### Available Variables

*   All registration variables entered by participant in the form, or as hidden fields.

# Validation Logic

Validations are multi-line checks to ensure that the data entered by a participant for a specific field is proper and adheres to the desired format or logic. These are processed on the frontend - React side.

### Structure of a Validation Row

Each validation row includes the following components:

1. **Name**: A unique identifier for internal tracking of the validation.
2. **Regex**: A regular expression used to evaluate the field's input. If the input does not match the regex, the validation fails.
3. **Error Message**: A descriptive message displayed to the participant if the regex validation fails, helping them understand how to correct the input.
4. **Validation Expression**: A JEXL expression that determines whether the validation is applicable. For example, a validation might only apply if the selected country is Germany.
    *   JEXL expressions are documented here: You don't have access to this Doc

### Validation Flow

1. **Enable Validation**: The `Validation Expression` is first evaluated using JEXL. If it evaluates to `true`, the validation becomes active.
2. **Perform Regex Check**: The input is validated against the provided `Regex`. If the regex fails, the validation fails, and the corresponding error message is displayed.
3. **Validation Outcome**: Only if all active validations pass will the field be marked as "OK" to proceed further.

### Regex Examples

Here are some common regex patterns used in validations:

#### Example 1: Email Validation

*   **Regex**:

```plain
^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
```

*   **Description**: Ensures the input is a valid email address.
*   **Error Message**: "Please enter a valid email address."

#### Example 2: Phone Number Validation

*   **Regex**:

```ruby
^\+?[1-9]\d{1,14}$
```

*   **Description**: Validates international phone numbers with an optional "+" prefix.
*   **Error Message**: "Please enter a valid phone number."

#### Example 3: Postal Code Validation (5 Digits)

*   **Regex**:

```elixir
^\d{5}$
```

*   **Description**: Ensures the input is a 5-digit postal code.
*   **Error Message**: "Postal code must be exactly 5 digits."

#### Example 4: Alphanumeric Field Validation

*   **Regex**:

```elixir
^[a-zA-Z0-9]+$
```

*   **Description**: Allows only alphanumeric characters without spaces.
*   **Error Message**: "This field accepts only alphanumeric characters."

### Example of a Validation Row

Here is an example of a validation row:

```plain
- Name: "Germany Postcode Validation"
  Regex: "^\d{5}$"
  Error Message: "Postal code must be exactly 5 digits."
  Validation Expression: "country == 'Germany'"
```

*   **Explanation**: This validation ensures that the postal code is exactly 5 digits but is only applicable if the selected country is Germany.

### Tips for Writing Validations

1. **Use Clear Names**: Ensure validation names are descriptive and easy to identify.
2. **Write User-Friendly Error Messages**: Messages should clearly guide participants to correct their input.
3. **Keep Regex Simple and Efficient**: Avoid overly complex regex patterns that could impact performance.
4. **Test Validations Thoroughly**: Validate inputs against all possible edge cases.

### Advanced Example: Combining Multiple Conditions

Condition: Validate that a phone number starts with "+49" only if the country is Germany.

```plain
- Name: "Germany Phone Number Validation"
  Regex: "^\+49\d{9,13}$"
  Error Message: "Phone numbers in Germany must start with '+49' and contain 9 to 13 digits."
  Validation Expression: "country == 'Germany'"
```

###

# ShouldCallAPI fields

*   List of fields that support **shouldCallAPI**:
    *   country\_id
    *   zip
    *   event\_category\_id
    *   coupon\_code
    *   batch\_id
    *   whatsapp\_same\_as\_phone

  

*   Other fixed notable fields: (which have fixed logic, and hence should have this exact key name)
    *   state\_id
    *   city
    *   taluk
    *   inventory\_id
    *   amount
    *   currency
    *   phone
    *   whatsapp

# Field Types

*   Generic Understanding:
    *   Field Key: For internal use only (DB key)
    *   Field Label: The label displayed with the field.
    *   Field Title: The internal back-end field name for reference.
    *   Placeholder Text: Comes as grey placeholder, which is removed on typing
    *   Helper Text: Comes below the field.
    *   Width: Half or Full Width
    *   Should Display: Whether to display the field or not
    *   Read only: Is the field read only
        *   Should Display Label: Whether to display the label or not
    *   Display Order: The order of display with regards to other fields
    *   Is Marketing Tracked: Should the value of this field be sent to Mixpanel on submit.
    *   Disabled: Mark the field as disabled. (Under review)
    *   Display Condition Expression: The expression which if succeeds, the field should be displayed. If empty, considered as true.
    *   Choices: The choices to show in Dropdown or Radio options. It can be from a master table(e.g. states), Categories, Languages, Batches or Custom.

  

*   Specific Field Types:
    *   **Text**
        *   Function: Normal Text Box
    *   **TextArea**
        *   Function: Expandable Large Text Box
    *   **Phone**
        *   Function: Displays phone number field with country code selection
        *   Notes: Country will be auto selected based on IP
    *   **SimpleSwitch**
        *   Function: Displays a simple switch which will have true or false value
        *   Notes: Label will be displayed as the switch label
    *   **Dropdown**
        *   Function: Displays a dropdown to choose values from
    *   **Date**
        *   Function: Displays a date picker
    *   **Time**
        *   Function: Displays a time input
    *   **Datetime**
        *   Function: Displays a datetime picker
    *   **File**
        *   Function: Displays a file picker
    *   **Image**
        *   Function: Displays a image picker with options
    *   **Password**
        *   Function: Displays a text field with hidden text \*\*\*
    *   **Dynamic Text**
        *   Function: Displays a text which is rendered dynamically. e.g. {{[event.name](http://event.name)}} or {{[registration.name](http://registration.name)}}
    *   **HTML**
        *   Function: Displays html which also supports dynamic text in it.
    *   **Radio**
        *   Function: Displays radio options
    *   **Checkbox**
        *   Function: Displays checkboxe(s)
        *   If single checkbox is needed, dont pass choices. Label will be used as the display text, and value will be true or false.
    *   **ScrollableCheckbox**
        *   Function: Displays a scrollable content checkbox for T&C
        *   Notes: Need further info to configure.
    *   **Submit**
        *   Function: Button to submit the form or go to next page
    *   **Rating**
        *   Function: A rating selector from 1 to 10
    *   **CouponCode**
        *   Function: A custom field for coupon code with apply button.

# Regex Expressions

## 📘 **Basic Regular Expressions (Regex) Guide**

  

### 💬 **Prompt to Share with an LLM to Generate a Valid Regex**

  

**"Generate a valid regex pattern that matches the following condition. Make sure the regex is safe, valid, and works with standard regex engines (like JavaScript or Python). Return the pattern in a code block and explain what it does.**

> **Condition: <your condition here>"**

  

#### ✅ Example Prompt:

> Generate a valid regex pattern that matches a U.S. ZIP code (5 digits or 5 digits followed by a hyphen and 4 digits). Return it in a code block and explain it.

* * *

  
  

### 🚫 **Cautions**

*   Escape special characters (`.`, `+`, `?`, `(`, `)`, etc.) when used literally.
*   Regex is greedy by default — use `*?`, `+?` for lazy matching if needed.
*   Use `^` and `$` anchors to match full string, not just part of it.

* * *

  

### 🧪 **Tools for Testing**

*   [regex101.com](https://regex101.com)
*   [regexr.com](https://regexr.com)
*   [RegExr Playground](https://www.regexr.com)

# ISO Integration

## Event APIs Documentation

These APIs allow you to **retrieve event data** from the system. Useful for admin panels, dashboards and ISO.

* * *

**Sample CURL** for get Event Details - Marketing Page: (event\_id: 26, lang\_id can be fr\_CA or en\_US)

```plain
curl --location --request GET 'https://uat-donations-api.sadhguru.org/admin/event/26?lang_id=fr_CA' \
--header 'Accept: application/json' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjExNzUzMTQyMjM3LCJjdXN0b21fY2xhaW1zIjp7InNzb190b2tlbiI6ImFkbWluIiwic3NvX2lkIjoiYWRtaW4ifX0.O-wUDXObVtfSz1ywH_n2ZM7ltV2Aft2A2X-lgxm4VSA' \
--header 'x-system-token: D8yV2_eEAdXtczhWUBh3MDRLm3tTxQSuGn5u_Op1kx8'
```

### 🔐 Authentication Headers

All requests to the following endpoints require the headers below:

```plain
Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjExNzUzMTQyMjM3LCJjdXN0b21fY2xhaW1zIjp7InNzb190b2tlbiI6ImFkbWluIiwic3NvX2lkIjoiYWRtaW4ifX0.O-wUDXObVtfSz1ywH_n2ZM7ltV2Aft2A2X-lgxm4VSA

x-system-token: D8yV2_eEAdXtczhWUBh3MDRLm3tTxQSuGn5u_Op1kx8
```

* * *

### API Endpoints

#### 1\. **Get All Events**

*   **Endpoint:** `GET /admin/list/events`
*   **Description:** Retrieves a list of all events.
*   **Parameters:** Optional query parameters such as pagination, filters, etc.

* * *

#### 2\. **Get Event Details**

*   **Endpoint:** `GET /admin/event/:eventId`
*   **Description:** Fetches details for a specific event by ID.
*   **Path Parameter:**
    *   `eventId`: Unique identifier for the event.

* * *

### 📚 API References

*   **Simplified Docs:** [https://uat-donations-api.sadhguru.org/docs/#/](https://uat-donations-api.sadhguru.org/docs/#/)
*   **Detailed Docs:** [https://uat-donations-api.sadhguru.org/docs/detailed#tag/admin-apis/get/admin/list/events](https://uat-donations-api.sadhguru.org/docs/detailed#tag/admin-apis/get/admin/list/events)

### Sample Screenshots

→ Event Details

![](https://t37438690.p.clickup-attachments.com/t37438690/47c6d8e7-3b30-4163-8037-86c01a071fc7/Screenshot%20from%202025-03-26%2017-44-10.png)

→ Event List

![](https://t37438690.p.clickup-attachments.com/t37438690/bb7ebeae-e3eb-4284-b038-2997745e4bcc/Screenshot%20from%202025-03-26%2017-42-38.png)

# MyMedic Suggested Integration

* * *

### Programs Flow:

MyMedic now fully integrates with the Global PRS System to enable and manage Isha Health Programs. While MyMedic remains the primary portal for admins and doctors, PRS handles program creation, participant registration, approval workflows, payments, and downstream data distribution.

1. **Program Setup & Launch:**
    *   Health programs are created as templates in PRS.
    *   The Health Solutions back end team launches a program by selecting the template, inventory, and start date.
    *   MyMedic calls PRS APIs to initiate the program.
    *   PRS pushes program details to the ISO website ([isha.sadhguru.org](http://isha.sadhguru.org)) for public display.
2. **Participant Registration (Initiated via ISO):**
    *   Participants discover the program on ISO.
    *   Clicking "Register" redirects to a PRS hosted form.
    *   They fill out the form but do not make a payment yet. (Includes HAF if needed)
    *   The registration enters a "waiting" state.
3. **Approval Workflow via MyMedic:**
    *   The participant data flows back to MyMedic, where doctors/admins can:
        *   View pending registrations
        *   Approve, reject, or request clarification
        *   Trigger email notifications (sent by PRS)
    *   Upon approval, PRS sends a payment link to the participant.
4. **Payment & Confirmation:**
    *   The participant makes the payment via the PRS payment link.
    *   Once completed, PRS:
        *   Sends a confirmation email
        *   Updates MyMedic with the payment status
5. **Post-Registration Actions:**
    *   Participants may request cancellations, upgrades, or modifications.
    *   These are triggered via MyMedic, which internally makes API calls to PRS.
6. **Downstream System Integration:**
    *   PRS distributes data to relevant systems:
        *   Ishangam
        *   ERP
        *   E-Receipts

###   

### **Appointments Flow**

Appointments continue to be managed end-to-end within MyMedic. Participants can view slots, book appointments, and complete payments directly on the portal. As part of the booking process, they are prompted to fill the Health Assessment Form (HAF) via a PRS-hosted flow, accessed through a link or embedded form. Upon submission, MyMedic retrieves the HAF data via PRS APIs and proceeds with the appointment workflow.

#### Step 1: Participant Access

*   Participant logs into the MyMedic portal.
*   Navigates to the Appointments section.

#### Step 2: Appointment Selection

*   Participant views available slots and selects a preferred appointment time.
*   Appointment is temporarily reserved in the system.

#### Step 3: HAF Form Completion

*   Before confirming the booking, participant is prompted to fill the Health Assessment Form (HAF).
    *   A link or embedded PRS form is presented in the portal.
    *   The link includes participant and appointment identifiers to pre-fill relevant details.

#### Step 4: PRS HAF Submission

*   Participant submits the HAF on the PRS-hosted form.
*   Upon submission:
    *   If embedded: form closes and returns to MyMedic seamlessly.
    *   If opened via link: PRS redirects the participant back to MyMedic.

#### Step 5: HAF Data Retrieval

*   MyMedic calls PRS APIs to fetch the submitted HAF data.
*   HAF submission is attached to the participant’s appointment record.

#### Step 6: Appointment Confirmation

*   Once HAF is submitted and validated, the appointment is confirmed.
*   Any required payment (if applicable) is completed on MyMedic.

#### Step 7: Post-Booking Management

*   Appointment management (modifications, cancellations, reminders, etc.) is handled by MyMedic.
*   Downstream system integrations (e.g., ERP, VMS) are also managed by MyMedic.

![](https://t37438690.p.clickup-attachments.com/t37438690/91ef7e4c-ff97-4718-a0bb-f6fe13824f4e/Isha_my_medic_appointments.drawio.png)

[Isha\_my\_medic\_appointments.drawio](https://t37438690.p.clickup-attachments.com/t37438690/74c34043-5012-4614-9566-5b3ca1f77e64/Isha_my_medic_appointments.drawio)

