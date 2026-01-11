# Power BI Visual Templates

By leveraging visual calculations, it is possible to create reusable, parameter-driven templates built on native visuals. These templates encapsulate business logic directly at the visual layer, allowing them to be easily reused across different reports with only a few simple setup steps.

Below are the links to the dedicated pages for each template I created. On each page, you’ll find the .pbix file containing the template ready to be copied and pasted into your report, along with detailed steps and instructions on how to use it. 

### Power BI Desktop Models vs. Direct Lake Models

Depending on how your semantic model is authored, you should choose the most compatible template:
- Templates for Power BI Desktop–authored models
- Templates for Models authored in Power BI Service / Microsoft Fabric and consumed via Direct Lake

When using Direct Lake–based models, measure references embedded in visuals can break or fail to synchronize correctly. For this reason, Direct Lake–compatible templates are designed with:
- Fewer measures
- Greater reliance on visual-level configuration instead of model-level control measures

This design has two key implications:
- Fewer references to fix when copying the visual into a report connected to a Direct Lake model
- Slightly reduced ease in configurability, compared to Desktop-based templates that can leverage a set of control and settings measures (easier to just amend in the measures table).

Desktop-based templates, on the other hand, offer maximum flexibility and control, as all supporting measures are authored and managed directly in the semantic model

<hr>

### Column Charts

| Template | Description | Compatibility | Link |
| -------- | ----------- | ---- | ---- |
| IBCS Multi-tier Column Chart | Layered IBCS analytical column chart combining absolute values, absolute variance (Δ), and relative variance (Δ%) in a single coordinated visual. | PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart) | 
| IBCS Multi-tier Column Chart - DL Compatible | Direct Lake–optimized IBCS multi-tier analytical column chart combining values, absolute variance (Δ), and percentage variance (Δ%). It uses a reduced measures set. | Direct Lake and PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart%20DL%20Compatible) | 
| IBCS Multi-tier Column Chart - Only Δ % | Simplified IBCS analytical column chart focused on relative performance, combining base values with percentage variance (Δ%) only. | PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart%20Simplified) |
| IBCS Integrated Variance Column Chart | Two-layer IBCS analytical column chart combining absolute values with integrated absolute variance (Δ) and a separate relative variance layer (Δ%). | PBI Desktop Models | Link not yet available |
| IBCS Integrated Variance Column Chart - DL Compatible | Direct Lake–optimized Two-layer IBCS analytical column chart combining absolute values with integrated absolute variance (Δ) and a separate relative variance layer (Δ%). It uses a reduced measures set. | Direct Lake and PBI Desktop Models | Link not yet available |

<hr>

### Line Charts

| Template | Description | Compatibility | Link |
| -------- | ----------- | ---- | ---- |
| Variance Line Chart | Variance Line Chart combining actual values, reference baseline, and embedded variance indicators in a single analytical visual. | PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/Variance%20Lines) |
| Variance Line Chart - DL Compatible | Direct Lake–optimized Variance Line Chart combining actual values, reference baseline, and embedded variance indicators in a single analytical visual. It uses a reduced measures set. | Direct Lake and PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/Variance%20Lines%20DL%20Compatible) |

<hr>

Feel free to reach out if you have ideas for improvements or enhancements.
