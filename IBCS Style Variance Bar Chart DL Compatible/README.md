# IBCS Integrated Variance Bar Chart - Direct Lake Compatible

IBCS integrated variance bar chart combining ranked absolute values with embedded absolute variance (Δ vs SPLY) and a dedicated relative variance (Δ%) comparison panel.

#### Visual Preview

<img width="870" height="636" alt="image" src="https://github.com/user-attachments/assets/85f96e67-a0a5-48a3-be89-ad1d5e88faf1" />


### Functionalities

This template allows you to visualize multiple measures within a single bar chart and seamlessly switch between them.

The template can be used for categorical analyses, such as Total Sales ($), Quantity Sold, Number of Orders, etc. by Product Name, Category Name, Country, etc.

This makes it suitable for a wide range of analytical scenarios while maintaining a consistent IBCS-style visual design.

### How to use it

**⚙️ Step 1: download the template in pbix**

[📥 Download Here](https://github.com/SteCiu01/Power-BI-Visual-Templates/raw/refs/heads/main/IBCS%20Style%20Variance%20Bar%20Chart%20DL%20Compatible/Files/Visuals%20Templates%20IBCS%20Bars%20Chart%20-%20DL%20Compatible.pbix)

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

Before running these DAX Query View codes in steps 3, 4 and 5 make sure the 'MeasuresTable' in the code has the same name of your table where you want to save the measures. 

‼️ This is crucial to make everything work smoothly: if your measures table is called 'Tables for Measures', for example, follow these steps:

- Rename the 'MeasuresTable' of the template you just downloaded into 'Tables for Measures'
- Change the codes below replacing 'MeasuresTable' with 'Tables for Measures'
- Run these codes in your model to add the measures.

Following these steps you avoid the mismatch between the template and your model. 

💡 Pro Tip: at this stage you can rename the table and the fields used in the y-axis in the template (e.g., dim_products and its product_name), as they are named in your model. This helps in faster synch later. 

In case you don't rename the y-axis fields, you will just need to substitute the y-axis fields with your fields.

```
DEFINE

///////////////////////////////////////////////
// Metrics to Modify
///////////////////////////////////////////////

MEASURE 'MeasuresTable'[Actual] = 
SWITCH(
    TRUE(),
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 0, [Total Sales $],
    SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Order]) = 1, [Total Sales Qty]
)

MEASURE 'MeasuresTable'[SPLY] = 
SWITCH(
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

///////////////////////////////////////////////
// Metrics to Modify
///////////////////////////////////////////////

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

///////////////////////////////////////////////
// Labels Format
///////////////////////////////////////////////

-- Format Actual
MEASURE 'MeasuresTable'[Actual_Format] = 

VAR _Order = SELECTEDVALUE ( 'IBCS-Column-Chart-Measures-Selector'[Order] )

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

-- Format Δ vs SPLY
MEASURE 'MeasuresTable'[Δ_vs_SPLY_Format] = 

VAR _Order = SELECTEDVALUE ( 'IBCS-Column-Chart-Measures-Selector'[Order] )

VAR _Value =
    [Actual] - [SPLY]

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
                "+$0""T"";-$00""T""",
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

-- Format Δ% vs SPLY
MEASURE 'MeasuresTable'[Δ_%_vs_SPLY_Format] = 

VAR _delta = [Actual] - [SPLY]

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
    ABS(_delta) = 0,
        FORMAT(_pct, "0.0%; 0.0%; 0.0%")
)

```

Guidelines:
- For Actual and Δ vs SPLY you need to amend the variable ```VAR IsCurrency = _Order IN {0}``` adding all the order numbers referring to measure that need to be formatted with currency.

**⚙️ Step 5: Create the Chart Control Measures**

We need 2 type of control measures: automatic and to be amended. 

Below the only control measure to be amended:

```
///////////////////////////////////////////////
// Control to Modify
//////////////////////////////////////////////

MEASURE 'MeasuresTable'[IBCS_Bar_Chart_Δ_Baseline_Positive] = 
MAXX(
    ALLSELECTED(dim_products), -- <-- your dim column you use in the y axis of the chart
    MAX([Actual], [SPLY])
) * 2.8
```

Guidelines:
- You need to substitute the template's column with the actual column you want to display on the y-axis. 

Now you just need to create the measures below without changing them:

```
DEFINE

///////////////////////////////////////////////
// Control Automatic
///////////////////////////////////////////////

MEASURE 'MeasuresTable'[IBCS_Bar_Chart_Title] = 
SELECTEDVALUE('IBCS-Column-Chart-Measures-Selector'[Measure]) & 
" - " & 
"⬛ AC | ⬜ SPLY"
     
MEASURE 'MeasuresTable'[IBCS_Bar_Chart_Actual_Upper_Bound] = 
IF(
    [Actual] - [SPLY] < 0,
    MAX([Actual], [SPLY])
)
   
MEASURE 'MeasuresTable'[IBCS_Bar_Chart_SPLY_Upper_Bound] = 
IF(
    [Actual] - [SPLY] >= 0,
    MAX([Actual], [SPLY])
)

MEASURE 'MeasuresTable'[IBCS_Bar_Chart_Δ%_Positive] = 

-- length = max value of the top item
var _step1 = DIVIDE([IBCS_Bar_Chart_Δ_Baseline_Positive], 2,BLANK())

-- max length of the % lollipop bar if 100%
-- if the second number in the divide = 1 means the percentage bar can be as long as the max bar of the values.
var _100_limit = DIVIDE(_step1, 1.4, BLANK())

-- limit the percentage in visual for bigger percenntage to have a decent bar length
var _perclimitedd = 
IF(
    DIVIDE (
	    [Actual] - [SPLY],
	    [SPLY]
        )   <=  1.2, 
        DIVIDE (
	    [Actual] - [SPLY],
	    [SPLY]
        ), 
         1.2
    )

-- proportion here is: 100% : _100_limit = _perclimitedd : _proportionX
var _proportionX = DIVIDE(_100_limit * _perclimitedd, 1, BLANK()) 

RETURN
IF(
    DIVIDE (
	    [Actual] - [SPLY],
	    [SPLY]
	) >= 0,
    [IBCS_Bar_Chart_Δ_Baseline_Positive] + _proportionX ,
    BLANK()
)

MEASURE 'MeasuresTable'[IBCS_Bar_Chart_Δ%_Negative] = 

-- length = max value of the top item
var _step1 = DIVIDE([IBCS_Bar_Chart_Δ_Baseline_Positive], 2,BLANK()) * -1

-- max length of the % lollipop bar if 100%
-- if the second number in the divide = 1 means the percentage bar can be as long as the max bar of the values.
var _100_limit = DIVIDE(_step1, 1.4, BLANK())

-- limit the percentage in visual for bigger percenntage to have a decent bar length
var _perclimitedd = 
IF(
    DIVIDE (
	    [Actual] - [SPLY],
	    [SPLY]
        )   >= - 1.2, 
        DIVIDE (
	    [Actual] - [SPLY],
	    [SPLY]
        ), 
        - 1.2
    )

-- proportion here is: 100% : _100_limit = _perclimitedd : _proportionX
var _proportionX = DIVIDE(_100_limit * _perclimitedd, 1, BLANK()) 

RETURN
IF(
    	DIVIDE (
	    [Actual] - [SPLY],
	    [SPLY]
	) < 0,
    [IBCS_Bar_Chart_Δ_Baseline_Positive] - _proportionX ,
    BLANK()
)

   
```

Guidelines:
- Start to create these measures without changing name and/or content.
- They should be populated and ready for the next steps.

**Pro Tip:** Organise all the measures we made until now in this folder structure below

```
📁 SPLY Column Chart
|
├─ 📁 0. Control Chart Settings - Automatic
│  ├─ IBCS_Bar_Chart_Actual_Upper_Bound
│  ├─ IBCS_Bar_Chart_SPLY_Upper_Bound
│  └─ IBCS_Bar_Chart_Title
|  └─ IBCS_Bar_Chart_Δ%_Negative
|  └─ IBCS_Bar_Chart_Δ%_Positive
|
├─ 📁 0. Control Chart Settings - Modify
│  ├─ IBCS_Bar_Chart_Δ_Baseline_Positive
│
├─ 📁 0. Control Chart Settings Labels Format
│  ├─ Actual_Format
│  ├─ Δ_%_vs_SPLY_Format
│  ├─ Δ_vs_SPLY_Format
│
└─ 📁 1. Metrics - Modify
   ├─ Actual
   └─ SPLY
```

**⚙️ Step 6: Bring in the Bar Chart**

You need to go to the .pbix file you downloaded at the beginning, and copy and paste the column chart into your report.

All should work correctly. 

However, the colour of the columns and variances, but also of other charts elements, might be different depending on your theme. If you want to change them, you need to manually play with the formatting.

If you want to keep the IBCS style theme you can download it [at this link](https://github.com/SteCiu01/Power-BI-Visual-Templates/blob/main/IBCS%20Style%20Variance%20Bar%20Chart%20DL%20Compatible/Files/Theme_IBCS.json) and upload it in your report.

Finally you might need to adjust: the font size of the data labels, remove their background and remove data labels for SPLY. You can use the template as reference to correct these aspects.

❗Note: in the template it is set the Top 15 products by Actual limit in the chart, in the visual level filters. You can either remove it or change it as best fits your needs.

### Final Considerations and limitations

After completing the six steps, your IBCS-style bar chart should be fully functional and ready for use across all your measures. 

This template offers maximum flexibility: you can control which measures appear on each report page by either hiding them from the slicer or selecting a single measure using the Measure column of the IBCS-Column-Chart-Measures-Selector as a visual-level filter. This is very important because if you have different topics in one model, you can build only one measure selector in the model and then, in each page you can decide what "allow" the users to see. Also, the measures selector might come handy also for other visuals.

Replicating this template in your own report is quick: depending on the number of measures, it should take no more than 5–10 minutes. Once set up, the template ensures a consistent IBCS-compliant visual design while allowing seamless scalability for additional measures and future analyses.

The only limitations are: 

- The need of re-mapping the measures as they break using Direct Lake.
- The need to fine-tune the colors of the bars and variance indicators depending on your report theme, as color settings cannot be fully automated via the measure-based color logic. This can easily be corrected using the IBCS theme included with the template.
- The need of fine tuning data labels after re-mappinng the measures.
- Adjust or remove your TopN at visual levvel.

Please let me know in case of bugs and/or improvements, I am very open to have some cooperations.
