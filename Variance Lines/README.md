# Variance Lines

#### Option A: Variance Area

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/e6a442a2-b009-422a-aa71-4cf59887a228" />

#### Option B: Variance Bars

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/fafe37af-4a80-48fa-b8a0-bd22307e0cdb" />

### Functionalities

This template allows you to visualize multiple measures within a single chart and seamlessly switch between them. It also supports drill-up and drill-down interactions across the date hierarchy defined on the X-axis.

The template can be used for time-based analyses, such as Year–Quarter–Month hierarchies.

The template includes 2 visualizations options: variance area and variance bars, you can choose what best fits your needs.

This makes it suitable for visualising the Actual vs. Target variance, for the selected metric.

**⚙️ Step 1: download the template in pbix**

[📥 Download Here](https://github.com/SteCiu01/Power-BI-Visual-Templates/raw/refs/heads/main/Variance%20Lines/Visuals%20Templates%20Variance%20Lines.pbix)

**⚙️ Step 2: Create the measures selector (parameter) table**

Create a calculated table in your model that will act as a measure selector parameter, using the following DAX code:

```
Variance-Line-Chart-Measures-Selector = 
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
Variance-Line-Chart-Measures-Selector =
DATATABLE (
    "Measure", STRING,
    "Order", INTEGER,
    {
        { "Total Sales ($)", 0 }
    }
)
```

**⚙️ Step 3: Create the measures [Actual] and [Target] that needs to be amended**

**⚠ IMPORTANT**

Before running these DAX Query View codes in steps 3 and 5 make sure the 'MeasuresTable' in the code has the same name of your table where you want to save the measures. 

‼️ This is crucial to make everything work smoothly: if your measures table is called 'Tables for Measures', for example, follow these steps:

- Rename the 'MeasuresTable' of the template you just downloaded into 'Tables for Measures'
- Change the codes below replacing 'MeasuresTable' with 'Tables for Measures'
- Run these codes in your model to add the measures.

Following these steps you avoid the mismatch between the template and your model. 

You will just need to substitute the x-axis fields with your fields.

```
DEFINE

// Metrics Measures to modify, see guidelines

   MEASURE 'MeasuresTable'[Actual] = SWITCH(
    TRUE(),
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 0, [Total Sales $],
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 1, [Total Sales Qty]
)
    MEASURE 'MeasuresTable'[Target] = SWITCH(
    TRUE(),
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 0, [SPLY Sales $],
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 1, [SPLY Sales Qty]
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
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 0, [Total Sales $]
)
    MEASURE 'MeasuresTable'[Target] = SWITCH(
    TRUE(),
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 0, [SPLY Sales $]
)
```
**⚙️ Step 4: Format [Actual] and [Target]**

Since you can decide to move from $ to count and vice versa, by switching measures, we need to set up a dynamic formatting for both measures, based on whether the selected measure is a $ or a count.

<img width="1040" height="219" alt="image" src="https://github.com/user-attachments/assets/6ca589ea-2d66-48a4-b4d3-7b2a10f84177" />

In the measure formatting options, choose dynamic. This let's you to write a conditional formatting for the measure, based on the type of measure you are calling using the parameter.

Here the FORMAT code for both [Actual] and [Target]:

```
SWITCH(
    TRUE(),
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 0, "\$#,0;(\$#,0);\$#,0" ,
    SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Order]) = 1, "#,0"
)
```

Guidelines: considering what measure corresponds to Order 0, 1, or 2, etc.. in your measures selector, you need to adapt this code to your use case so that if Order = 0 is a currency and Order = 1 is a whole number, you amend the code accordingly.

Single-measure note:

You could set a standard formatting, although I advise you to set it up as above for scalability

**⚙️ Step 5: Create the Automatic measures**

Now you just need to create the measures below without changing them.

```
DEFINE

// Automatic measures that potentially don't need to be amended

    MEASURE 'MeasuresTable'[Δ % vs. Target] = DIVIDE (
	    [Actual] - [Target],
	    [Target]
	)
    

    MEASURE 'MeasuresTable'[Variance_Line_Chart_Highlight_Area] = MIN([Actual], [Target])
    MEASURE 'MeasuresTable'[Variance_Line_Chart_Min-y-axis] = 0.6
    MEASURE 'MeasuresTable'[Variance_Line_Chart_Lable_RedGreen] = SWITCH(
    TRUE(),
    ([Actual] - [Target]) >= 0, [Variance_Line_Chart_ColorBlueGreen_CB],
    [Variance_Line_Chart_ColorRed]
)
    MEASURE 'MeasuresTable'[Variance_Line_Chart_Max-y-axis] = 1.1
    MEASURE 'MeasuresTable'[Variance_Line_Chart_Title] = SELECTEDVALUE('Variance-Line-Chart-Measures-Selector'[Measure])
    & "‌‌‏‏‎ ‎‏‏‎ ‎‏‏‎ ‎"
    &  "━ Actual vs. ╌ Target"
    

    MEASURE 'MeasuresTable'[Variance_Line_Chart_ColorTransparent] = "#FFFFFF00"
    MEASURE 'MeasuresTable'[Variance_Line_Chart_ColorLightBlack] = "#404040"   -- Actual bars (Variance dark gray)
    MEASURE 'MeasuresTable'[Variance_Line_Chart_ColorWhite] = "#FFFFFF"   -- Plain white
    MEASURE 'MeasuresTable'[Variance_Line_Chart_ColorBlack] = "#000000"   -- Plain black (text / axes)
    MEASURE 'MeasuresTable'[Variance_Line_Chart_ColorRed] = "#FF0000"   -- Bad variance
    MEASURE 'MeasuresTable'[Variance_Line_Chart_ColorBlueGreen_CB] = "#008E96"   -- Good variance (red-green color blind safe)
    MEASURE 'MeasuresTable'[Variance_Line_Chart_ColorLightGrey] = "#A6A6A6"   -- Previous year / second stack

```

Guidelines:
- Start to create these measures without changing name and/or content.
- They should be populated and ready for the next steps.

**Pro Tip:** Organise all the measures we made until now in this folder structure below

```
📁 SPLY Column Chart
|
├─ 📁 0. Control Chart Settings
│  ├─ Variance_Line_Chart_Highlight_Area
│  ├─ Variance_Line_Chart_Lable_RedGreen
│  ├─ Variance_Line_Chart_Max-y-axis
│  ├─ Variance_Line_Chart_Min-y-axis
│  ├─ Variance_Line_Chart_Title
│
├─ 📁 0. Control Colors
│  ├─ Variance_Line_Chart_ColorBlack
│  ├─ Variance_Line_Chart_ColorBlueGreen_CB
│  ├─ Variance_Line_Chart_ColorLightBlack
│  ├─ Variance_Line_Chart_ColorLightGrey
│  ├─ Variance_Line_Chart_ColorRed
│  ├─ Variance_Line_Chart_ColorTransparent
│  └─ Variance_Line_Chart_ColorWhite
│
├─ 📁 1. Metrics Automatic
│  ├─ Δ % vs. SPLY
│
└─ 📁 1. Metrics to Modify
   ├─ Actual
   └─ Target
```

**⚙️ Step 6: Bring in the Variance Line Chart**

You need to go to the .pbix file you downloaded at the beginning, and copy and paste the line chart into your report.

All should work correctly. 

However, the colour of the lines and variances might be different depending on your theme. If you want to adjust them based on your theme, you need to manually play with the formatting for the following:
- Lines
- Markers
- Error Bars/Bands

If you want to keep the IBCS style theme to match the color coding of the template you can download it [at this link](https://github.com/SteCiu01/Power-BI-Visual-Templates/blob/main/Variance%20Lines/Theme_IBCS.json) and upload it in your report.

### Final Considerations and limitations

After completing the six steps, your Variance Line chart should be fully functional and ready for use across all your measures. 

This template offers maximum flexibility: you can control which measures appear on each report page by either hiding them from the slicer or selecting a single measure using the Measure column of the Variance-Line-Chart-Measures-Selector as a visual-level filter. This is very important because if you have different topics in one model, you can build only one measure selector in the model and then, in each page you can decide what "allow" the users to see. Also, the measures selector might come handy also for other visuals.

Replicating this template in your own report is quick: depending on the number of measures, it should take no more than 5–10 minutes. Once set up, the template ensures a consistent Variance Line Chart visual design while allowing seamless scalability for additional measures and future analyses.

The only limitations are: 

- The need to fine-tune the colors of the lines and variance bars/areas depending on your report theme, as color settings cannot be fully automated via the measure-based color logic. This can easily be corrected using the IBCS theme included with the template.

Please let me know in case of bugs and/or improvements, I am very open to have some cooperations.
