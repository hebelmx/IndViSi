# OCR filter research

Python scripts behind the image preprocessing used before OCR. They find which filter settings
give Tesseract the best reading for an image of a given quality. The coefficients they produced
are hard-coded in `Code/Src/CSharp/01-Core/Domain/Models/PolynomialModelOptions.cs` and used by
`Infrastructure.Imaging`. The C# build does not run these scripts.

Result: a polynomial model with R² above 0.89 for every filter parameter, which cut OCR edit
distance by 18.4% against the baseline. The model beat the cluster lookup table, which
improved by 12.3%.

## Pipeline

1. **Degrade**: `generate_degradation_spectrum*.py` and `degrade_images_for_ocr_testing.py` make
   copies of the sample documents with blur, noise, stains, folds, skew and scan lines.
2. **Measure**: `analyze_image_quality.py`, `image_quality_analyzer.py`, `baseline_ocr_pristine.py`
   and `measure_ocr_quality.py` record image properties and baseline OCR quality.
3. **Cluster**: `cluster_by_image_properties.py`, `determine_image_clusters.py` and
   `cluster_and_fit_quality_model.py` group images by blur, noise and contrast.
4. **Optimize**: `optimize_filters_nsga2*.py`, `optimize_cluster{0,1,2}_{pil,opencv}.py` and
   `ga_*.py` search filter parameters per cluster with genetic algorithms (DEAP).
   `launch_cluster_gas.sh` runs them in parallel.
5. **Fit**: `fit_polynomial_model_v2.py` and `retrain_polynomial_model.py` map image properties
   to filter parameters.
6. **Validate and apply**: `validate_*.py`, `test_all_*.py`, `analyze_pareto_fronts.py` and
   `production_filter_inference.py`.

Read `OCR_FILTER_OPTIMIZATION_RETROSPECTIVE.md` first: it explains why the first single-model
NSGA-II attempt failed and how the per-cluster approach replaced it.
`POLYNOMIAL_FILTER_IMPLEMENTATION_GUIDE.md` describes how the model was ported to C#, and
`MLOPS_WORKFLOW.md` describes how to retrain it.

Older numbered versions (`_v2` to `_v6`) are kept to show how the method evolved.

`demo/` holds a SQL script that clears demo data from the database.
