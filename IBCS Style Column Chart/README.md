# IBCS Style Column Chart 

#### Over Time

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/24aa62aa-1f3a-415a-b4ec-0af5b2b55c19" />

#### Categories

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/94b40e2b-2fc5-4fde-88c6-8592f31482a8" />

### Functionalities

This template allows you to visualize multiple measures within a single chart and seamlessly switch between them. It also supports drill-up and drill-down interactions across the hierarchy defined on the X-axis.

The template can be used for both:

- Time-based analyses, such as Year–Quarter–Month hierarchies
- Categorical analyses, such as product or category hierarchies

This makes it suitable for a wide range of analytical scenarios while maintaining a consistent IBCS-style visual design.

### How to use it

**Step 1: download the template in pbix**

[📥 Download Here](https://github.com/SteCiu01/Power-BI-Visual-Templates/raw/refs/heads/main/IBCS%20Style%20Column%20Chart/Files/Visuals%20Templates%20IBCS%20Column%20Chart.pbix)

**Step 2: Create the measures selector (parameter) table**

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
- Use user-friendly names for the measures.
- For example, if your actual measure is called [tot_sales_usd], expose it as "Total Sales ($)".

Assign an explicit order, following the same concept as field parameters, to control how measures appear in the selector.

Note
Even if you only need to visualize a single measure, it is recommended to create this table anyway. This approach ensures future scalability, allowing you to add more measures later without changing the visual logic.

Single-measure example

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
