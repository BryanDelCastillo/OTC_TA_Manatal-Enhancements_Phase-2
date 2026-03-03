# Project Scope: Manatal Enhancements Phase 2

## Context

This project aims to enhance the OTC Talent Acquisition workflow in Manatal (a recruitment/ATS system) based on feedback from the TA team. The enhancements focus on simplifying the UI, improving data structure, automating repetitive tasks, and reducing errors in the hiring process. These changes will streamline the recruitment workflow, reduce manual effort, and improve data quality.

## Project Information Sources

- **notes.txt** - Priority-ordered enhancement requirements
- **Sprout Creation.xlsx** (Manatal tab) - Field-level details for UI simplification
- **Manatal Enhancement Project_Project Charter.pdf** - Complete project specifications (requires manual review)

## Technical Scope Breakdown

### 1. UI Simplification (Priority 1)

**Objective:** Remove clutter from Manatal UI by eliminating unused fields

**Tasks:**
- Audit current Manatal form fields against the Sprout Creation.xlsx (Manatal tab)
- Identify unused/rarely-used fields for removal
- Update Manatal configuration/schema to hide or remove fields
- Add "Hire Name" section in Job Offer interface

**Dependencies:**
- Access to Sprout Creation.xlsx to identify specific fields
- Understanding of Manatal's field configuration system (custom fields, required fields, etc.)
- Potential API access or admin portal access to Manatal

**Technical Considerations:**
- Fields may be referenced in existing automations or integrations
- Need to verify data migration/archival strategy for historical records
- May require Manatal admin permissions or API access

### 2. Job Description Structure (Priority 2)

**Objective:** Break down job descriptions into separate, structured fields for better readability

**Current State:** Likely single text field for entire job description

**Target State:** Four separate fields:
1. Introduction
2. Job Description
3. Job Requirements
4. Benefits

**Tasks:**
- Add four new custom fields to Manatal job posting schema
- Update job posting form UI to display separate fields
- Migrate existing job descriptions (may need parsing logic)
- Update any job posting exports/integrations to use new structure

**Technical Considerations:**
- May need to parse/split existing job descriptions
- Consider rich text formatting support for each field
- Integration with job posting channels (if Manatal pushes to job boards)
- Template creation for consistency

### 3. Job Offer Breakdown (Priority 3)

**Objective:** Itemize compensation components instead of single total figure

**Current State:** Likely single "salary" or "compensation" field

**Target State:** Separate fields with preset values:
- Base Pay (variable)
- Rice Allowance: 2000
- Clothing Allowance: 550
- Laundry Allowance: 300
- Wellbeing Allowance (Managers only)
- Transportation Allowance (Managers only)
- Special case: Contractor (different structure)

**Tasks:**
- Add compensation component fields to job offer schema
- **Create allowance reference system** (JSON/YAML config file or Manatal custom field configuration)
  - Rice Allowance: 2000 (default, configurable)
  - Clothing Allowance: 550 (default, configurable)
  - Laundry Allowance: 300 (default, configurable)
- Build admin interface or process for updating allowance values
- Implement role-based field visibility (Manager vs non-Manager)
  - Manager detection: Tickbox/checkbox field in Manatal
  - Show Wellbeing and Transportation allowances for Managers only
- Add contractor exception handling
  - Contractor detection: Dropdown field in Manatal
  - Implement alternative compensation structure for contractors
- Create calculated field for total compensation
- Update job offer templates to show breakdown
- Add validation rules (e.g., Manager role triggers additional allowances)

**Technical Considerations:**
- Manager role: Defined by tickbox field (identify exact field name)
- Contractor identification: Dropdown field (identify field name and values)
- Currency formatting and calculations
- Integration with payroll or HRIS systems
- **Allowance amounts must be configurable** (not hardcoded in code)
- Consider versioning/audit trail for allowance changes
- Test CRM integration compatibility after adding new fields

### 4. Document Generation Automation (Priority 4)

**Objective:** Auto-generate job offer letters and employment contracts from Manatal data

**⚠️ BLOCKED:** Implementation blocked until TA team provides document templates

**Tasks:**
- **Request document templates from TA team:**
  - Job offer letter template
  - Employment contract template
  - Template variable/placeholder documentation
- Determine template format (Word .docx, PDF, or both)
- Build document generation logic:
  - Map Manatal fields to template placeholders
  - Handle conditional sections (Manager allowances, Contractor structure, etc.)
  - Generate PDF or Word documents from templates
- Integrate with Manatal (trigger on status change or manual action)
- Store generated documents in Manatal or external system

**Technical Considerations:**
- **Template availability:** Waiting on TA team to provide templates
- Template format flexibility: Support Word .docx and/or PDF
- Template engine selection (Jinja2, Docx-template, python-docx, etc.)
- Document storage location (Manatal attachments? SharePoint? Cloud storage?)
- Version control for templates
- E-signature integration (if needed)
- Multi-language support (if applicable)
- Legal review requirements for contract templates

**Dependencies:**
- **BLOCKING:** TA team must provide document templates before implementation
- Requires Enhancement #3 (compensation breakdown) to be complete
- Requires Enhancement #1 (hire name field) to be complete

### 5. Duplicate Prevention (Priority 5)

**Objective:** Improve automation to prevent duplicate candidate/job records

**Current State:** Existing automation occasionally creates duplicates

**Tasks:**
- Identify duplicate creation scenarios:
  - Same email address?
  - Same name + phone?
  - External ID conflicts?
- Analyze existing automation logic to find duplicate creation points
- Implement deduplication checks:
  - Pre-insert validation
  - Fuzzy matching for names
  - Email normalization
- Add duplicate detection warnings/blocks
- Create merge utility for existing duplicates (optional)

**Technical Considerations:**
- Define duplicate criteria (exact match vs fuzzy match)
- Performance impact of duplicate checking
- Handling of legitimate duplicates (e.g., two candidates with same name)
- API rate limiting if using Manatal API for checks
- Transaction handling to prevent race conditions

### 6. Email Automation (Priority 6)

**Objective:** Auto-send templated emails to candidates based on stage/status triggers

**⚠️ IMPORTANT:** Manatal's Outlook integration does NOT support automation. Must use external email service.

**Technical Architecture:**
```
Manatal Stage/Status Change → Webhook → Email Service API → Email Sent to Candidate
```

**Tasks:**
- **Select external email service:**
  - Option A: Microsoft Graph API (recommended - maintains Outlook ecosystem)
  - Option B: SendGrid (dedicated email service)
  - Option C: AWS SES (cost-effective for high volume)
- Set up email service account and API credentials
- Configure Manatal webhooks for stage/status change events
- Define stage/status triggers and corresponding email templates:
  - Application received → Acknowledgment email
  - Interview scheduled → Interview details email
  - Offer extended → Offer letter email
  - Rejected → Rejection email
  - Hired → Onboarding email
- Create email templates for each trigger
- Build webhook receiver and automation logic:
  - Listen for stage/status changes via Manatal webhook
  - Select appropriate template based on trigger
  - Populate template with candidate data from webhook payload
  - Send via external email service API
- Add opt-out/unsubscribe handling
- Implement email logging and tracking

**Technical Considerations:**
- **Cannot use Manatal's native Outlook integration** (limited to calendar/email sync only)
- Email service integration: Microsoft Graph API (preferred), SendGrid, or AWS SES
- Webhook vs polling: Prefer webhooks for real-time triggers
- Email deliverability and spam prevention
- Personalization tokens in templates
- Error handling (email send failures, retry logic)
- Compliance (GDPR, CAN-SPAM)
- Rate limiting for bulk sends
- Preview/approval workflow (or fully automated?)
- Webhook security (authentication, payload validation)

## Integration Compatibility and Testing

### Existing CRM Integration

⚠️ **CRITICAL:** The project must maintain compatibility with existing CRM → Manatal API integration.

**Integration Functions:**
1. **Account Sync:** CRM account creation triggers Manatal account creation
2. **Job Sync:** CRM deal creation triggers Manatal job creation
3. **Data Sync:** Information syncs from CRM to Manatal at certain stages

**Testing Requirements:**
- Test all 3 sync functions after any schema changes
- Verify field mappings remain intact after adding/removing fields
- Coordinate with CRM integration developer/owner for testing
- Document any required changes to CRM integration code

**Risk Mitigation:**
- **Before removing fields:** Verify the field is not used by CRM integration
- Test in non-production environment first
- Have rollback plan ready for schema changes
- Establish communication plan with CRM integration stakeholders
- Maintain field mapping documentation (CRM fields ↔ Manatal fields)

**Action Items:**
- Obtain CRM integration documentation or code repository
- Identify contact: CRM integration developer/owner
- Request access to CRM test environment for integration testing
- Document which Manatal fields are currently used by CRM integration

## Implementation Approach

### Phase A: Prerequisites & Discovery
1. **Access Setup**
   - Obtain Manatal admin/API credentials
   - Review Manatal API documentation
   - Identify Manatal's custom field capabilities

2. **Data Analysis**
   - Review Sprout Creation.xlsx for field specifications
   - Review project charter PDF for additional requirements
   - Document current Manatal configuration
   - Identify all integration points

### Phase B: Core Enhancements (Priority 1-3)
Sequential implementation:
1. UI Simplification (foundational, affects all other work)
2. Job Description Structure (independent, can be parallel with #3)
3. Job Offer Breakdown (required for #4)

### Phase C: Automation (Priority 4-6)
Can be implemented in parallel after Phase B:
1. Document Generation (depends on #1 and #3)
2. Duplicate Prevention (independent)
3. Email Automation (independent)

## Technology Stack

**Primary Approach:** Manatal native features (custom fields, workflows, webhooks)
- Admin access with API credentials available
- Prefer configuration over custom development
- API available as fallback for advanced automation needs

**Implementation Strategy:**
1. **UI Changes (#1, #2, #3):** Use Manatal's custom fields
2. **Allowance Configuration (#3):** JSON/YAML reference file or Manatal custom field configuration
   - Must be maintainable without code changes
   - Consider simple file-based config (allowances.json) or Manatal custom settings
3. **Document Generation (#4):** Manatal API + document generation library
   - Template format: Word .docx and/or PDF (templates provided by TA)
   - Python libraries: python-docx, docx-template, or jinja2 + pdfkit
   - Trigger: Manatal webhook or manual action
4. **Duplicate Prevention (#5):** Manatal API + validation logic
   - Pre-insert validation using Manatal API
   - Email normalization and fuzzy matching
5. **Email Automation (#6):** Manatal webhook + external email service
   - **Email Service Options:**
     - **Microsoft Graph API** (recommended - maintains Outlook ecosystem)
     - SendGrid (dedicated email service)
     - AWS SES (cost-effective for high volume)
   - Architecture: Manatal webhook → Email Service API
6. **CRM Integration Compatibility:** Monitor and test existing CRM → Manatal integration during all schema changes

## Success Criteria

1. **UI Simplification:** TA team reports cleaner interface, faster data entry
2. **Job Description:** Job postings have structured, readable sections
3. **Job Offer:** Offers display itemized compensation with correct allowances
4. **Document Generation:** Job offers and contracts generated automatically with 100% accuracy
5. **Duplicate Prevention:** Zero duplicate records created post-implementation
6. **Email Automation:** Candidates receive timely, accurate emails at each stage

## Risks & Assumptions

**Risks:**
- Manatal API limitations may restrict customization
- Data migration for existing records may be complex
- **CRM integration compatibility:** Schema changes may break existing CRM → Manatal integration (3 sync functions)
- **Document template availability:** Enhancement #4 blocked until TA team provides templates
- **Email service integration:** Additional external service required (Manatal's Outlook integration insufficient for automation)
- Template legal approval may delay document generation
- Email service costs (if using SendGrid/SES) may require budget approval
- Manager tickbox and Contractor dropdown fields may have different names than expected

**Assumptions:**
- Have or can obtain Manatal admin/API access
- Manatal supports custom fields and automation
- TA team can provide test data and validation
- **TA team will provide document templates** in compatible format (Word .docx or PDF)
- **CRM integration developer/owner available** for coordination and testing
- **Budget available** for external email service (if using SendGrid/SES)
- Legal/compliance team will review document templates
- Test/sandbox environment available for integration testing
- Manager role and Contractor status fields exist in Manatal schema

## Implementation Roadmap

### Sprint 1: Foundation (Weeks 1-2)
- Document current Manatal configuration
- Review Sprout Creation.xlsx field specifications
- **Identify Manager tickbox field name** in Manatal schema (get API name/ID)
- **Identify Contractor dropdown field name and values** in Manatal
- **Identify fields used by existing CRM integration** (do not remove these)
- **Request document templates from TA team** (job offer, employment contract)
- **Request CRM integration documentation/contact** (developer/owner info)
- Set up API access and testing workspace
- Identify existing CRM → Manatal integration test environment

### Sprint 2: UI & Data Structure (Weeks 3-4)
- **Enhancement #1:** Remove unused fields, add Hire Name section
- **Enhancement #2:** Add 4 separate job description fields
- Test with sample job postings

### Sprint 3: Compensation Structure (Weeks 5-6)
- **Enhancement #3:** Implement compensation breakdown
  - **Create allowance reference system** (JSON/YAML config or Manatal custom fields)
    - Rice Allowance: 2000 (configurable)
    - Clothing Allowance: 550 (configurable)
    - Laundry Allowance: 300 (configurable)
  - Add allowance fields to Manatal job offer schema
  - Implement role-based logic using Manager tickbox field
  - Handle contractor exceptions using employment type dropdown
  - Create calculated field for total compensation
- Test with various role types (Employee, Manager, Contractor)
- **Test CRM integration compatibility** after adding allowance fields
  - Verify all 3 sync functions still work
  - Coordinate with CRM integration developer

### Sprint 4: Automation - Documents (Weeks 7-8)
- **Enhancement #4:** Document generation
  - ⚠️ **BLOCKED until templates received from TA team**
  - Review and validate templates (job offer, employment contract)
  - Set up document generation library (python-docx, docx-template, etc.)
  - Build generation script using Manatal API
    - Map Manatal fields to template placeholders
    - Handle conditional sections (Manager, Contractor)
  - Integrate with Manatal workflow (webhook or manual trigger)
  - Store generated documents (Manatal attachments or external storage)
- **Alternative:** If templates delayed, proceed with Sprint 5 work first

### Sprint 5: Automation - Quality & Email (Weeks 9-10)
- **Enhancement #5:** Duplicate prevention logic
  - Implement pre-insert validation using Manatal API
  - Add email normalization and fuzzy matching
  - Test duplicate detection and blocking
- **Enhancement #6:** Email automation setup
  - **Select and configure external email service:**
    - Microsoft Graph API (recommended), SendGrid, or AWS SES
    - Set up account and obtain API credentials
  - Configure Manatal webhooks for stage/status change events
  - **Implement webhook receiver** (server-side endpoint)
  - Define stage/status triggers and email template mappings
  - Create email templates for each trigger
  - **Build automation logic:** Webhook → Email Service API
  - Test automated sending for all triggers
  - Implement error handling and retry logic

### Sprint 6: Testing & Rollout (Weeks 11-12)
- End-to-end testing with TA team
- **Comprehensive CRM integration testing:**
  - Test Account Sync (CRM → Manatal account creation)
  - Test Job Sync (CRM deal → Manatal job creation)
  - Test Data Sync (information syncing at certain stages)
  - Verify all field mappings remain intact
- **CRM integration stakeholder sign-off**
- Document changes for TA team
  - User guide for new features
  - Allowance reference file maintenance process
  - Email template management
- Production rollout and monitoring
- Post-deployment CRM integration validation

## Prerequisites and Dependencies

### Information Needed Before Starting

#### 1. Manager Field Identification
- Exact field name for Manager tickbox in Manatal
- Field API name/ID for programmatic access
- Field location in Manatal UI (for documentation)

#### 2. Contractor Field Identification
- Exact field name for employment type dropdown
- Complete list of dropdown values
- Which value(s) indicate "Contractor" status
- Field API name/ID for programmatic access

#### 3. CRM Integration Details
- Integration documentation or code repository
- Contact: CRM integration developer/owner name and contact info
- Field mapping documentation (CRM fields → Manatal fields)
- List of Manatal fields currently used by CRM integration
- Test environment availability for integration testing
- Deployment/update process for CRM integration changes

#### 4. Document Templates
- Job offer letter template from TA team (format: Word .docx or PDF)
- Employment contract template from TA team (format: Word .docx or PDF)
- Template variable/placeholder documentation
  - Which Manatal fields should populate which template placeholders
  - Conditional sections (Manager-only, Contractor-specific, etc.)
- Legal/compliance approval status

### External Service Setup

#### 1. Email Service (select one option)

**Option A: Microsoft Graph API** (recommended - maintains Outlook ecosystem)
- Requires: Azure app registration
- Requires: Microsoft 365 admin access for app permissions
- Cost: Included with Microsoft 365 subscription
- Setup time: ~2-3 hours

**Option B: SendGrid** (dedicated email service)
- Requires: SendGrid account creation
- Requires: API key generation
- Cost: Free tier (100 emails/day) or paid plans
- Setup time: ~1 hour

**Option C: AWS SES** (cost-effective for high volume)
- Requires: AWS account
- Requires: SES configuration and verification
- Cost: $0.10 per 1000 emails
- Setup time: ~2-3 hours

#### 2. Development Environment
- Manatal API access (admin credentials)
- Manatal API documentation access
- Test/sandbox Manatal instance (if available)
- CRM test environment for integration testing
- Webhook receiver hosting environment (server/cloud function)

### Timeline Dependencies

**Sprint 1 Blockers:**
- Access to Manatal admin account (to identify field names)
- Access to CRM integration documentation or developer

**Sprint 4 Blockers:**
- Document templates from TA team (Enhancement #4 cannot start without these)
- If templates delayed → proceed with Sprint 5 work first

**Sprint 5 Blockers:**
- Email service selection and account setup
- Webhook receiver hosting environment setup

**Sprint 6 Blockers:**
- CRM integration developer availability for testing
- Access to CRM test environment

## Clarifications Received

### 1. Compensation Allowance Maintenance
**Answer:** Default amounts are 2000 (Rice), 550 (Clothing), 300 (Laundry). Should maintain a reference file to add/update/remove allowances as necessary.

**Action Items:**
- Create allowance reference system (JSON/YAML config or Manatal custom field configuration)
- Design for maintainability without code changes
- Consider versioning/audit trail for allowance changes

### 2. Email Service Integration
**Answer:** Manatal has direct Outlook integration, but it's limited to calendar and email syncing only.

**Action Items:**
- Cannot use Manatal's native Outlook integration for automation
- Must integrate external email service (Microsoft Graph API, SendGrid, or AWS SES)
- Implement Manatal webhook → Email Service API architecture

### 3. Document Templates
**Answer:** To be provided by TA team.

**Action Items:**
- Request templates from TA team (job offer letter, employment contract)
- Plan for format flexibility (Word .docx, PDF, or both)
- Enhancement #4 blocked until templates received

### 4. Manager Role Definition
**Answer:** Defined by a tickbox/checkbox field in Manatal.

**Action Items:**
- Identify exact Manager tickbox field name in Manatal schema
- Obtain field API name/ID for programmatic access
- Implement simple boolean logic for Manager-only allowances

### 5. Contractor Identification
**Answer:** Defined by a dropdown field in Manatal.

**Action Items:**
- Identify employment type dropdown field name
- Document complete list of dropdown values
- Determine which value indicates "Contractor" status

### 6. Existing Basic Integrations
**Answer:** CRM → Manatal API integration with 3 functions:
- Account creation sync (CRM → Manatal)
- Deal → Job creation sync (CRM deals become Manatal jobs)
- Information syncing from CRM to Manatal at certain stages

**Action Items:**
- **CRITICAL:** Identify fields used by CRM integration (do not remove)
- Test all 3 sync functions after any schema changes
- Obtain CRM integration documentation/code repository
- Coordinate with CRM integration developer/owner
- Establish test environment for integration testing
