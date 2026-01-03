# IBCS Style Column Chart v2 - Direct Lake Compatible

#### Over Time

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/5f619bec-b9d9-4259-9ca6-8a66ac12b21b" />

#### Categories

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/3a83da0f-4104-47ff-8c8b-beb415b86f5f" />

### Functionalities

This template allows you to visualize multiple measures within a single chart and seamlessly switch between them. It also supports drill-up and drill-down interactions across the hierarchy defined on the X-axis.

The template can be used for both:

- Time-based analyses, such as Year–Quarter–Month hierarchies
- Categorical analyses, such as Category-Product hierarchies

This makes it suitable for a wide range of analytical scenarios while maintaining a consistent IBCS-style visual design.

### How to use it

**⚙️ Step 1: download the template in pbix**

[📥 Download Here](https://github.com/SteCiu01/Power-BI-Visual-Templates/raw/refs/heads/main/IBCS%20Style%20Column%20Chart%20v2/Visuals%20Templates%20IBCS%20Column%20Chart%20-%20v2.pbix)

**⚙️ Step 2: Create the measures selector (parameter) table**

Create a calculated table in your model that will act as a measure selector parameter, using the following DAX code:

```
IBCS-Column-Chart-Measures-Selector =
DATATABLE (
    "Measure", STRING,
    "Order", INTEGER,
    {
        { "Total Sales ($)", 0 },
        { "Total Qty Sold", 1 }
    }
)
```
Guidelines
- Use user-friendly names for the measures
- For example, if your actual measure is called [tot_sales_usd], expose it as "Total Sales ($)"
- Create as many rows as the measures you need to use in this visual

Assign an explicit order, following the same concept as field parameters, to control how measures appear in the selector.

Single-measure note:

Even if you only need to visualize a single measure, it is recommended to create this table anyway. This approach ensures future scalability, allowing you to add more measures later without changing the visual logic.

Single-measure example:

```
IBCS-Column-Chart-Measures-Selector =
DATATABLE (
    "Measure", STRING,
    "Order", INTEGER,
    {
        { "Total Sales ($)", 0 }
    }
)
```

**⚙️ Step 3: Create the measures [Actual] and [SPLY] that needs to be amended**

**⚠ IMPORTANT**

Before running these DAX Query View codes in steps 3 and 5 make sure the 'MeasuresTable' in the code has the same name of your table where you want to save the measures. 

‼️ This is crucial to make everything work smoothly: if your measures table is called 'Tables for Measures', for example, follow these steps:

- Rename the 'MeasuresTable' of the template you just downloaded into 'Tables for Measures'
- Change the codes below replacing 'MeasuresTable' with 'Tables for Measures'
- Run these codes in your model to add the measures.

```
DEFINE

// Metrics Measures to modify, see guidelines

    MEASURE 'MeasuresTable'[Actual] = SWITCH(
    TRUE(),
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 0, [Total Sales $],
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 1, [Total Sales Qty]
)
    MEASURE 'MeasuresTable'[SPLY] = SWITCH(
    TRUE(),
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 0, [SPLY Sales $],
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 1, [SPLY Sales Qty]
)
```
Guidelines
- These measures are based on the parameter table we created earlier.
- The structure is set so that when the parameter is used in a slicer, the correct measure is displayed based on the selection.
- In our template we set in the parameter the possibility to switch between Total Sales ($) and Total Qty Sold, therefore for both metrics we need Actual and SPLY.
- Replace the 4 sample measures of the template ([Total Sales $], [Total Sales Qty], [SPLY Sales $], [SPLY Sales Qty]) with your measures that you have referenced in the parameter table.
- Once creates, format Actual and SPLY ad Decimal.

Here you can see the column "Measure" of the parameter table used in the slicer for selecting what measure to display

<img width="380" height="385" alt="image" src="https://github.com/user-attachments/assets/60e5da8b-3c1e-412d-a323-60e191a542cb" />

This slicer will allow you to switch between metrics (in the template example between Total Sales $ and Total Sales Qty), and for each metric see Actual vs. SPLY.

Single-measure note:

Even if you only need to visualize a single measure, it is recommended to create the measures this way anyway. This approach ensures future scalability, allowing you to add more measures later without changing the visual logic.

If you have only one measure you don't need the slicer as the selectedvalue returns your only measure.

Single-measure example

```
DEFINE

// Metrics Measures to modify, see guidelines

    MEASURE 'MeasuresTable'[Actual] = SWITCH(
    TRUE(),
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 0, [Total Sales $]
)
    MEASURE 'MeasuresTable'[SPLY] = SWITCH(
    TRUE(),
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 0, [SPLY Sales $]
)
```

**⚙️ Step 4: Labels Format Measures**

Since you can decide to move from $ to count and vice versa, by switching measures, we need to set up a dynamic formatting for both measures, based on whether the selected measure is a $ or a count.

However, in reports that are consuming models in Direct Lake mode the dynamic formatting creates issues. Therefore we need to create measures to dynamically display values in the Data Labels.

Here the Labels Format measures to create:

```
DEFINE    

-- 1 - Format Actual

MEASURE 'MeasuresTable'[Actual_Format] = 
VAR _Order =
    SELECTEDVALUE ( 'IBCS-Column-Chart-Measures-Selector'[Order] )

VAR _Value =
    [Actual]

VAR AbsValue =
    ABS ( _Value )

VAR IsCurrency =
    _Order IN {0} -- Add all the order numbers referring to measure that need to be formatted with currency

RETURN
SWITCH (
    TRUE(),
    -- TRILLIONS
    AbsValue >= 1000000000000,
        FORMAT (
            _Value / 1000000000000,
            IF (
                IsCurrency,
                "$0.0""T"";($0.0""T"")",
                "0.0""T"";(0.0""T"")"
            )
        ),
    -- BILLIONS
    AbsValue >= 1000000000,
        FORMAT (
            _Value / 1000000000,
            IF (
                IsCurrency,
                "$0.0""B"";($0.0""B"")",
                "0.0""B"";(0.0""B"")"
            )
        ),
    -- MILLIONS
    AbsValue >= 1000000,
        FORMAT (
            _Value / 1000000,
            IF (
                IsCurrency,
                "$0.0""M"";($0.0""M"")",
                "0.0""M"";(0.0""M"")"
            )
        ),
    -- THOUSANDS
    AbsValue >= 1000,
        FORMAT (
            _Value / 1000,
            IF (
                IsCurrency,
                "$0.0""K"";($0.0""K"")",
                "0.0""K"";(0.0""K"")"
            )
        ),
    -- BELOW 1K
    FORMAT (
        _Value,
        IF (
            IsCurrency,
            "$#,0;($#,0)",
            "#,0;(#,0)"
        )
    )
)


-- 2 - Format Δ % vs SPLY

MEASURE 'MeasuresTable'[Δ_%_vs_SPLY_Format] = 
FORMAT(
    DIVIDE(
        ([Actual]-[SPLY]),
        [SPLY],
        BLANK()
    ),
    "0.0%; (0.0%); 0.0%"
)


-- 3 - Format Δ vs SPLY

MEASURE 'MeasuresTable'[Δ_vs_SPLY_Format] = 
VAR _Order =
    SELECTEDVALUE ( 'IBCS-Column-Chart-Measures-Selector'[Order] )

VAR _Value =
    [Actual]-[SPLY]

VAR AbsValue =
    ABS ( _Value )

VAR IsCurrency =
    _Order IN {0} -- Add all the order numbers referring to measure that need to be formatted with currency

RETURN
SWITCH (
    TRUE(),
    -- TRILLIONS
    AbsValue >= 1000000000000,
        FORMAT (
            _Value / 1000000000000,
            IF (
                IsCurrency,
                "$0.0""T"";($0.0""T"")",
                "0.0""T"";(0.0""T"")"
            )
        ),
    -- BILLIONS
    AbsValue >= 1000000000,
        FORMAT (
            _Value / 1000000000,
            IF (
                IsCurrency,
                "$0.0""B"";($0.0""B"")",
                "0.0""B"";(0.0""B"")"
            )
        ),
    -- MILLIONS
    AbsValue >= 1000000,
        FORMAT (
            _Value / 1000000,
            IF (
                IsCurrency,
                "$0.0""M"";($0.0""M"")",
                "0.0""M"";(0.0""M"")"
            )
        ),
    -- THOUSANDS
    AbsValue >= 1000,
        FORMAT (
            _Value / 1000,
            IF (
                IsCurrency,
                "$0.0""K"";($0.0""K"")",
                "0.0""K"";(0.0""K"")"
            )
        ),
    -- BELOW 1K
    FORMAT (
        _Value,
        IF (
            IsCurrency,
            "$#,0;($#,0)",
            "#,0;(#,0)"
        )
    )
)

```

Guidelines:
- For Actual and Δ vs SPLY you need to amend the variable ```VAR IsCurrency = _Order IN {0}``` adding all the order numbers referring to measure that need to be formatted with currency.

Single-measure note:

You could set a standard formatting, although I advise you to set it up as above for scalability

**⚙️ Step 5: Create the Automatic Control Chart Settings measures**

Now you just need to create the measures below without changing them.

```
DEFINE

// Automatic measures that potentially don't need to be amended

MEASURE 'MeasuresTable'[IBCS_Column_Chart_Δ-%-grey-line-height] = 0.0000001 
MEASURE 'MeasuresTable'[IBCS_Column_Chart_Δ-grey-line-height] = -3.5

MEASURE 'MeasuresTable'[IBCS_Column_Chart_Title] = SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Measure]) & "‏‏‎ ‎‏‏‎ ‎‏‏‎ ‎" & "⬛ Actual vs. ⬜ SPLY"
```

Guidelines:
- Start to create these measures without changing name and/or content.
- They should be populated and ready for the next steps.

**Pro Tip:** Organise all the measures we made until now in this folder structure below

```
📁 SPLY Column Chart
|
├─ 📁 0. Control Chart Settings
│  ├─ IBCS_Column_Chart_Title
│  ├─ IBCS_Column_Chart_Δ-%-grey-line-height
│  └─ IBCS_Column_Chart_Δ-grey-line-height
│
├─ 📁 0. Control Chart Settings Labels Format
│  ├─ Actual_Format
│  ├─ Δ_%_vs_SPLY_Format
│  ├─ Δ_vs_SPLY_Format
│
└─ 📁 1. Metrics to Modify
   ├─ Actual
   └─ SPLY
```

**⚙️ Step 6: Bring in the Column Chart and re-sync the measures**

You need to go to the .pbix file you downloaded at the beginning, and copy and paste the column chart into your report.

The measures references in the visual will be now broken as this is what happens in report using Direct Lake.

You need to fix them substituting the broken measures as in the following places:

```
[Actual] -> Column y-axis 
[SPLY] -> Column y-axis 
        
[Actual_Format] -> Visual Format > Data Labels > Actual > Value
[Δ_%_vs_SPLY_Format] -> Visual Format > Data Labels > Δ % Positive and Δ % Negative > Value
[Δ_vs_SPLY_Format] -> Visual Format > Data Labels > Δ Positive and Δ Negative > Value
        
[IBCS_Column_Chart_Δ-%-grey-line-height] -> Line y-axis (hidden) and Visual Format > Error Bars > Δ % Positive > Upper Bound and Δ % Negative > Lower Bound
[IBCS_Column_Chart_Δ-grey-line-height] -> Line y-axis (hidden) and Visual Format > Error Bars > Δ Positive > Upper Bound and Δ Negative > Lower Bound
[IBCS_Column_Chart_Title] -> Visual Format > Title > Text
```
However, the colour of the columns and variances, but also of other charts elements, might be different depending on your theme. If you want to change them, you need to manually play with the formatting.

If you want to keep the IBCS style theme you can download it [at this link](https://github.com/SteCiu01/Power-BI-Visual-Templates/blob/main/IBCS%20Style%20Column%20Chart%20v2/Theme_IBCS.json) and upload it in your report.

Finally you might need to adjust the font size of the data labels, remove their background and remove data labels for SPLY. You can use the template as reference to correct these aspects.

### Final Considerations and limitations

After completing the six steps, your IBCS-style column chart should be fully functional and ready for use across all your measures. 

This template offers maximum flexibility: you can control which measures appear on each report page by either hiding them from the slicer or selecting a single measure using the Measure column of the IBCS-Column-Chart-Measures-Selector as a visual-level filter. This is very important because if you have different topics in one model, you can build only one measure selector in the model and then, in each page you can decide what "allow" the users to see. Also, the measures selector might come handy also for other visuals.

You can also choose between time-based trends or Actual vs. SPLY comparisons for specific categories.

Replicating this template in your own report is quick: depending on the number of measures, it should take no more than 5–10 minutes. Once set up, the template ensures a consistent IBCS-compliant visual design while allowing seamless scalability for additional measures and future analyses.

The only limitations are: 

- The need of re-mapping the measures as they break using Direct Lake.
- The need to fine-tune the colors of the bars and variance indicators depending on your report theme, as color settings cannot be fully automated via the measure-based color logic. This can easily be corrected using the IBCS theme included with the template.
- The need of fine tuning data labels after re-mappinng the measures.

Please let me know in case of bugs and/or improvements, I am very open to have some cooperations.
