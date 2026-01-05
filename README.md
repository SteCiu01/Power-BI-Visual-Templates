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
| IBCS Style Column Chart | Column Chart formatted in IBCS stye | PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart) |
| IBCS Style Column Chart v2 - DL Compatible | Column Chart formatted in IBCS stye with reduced numbers of measures, easier to implement in DL models | Direct Lake and PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart%20v2) |
| IBCS Style Column Chart Simplified | Column Chart formatted in IBCS stye, but simplified and displaying the % Variance only | PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/tree/main/IBCS%20Style%20Column%20Chart%20Simplified) |

<hr>

### Line Charts

| Template | Description | Compatibility | Link |
| -------- | ----------- | ---- | ---- |
| Variance Lines | Variance lines showing Actual vs. Target, over time. | PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/blob/main/Variance%20Lines/README.md) |
| Variance Lines v2 - DL Compatible | Variance lines showing Actual vs. Target, over time, with reduced numbers of measures, easier to implement in DL models | Direct Lake and PBI Desktop Models | [Link](https://github.com/SteCiu01/Power-BI-Visual-Templates/blob/main/Variance%20Lines%20v2/README.md) |

<hr>

Feel free to reach out if you have ideas for improvements or enhancements.
