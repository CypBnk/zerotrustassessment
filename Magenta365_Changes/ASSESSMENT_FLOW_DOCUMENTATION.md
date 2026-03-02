# Zero Trust Assessment - Flow and Customization Guide

## 📋 Overview

This document explains how the Zero Trust Assessment works, which files are involved in each stage, and where you can add your corporate branding/identity.

---

## 🔄 Assessment Flow

### Stage 1: Entry Point

**File:** `src/powershell/public/Invoke-ZtAssessment.ps1`

**What happens:**

- Main command that orchestrates the entire assessment
- Displays banner and processes command-line parameters
- Loads configuration file if specified
- Validates preview pillar requirements

**Key functions:**

- `Show-ZtiBanner()` - Displays the ASCII banner at startup
- Configuration loading from JSON files

---

### Stage 2: Data Collection

**Files:**

- `src/powershell/private/export/Export-ZtTenantData.ps1`
- `src/powershell/private/export/Export-TenantData.ps1`

**What happens:**

- Connects to Microsoft Graph API
- Exports tenant data including:
  - Users and groups
  - Devices (managed/unmanaged)
  - Applications
  - Sign-in logs
  - Conditional Access policies
  - Security settings

---

### Stage 3: Test Execution

**Files:**

- `src/powershell/tests/Test-Assessment.*.ps1` (individual test files)
- `src/powershell/classes/ZtTest.ps1` (test framework)

**What happens:**

- Runs individual security tests against collected data
- Each test evaluates specific Zero Trust principles
- Tests are organized by pillars: Identity, Devices, Data, Network
- Results are stored with Pass/Fail status and detailed markdown

**Example test files:**

- `Test-Assessment.21770.ps1` - Checks specific identity controls
- `Test-Assessment.24823.ps1` - Validates Company Portal branding
- etc.

---

### Stage 4: Tenant Info Aggregation

**Files:**

- `src/powershell/private/tenantinfo/devices/Add-ZtDeviceOverview.ps1`
- Other tenant info collectors

**What happens:**

- Aggregates statistics and overview data
- Calculates summaries like:
  - Total users, guests, groups
  - Device counts (corporate vs personal)
  - Compliance statistics
  - Desktop device summaries

---

### Stage 5: JSON Report Generation

**Files:**

- `src/powershell/public/Invoke-ZtAssessment.ps1` (lines 400-420)

**What happens:**

- Combines all test results and tenant info into JSON
- Creates `ZeroTrustAssessmentReport.json`
- JSON structure includes:
  - ExecutedAt, TenantId, TenantName, Domain
  - Account (who ran the assessment)
  - TestResultSummary (passed/total counts)
  - Tests array (all test results)
  - TenantInfo (overview data)

---

### Stage 6: HTML Report Generation

**Files:**

- `src/powershell/private/core/Get-HtmlReport.ps1`
- `src/powershell/assets/ReportTemplate.html` (pre-compiled React app)

**What happens:**

- Loads the HTML template
- Injects the JSON data into the template
- Creates `ZeroTrustAssessmentReport.html`
- Opens the report in default browser

**Technical details:**

```powershell
# The HTML template is a single-file React application
# JSON is injected between markers:
$startMarker = 'reportData={'
$endMarker = 'EndOfJson:"EndOfJson"}'
```

---

## 🎨 Customization: Adding Corporate Branding

### 🔷 1. Application Name and Links

**File:** `src/report/src/config/app.ts`

```typescript
export const ztAppConfig: AppConfig = {
  name: "Zero Trust Assessment", // ← Change this to your company name
  github: {
    title: "GitHub",
    url: "https://github.com/microsoft/zerotrustassessment", // ← Update URL
  },
};
```

**Impact:** Changes the name displayed in header, footer, and logo area

---

### 🔷 2. Logo

**File:** `src/report/src/components/icons.tsx`

**Current logo:** Microsoft logo (4-color squares)

```tsx
export const Icons = {
  logo: (props: IconProps) => (
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 18 18" {...props}>
      <path
        fill-rule="evenodd"
        clip-rule="evenodd"
        fill="#F35123"
        d="M0 0h7v7h-7z"
      />
      <path
        fill-rule="evenodd"
        clip-rule="evenodd"
        fill="#01A4EF"
        d="M0 9h7v7h-7z"
      />
      <path
        fill-rule="evenodd"
        clip-rule="evenodd"
        fill="#7FBA00"
        d="M9 0h7v7h-7z"
      />
      <path
        fill-rule="evenodd"
        clip-rule="evenodd"
        fill="#FFB901"
        d="M9 9h7v7h-7z"
      />
    </svg>
  ),
  // ... other icons
};
```

**How to customize:**

1. Replace the SVG code with your company logo SVG
2. Maintain the same viewBox dimensions or adjust as needed
3. Keep the `(props: IconProps)` parameter for proper styling

**Used in:**

- `src/report/src/components/logo.tsx`
- `src/report/src/components/layouts/Header.tsx`
- `src/report/src/components/layouts/Footer.tsx`

---

### 🔷 3. Header Component

**File:** `src/report/src/components/layouts/Header.tsx`

**Displays:**

- Logo and application name
- Navigation menu
- Tenant information dropdown
- Assessment details

**Customization opportunities:**

- Add company-specific navigation items
- Modify menu structure (`mainMenu` from `src/report/src/config/menu.ts`)
- Add custom branding elements

---

### 🔷 4. Footer Component

**File:** `src/report/src/components/layouts/Footer.tsx`

**Displays:**

- Application information
- Assessment metadata
- Links to documentation
- Copyright/legal information

**Current structure:**

```tsx
<div className="flex items-center space-x-2">
  <Icons.logo className="h-6 w-6" />
  <span className="font-semibold text-foreground">Zero Trust Assessment</span>
</div>
```

**Customization:**

- Add company contact information
- Add legal disclaimers
- Add company-specific links

---

### 🔷 5. Dashboard Page

**File:** `src/report/src/pages/Dashboard.tsx`

**What it displays:**

- Tenant overview cards
- Test result summaries
- Device statistics (charts)
- Compliance visualizations

**Customization opportunities:**

- Add company-specific KPIs
- Modify chart colors to match brand
- Add custom widgets or cards

---

### 🔷 6. Styling and Theme

**Files:**

- `src/report/src/App.css` - Global styles
- `src/report/tailwind.config.js` - Tailwind configuration
- Component-level styles using Tailwind classes

**Brand colors:** Modify in tailwind config or CSS variables

---

### 🔷 7. Assessment Banner (PowerShell)

**File:** `src/powershell/public/Invoke-ZtAssessment.ps1`

**Function:** `Show-ZtiBanner()`

```powershell
function Show-ZtiBanner {
    $banner = @"
╔═════════════════════════════════════════════════════════════════════════════╗
║                    🛡️  Microsoft Zero Trust Assessment v2                   ║
║                                                                             ║
║    Comprehensive security posture evaluation for your Microsoft 365 tenant  ║
╚═════════════════════════════════════════════════════════════════════════════╝
"@
    Write-Host $banner -ForegroundColor Cyan
}
```

**Customization:** Change banner text to include your company name

---

## 📁 File Structure Summary

```
src/
├── powershell/                          # PowerShell module
│   ├── public/
│   │   └── Invoke-ZtAssessment.ps1     # ★ MAIN ENTRY POINT
│   ├── private/
│   │   ├── core/
│   │   │   └── Get-HtmlReport.ps1      # ★ HTML GENERATION
│   │   ├── export/
│   │   │   └── Export-ZtTenantData.ps1 # Data collection
│   │   └── tenantinfo/                 # Tenant statistics
│   ├── tests/
│   │   └── Test-Assessment.*.ps1       # Individual tests
│   ├── classes/
│   │   └── ZtTest.ps1                  # Test framework
│   └── assets/
│       └── ReportTemplate.html         # ★ PRE-COMPILED REACT APP
│
└── report/                              # React application
    └── src/
        ├── config/
        │   ├── app.ts                   # ★ APP NAME & LINKS
        │   ├── menu.ts                  # Navigation menu
        │   └── report-data.ts           # JSON data interface
        ├── components/
        │   ├── icons.tsx                # ★ LOGO DEFINITION
        │   ├── logo.tsx                 # Logo component
        │   └── layouts/
        │       ├── Header.tsx           # ★ HEADER COMPONENT
        │       └── Footer.tsx           # ★ FOOTER COMPONENT
        └── pages/
            ├── Dashboard.tsx            # ★ MAIN DASHBOARD
            ├── Identity.tsx             # Identity pillar page
            ├── Devices.tsx              # Devices pillar page
            └── Data.tsx                 # Data pillar page
```

---

## 🎯 Quick Customization Checklist

To add your corporate identity, modify these files:

- [ ] `src/report/src/config/app.ts` - Change app name and URLs
- [ ] `src/report/src/components/icons.tsx` - Replace logo SVG
- [ ] `src/report/src/components/layouts/Footer.tsx` - Add company info
- [ ] `src/powershell/public/Invoke-ZtAssessment.ps1` - Update banner
- [ ] `src/report/tailwind.config.js` - Adjust brand colors (optional)

---

## 🔨 Building After Customization

After making changes to the React app:

1. Navigate to the report directory:

   ```powershell
   cd src/report
   ```

2. Install dependencies (first time only):

   ```powershell
   npm install
   ```

3. Build the production app:

   ```powershell
   npm run build
   ```

4. Copy the built HTML to assets:

   ```powershell
   Copy-Item dist/index.html ../../powershell/assets/ReportTemplate.html -Force
   ```

5. Test the assessment:
   ```powershell
   Import-Module .\src\powershell\ZeroTrustAssessment.psd1 -Force
   Invoke-ZtAssessment
   ```

---

## 📝 Notes

- The HTML report is a **single-file application** - all JavaScript, CSS, and data are embedded
- JSON data is injected at runtime by the PowerShell script
- The React app is pre-compiled and stored in `assets/ReportTemplate.html`
- Customization requires rebuilding the React app and updating the template

---

## 🔗 Additional Resources

- **Cobranding Guide:** `src/react/docs/workshop-guidance/cobrandingguide.md`
- **Company Portal Customization Test:** `src/powershell/tests/Test-Assessment.24823.ps1`
- **Demo Report Generator:** `build/demo-report/New-DemoReport.ps1`

---

## ⚠️ Important Security Note

Remember to update the security warning in the report generation to include your company's data handling policies:

**File:** `src/powershell/public/Invoke-ZtAssessment.ps1`
**Function:** `Show-ZtiSecurityWarning()`

```powershell
function Show-ZtiSecurityWarning {
    Write-Host "⚠️ SECURITY REMINDER: The report and export folder contain sensitive tenant information." -ForegroundColor Yellow
    Write-Host "Please delete the export folder and restrict access to the report." -ForegroundColor Yellow
    # Add your company-specific security guidelines here
}
```

---

**Last Updated:** December 5, 2025
**Version:** Based on ZeroTrustAssessment v0.18.0
