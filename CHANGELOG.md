# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-04-04

### Added
- **IBCS Multi-tier Column Chart** - Three-layer analytical chart combining base values, absolute variance (Δ), and percentage variance (Δ%) for Desktop models
- **IBCS Multi-tier Column Chart - DL Compatible** - Simplified three-layer chart with reduced measure dependencies for Direct Lake stability
- **IBCS Multi-tier Column Chart - Simplified** - Two-layer chart focused on relative performance (base values + Δ% only)
- **IBCS Integrated Variance Column Chart** - Two-layer chart with embedded absolute variance integrated into base layer, plus Δ% layer
- **IBCS Integrated Variance Column Chart - DL Compatible** - Simplified two-layer integrated variance for Direct Lake models
- **Variance Line Chart** - Analytical line chart combining actual values, reference baseline, and embedded variance indicators
- **Variance Line Chart - DL Compatible** - Simplified variance line chart for Direct Lake models
- **IBCS Integrated Variance Bar Chart - DL Compatible** - Ranked bar chart with absolute values, embedded variance (Δ), and Δ% comparison
- Comprehensive documentation for all templates
- Template selection guide for Power BI Desktop vs. Direct Lake models
- IBCS theme file (Theme_IBCS.json) for consistent styling
- MIT License

### Notes
- All templates include downloadable .pbix files ready for copy-paste implementation
- Parameter-driven design using DAX DATATABLE for measure selectors
- Support for hierarchical drill-down across time and categorical dimensions
- Two template versions available per chart type to optimize for model type
