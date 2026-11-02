# Topic 15 – Production-Ready Business Automation Capstone
## Document AI, Multi-System Orchestration & Resilient Rollback

### 1. Project Overview

This project implements an enterprise employee onboarding automation workflow using n8n.

The workflow receives a signed employment contract PDF, extracts employee information, validates the details, and automatically coordinates onboarding activities across multiple simulated enterprise systems.

The automation integrates four systems:

- HRIS – Employee record creation.
- Identity Provider (IDP) – User account provisioning.
- Asset Management – Hardware requisition.
- Slack – Employee welcome announcement.

It also includes error handling, compensating rollback operations, and centralized onboarding audit logging.

### 2. Project Objectives

The main objectives are:

- Receive and process signed employment contract PDFs.
- Extract employee information from PDF documents.
- Validate mandatory fields and check duplicate submissions.
- Automate employee onboarding across multiple systems.
- Determine hardware requirements based on employee details.
- Process hardware requisitions using batch processing.
- Send employee welcome announcements.
- Handle failures using a separate error workflow.
- Execute rollback operations for completed onboarding steps.
- Maintain an onboarding execution and rollback audit.

### 3. Workflow Architecture

The project consists of three workflows.

#### Workflow 1: Main Employee Onboarding Workflow

This is the primary workflow responsible for processing the employment contract and coordinating the complete onboarding process.

**Main execution sequence:**

1. Manual Trigger – Starts the onboarding workflow.
2. Code_SimulateSignedContractPDF – Generates the PDF binary using the actual PDF Base64 content.
3. Extract_PDF_Contract – Extracts text from the employment contract PDF.
4. Transform_ParseContractText – Parses the extracted text into structured employee information.
5. IF_IsValidAndUnique – Validates mandatory employee details and checks duplicate submissions.
6. HTTP_CreateHRISRecord – Creates the employee record in the HRIS system.
7. HTTP_ProvisionIdentityAccount – Provisions the employee's identity account.
8. Transform_DetermineHardwareBundle – Determines the required hardware based on employee details.
9. Split_Out_HardwareUnits – Splits the hardware bundle into individual items.
10. Item_Lists_BatchHardware – Processes hardware items in batches.
11. HTTP_CreateAssetTicket – Creates the hardware requisition.
12. HTTP_PostSlackWelcome – Simulates posting the employee welcome announcement.
13. Code_MasterOnboardingAudit – Generates the final onboarding execution audit.

#### Workflow 2: Enterprise Mock SaaS Receiver

This helper workflow simulates communication with external enterprise applications.

**Nodes:**

1. Webhook – Receives POST requests at `/mock/saas/dispatch`.
2. Code_MockSaaSRouter – Identifies the system and action and generates the corresponding response.
3. Respond_MockSaaSResponse – Returns the simulated JSON response.

**Supported operations:**

| System | Operation |
|---|---|
| HRIS | Create Employee |
| HRIS | Rollback Employee |
| IDP | Provision User |
| IDP | Rollback User |
| Asset | Create Requisition |
| Asset | Rollback Requisition |
| Slack | Welcome Announcement |

The main workflow communicates with this helper workflow through HTTP Request nodes.

#### Workflow 3: Error Handling and Rollback Workflow

This workflow handles failures during the onboarding process.

**Nodes:**

1. Error Trigger – Receives workflow execution errors.
2. Code_ExecuteSagaRollback – Identifies the completed operations that require compensation.
3. Split_Out_RollbackActions – Separates rollback operations.
4. Switch_RollbackSystem – Identifies the target system.
5. HTTP_ExecuteCompensation – Calls the corresponding rollback API.
6. Code_RollbackAudit – Records rollback execution results.

The main workflow is configured to use this workflow through its Error Workflow setting.

### 4. Employee Contract Sample

The workflow uses the following sample employee information:

| Field | Value |
|---|---|
| Employee Name | Dr. Alexander Vance |
| Email | alexander.vance@enterprise-labs.io |
| Job Title | Principal AI Systems Architect |
| Department | Engineering |
| Start Date | 2026-11-01 |
| Salary | 245000 USD |
| Security Clearance | LEVEL_4 |

### 5. Main Workflow Processing

**Step 1 – Document Processing**

The workflow receives a signed employment contract PDF.

The Extract from File node extracts the document text, and the Code node transforms it into structured employee information.

**Step 2 – Validation and Duplicate Check**

The IF node validates mandatory employee information and checks whether the employee submission has already been processed.

Invalid or duplicate records are prevented from continuing through the onboarding process.

**Step 3 – HRIS Integration**

The workflow sends employee information to the mock HRIS API.

The API returns an employee ID and onboarding status.

**Step 4 – Identity Provisioning**

The returned employee information is used to provision a simulated identity account with MFA enabled.

**Step 5 – Hardware Requisition**

The workflow determines the hardware bundle based on the employee's role and department.

The hardware array is split into individual items and processed in batches before creating the asset requisition ticket.

**Step 6 – Slack Notification**

After the onboarding operations succeed, the workflow sends a welcome announcement request to the simulated Slack API.

**Step 7 – Final Audit**

The workflow records the employee information, system responses, generated IDs, and overall onboarding execution status.

Git Url:

https://github.com/SagarBommela/n8n-Hands-On-Level-2-Topic-15

Loom video:

https://www.loom.com/share/e40f854d984e42b998e5dd58958856ea



