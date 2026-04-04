# Power BI Visual Templates

[![Latest Release](https://img.shields.io/badge/version-1.0.0-blue)](https://github.com/SteCiu01/Power-BI-Visual-Templates/releases)

Reusable, parameter-driven visual templates built on native Power BI visuals with visual calculations.

These templates encapsulate business logic at the visual layer, making them easy to copy-paste across reports with minimal setup.

**What's included:** Each template page contains the .pbix file ready to copy-paste and step-by-step setup instructions.

---

## Choosing the Right Template

### Power BI Desktop Models vs. Direct Lake Models

Your semantic model type determines which template version to use:

| Model Type | Best Template Version | Characteristics |
|------------|----------------------|-----------------|
| **Power BI Desktop** | Standard templates | • Maximum flexibility and control<br>• Full measure-based configuration<br>• All model-level settings available |
| **Direct Lake / Fabric** | DL-Compatible templates | • Reduced measure dependencies<br>• Visual-level configuration focus<br>• Stable measure references |

#### Why Two Versions?

**Direct Lake Limitation:** When using Direct Lake models, measure references embedded in visuals can break or fail to synchronize correctly when copying visuals between reports.

**DL-Compatible Templates** are designed with:
- Fewer measure dependencies = fewer broken references to fix
- More visual-level configuration = less measure editing required
- Slightly less configurable than Desktop versions

**Desktop Templates** offer:
- Full control via measures table
- Maximum flexibility through control measures
- Easier configuration (just amend measures)
- Require stable Desktop-authored semantic models

---

## Template Library

### Column Charts

| Template | Description | Compatibility | Link |
| -------- | ----------- | ------------- | ---- |
| **IBCS Multi-tier Column Chart** | Three-layer analytical chart combining base values, absolute variance (Δ), and percentage variance (Δ%) in a single coordinated visual | Desktop | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart) | 
| **IBCS Multi-tier Column Chart - DL Compatible** | Simplified three-layer chart with reduced measure dependencies for Direct Lake stability | Desktop + DL | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart%20DL%20Compatible) | 
| **IBCS Multi-tier Column Chart - Only Δ %** | Two-layer analytical chart focused on relative performance: base values + percentage variance (Δ%) only | Desktop | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart%20Simplified) |
| **IBCS Integrated Variance Column Chart** | Two-layer chart with embedded absolute variance (Δ) integrated into the base layer, plus a separate percentage variance (Δ%) layer | Desktop | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Variance%20Column%20Chart) |
| **IBCS Integrated Variance Column Chart - DL Compatible** | Simplified two-layer integrated variance chart with reduced measure dependencies for Direct Lake stability | Desktop + DL | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Variance%20Column%20Chart%20DL%20Compatible) |

---

### Line Charts

| Template | Description | Compatibility | Link |
| -------- | ----------- | ------------- | ---- |
| **Variance Line Chart** | Analytical line chart combining actual values, reference baseline, and embedded variance indicators in a single visual | Desktop | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/Variance%20Lines) |
| **Variance Line Chart - DL Compatible** | Simplified variance line chart with reduced measure dependencies for Direct Lake stability | Desktop + DL | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/Variance%20Lines%20DL%20Compatible) |

---

### Bar Charts

| Template | Description | Compatibility | Link |
| -------- | ----------- | ------------- | ---- |
| **IBCS Integrated Variance Bar Chart - DL Compatible** | Ranked bar chart combining absolute values with embedded absolute variance (Δ vs SPLY) and a dedicated percentage variance (Δ%) comparison panel | Desktop + DL | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Variance%20Bar%20Chart%20DL%20Compatible) |

---

## Contributing

Have a pattern to add or a correction to suggest? Open an issue or reach out on [LinkedIn](https://www.linkedin.com/in/stefano-ciurlia/)

---

## License

This project is licensed under the MIT License — see the [LICENSE](https://github.com/SteCiu01/Power-BI-Visual-Templates/blob/main/LICENSE) file for details.

---

## About This Repository

These resources have been developed through real-world implementations across various use cases. Each template represents a solved problem that was complex enough to warrant documentation and reuse.

The focus is on **practical, production-ready solutions** rather than theoretical examples — everything here has been used to solve actual business requirements in Power BI / Fabric environment.

---

**Repository Maintained by:** [Stefano Ciurlia](https://github.com/SteCiu01)
