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
- Implement role-based field visibility (Manager vs non-Manager)
- Add contractor exception handling
- Create calculated field for total compensation
- Update job offer templates to show breakdown
- Add validation rules (e.g., Manager role triggers additional allowances)

**Technical Considerations:**
- Role detection logic (how is "Manager" determined?)
- Contractor identification and alternative structure
- Currency formatting and calculations
- Integration with payroll or HRIS systems
- Consider making allowance amounts configurable (not hardcoded)

### 4. Document Generation Automation (Priority 4)

**Objective:** Auto-generate job offer letters and employment contracts from Manatal data

**Tasks:**
- Create job offer document template(s)
- Create employment contract template(s)
- Build document generation logic:
  - Map Manatal fields to template placeholders
  - Handle conditional sections (Manager, Contractor, etc.)
  - Generate PDF or Word documents
- Integrate with Manatal (trigger on status change or manual action)
- Store generated documents in Manatal or external system

**Technical Considerations:**
- Document template format (Word, PDF, HTML?)
- Template engine selection (Jinja2, Docx-template, etc.)
- Document storage location (Manatal attachments? SharePoint? Cloud storage?)
- Version control for templates
- E-signature integration (if needed)
- Multi-language support (if applicable)
- Legal review requirements for contract templates

**Dependencies:**
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

**Tasks:**
- Define stage/status triggers and corresponding email templates:
  - Application received → Acknowledgment email
  - Interview scheduled → Interview details email
  - Offer extended → Offer letter email
  - Rejected → Rejection email
  - Hired → Onboarding email
- Create email templates for each trigger
- Build automation logic:
  - Listen for stage/status changes in Manatal
  - Select appropriate template
  - Populate template with candidate data
  - Send via email service
- Add opt-out/unsubscribe handling
- Implement email logging and tracking

**Technical Considerations:**
- Email service integration (SendGrid, Manatal's built-in, SMTP, etc.)
- Webhook or polling for status changes
- Email deliverability and spam prevention
- Personalization tokens in templates
- Error handling (email send failures)
- Compliance (GDPR, CAN-SPAM)
- Rate limiting for bulk sends
- Preview/approval workflow (or fully automated?)

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
1. Use Manatal's custom fields for UI changes (#1, #2, #3)
2. Use Manatal's workflow automation for email triggers (#6)
3. Use Manatal API + lightweight scripts for document generation (#4) and duplicate prevention (#5)
4. Monitor existing basic integrations during schema changes

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
- Existing integrations may break with schema changes
- Template legal approval may delay document generation

**Assumptions:**
- Have or can obtain Manatal admin/API access
- Manatal supports custom fields and automation
- TA team can provide test data and validation
- Legal/compliance team will review document templates

## Implementation Roadmap

### Sprint 1: Foundation (Weeks 1-2)
- Document current Manatal configuration
- Review Sprout Creation.xlsx field specifications
- Identify existing basic integrations and test environments
- Set up API access and testing workspace

### Sprint 2: UI & Data Structure (Weeks 3-4)
- **Enhancement #1:** Remove unused fields, add Hire Name section
- **Enhancement #2:** Add 4 separate job description fields
- Test with sample job postings

### Sprint 3: Compensation Structure (Weeks 5-6)
- **Enhancement #3:** Implement compensation breakdown
  - Add allowance fields
  - Implement role-based logic (Manager detection)
  - Handle contractor exceptions
- Test with various role types

### Sprint 4: Automation - Documents (Weeks 7-8)
- **Enhancement #4:** Document generation
  - Create templates (job offer, contract)
  - Build generation script using Manatal API
  - Integrate with Manatal workflow

### Sprint 5: Automation - Quality & Email (Weeks 9-10)
- **Enhancement #5:** Duplicate prevention logic
- **Enhancement #6:** Email automation setup
  - Configure stage/status triggers
  - Create email templates
  - Test automated sending

### Sprint 6: Testing & Rollout (Weeks 11-12)
- End-to-end testing with TA team
- Address existing integration compatibility
- Document changes for TA team
- Production rollout and monitoring

## Remaining Questions

Need clarification on:
1. Who maintains the compensation allowance amounts (2000, 550, 300)?
2. What email service is Manatal currently using?
3. Are there existing document templates to use as a base?
4. How is the "Manager" role defined in Manatal (job title, department, level)?
5. How are contractors identified (employment type field, tag, custom field)?
6. Which basic integrations exist and need testing?
