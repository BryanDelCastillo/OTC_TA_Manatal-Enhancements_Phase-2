# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the project documentation directory for **OTC TA Manatal Enhancements Phase 2**. Manatal is a talent acquisition system used for managing candidate recruitment, job offers, and hiring workflows.

## Project Documents

- **Manatal Enhancement Project_Project Charter.pdf** - Complete project charter with detailed specifications
- **notes.txt** - Priority-ordered enhancement requirements from TA team follow-up meeting
- **Sprout Creation.xlsx** - Contains Manatal tab showing UI fields and simplification requirements

## Enhancement Requirements (Priority Order)

### 1. UI Simplification
- Remove unused fields in Manatal
- Reference: "Sprout Creation.xlsx", Manatal tab for field details
- Sub-task: Add hire name section in the Job Offer section

### 2. Job Description Structure Improvements
Create separate fields to improve readability and ease of use:
- Introduction field
- Job Description field
- Job Requirements field
- Benefits field

### 3. Job Offer Breakdown
Restructure job offer to show itemized compensation:
- Base Pay
- Rice Allowance: 2000
- Clothing Allowance: 550
- Laundry Allowance: 300
- Wellbeing (Managers only)
- Transportation (Managers only)
- Exception: Contractor (different structure)

### 4. Document Generation Automation
Automate generation of:
- Job Offer documents
- Employment contracts
- Use available information from Manatal system

### 5. Duplicate Prevention
Improve existing automation to prevent creation of duplicate records in the system.

### 6. Email Automation
Implement stage/status-based email automation:
- Auto-send emails to candidates
- Trigger based on specific stage/status changes
- Use templated email content

## Development Context

When implementing these enhancements:
- This is Phase 2 of the Manatal enhancement project
- Enhancements target the Talent Acquisition workflow
- Changes should maintain data integrity and prevent duplicates
- Automation should reduce manual work for TA team
- UI changes should simplify the interface by removing clutter
