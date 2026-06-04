# Pastey Documentation Setup

This directory contains the complete dependability documentation for the Pastey procedural macro crate, including requirements, architectural design, safety analysis (FMEA), and assumptions of use.

## Documentation Structure

```
docs/pastey/docs/
├── BUILD                           # Main dependable element configuration
├── component/                      # Component documentation
│   └── BUILD
├── component_classification.rst   # Component classification analysis
├── design/                         # Architectural design
│   ├── BUILD
│   ├── architectural_design.rst
│   ├── public_api.puml
│   └── static_design.puml
├── requirement/                    # Requirements (TRLC format)
│   ├── BUILD
│   ├── assumed_system_requirements.trlc
│   ├── component_requirements.trlc
│   └── feature_requirements.trlc
└── safety_analysis/                # Safety analysis
    ├── BUILD
    ├── aou.trlc                    # Assumptions of Use (TRLC format)
    ├── failure_modes.trlc          # FMEA failure modes
    └── root_cause.puml             # Root cause analysis (FTA)
```

## TRLC to RST Conversion

All requirements are written in TRLC format (`.trlc` files) and are automatically converted to RST format during the Bazel build process. This conversion is handled by:

- **lobster-trlc** tool (from @lobster repository)
- Bazel rules: `assumed_system_requirements`, `feature_requirements`, `component_requirements`
- FMEA rule: Converts `aou.trlc` and `failure_modes.trlc` to RST

### TRLC Files Converted:

1. **Requirements:**
   - `requirement/assumed_system_requirements.trlc` → RST
   - `requirement/feature_requirements.trlc` → RST
   - `requirement/component_requirements.trlc` → RST

2. **Safety Analysis:**
   - `safety_analysis/aou.trlc` → RST (Assumptions of Use)
   - `safety_analysis/failure_modes.trlc` → RST (FMEA)

The generated RST files use the `.. requirement:definition::` directive from the sphinx-needs extension and are automatically included in the final documentation.

## Building Documentation Locally

### Prerequisites
- Bazel/Bazelisk installed
- All dependencies will be fetched automatically by Bazel

### Build Command
```bash
bazel build //docs/pastey/docs:pastey_doc
```

### View Generated Documentation
After building, the HTML documentation is available at:
```bash
bazel-bin/docs/pastey/docs/pastey_doc/html/index.html
```

Open this file in a web browser to view the complete documentation.

### Verify TRLC Conversion
To verify that TRLC files are being converted to RST:
```bash
# Check generated RST files
find bazel-bin/docs/pastey/docs/pastey_index -name "*.rst"

# Check FMEA control measures (from aou.trlc)
cat bazel-bin/docs/pastey/docs/safety_analysis/pastey_fmea/controlmeasures.inc

# Check FMEA failure modes
cat bazel-bin/docs/pastey/docs/safety_analysis/pastey_fmea/failuremodes.inc
```

## CI/CD Workflows

### 1. Build Documentation (`.github/workflows/build_docs.yml`)
- **Triggers:** Push/PR to main/develop branches with docs changes
- **Purpose:** Validates documentation builds correctly
- **Verifies:**
  - TRLC to RST conversion is working
  - All requirements RST files are generated
  - FMEA control measures and failure modes are generated
  - HTML documentation is complete

### 2. Deploy Documentation (`.github/workflows/deploy_docs.yml`)
- **Triggers:** 
  - Push to main branch
  - Version tags (v*)
  - Manual workflow dispatch
- **Purpose:** Builds and deploys documentation to GitHub Pages
- **Features:**
  - Version management (latest, tagged versions)
  - Uploads documentation archives to GitHub Releases
  - Deploys to GitHub Pages with version selector
  - PR previews (uploaded as artifacts)

### 3. ReadTheDocs Integration (`.readthedocs.yaml`)
- **Purpose:** Automated documentation hosting on ReadTheDocs
- **Configuration:** Uses Bazel to build documentation
- **URL:** Configure in ReadTheDocs dashboard

## GitHub Pages Setup

To enable GitHub Pages deployment:

1. Go to repository Settings → Pages
2. Set Source to "GitHub Actions"
3. The documentation will be automatically deployed on push to main
4. Access at: `https://<org>.github.io/<repo>/latest/`

## Documentation Contents

The generated documentation includes:

1. **Dependable Element Overview**
   - Integrity level (ASIL B)
   - Component classification
   - High-level description

2. **Requirements** (from TRLC)
   - Assumed system requirements
   - Feature requirements
   - Component requirements
   - Full traceability

3. **Architectural Design**
   - Static design views (PlantUML)
   - Public API documentation
   - Component structure

4. **Safety Analysis**
   - FMEA (Failure Mode and Effects Analysis)
   - Root cause analysis (FTA)
   - Control measures
   - Failure modes

5. **Assumptions of Use** (from TRLC)
   - Safety assumptions
   - Usage constraints
   - Environmental assumptions

## Adding New Requirements

To add new requirements:

1. Edit the appropriate `.trlc` file in `requirement/` or `safety_analysis/`
2. Follow the TRLC syntax (see existing files for examples)
3. Build the documentation to verify conversion
4. The requirements will automatically appear in the generated RST/HTML

Example TRLC requirement:
```trlc
PasteyComReq.REQ_COMP_PASTEY_001 "Requirement title" {
    text = """
    The pastey component shall perform the required behavior.
    """
}
```

## Viewing Live Documentation

Once deployed, documentation is available at:
- **Latest:** `https://<org>.github.io/score-crates/latest/`
- **Tagged version:** `https://<org>.github.io/score-crates/v1.0.0/`
- **ReadTheDocs:** `https://score-crates.readthedocs.io/`

## Troubleshooting

### Documentation Build Fails
```bash
# Check Bazel build errors
bazel build //docs/pastey/docs:pastey_doc --verbose_failures

# Verify TRLC syntax
bazel build //docs/pastey/docs/requirement:all
bazel build //docs/pastey/docs/safety_analysis:all
```

### TRLC Conversion Issues
```bash
# Test TRLC conversion directly
bazel build //docs/pastey/docs/requirement:feature_requirements
bazel build //docs/pastey/docs/safety_analysis:aou_trlc
```

### Missing RST Files
Ensure all TRLC files are listed in the appropriate `BUILD` file rules:
- `assumed_system_requirements()` for system requirements
- `feature_requirements()` for feature requirements
- `component_requirements()` for component requirements
- `fmea()` with `controlmeasures` parameter for AOU

## References

- SCORE Tooling: Provides Bazel rules for dependable elements
- Lobster TRLC: Converts TRLC requirements to RST format
- Sphinx: Documentation generator
- sphinx-needs: Requirement management extension for Sphinx
- PlantUML: Diagram generation for architectural designs
