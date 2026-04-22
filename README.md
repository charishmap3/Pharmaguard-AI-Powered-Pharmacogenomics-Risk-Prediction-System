# PharmaGuard: Pharmacogenomic Risk Prediction System

**A clinical-grade AI-powered web application for pharmacogenomic analysis and personalized drug risk prediction**

##  Project Overview

PharmaGuard analyzes patient genetic data (VCF files) to predict personalized pharmacogenomic risks and provides Claude AI-generated clinical explanations. Built for the RIFT 2026 Hackathon HealthTech track.

### Key Features
- **VCF File Upload** - Drag-and-drop interface for genetic variant data
- **6-Gene Pharmacogenomic Analysis** - CYP2D6, CYP2C19, CYP2C9, SLCO1B1, TPMT, DPYD
- **6-Drug Support** - CODEINE, WARFARIN, CLOPIDOGREL, SIMVASTATIN, AZATHIOPRINE, FLUOROURACIL
- **AI-Generated Explanations** - Claude API integration for clinical insights
- **CPIC Guidelines** - Dosing recommendations from Clinical Pharmacogenetics Implementation Consortium
- **Risk Classification** - Safe | Adjust Dosage | Toxic | Ineffective | Unknown
- **JSON Export** - Download analysis results in required format

## 🚀 Quick Start

### Backend Setup

```bash
# Navigate to project directory
cd PharmaGuard-HealthTech

# Create Python virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Set up environment variables
cp .env.example .env
# Edit .env and add your ANTHROPIC_API_KEY

# Run Flask backend (port 5000)
python -m flask --app backend.app run
```

### Frontend Setup

```bash
# In another terminal, navigate to frontend
cd frontend

# Install dependencies
npm install

# Start development server (port 3000)
npm run dev

# Build for production
npm run build
```

### Testing Locally

1. Open `http://localhost:3000` in your browser
2. Upload `backend/sample_data/sample.vcf`
3. Select drugs (e.g., CODEINE)
4. Click "Analyze"
5. View results and download JSON

##  Technology Stack

### Backend
- **Framework:** Flask 3.0.3
- **Language:** Python 3.9+
- **VCF Parsing:** PyVCF
- **LLM:** Anthropic Claude 3.5 Sonnet API
- **Validation:** jsonschema

### Frontend
- **Framework:** React 18
- **Build Tool:** Vite 5
- **Styling:** Tailwind CSS
- **File Upload:** react-dropzone
- **HTTP Client:** Axios

### Deployment
- **Frontend:** Vercel
- **Backend:** Render.com (Flask)
- **Version Control:** GitHub

## 📚 API Documentation

### Main Analysis Endpoint

**POST `/api/analyze`**

Request:
```json
{
  "vcf_file": "<VCF file content as string>",
  "drugs": ["CODEINE", "WARFARIN"],
  "patient_id": "PATIENT_123" (optional)
}
```

Response (Array of drug analysis results):
```json
[
  {
    "patient_id": "PATIENT_123",
    "drug": "CODEINE",
    "timestamp": "2026-02-19T15:30:00Z",
    "risk_assessment": {
      "risk_label": "Adjust Dosage",
      "confidence_score": 0.92,
      "severity": "moderate"
    },
    "pharmacogenomic_profile": {
      "primary_gene": "CYP2D6",
      "diplotype": "*1/*4",
      "phenotype": "IM",
      "detected_variants": [
        {
          "rsid": "rs1065852",
          "gene": "CYP2D6",
          "consequence": "Loss of function"
        }
      ]
    },
    "clinical_recommendation": {
      "action": "Reduce dose",
      "cpic_guideline": "Consider 25-50% dose reduction",
      "monitoring": "Monitor for pain relief"
    },
    "llm_generated_explanation": {
      "summary": "Patient with CYP2D6 intermediate metabolizer phenotype...",
      "biological_mechanism": "The rs1065852 variant reduces enzyme activity...",
      "variant_effects": {
        "rs1065852": "Loss of function"
      }
    },
    "quality_metrics": {
      "vcf_parsing_success": true,
      "variant_confidence": 0.92,
      "completeness": 1.0
    }
  }
]
```

### Health Check

**GET `/api/health`**

Returns: `{"status": "healthy", "service": "PharmaGuard-HealthTech", "version": "1.0.0"}`

### Supported Drugs

**GET `/api/supported-drugs`**

Returns list of all supported drugs.

### VCF Validation

**POST `/api/validate-vcf`**

Request: `{"vcf_file": "<VCF content>"}`

Validates VCF file format without running full analysis.

##  Project Architecture

```
┌─────────────────────────────────────────────┐
│         React Frontend (Vite)               │
│  - FileUploader Component                   │
│  - DrugSelector Component                   │
│  - ResultsDisplay with Visualization        │
│  - Export to JSON                           │
└────────────┬────────────────────────────────┘
             │ HTTP/JSON
┌────────────▼────────────────────────────────┐
│      Flask Backend REST API                 │
│  (/api/analyze, /api/health, etc.)         │
└────────────┬────────────────────────────────┘
             │
      ┌──────┴──────┬──────────┬──────────┐
      │             │          │          │
┌─────▼──┐  ┌────────▼─┐  ┌────▼────┐  ┌──▼──────┐
│  VCF   │  │ Variant  │  │  Risk   │  │  CPIC   │
│ Parser │  │ Matcher  │  │ Predictor  │Guidelines│
└─────┬──┘  └────┬─────┘  └────┬────┘  └──┬──────┘
      │          │             │          │
      └──────────┼─────────────┼──────────┘
                 │             │
         ┌───────▼─────────────▼──┐
         │ Pharmacogenomic DB    │
         │ (6 genes, variants)   │
         └───────┬─────────────┬──┘
                 │             │
             ┌───▼─┐      ┌────▼────┐
             │Drug │      │ Claude  │
             │Gene │      │   LLM   │
             │Map  │      │  API    │
             └─────┘      └────┬────┘
                              │
                         ┌─────▼─────┐
                         │Explanation│
                         └───────────┘
```

##  Project Structure

```
PharmaGuard-HealthTech/
├── backend/
│   ├── app.py                    # Flask app factory
│   ├── config.py                 # Configuration
│   ├── routes/
│   │   ├── health.py             # Health check endpoint
│   │   └── analysis.py           # Main /api/analyze endpoint
│   ├── services/
│   │   ├── vcf_parser.py         # VCF file parsing
│   │   ├── variant_matcher.py    # Variant matching logic
│   │   ├── risk_predictor.py     # Drug risk prediction
│   │   ├── cpic_recommendations.py # CPIC guideline lookup
│   │   └── llm_explainer.py      # Claude API integration
│   ├── data/
│   │   ├── pharmacogenomic_db.py # Gene variant data (6 genes)
│   │   ├── drug_gene_mapping.py  # Drug-to-gene associations
│   │   └── cpic_guidelines.py    # CPIC dosing rules
│   ├── utils/
│   │   ├── validators.py         # Input validation
│   │   └── json_schema.py        # Output schema validation
│   ├── tests/
│   │   └── test_*.py             # Unit tests
│   └── sample_data/
│       ├── sample.vcf            # Example VCF file
│       └── expected_output.json   # Expected API response
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx               # Main React component
│   │   ├── main.jsx              # Entry point
│   │   ├── components/
│   │   │   ├── FileUploader.jsx   # VCF upload component
│   │   │   ├── DrugSelector.jsx   # Drug selection component
│   │   │   ├── ResultsDisplay.jsx # Results visualization
│   │   │   ├── RiskBadge.jsx      # Color-coded risk badge
│   │   │   ├── VariantTable.jsx   # Variant table display
│   │   │   ├── LLMExplanation.jsx # AI explanation display
│   │   │   └── DownloadButton.jsx # JSON export button
│   │   └── styles/
│   │       ├── App.css            # Main styles
│   │       ├── components.css     # Component styles
│   │       └── index.css          # Global styles
│   ├── public/
│   │   └── index.html            # HTML template
│   ├── vite.config.js            # Vite configuration
│   ├── package.json              # Node dependencies
│   └── .env.example              # Frontend config template
│
├── docs/
│   ├── ARCHITECTURE.md           # Detailed system design
│   ├── API_DOCUMENTATION.md      # Full API specs
│   ├── INSTALLATION.md           # Setup guide
│   └── DEPLOYMENT.md             # Deployment instructions
│
├── .gitignore
├── .env.example                  # Backend config template
├── requirements.txt              # Python dependencies
├── package.json                  # Root package info
├── vercel.json                   # Vercel deployment config
└── README.md                     # This file
```

##  Supported Genes & Drugs

### Genes (6)
| Gene | Function | Drugs |
|------|----------|-------|
| **CYP2D6** | Cytochrome P450 2D6 | CODEINE |
| **CYP2C19** | Cytochrome P450 2C19 | CLOPIDOGREL |
| **CYP2C9** | Cytochrome P450 2C9 | WARFARIN |
| **SLCO1B1** | Organic Anion Transporter 1B1 | SIMVASTATIN |
| **TPMT** | Thiopurine Methyltransferase | AZATHIOPRINE |
| **DPYD** | Dihydropyrimidine Dehydrogenase | FLUOROURACIL |

### Phenotypes
- **PM** - Poor Metabolizer (reduced function)
- **IM** - Intermediate Metabolizer (partial function)
- **NM** - Normal Metabolizer (normal function)
- **RM** - Rapid Metabolizer (increased function)
- **URM** - Ultra-rapid Metabolizer (very rapid clearance)

### Risk Labels
- **Safe** - Standard dosing appropriate
- **Adjust Dosage** - Consider dose modification
- **Toxic** - High adverse effect risk, dose reduction needed
- **Ineffective** - May not achieve therapeutic benefit
- **Unknown** - Insufficient data

## 🔧 Environment Variables

### Backend (.env)
```
FLASK_ENV=development
FLASK_DEBUG=False
SECRET_KEY=your-secret-key-here
ANTHROPIC_API_KEY=sk-ant-xxxxx  # Required for Claude API
FRONTEND_URL=http://localhost:3000
MAX_VCF_FILE_SIZE=5242880  # 5MB
ALLOWED_DRUGS=CODEINE,WARFARIN,CLOPIDOGREL,SIMVASTATIN,AZATHIOPRINE,FLUOROURACIL
```

### Frontend (.env)
```
VITE_API_URL=http://localhost:5000
REACT_APP_API_URL=http://localhost:5000
VITE_APP_NAME=PharmaGuard
```

##  Sample VCF File

```vcf
##fileformat=VCFv4.2
##fileDate=20260213
##reference=GRCh37
#CHROM	POS	ID	REF	ALT	QUAL	FILTER	INFO	FORMAT	SAMPLE1
22	42521919	rs1065852	A	G	60	.	DP=50;Gene=CYP2D6	GT	0/1
10	96741617	rs4244285	G	A	60	.	DP=48;Gene=CYP2C19	GT	0/1
10	98296527	rs1799853	C	T	60	.	DP=45;Gene=CYP2C9	GT	0/1
```

## 🧪 Testing

### Run Backend Tests
```bash
pytest backend/tests/
```

### Test with Sample VCF
```bash
curl -X POST http://localhost:5000/api/analyze \
  -H "Content-Type: application/json" \
  -d @- << 'EOF'
{
  "vcf_file": "$(cat backend/sample_data/sample.vcf)",
  "drugs": ["CODEINE"],
  "patient_id": "PATIENT_TEST"
}
EOF
```

##  Deployment

### Deploy to Vercel (Frontend + Backend)

```bash
# 1. Set up Vercel project
vercel login
vercel

# 2. Set environment variables in Vercel dashboard
#  - ANTHROPIC_API_KEY
#  - FLASK_ENV=production

# 3. Deploy
vercel --prod
```

### Alternative: Separate Backends

**Frontend on Vercel:**
```bash
cd frontend
vercel --prod
```

**Backend on Render.com:**
1. Push repository to GitHub
2. Create new Web Service on Render
3. Configure with `backend.app:app` as start command
4. Set `ANTHROPIC_API_KEY` environment variable
5. Update frontend `VITE_API_URL` to Render backend URL

##  LinkedIn Demo Requirements

Create a 2-5 minute public video demonstrating:
1. Application interface and navigation
2. VCF file upload and drug selection
3. Live analysis execution
4. Results display and risk assessment
5. JSON export functionality
6. AI-generated explanation

Tag: @RIFT
Hashtags: #RIFT2026 #PharmaGuard #Pharmacogenomics #AIinHealthcare

##  Submission Checklist

- [x] Live deployable URL (Vercel/hosting)
- [x] Public GitHub repository
- [x] Complete source code with .env.example
- [x] README.md with setup, API docs, architecture
- [x] Sample VCF files for testing
- [x] requirements.txt and package.json
- [x] VCF file upload functionality
- [x] Drug selection interface
- [x] JSON output matching exact schema
- [x] Risk labels (Safe/Adjust Dosage/Toxic/Ineffective/Unknown)
- [x] Confidence scores and phenotype classification
- [x] Color-coded UI indicators
- [x] Error handling for invalid VCF files
- [ ] LinkedIn demo video (public, with hashtags)
- [ ] Deployment to live URL
- [ ] GitHub push with all code


##  License

MIT License - See LICENSE file for details

##  References

- [VCF Format Specification](https://samtools.github.io/hts-specs/VCFv4.2.pdf)
- [CPIC Guidelines](https://cpicpgx.org/)
- [PharmGKB](https://www.pharmgkb.org/)
- [Anthropic Claude API](https://docs.anthropic.com/)

---


