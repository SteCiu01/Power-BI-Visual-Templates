# IBCS Integrated Variance Column Chart

Two-layer IBCS analytical column chart that integrates absolute variance (Δ) directly into the value columns while displaying relative variance (Δ%) as a separate lollipop layer.

#### Over Time

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/41b683df-36ac-430b-afa5-7f3922ca854e" />

#### Categories

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/5a491ea5-451b-4ca5-bf95-360782261674" />

### Functionalities

This template allows you to visualize multiple measures within a single chart and seamlessly switch between them. It also supports drill-up and drill-down interactions across the hierarchy defined on the X-axis.

The template can be used for both:

- Time-based analyses, such as Year–Quarter–Month hierarchies
- Categorical analyses, such as Category-Product hierarchies

This makes it suitable for a wide range of analytical scenarios while maintaining a consistent IBCS-style visual design.

### How to use it

**⚙️ Step 1: download the template in pbix**

[📥 Download Here](https://github.com/SteCiu01/Power-BI-Visual-Templates/raw/refs/heads/main/IBCS%20Style%20Variance%20Column%20Chart/Files/IBCS%20Variance%20Column%20Chart.pbix)

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

Before running these DAX Query View codes in steps 3, 4 and 5 make sure the 'MeasuresTable' in the code has the same name as your table where you want to save the measures. 

‼️ This is crucial to make everything work smoothly: if your measures table is called 'Tables for Measures', for example, follow these steps:

- Rename the 'MeasuresTable' of the template you just downloaded into 'Tables for Measures'
- Change the codes below replacing 'MeasuresTable' with 'Tables for Measures'
- Run these codes in your model to add the measures.

Following these steps you avoid the mismatch between the template and your model. 

💡 Pro Tip: at this stage you can rename the table and the fields used in the x-axis in the template (e.g., calendar table and its column names or dim_products and its column names), as they are named in your model. This helps in faster synch later. 

In case you don't rename the x-axis fields, you will just need to substitute the x-axis fields with your fields.

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

Here the Labels Format measures to create:

```
DEFINE    

-- 1. Format Actual

MEASURE 'MeasuresTable'[Actual_Format] = VAR _Order =
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
    -- TRILLIONS UPPER
    AbsValue >= 10000000000000,
        FORMAT (
            _Value / 1000000000000,
            IF (
                IsCurrency,
                "$0""T"";($0""T"")",
                "0""T"";(0""T"")"
            )
        ),    
    -- TRILLIONS LOWER
    AbsValue >= 1000000000000,
        FORMAT (
            _Value / 1000000000000,
            IF (
                IsCurrency,
                "$0.0""T"";($0.0""T"")",
                "0.0""T"";(0.0""T"")"
            )
        ),
    -- BILLIONS UPPER
    AbsValue >= 10000000000,
        FORMAT (
            _Value / 1000000000,
            IF (
                IsCurrency,
                "$0""B"";($0""B"")",
                "0""B"";(0""B"")"
            )
        ),
    -- BILLIONS LOWER
    AbsValue >= 1000000000,
        FORMAT (
            _Value / 1000000000,
            IF (
                IsCurrency,
                "$0.0""B"";($0.0""B"")",
                "0.0""B"";(0.0""B"")"
            )
        ),
    -- MILLIONS UPPER
    AbsValue >= 10000000,
        FORMAT (
            _Value / 1000000,
            IF (
                IsCurrency,
                "$0""M"";($0""M"")",
                "0""M"";(0""M"")"
            )
        ),
    -- MILLIONS LOWER
    AbsValue >= 1000000,
        FORMAT (
            _Value / 1000000,
            IF (
                IsCurrency,
                "$0.0""M"";($0.0""M"")",
                "0.0""M"";(0.0""M"")"
            )
        ),
    -- THOUSANDS UPPER
    AbsValue >= 10000,
        FORMAT (
            _Value / 1000,
            IF (
                IsCurrency,
                "$0""K"";($0""K"")",
                "0""K"";(0""K"")"
            )
        ),
    -- THOUSANDS LOWER
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

-- 2. Format Δ%

MEASURE 'MeasuresTable'[Δ_%_vs_SPLY_Format] = VAR _delta = [Actual] - [SPLY]
VAR _pct =
    DIVIDE(
        _delta,
        [SPLY],
        BLANK()
    )
RETURN
SWITCH(
    TRUE(),
    _delta > 0,
        FORMAT(_pct, "+0.0%; +0.0%; +0.0%"),
    _delta < 0,
        FORMAT(_pct, "-0.0%; -0.0%; -0.0%"),
    ABS(_delta) < 1e-6,
        FORMAT(_pct, "0.0%; 0.0%; 0.0%")
)

-- 3. Format Δ

MEASURE 'MeasuresTable'[Δ_vs_SPLY_Format] = VAR _Order =
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
    -- TRILLIONS UPPER
    AbsValue >= 10000000000000,
        FORMAT (
            _Value / 1000000000000,
            IF (
                IsCurrency,
                "+$0""T"";-$0""T""",
                "+0""T"";-0""T"""
            )
        ),    
    -- TRILLIONS LOWER
    AbsValue >= 1000000000000,
        FORMAT (
            _Value / 1000000000000,
            IF (
                IsCurrency,
                "+$0.0""T"";-$0.0""T""",
                "+0.0""T"";-0.0""T"""
            )
        ),
    -- BILLIONS UPPER
    AbsValue >= 10000000000,
        FORMAT (
            _Value / 1000000000,
            IF (
                IsCurrency,
                "+$0""B"";-$0""B""",
                "+0""B"";-0""B"""
            )
        ),
    -- BILLIONS LOWER
    AbsValue >= 1000000000,
        FORMAT (
            _Value / 1000000000,
            IF (
                IsCurrency,
                "+$0.0""B"";-$0.0""B""",
                "+0.0""B"";-0.0""B"""
            )
        ),
    -- MILLIONS UPPER
    AbsValue >= 10000000,
        FORMAT (
            _Value / 1000000,
            IF (
                IsCurrency,
                "+$0""M"";-$0""M""",
                "+0""M"";-0""M"""
            )
        ),
    -- MILLIONS LOWER
    AbsValue >= 1000000,
        FORMAT (
            _Value / 1000000,
            IF (
                IsCurrency,
                "+$0.0""M"";-$0.0""M""",
                "+0.0""M"";-0.0""M"""
            )
        ),
    -- THOUSANDS UPPER
    AbsValue >= 10000,
        FORMAT (
            _Value / 1000,
            IF (
                IsCurrency,
                "+$0""K"";-$0""K""",
                "+0""K"";-0""K"""
            )
        ),
    -- THOUSANDS LOWER
    AbsValue >= 1000,
        FORMAT (
            _Value / 1000,
            IF (
                IsCurrency,
                "+$0.0""K"";-$0.0""K""",
                "+0.0""K"";-0.0""K"""
            )
        ),
    -- BELOW 1K
    FORMAT (
        _Value,
        IF (
            IsCurrency,
            "+$#,0;-$#,0",
            "+#,0;-#,0"
        )
    )
)

```

Guidelines:
- For Actual and Δ vs SPLY you need to amend the variable ```VAR IsCurrency = _Order IN {0}``` adding all the order numbers referring to measure that need to be formatted with currency.

Single-measure note:

You could set a standard formatting, although I advise you to set it up as above for scalability

**⚙️ Step 5: Create the Automatic measures**

Now you just need to create the measures below without changing them.

```
DEFINE

//////////////////////////////////////////
-- Control Chart Settings
/////////////////////////////////////////


    MEASURE 'MeasuresTable'[IBCS_Column_Chart_Δ-%-grey-line-height] = 0.0000001
    MEASURE 'MeasuresTable'[Δ vs. SPLY] = [Actual] - [SPLY]
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_Secondary-Axis-Max] = 2
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_Secondary-Axis-Min] = -7
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_Max-y-axis-FullChart] = 2
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_Title] = SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Measure]) & "‏‏‎ ‎‏‏‎ ‎‏‏‎ ‎" & "⬛ Actual vs. ⬜ SPLY"
     MEASURE 'MeasuresTable'[IBCS_Column_Chart_Actual_Upper_Bound] = IF(
    [Δ vs. SPLY] < 0,
    MAX([Actual], [SPLY])
)
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_SPLY_Upper_Bound] = IF(
    [Δ vs. SPLY] >= 0,
    MAX([Actual], [SPLY])
)


//////////////////////////////////////////
-- Control Chart Settings Colors
/////////////////////////////////////////
	
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_ColorTransparent] = "#FFFFFF00"

	  MEASURE 'MeasuresTable'[IBCS_Column_Chart_ColorLightBlack] = "#404040"   -- Actual bars (IBCS dark gray)
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_ColorWhite] = "#FFFFFF"   -- Plain white
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_ColorBlack] = "#000000"   -- Plain black (text / axes)
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_ColorRed] = "#FF0000"   -- Bad variance
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_ColorBlueGreen_CB] = "#008E96"   -- Good variance (red-green color blind safe)
    MEASURE 'MeasuresTable'[IBCS_Column_Chart_ColorLightGrey] = "#A6A6A6"   -- Previous year / second stack
   
```

Guidelines:
- Start to create these measures without changing name and/or content.
- They should be populated and ready for the next steps.

**Pro Tip:** Organise all the measures we made until now in this folder structure below

```
📁 SPLY Column Chart
|
├─ 📁 0. Control Chart Settings
│  ├─ IBCS_Column_Chart_Actual_Upper_Bound
│  ├─ IBCS_Column_Chart_Max-y-axis-FullChart
│  ├─ IBCS_Column_Chart_Secondary-Axis-Max
│  ├─ IBCS_Column_Chart_Secondary-Axis-Min
│  ├─ IBCS_Column_Chart_SPLY_Upper_Bound
│  └─ IBCS_Column_Chart_Title
|  └─ IBCS_Column_Chart_Δ-%-grey-line-height
│
├─ 📁 0. Control Chart Settings Labels Format
│  ├─ Actual_Format
│  ├─ Δ_%_vs_SPLY_Format
│  ├─ Δ_vs_SPLY_Format
|
├─ 📁 0. Control Colors
│  ├─ IBCS_Column_Chart_ColorBlack
│  ├─ IBCS_Column_Chart_ColorBlueGreen_CB
│  ├─ IBCS_Column_Chart_ColorLightBlack
│  ├─ IBCS_Column_Chart_ColorLightGrey
│  ├─ IBCS_Column_Chart_ColorRed
│  ├─ IBCS_Column_Chart_ColorTransparent
│  └─ IBCS_Column_Chart_ColorWhite
│
├─ 📁 1. Metrics Automatic
│  ├─ Δ % vs. SPLY
│  └─ Δ vs. SPLY
│
└─ 📁 1. Metrics to Modify
   ├─ Actual
   └─ SPLY
```

**⚙️ Step 6: Bring in the Column Chart**

You need to go to the .pbix file you downloaded at the beginning, and copy and paste the column chart into your report.

All should work correctly. 

However, the colour of the columns and variances might be different depending on your theme. If you want to adjust them based on your theme, you need to manually play with the formatting for the following:
- Columns
- Lines
- Markers
- Error Bars

If you want to keep the IBCS style theme you can download it [at this link](https://github.com/SteCiu01/Power-BI-Visual-Templates/blob/main/IBCS%20Style%20Variance%20Column%20Chart/Files/Theme_IBCS.json) and upload it in your report.

### Final Considerations and limitations

After completing the six steps, your IBCS-style column chart should be fully functional and ready for use across all your measures. 

This template offers maximum flexibility: you can control which measures appear on each report page by either hiding them from the slicer or selecting a single measure using the Measure column of the IBCS-Column-Chart-Measures-Selector as a visual-level filter. This is very important because if you have different topics in one model, you can build only one measure selector in the model and then, in each page you can decide what "allow" the users to see. Also, the measures selector might come handy also for other visuals.

You can also choose between time-based trends or Actual vs. SPLY comparisons for specific categories.

Replicating this template in your own report is quick: depending on the number of measures, it should take no more than 5–10 minutes. Once set up, the template ensures a consistent IBCS-compliant visual design while allowing seamless scalability for additional measures and future analyses.

The only limitations are: 

- The need to fine-tune the colors of the bars and variance indicators depending on your report theme, as color settings cannot be fully automated via the measure-based color logic. This can easily be corrected using the IBCS theme included with the template.

Please let me know in case of bugs and/or improvements, I am very open to have some cooperations.
