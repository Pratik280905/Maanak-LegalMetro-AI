# Maanak-LegalMetro-AI
LegalMetro AI: AI-Powered Compliance Verification System for Packaged Commodities
Smart India Hackathon (SIH) — Problem Statement ID: 26034
Organization: Ministry of Consumer Affairs, Food & Public Distribution
Department: Department of Consumer Affairs (DoCA)
Regulatory Framework: Legal Metrology Act, 2009 & Legal Metrology (Packaged Commodities) Rules, 2011 (as amended)

🏛️ Executive Summary
LegalMetro AI transforms label-compliance verification from a slow, manual, subjective process into an objective, auditable, AI-augmented workflow. It scans packaged commodity labels, extracts mandatory statutory declarations, geometrically verifies numeral font heights against the Second Schedule, validates rules codified under Rule 6, Rule 18-23, and Rule 27, maintains an immutable SHA-256 cryptographic hash-chain audit log, and auto-generates official court-admissible compliance reports in PDF and DOCX formats.

🚀 Key System Capabilities
1. Computer Vision & Bounding Box Localization
Image Preprocessing: CLAHE contrast enhancement, deskewing via Hough transform, reflection & glare filtering.
Principal Display Panel (PDP) Segmentation: Localizes PDP area (
c
m
2
cm 
2
 ) and computes real-world scale factors (
m
m
/
p
x
mm/px).
2. Full Rule 6 Mandatory Declaration Extraction (9 Checks)
Rule 6(1)(a): Name & Address of Manufacturer / Packer / Importer.
Rule 6(1)(f) / Rule 5: Generic or Common Name of Commodity.
Rule 6(1)(c), Rules 18–23: Net Quantity in standard SI metric units (g, kg, ml, l, m, number).
Rule 6(1)(d): Month & Year of Manufacture/Packing (not future-dated).
Rule 6(1)(e), Rule 18: Maximum Retail Price (MRP) with "inclusive of all taxes" qualifier; strictly prohibits open-ended terms ("MRP up to").
Rule 6(1)(b): Consumer Care Cell contact details (telephone/email).
Rule 27: Country of Origin (statutory for imported goods).
Rule 8: Commodity Dimensions (
L
×
W
×
H
L×W×H).
Rule 6(1)(e) read with Rule 18: Unit Sale Price (USP consistency).
3. Second Schedule Font-Size Calibration
Evaluates measured numeral height against statutory tiers:

PDP Area
≤
50
 cm
2
PDP Area≤50 cm 
2
 : 
min
⁡
1.0
 mm
min1.0 mm (
1.5
 mm
1.5 mm blown/moulded)
50
<
Area
≤
100
 cm
2
50<Area≤100 cm 
2
 : 
min
⁡
1.5
 mm
min1.5 mm (
2.0
 mm
2.0 mm blown)
100
<
Area
≤
500
 cm
2
100<Area≤500 cm 
2
 : 
min
⁡
2.0
 mm
min2.0 mm (
4.0
 mm
4.0 mm blown)
500
<
Area
≤
2500
 cm
2
500<Area≤2500 cm 
2
 : 
min
⁡
4.0
 mm
min4.0 mm (
6.0
 mm
6.0 mm blown)
Area
>
2500
 cm
2
Area>2500 cm 
2
 : 
min
⁡
6.0
 mm
min6.0 mm
4. Confidence-Weighted Verdict Logic (Section 12.3)
If minimum confidence 
<
0.75
→
<0.75→ routes to NEEDS_REVIEW officer tier to prevent false non-compliance.
Otherwise: COMPLIANT or NON_COMPLIANT.
Multi-pass evaluation without short-circuiting so all violations are captured simultaneously.
5. Tamper-Evident SHA-256 Hash Chain Audit Trail (Section 9.9 & 13)
Every scan, verdict override, rule update, or login is recorded in an append-only, cryptographic hash chain: 
curr_hash
=
SHA256
(
prev_hash
∣
user_id
∣
action
∣
entity_id
∣
timestamp
∣
details
)
curr_hash=SHA256(prev_hash∣user_id∣action∣entity_id∣timestamp∣details)
Built-in one-click cryptographic chain verification to detect retroactive tampering.
6. Official Evidence-Backed Reports
PDF Report: Formatted with Government of India header, summary metadata table, color-coded verdict banner, detailed declaration breakdown, violation citations, bounding box photographic evidence, digital SHA-256 integrity stamp, and officer sign-off block.
DOCX Report: Fully editable Word document for drafting legal notices.
🛠️ Technology Stack
Layer	Technologies Used
Frontend	React 18, TypeScript, Vite, Tailwind CSS, Lucide Icons, Recharts
Backend	Python 3.13, FastAPI, SQLite / SQLAlchemy 2.0, Pydantic, Uvicorn
Computer Vision	OpenCV, PIL (Pillow), NumPy
Document Generation	ReportLab (PDF), python-docx (DOCX)
Security & Integrity	Cryptographic SHA-256 Hash Chaining, RBAC
📁 Repository Structure

legalmetro-ai/
├── start_legalmetro.ps1          # One-click startup script for backend & frontend
├── README.md                     # System documentation
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   └── endpoints.py      # REST API router (scans, reports, audit, rules)
│   │   ├── services/
│   │   │   ├── audit.py          # SHA-256 cryptographic hash-chain ledger
│   │   │   ├── cv_pipeline.py    # CLAHE, deskewing, scale calibration, bounding boxes
│   │   │   ├── ocr_ner.py        # 9 declaration extraction & classification
│   │   │   ├── report_generator.py # PDF (ReportLab) & DOCX reports
│   │   │   └── rules_engine.py   # Codified Rules 2011 & Second Schedule font rules
│   │   ├── database.py           # SQLAlchemy database configuration
│   │   ├── models.py             # 9 database tables matching Section 9 schema
│   │   ├── schemas.py            # Pydantic schemas
│   │   └── main.py               # FastAPI application entry point
│   ├── tests/
│   │   ├── test_rules_engine.py  # Unit tests for Legal Metrology Rules & Second Schedule
│   │   ├── test_audit_chain.py   # Cryptographic integrity and tamper tests
│   │   └── test_api.py           # REST API endpoints test suite
│   ├── sample_data/              # Generated FMCG test label packages
│   ├── uploads/                  # Raw and annotated scan images
│   ├── reports/                  # Pre-generated PDF & DOCX reports
│   ├── requirements.txt
│   └── seed_data.py              # Database seeder with sample scans & audit trail
└── frontend/
    ├── src/
    │   ├── components/
    │   │   └── Header.tsx        # Department banner, RBAC switcher, tab navigation
    │   ├── pages/
    │   │   ├── ScanStudio.tsx    # Live scan canvas, sample packs, report downloads
    │   │   ├── Analytics.tsx     # National KPIs, violation charts, repeat-offender matrix
    │   │   ├── Repository.tsx    # Faceted search, detail modal, officer override
    │   │   ├── RuleEditor.tsx    # Admin interface for versioned rules
    │   │   └── AuditLedger.tsx   # Visual cryptographic ledger & chain verification
    │   ├── types.ts              # TypeScript interfaces
    │   ├── App.tsx               # Main application container
    │   └── index.css             # Tailwind styling
    ├── package.json
    └── vite.config.ts
🏁 Quickstart Guide
1. Launch Backend API
powershell

cd C:\Users\Priyanka\.gemini\antigravity\scratch\legalmetro-ai\backend
python -m uvicorn app.main:app --port 8000 --reload
API Swagger UI: http://localhost:8000/docs
Health Status: http://localhost:8000/
2. Launch Frontend UI
powershell

cd C:\Users\Priyanka\.gemini\antigravity\scratch\legalmetro-ai\frontend
npm run dev
Web Application: http://localhost:5173
3. Run Automated Tests
powershell

cd C:\Users\Priyanka\.gemini\antigravity\scratch\legalmetro-ai\backend
python -m unittest discover tests
🧪 Demo Scenarios Pre-Configured
Compliant FMCG Pack (NutriBake Biscuits):
All 9 declarations present, valid font size (2.5mm vs 2.0mm required), compliant taxes formatting.
Verdict: COMPLIANT
Multi-Violation Pack (SilkGlow Shampoo):
Undersized Net Qty font (0.8mm vs 2.0mm required under Second Schedule), missing "inclusive of all taxes" qualifier on MRP, missing consumer care telephone/email.
Verdict: NON_COMPLIANT (3 violations cited)
Needs Review Pack (Sunrise Orange Juice):
Blurry OCR on manufacturing date & address (confidence 
<
0.75
<0.75).
Verdict: NEEDS_REVIEW (routes to Legal Metrology Officer queue)
Imported Pack (Belgique Chocolate):
Imported product missing mandatory Country of Origin declaration under Rule 27.
Verdict: NON_COMPLIANT (Rule 27 violation cited)
