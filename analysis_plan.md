# Data Analysis Plan
Demo data analysis plan for the study *From Diet to MASH Fibrosis: How Microbiota and Host Molecular Profiles Interact and Influence Disease Severity* at Zhang's Microbiome Epidemiology Lab. 

**Prepared by:** Adeeba Tak, M.S.  
**For:** Dr. Xiaotao (Rony) Zhang  
**Purpose:** Demonstrate the transformation and integration of diet, microbiome, metabolomic, proteomic, genomic, and clinical (include demographic) data, along with key metadata, into analysis-ready datasets suitable for robust modeling of liver fibrosis severity.

## Step-by-Step Workflow

**0. Data ingestion:** After confirming access and compliance, pull raw files to a read-only landing area and copy to a curated area for validation and editing (raw files must never be edited). Enforce standardized file names. Keep a *provenance.csv* with the relevant information (source owner, extract date, file hash, version, row count, etc). Note systems and owners for each domain (e.g. Bionutrition Core for diet, MASH registry, Microbiome Core for sequencing outputs, etc). Record file formats, encodings, and expected key variables. Include small "tests" (e.g. via SQL) to extract row-level slices to verify columns and observations. 

**1. Define analytical unit and outcomes:** Use patient-visit combinations as the fundamental unit of analysis, consistent with the Mt. Sinai MASH registry design. Define core identifiers: patient_id, visit_id, visit_date, enabling longitudinal tracking while maintaining visit-level granularity. Specify primary outcomes, such as liver fibrosis severity measures like fibrosis stage, liver stiffness measurements, or composite fibrosis indices like SAFE or FIB-4, and secondary outcomes, such as liver enzymes, metabolic parameters, and disease progression markers.

**2. Compile data dictionaries for each domain:** Develop standardized data dictionaries for each domain (diet, microbiome, metabolomics, proteomics, genomics, clinical), cataloging: variable nomenclature & definition, measurement units, acceptable value ranges, preprocessing transformations, and batch or center identifiers. Metadata keys such as center_id, batch_id should be documented too.  
Documentation should be tailored to each data source's complexity and analytical requirements.

**3. Domain-specific raw data cleaning:** High-level cleaning could potentially look like:
- Clinical: Validate physiological ranges and units, identify and flag outliers in laboratory values, handle missing visits, assess data completeness
- Dietary: Flag implausible consumption values (e.g. >5000 or <500 kcal/day), handle seasonal variations, assess questionnaire completeness
- Microbiome (sequencing/profiling): Filter low-abundance taxa/contaminant sequences, assess sequencing depth adequacy, remove failed samples
- Metabolomics/Proteomics: Identify technical outliers, assess missing value patterns, flag samples with excessive missingness (e.g. >50%)
- Genomics data: Quality control variant calls, assess call rates, handle population stratification, remove low-quality variants
- Metadata and quality control: Standardize center and batch identifiers, validate protocol compliance, flag potential confounders, document data provenance

Missing data strategies should be developed based on domain knowledge and team discussions. This step may be concluded with TFLs summarizing the data (per domain: N, missing %, outlier %, etc).


**4. Data integration and alignment:** Merge cleaned datasets using standardized identifiers (patient_id, visit_id, visit_date) with temporal matching criteria (e.g. ±7 days window for visit alignment). Create a master analytical dataset where each row represents one patient-visit with columns spanning all domains. Handle different sampling frequencies. Document join statistics and missing data patterns across domains. Create analysis flags indicating which data types are available for each patient-visit to enable sensitivity analyses and domain-specific modeling approaches.

**5. Feature transformation and standardization:** Transform cleaned data into analysis-ready features for multi-omics integration. Derive composite clinical indices (BMI, FIB-4, SAFE) from validated formulas. Use PCA or factor analysis for dimension reduction. For microbiome data, implement transformations to handle compositional constraints and zero-inflation. Apply transformations to metabolomics/proteomics data followed by robust scaling or batch correction (e.g., ComBat/RUV) when multiple processing batches are identified. Summarize genomic variants into pathway scores or polygenic risk scores. Standardize all continuous features using z-score normalization within each domain to ensure comparable scales. Apply batch effect correction when multiple processing batches are identified to account for bias. Create indicator variables for categorical factors. To prevent leakage, ensure all transformations and batch corrections are fitted on training data only and applied consistently to validation sets.

**6. Data quality validation:**
Conduct quality assessments on the integrated dataset. Verify successful joins and identify any systematic missing patterns across domains. Generate correlation matrices within and between data domains to detect unexpected relationships or potential data corruption. Assess batch effects through visualization (PCA plots colored by batch/center) and statistical tests. If present, confirm that within-fold correction (e.g., ComBat/RUV) reduces separation by batch without degrading signal. Validate transformation effectiveness by examining distribution shapes and identifying remaining outliers. Create data quality reports, flagging any samples or variables that fail quality thresholds for potential exclusion or sensitivity analyses.

**7. Exploratory and descriptive analysis:**
Generate descriptive statistics and visualizations. Create patient flow diagrams showing data availability across visits and domains. Produce summary tables of baseline characteristics stratified by fibrosis severity. Generate correlation heatmaps between domains and principal component analysis (PCA) plots to visualize overall data structure. Create plots to examine outcome distributions across key covariates. Assess temporal patterns in longitudinal data and identify potential confounders or effect modifiers. Generate preliminary univariate associations between each domain and fibrosis outcomes to guide variable selection strategies.

**8. Statistical analysis:** This will be the final step, and perhaps the most extensive one. Begin with a first-pass, sparse model and then informed by prior scholarship in the field (annotated bibliographies), data-driven sample size and variable selection, and expertise from the PI and other RA's, the best modeling strategies will be evaluated. Model interpretation, calibration, and validation will follow. A plan for scaling-up may also be created.