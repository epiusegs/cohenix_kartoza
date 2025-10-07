# Kartoza Module Changes by Kera Chandler

**Author:** Kera Chandler (kera.chandler@epiuse.com)  
**Date:** August 21, 2025  
**Commit:** e6c1b4d - "Removal of further Bitemporal related items"

## Overview

This document explains the changes made to create the **Kartoza module** - a comprehensive South African localization for Cohenix ERP (formerly ERPNext). This module adds all the features needed for businesses operating in South Africa to comply with local tax laws, payroll regulations, and statutory reporting requirements.

Think of this as adding a "South African business pack" to the ERP system, making it understand and handle things like:
- How South African taxes work (PAYE, UIF, SDL)
- How to file returns with SARS (South African Revenue Service)
- Workplace injury compensation (COIDA)
- South African VAT rules
- Employee tax certificates (IRP5)

---

## What Was Created

### High-Level Summary

The Kartoza module consists of:
- **90 Python files** - Core business logic and calculations
- **46 JSON files** - Database structure definitions for new forms/documents
- **20 JavaScript files** - User interface enhancements
- **28 New Document Types** (forms) - For managing South African-specific data
- **Custom Fields** - Extensions to existing employee, payroll, and company forms
- **2 Reports** - For statutory submissions
- **Comprehensive Documentation** - Guides for using the system

---

## 1. Core Features Added

### 1.1 South African Payroll System

**What it does:** Calculates employee salaries according to South African tax laws

**Key Components:**

#### Custom Salary Slip Processing (`custom_py/salary_slip.py`)
- **1,395 lines of code** that handle the complex calculation of South African payroll
- Calculates PAYE (Pay As You Earn) tax with proper tax rebates
- Handles medical aid tax credits
- Calculates Employment Tax Incentive (ETI) - a government incentive for hiring young workers
- Processes retirement annuity contributions
- Handles annual bonuses properly for tax purposes
- Ensures working days are calculated according to South African calendar

**Why it matters:** Without this, the system wouldn't know how to calculate South African taxes correctly. Each country has different tax rules, and this makes sure employees are taxed correctly.

#### Custom Payroll Entry (`custom_py/payroll_entry.py`)
- **968 lines of code** for processing monthly payroll runs
- Creates proper bank payment entries for different bank accounts
- Handles company contributions (like pension or medical aid contributions paid by the employer)
- Supports different payroll frequencies (monthly, weekly, etc.)
- Integrates with SARS submissions

**Why it matters:** This automates the monthly payroll process, ensuring all the right deductions and payments are calculated and recorded.

#### Custom Additional Salary (`custom_py/additional_salary.py`)
- **35 lines of code** for handling one-time payments or deductions
- Distinguishes between employee payments and company contributions
- Prevents duplicate entries

**Why it matters:** Sometimes employees receive bonuses, commissions, or one-time deductions. This makes sure these are handled correctly.

---

### 1.2 Tax Compliance & SARS Reporting

**What it does:** Helps companies submit required tax reports to SARS

#### EMP201 Submission (DocType)
This is the monthly tax return that companies must submit to SARS, showing:
- PAYE (employee income tax) collected
- UIF (Unemployment Insurance Fund) contributions
- SDL (Skills Development Levy) paid
- ETI (Employment Tax Incentive) claimed

**Files:**
- `kartoza/doctype/emp201_submission/` (JSON + Python)
- `kartoza/report/emp201_report/` (Report generation)

**Why it matters:** Companies are legally required to submit this monthly. This automates the process and reduces errors.

#### EMP501 Reconciliation (DocType)
This is the bi-annual (twice yearly) reconciliation where companies prove that:
- All the monthly EMP201s they submitted match their actual payroll records
- All employee tax certificates (IRP5s) are accounted for

**Files:**
- `kartoza/doctype/emp501_reconciliation/` (JSON + Python + JavaScript)

**Why it matters:** This is like "closing the books" for payroll taxes. Without proper reconciliation, companies can face penalties.

#### IRP5 Certificate (DocType)
This is the South African equivalent of a W-2 form (US) or P60 (UK) - the annual tax certificate given to employees showing:
- Total earnings for the year
- Tax paid
- Medical aid contributions
- Retirement fund contributions

**Files:**
- `kartoza/doctype/irp5_certificate/` (JSON + Python)
- Supporting child tables for income, deductions, and company contributions

**Why it matters:** Employees need this to file their personal tax returns. It's legally required.

#### Employment Tax Incentive (ETI) System
ETI is a South African government incentive that reduces taxes for companies that hire young workers (18-29 years old).

**New Document Types:**
- **Employee ETI Log** - Tracks which employees qualify for ETI and calculates the monthly benefit
- **ETI Slab** - Defines the tax credit amounts based on employee age and salary
- **ETI Slab Details** - The actual rate tables

**Why it matters:** Companies can save significant money through ETI. This automates the complex qualification and calculation process.

---

### 1.3 COIDA Management (Workplace Injury Compensation)

**What it does:** Manages the Compensation for Occupational Injuries and Diseases Act - South Africa's workplace injury insurance system

#### New Document Types:
1. **COIDA Settings** - Configuration for industry risk rates
2. **COIDA Annual Return** - Yearly submission to the Compensation Fund
3. **Workplace Injury** - Records when an employee gets injured at work
4. **OID Claim** - Occupational Injury/Disease claim submissions
5. **OID Medical Report** - Medical documentation for claims

**Custom JavaScript:**
- `custom_js/coida_annual_return.js` - Makes the annual return form easier to use
- `custom_js/workplace_injury.js` - Streamlines injury recording
- `custom_js/oid_claim.js` - Simplifies claim submission process

**Why it matters:** When employees get injured at work, companies need to report it and process claims. This system ensures compliance and proper documentation.

---

### 1.4 VAT (Value Added Tax) Management

**What it does:** Handles South African VAT, which is similar to sales tax but works differently

#### New Document Types:
1. **South African VAT Settings** - Configure VAT rates (15% standard rate)
2. **VAT201 Return** - Bi-monthly VAT return to SARS
3. **VAT Vendor Type** - Classify suppliers by VAT status
4. **South African VAT Rate** - Historical VAT rate tracking

**Report:**
- **VAT Analysis Report** - Analyzes all transactions for VAT compliance

**Why it matters:** VAT is a complex tax system. Companies must charge the right VAT rate, claim input VAT correctly, and submit regular returns. This automates the process.

---

### 1.5 Employee Management Enhancements

**What it does:** Adds South African-specific employee information and features

#### New Document Types:
1. **Employee Type** - Categorize employees (permanent, contract, etc.) for tax purposes
2. **Employee Payroll Frequency** - Define different payment frequencies
3. **Employee Private Benefit** - Track taxable benefits (company car, housing allowance)
4. **Employee Frequency Detail** - Support for employees paid at different frequencies

#### Custom Fields Added to Employee:
- **ID Number** - South African ID number (required for tax)
- **Employee Type** - For tax calculations
- **Payroll Payable Bank Account** - Which bank account to use for salary payment
- **Hours Per Month** - For hourly workers
- **Special Economic Zone** - For SEZ tax benefits

**Custom JavaScript:**
- `custom_js/employee.js` - Enhances employee form

**Why it matters:** South African labor law and tax law require specific employee information. This ensures all required data is captured.

---

### 1.6 Medical and Tax Credit Management

**What it does:** Calculates medical aid tax credits (tax relief for medical insurance)

#### New Document Types:
1. **Medical Tax Credit Rate** - Government-set monthly credit amounts
2. **Tax Rebates and Medical Tax Credit** - Combines age-based rebates with medical credits
3. **Tax Rebates Rate** - Primary, secondary, and tertiary rebate amounts
4. **Retirement Annuity Slab** - Tax deduction limits for retirement savings

**Why it matters:** South Africa has a unique system where medical insurance gives tax credits (not deductions). This ensures correct tax calculations.

---

## 2. Installation and Configuration

### 2.1 Installation Hooks (`install.py`)

**What it does:** Automatically sets up the system when the module is installed

**Key Functions:**

#### `before_install()`
- Creates a custom "Company Contribution" table (466 lines of definition)
- This table stores employer contributions to things like pension and medical aid

#### `after_install()`
- Calls `make_custom_fields()` to add South African fields to existing forms

#### `make_custom_fields()`
Creates custom fields on existing DocTypes:

**HR Settings:**
- Amount Per Kilometer (for travel reimbursements)

**Payroll Settings:**
- Calculate Annual Taxable Amount Based On (Joining Date vs Payroll Period)
- PAYE Salary Component
- UIF Employee/Employer Salary Components
- SDL Salary Component
- COIDA Salary Component
- Disable ETI Calculation checkbox

**Employee:**
- Payroll Payable Bank Account
- Hours Per Month

**Additional Salary:**
- Is Company Contribution (to separate employer vs employee amounts)

**Salary Structure Assignment:**
- Annual Bonus

**Company:**
- COIDA Registration Number
- VAT Number
- SDL Reference Number
- UIF Reference Number

#### `rename_duplicate_fields()`
Handles migration from older versions where fields might have been prefixed with "custom_"

**Why it matters:** This automates the setup process. Without it, administrators would have to manually create all these fields and configurations.

---

### 2.2 Hooks Configuration (`hooks.py`)

**What it does:** Tells Cohenix ERP how to integrate the Kartoza module

**Key Configurations:**

#### Fixtures
Defines which customizations should be exportable:
- Property setters for Salary Structure Assignment and Salary Slip
- Custom fields for Employee, IRP5 Certificate, Payroll Settings, Company, etc.

#### DocType JavaScript Overrides
Adds custom behavior to existing forms:
- Payroll Entry
- Employee
- Employee Benefit Claim
- Salary Structure

#### DocType Class Overrides
Replaces standard logic with South African logic:
- **Salary Slip** → `CustomSalarySlip` (SA tax calculations)
- **Payroll Entry** → `CustomPayrollEntry` (SA payroll processing)
- **Additional Salary** → `CustomAdditionalSalary` (company contributions)

#### Method Overrides
- Overrides `get_payroll_entry_bank_entries` to handle multiple bank accounts for payroll payments

**Why it matters:** This is the "glue" that integrates all the custom South African logic into the main ERP system.

---

## 3. User Interface Enhancements

### 3.1 Payroll Entry Customization (`custom_js/payroll_entry.js`)

**What it does:** Makes the monthly payroll process easier

**Key Features:**
- **Custom "Make Bank Entry" Button** (188 lines of JavaScript)
  - Groups employees by their bank account
  - Shows which employees have already been paid
  - Allows selective payment processing
  - Tracks company contributions separately

**User Experience:**
1. HR runs monthly payroll
2. System generates salary slips
3. Custom button shows: "Bank A has 50 employees ready to pay"
4. HR can process payments by bank account
5. System tracks what's been paid vs what's pending

**Why it matters:** Companies often use multiple bank accounts. This makes it easy to process payments in batches by account.

---

### 3.2 Employee Benefit Claim Customization (`custom_js/employee_benefit_claim.js`)

**What it does:** Improves the process for claiming employee benefits

**Why it matters:** Makes it easier for employees to claim benefits like medical reimbursements or education allowances.

---

### 3.3 Salary Structure Customization (`custom_js/salary_structure.js`)

**What it does:** Enhances the salary structure template form

**Why it matters:** Salary structures define how salaries are calculated. The customization ensures South African components are properly configured.

---

## 4. Configuration and Setup

### 4.1 Module Configuration (`config/kartoza.py`)

**What it does:** Defines the module's menu structure - 205 lines organizing everything into logical groups

**Menu Structure:**

1. **COIDA Management**
   - COIDA Settings
   - COIDA Annual Return
   - Workplace Injury
   - OID Claim

2. **South African Statutory Reports**
   - EMP201 Submission
   - EMP201 Report

3. **South African Payroll**
   - Employee ETI Log
   - ETI Slab
   - Medical Tax Credit Rate
   - Tax Rebates and Medical Tax Credit
   - Employee Type

4. **Tax Certificates and Reconciliations**
   - EMP501 Reconciliation
   - IRP5 Certificate
   - IT3a Certificate
   - SARS e-Filing Integration

5. **Regulatory Compliance**
   - B-BBEE Certificate
   - Employment Equity Report
   - SETA Report

6. **Bargaining Councils**
   - Bargaining Council
   - Bargaining Council Deduction

7. **South African VAT**
   - South African VAT Settings
   - VAT201 Return
   - VAT Analysis Report
   - VAT Vendor Type

8. **South African Leave Management**
   - South African Holiday
   - Leave Policy Assignment

9. **Setup**
   - Employee Payroll Frequency
   - Employee Private Benefit

**Why it matters:** This creates a user-friendly menu system. Users can easily find the South African features they need.

---

### 4.2 Desktop Configuration (`config/desktop.py`)

**What it does:** Adds a "Kartoza" icon to the ERP desktop

**Why it matters:** Gives users quick access to South African features from the main dashboard.

---

## 5. Documentation Created

### 5.1 README.md (810 lines)
Comprehensive guide covering:
- Installation instructions
- Feature overview
- Module structure
- Configuration steps
- Development guide
- ETI implementation details
- IRP5 certificates
- HRMS integration
- Salary slip calculations

### 5.2 Specialized Documentation Files

#### `docs/comprehensive_documentation.md` (752 lines)
In-depth technical documentation for developers

#### `docs/south_african_payroll_guide.md` (241 lines)
Step-by-step guide for payroll processing

#### `docs/testing_south_african_payroll.md` (720 lines)
Complete testing scenarios and test cases

#### `docs/south_african_vat_guide.md` (196 lines)
Guide for VAT setup and processing

#### `kartoza/docs/` - Module-specific documentation:
- **custom_field_standards.md** (178 lines) - Standards for creating custom fields
- **employment_tax_incentive.md** (162 lines) - Detailed ETI guide
- **field_naming_impact.md** (75 lines) - Naming conventions
- **south_african_tax_guide.md** (121 lines) - Tax calculation guide
- **vat201_integration.md** (130 lines) - VAT201 submission guide

#### IRP5 Integration Documentation:
- **IRP5_TEMPLATE_INTEGRATION_COMPLETE.md** (195 lines) - Complete IRP5 certificate integration guide
- **README_IRP5_PRINT_FORMAT.md** (140 lines) - How to print IRP5 certificates
- **Employee Income Payroll Certificate - IRP5 form.pdf** - Official SARS form template

**Why it matters:** Comprehensive documentation means users and developers can understand and maintain the system without the original developer.

---

## 6. Data Fixtures

### 6.1 Custom Field Fixtures (`fixtures/custom_field.json`)
Pre-defined custom fields that can be imported:
- Employee fields (ID number, employee type, etc.)
- IRP5 certificate fields
- Payroll settings fields
- Company registration numbers

### 6.2 Property Setter Fixtures (`fixtures/property_setter.json`)
Modifications to existing field properties:
- Makes certain fields read-only
- Changes field requirements
- Adjusts field visibility

**Why it matters:** Fixtures allow the module to be easily installed on different systems with consistent configuration.

---

## 7. Supporting Infrastructure

### 7.1 Document Events (`doc_events.py`)
Hooks into document lifecycle events (save, submit, cancel) to:
- Validate South African-specific rules
- Trigger calculations
- Update related documents

### 7.2 Patches (`patches/`)
Database migration scripts to:
- Remove duplicate fields from older versions
- Update existing data to new formats
- Fix historical data issues

**Key Patch:**
- `remove_duplicate_fields.py` - Cleans up fields that were created with "custom_" prefix and renames them properly

---

## 8. Complete List of New Document Types

Here's every new form/document type created:

### Payroll & Tax (10)
1. **EMP201 Submission** - Monthly SARS tax return
2. **EMP501 Reconciliation** - Bi-annual tax reconciliation
3. **IRP5 Certificate** - Employee tax certificate
4. **Employee ETI Log** - ETI eligibility tracking
5. **ETI Slab** - ETI calculation rates
6. **ETI Slab Details** - Detailed ETI rate breakdowns
7. **Medical Tax Credit Rate** - Medical aid tax credit amounts
8. **Tax Rebates and Medical Tax Credit** - Combined rebate/credit settings
9. **Tax Rebates Rate** - Age-based tax rebate amounts
10. **Retirement Annuity Slab** - Retirement contribution limits

### COIDA (4)
11. **COIDA Settings** - COIDA configuration
12. **COIDA Annual Return** - Annual submission to Compensation Fund
13. **COIDA Industry Rate** - Industry risk classification rates
14. **Workplace Injury** - Injury incident recording
15. **OID Claim** - Occupational injury/disease claim
16. **OID Medical Report** - Medical documentation for claims

### VAT (4)
17. **South African VAT Settings** - VAT configuration
18. **VAT201 Return** - VAT return submission
19. **South African VAT Rate** - VAT rate history
20. **VAT Vendor Type** - Supplier VAT classification

### Employee Management (4)
21. **Employee Type** - Employee categorization for tax
22. **Employee Payroll Frequency** - Payment frequency settings
23. **Employee Frequency Detail** - Individual frequency assignments
24. **Employee Private Benefit** - Taxable fringe benefits

### Supporting Tables (4)
25. **EMP501 EMP201 Reference** - Links EMP501 to EMP201s
26. **EMP501 IRP5 Reference** - Links EMP501 to IRP5s
27. **IRP5 Income Detail** - Income breakdown on IRP5
28. **IRP5 Deduction Detail** - Deduction breakdown on IRP5
29. **IRP5 Company Contribution Detail** - Company contribution breakdown on IRP5

---

## 9. Technical Statistics

### Code Volume:
- **Python**: 90 files - Core business logic
- **JavaScript**: 20 files - User interface enhancements
- **JSON**: 46 files - Database structure definitions
- **Markdown**: 14 documentation files

### Lines of Code (Major Files):
- `salary_slip.py`: 1,395 lines - Tax and payroll calculations
- `payroll_entry.py`: 968 lines - Payroll processing logic
- `install.py`: 566 lines - Installation and setup
- `hooks.py`: 236 lines - System integration
- `config/kartoza.py`: 205 lines - Menu and workspace configuration

### Total Files Added: 181 files

---

## 10. What This Means for Users

### For HR and Payroll Staff:
- Automated monthly payroll with correct South African tax calculations
- Easy generation of employee tax certificates (IRP5)
- Streamlined SARS submissions (EMP201, EMP501)
- Proper tracking of workplace injuries and COIDA claims
- Multiple bank account support for salary payments

### For Finance/Accounting:
- Accurate VAT calculations and returns
- Proper handling of company contributions
- Automated statutory reporting
- Integration with general ledger accounts

### For Compliance Officers:
- Built-in SARS compliance
- COIDA management and reporting
- Employment equity tracking
- ETI optimization

### For Employees:
- Accurate salary calculations
- Proper tax deductions
- Annual tax certificates (IRP5)
- Transparent view of deductions and contributions

---

## 11. Key Innovations

### 1. Smart Tax Calculation
The system doesn't just calculate tax - it understands:
- Age-based tax rebates (primary, secondary, tertiary)
- Medical aid tax credits
- Retirement annuity deductions
- ETI benefits
- Taxable fringe benefits

### 2. Multi-Frequency Payroll
Handles employees paid at different frequencies:
- Monthly
- Weekly
- Bi-weekly
- Multiple frequencies in one company

### 3. Intelligent Bank Processing
Automatically groups employees by bank account for efficient payment processing, while tracking:
- Who's been paid
- What's pending
- Company contribution status

### 4. Comprehensive Audit Trail
Every calculation is logged:
- ETI qualification and amounts
- Tax calculations with breakdowns
- Payroll period tracking
- Submission history

---

## 12. Compliance Achievements

This module ensures compliance with:
- **SARS (South African Revenue Service)** - Tax collection agency
- **COIDA (Compensation for Occupational Injuries and Diseases Act)** - Workplace injury compensation
- **Department of Labour** - Employment regulations
- **Companies Act** - Corporate governance
- **B-BBEE (Broad-Based Black Economic Empowerment)** - Economic transformation requirements

---

## Summary

Kera Chandler created a complete, production-ready South African localization module that:

✅ Handles complex South African tax calculations  
✅ Automates SARS submissions  
✅ Manages workplace injuries and COIDA  
✅ Processes VAT correctly  
✅ Generates compliant tax certificates  
✅ Supports multiple payroll scenarios  
✅ Provides comprehensive documentation  
✅ Includes extensive user interface enhancements  
✅ Ensures regulatory compliance  
✅ Offers flexibility for different business needs  

This is not just an add-on - it's a comprehensive solution that makes Cohenix ERP fully functional for South African businesses, handling everything from daily payroll operations to annual statutory submissions.

The module transforms a generic ERP system into a South African business management system that understands local laws, regulations, and business practices.
