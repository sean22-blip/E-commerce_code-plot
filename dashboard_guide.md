# E-commerce Sales Dashboard – Insights & Power BI Build Guide

Source: `ecommerce_cleaned.csv` – 3,660 orders, 1 Jan 2024 to 21 Nov 2024 (November is a partial month, 21 days).
Columns: Category, Price (Rs.), Discount (%), Final_Price(Rs.), Payment_Method, Purchase_Date.

## 0. Dataset overview

**What it is:** transaction records from an online store. One row = one order of one product.

| | Raw (`ecommerce_dataset_updated.csv`) | Cleaned (`ecommerce_cleaned.csv`) |
|---|---|---|
| Rows | 3,660 | 3,660 |
| Columns | 8 | 6 |
| Missing values | 0 | 0 |
| Duplicate rows | 0 | 0 |
| Date format | `DD-MM-YYYY` text | `YYYY-MM-DD` date |

**Columns (cleaned):**

| Column | Type | Values |
|---|---|---|
| Category | Text, 7 values | Beauty, Books, Clothing, Electronics, Home & Kitchen, Sports, Toys |
| Price (Rs.) | Decimal | List price before discount, Rs 10.09 – 499.96 (median 253.85) |
| Discount (%) | Whole number, 8 tiers | 0, 5, 10, 15, 20, 25, 30, 50 (median 15) |
| Final_Price(Rs.) | Decimal | Price the customer paid, Rs 5.89 – 496.82 (median 199.19) |
| Payment_Method | Text, 5 values | Credit Card, Debit Card, UPI, Net Banking, Cash on Delivery |
| Purchase_Date | Date | 1 Jan 2024 – 21 Nov 2024 (326 distinct days) |

**What cleaning removed:** `User_ID` and `Product_ID`. Every value in both was unique (3,660 users, 3,660 products), so they can't be grouped or used to find repeat customers.

**Limitations (worth stating when presenting):**
- No customer data: we can't measure loyalty, repeat purchases, or customer lifetime value.
- No quantity, cost, or profit columns: we see revenue, not margin. "Discount cost" means revenue given up, not profit lost.
- November is incomplete (21 days), and there is no December, so we can't see the holiday season.
- No region, channel, or product name, so the analysis stops at category level.

## 1. Business insights

1. **Discounts cost a lot and buy almost nothing.**
   - Rs 175,292 given away = 18.8% of gross sales (Rs 932,570 → Rs 757,278 net). 87% of orders had a discount.
   - Order count is flat across every discount tier (412–480 orders per tier; 0% = 480, 50% = 430). Deep discounts do not bring in more orders.
   - Customers buy the same-priced items at every tier (avg list price Rs 243–260; correlation price vs discount = −0.003). Discounts are not moving people to bigger baskets.
   - The 50% tier is the worst: 11.7% of orders but 31.5% of all discount money (Rs 55,259). We gave away as much as we collected on those orders.
   - Action: cap discounts at 20–30%, test removing the 50% tier, and target discounts at specific goals instead of applying them broadly.

2. **No category dominates – the portfolio is balanced.**
   - Clothing leads at Rs 115,315 (15.2%); Electronics is last at Rs 100,462 (13.3%) and has the fewest orders (498).
   - Electronics has no price premium (AOV Rs 202 vs Rs 207 overall), which is unusual for that category. Action: review the Electronics range/pricing; there is room to add higher-value products.
   - Home & Kitchen has the most orders (549) but the highest average discount (19.6%) – volume is partly bought with discounts.

3. **Payments are evenly split; 81% are digital.**
   - Credit Card 20.6%, UPI 20.4%, Debit Card 20.3%, Net Banking 19.4%, Cash on Delivery 19.3%.
   - COD has the highest AOV (Rs 210) but carries return/collection risk. Action: small incentive to move COD buyers to UPI.
   - Strongest combination: Clothing × Credit Card (Rs 24,780). Weakest: Electronics × COD (Rs 17,481) – buyers avoid paying cash for electronics.

4. **Sales are stable month to month, with October the peak.**
   - Revenue per day ranges Rs 2,142 (June, lowest) to Rs 2,479 (April) and Rs 2,453 (October). November is tracking Rs 2,472/day, on pace for ~Rs 74k.
   - Use revenue per day, not monthly total, when comparing months – November's raw total (Rs 51,915) looks like a drop only because the month is incomplete.

5. **Sunday is the best day, Saturday the weakest.**
   - Sunday Rs 115,282 (566 orders) vs Saturday Rs 98,827 (477 orders), on the same number of days (46 each) – about 17% apart.
   - Action: schedule campaigns and email pushes for Sunday; investigate why Saturday underperforms.

**Headline for the presentation:** revenue is healthy and balanced across categories and payments; the biggest lever is the discount policy, which reduces revenue by ~19% without increasing orders.

## 1b. Business questions the dashboard answers

Each question points to the visual that answers it (letters match the layout in section 2).

**Overall performance (what happened?)**

| # | Question | Visual | Answer |
|---|---|---|---|
| Q1 | How much revenue did we make in 2024 so far? | KPI cards | Rs 757,278 net from 3,660 orders |
| Q2 | How much does a typical order bring in? | KPI – AOV | Rs 206.91 |
| Q3 | How much revenue did we give up to discounts? | KPI – Total Discount, Discount % | Rs 175,292, which is 18.8% of gross sales |
| Q4 | Are sales growing, falling, or stable? | A – Revenue per day | Stable: Rs 2,142–2,479 per day all year |
| Q5 | Which month was strongest and which was weakest? | A | Strongest April (Rs 2,479/day) and October (Rs 2,453/day). Weakest June (Rs 2,142/day) |

**Discounts (why is it happening?)**

| # | Question | Visual | Answer |
|---|---|---|---|
| Q6 | Do bigger discounts bring in more orders? | B (tooltip: Orders) | No. Every tier has 412–480 orders; 50% gets fewer than 0% (430 vs 480) |
| Q7 | Do discounts get customers to buy more expensive items? | C | No. Avg list price stays Rs 243–260 at every tier; only the paid price falls |
| Q8 | Which discount tier costs us the most? | B | 50%: Rs 55,259, or 31.5% of all discount money from 11.7% of orders |
| Q9 | Which category is discounted most heavily? | D (tooltip: Avg Discount) or slicer + KPI | Home & Kitchen (19.6% avg); Clothing the least (17.9%) |

**Categories and payments (where does revenue come from?)**

| # | Question | Visual | Answer |
|---|---|---|---|
| Q10 | Which category earns the most and least? | D | Clothing most (Rs 115k, 15.2%); Electronics least (Rs 100k, 13.3%) |
| Q11 | Does any category depend on us for most of the revenue? | D | No. The gap between first and last is only 2 points of share |
| Q12 | Which payment method do customers prefer? | F | Credit Card slightly (20.6%), but all five are within 19–21% |
| Q13 | Which category + payment combination sells best and worst? | E | Best: Clothing × Credit Card (Rs 24,780). Worst: Electronics × COD (Rs 17,481) |

**Timing (when do customers buy?)**

| # | Question | Visual | Answer |
|---|---|---|---|
| Q14 | Which day of the week is best for sales? | G | Sunday (Rs 115k, 566 orders) |
| Q15 | Which day is weakest? | G | Saturday (Rs 99k, 477 orders), ~17% below Sunday |

**Decisions (what should we do?)** – answered by combining visuals

| # | Question | Based on | Suggested answer |
|---|---|---|---|
| Q16 | Should we keep the 50% discount? | B + C + Q6 | Test removing it. It costs the most and doesn't add orders |
| Q17 | When should we run marketing campaigns? | G + A | Sundays, and before the April/October peaks |
| Q18 | Which category needs attention? | D + E | Electronics: lowest revenue, no price premium, weak on COD |
| Q19 | Should we push customers away from Cash on Delivery? | F + E | Yes, gently (e.g. a small UPI incentive). COD is 19% of revenue and carries collection risk |

**Questions this dataset can't answer** (good for the limitations slide): Who are our repeat customers? Which products are most profitable? How do sales differ by region? What happens in December?

## 2. Dashboard layout (one page, 16:9)

```
┌────────────────────────────────────────────────────────────────────────┐
│ Title: E-commerce Sales Performance 2024        [Date] [Category] [Payment] slicers │
├──────────┬──────────┬──────────┬──────────┬──────────────────────────────┤
│ Net Rev  │ Orders   │ AOV      │ Discount │ Discount % of gross           │  ← KPI cards
├──────────┴──────────┴──────────┴──────────┴──────────────────────────────┤
│ (A) Revenue per day by month – line      │ (B) Discount given by tier – column │
├──────────────────────────────────────────┼─────────────────────────────────┤
│ (C) Avg list vs avg paid price by tier   │ (D) Revenue by category – bar     │
├──────────────────────────────────────────┼─────────────────────────────────┤
│ (E) Category × Payment – heatmap matrix  │ (F) Revenue by payment – bar      │
│                                          │ (G) Revenue by weekday – column   │
└──────────────────────────────────────────┴─────────────────────────────────┘
```

Story order (top-left → bottom-right): how big is the business → is it growing → where is the money leaking (discounts) → where does revenue come from → how do people pay / when do they buy.

## 3. Chart choices and why

| # | Visual | Fields | Why this chart | Why not the alternative |
|---|---|---|---|---|
| KPI | Card (×5) | Net Revenue, Orders, AOV, Total Discount, Discount % | The story is one number each – a chart would add nothing. Viewers read these first. | A gauge needs a target we don't have. |
| A | Line chart | X: Month, Y: Revenue per Day | Lines show change over time; the eye follows the trend. Per-day fixes the partial-November problem. | Column chart breaks time into separate blocks and makes trend harder to see. |
| B | Column chart | X: Discount (%) tier, Y: Total Discount | Tiers are ordered (0 → 50), so vertical columns left-to-right match the order. Length makes the 50% tier jump out. | Pie can't compare 8 close slices. |
| C | Line chart, 2 lines | X: Discount tier, Y: Avg Price and Avg Final Price | Both lines are in Rs on **one** axis. The flat list-price line next to the falling paid line *proves* discounts don't change what people buy. | Dual-axis chart would invent a fake relationship between scales. |
| D | Horizontal bar, sorted desc | Y: Category, X: Revenue | Category names are long; horizontal bars keep them readable. Sorting makes ranking instant. Axis starts at 0 so small differences aren't exaggerated. | Pie/donut – 7 slices of 13–15% look identical. |
| E | Matrix with background color scale | Rows: Category, Cols: Payment, Values: Revenue | Two categorical dimensions × one value = heatmap. Darker = more revenue, so best/worst combos pop out, and exact numbers are still printed. | Stacked bars with 35 segments are unreadable. |
| F | Horizontal bar | Y: Payment, X: Revenue | Five near-equal values – bars show "they're almost the same" honestly. | Donut is the usual choice, but with 19–21% shares the slices look equal and you can't rank them. |
| G | Column chart | X: Weekday (Mon→Sun), Y: Revenue | Weekdays are ordered, so keep Mon→Sun (don't sort by value). Highlight Sunday. | — |

Slicers: Purchase_Date (between), Category (dropdown), Payment_Method (dropdown) in one row at the top so every visual filters together.

## 4. Color system and why

Import `powerbi_theme.json` (View → Themes → Browse for themes) to apply these automatically.

| Role | Hex | Where used | Reason |
|---|---|---|---|
| Primary blue | `#2a78d6` | All single-series bars/lines (A, D, F, G), paid-price line in C | Blue is neutral: it doesn't say "good" or "bad", so it doesn't bias the reader. It stays readable for colorblind viewers. |
| Highlight orange | `#eb6834` | 50% tier in B, Sunday in G | Warm vs cool contrast with blue – the one bar that matters stands out instantly. Blue/orange is the classic colorblind-safe pair (validated: ΔE 24.7 under protanopia, contrast ≥ 3:1 on white). |
| Context gray | `#c3c2b7` | Non-highlighted bars in B and G; list-price line in C | Gray pushes "background" data back so the highlight carries the message (emphasis by contrast, not by adding more colors). |
| Sequential blue | `#cde2fb` → `#104281` | Heatmap E (min → max) | One hue light→dark = magnitude. Everyone reads "darker = more". A rainbow scale would have no natural order. |
| Text | `#0b0b0b` / `#52514e` | Titles / labels and axes | Text stays dark ink, never series color, for readability. |
| Background | `#fcfcfb`, gridlines `#e1e0d9` | Canvas and gridlines | Off-white and light hairline grid keep the chrome quiet so data is the focus. |

Rules followed (useful to state when presenting):
- **One color for categories in D, F.** Categories have no order, and the bar length already shows the value – giving each bar its own color adds nothing and makes readers hunt for a legend.
- **No red/green.** About 8% of men can't tell red from green, and red/green imply good/bad judgments we don't want on neutral sales data.
- **Few colors, used on purpose.** Blue = data, orange = "look here", gray = context. If everything is highlighted, nothing is.
- Data labels only where they help (KPI cards, heatmap numbers, bar ends in D/F) – not on every point of the line charts.

## 5. DAX measures

```DAX
Net Revenue      = SUM ( ecommerce_cleaned[Final_Price(Rs.)] )
Gross Sales      = SUM ( ecommerce_cleaned[Price (Rs.)] )
Total Discount   = [Gross Sales] - [Net Revenue]
Discount % Gross = DIVIDE ( [Total Discount], [Gross Sales] )
Orders           = COUNTROWS ( ecommerce_cleaned )
AOV              = DIVIDE ( [Net Revenue], [Orders] )
Avg List Price   = AVERAGE ( ecommerce_cleaned[Price (Rs.)] )
Avg Paid Price   = AVERAGE ( ecommerce_cleaned[Final_Price(Rs.)] )
Revenue per Day  = DIVIDE ( [Net Revenue], DISTINCTCOUNT ( ecommerce_cleaned[Purchase_Date] ) )
```

Calculated columns:

```DAX
Month      = FORMAT ( ecommerce_cleaned[Purchase_Date], "MMM" )
MonthNo    = MONTH ( ecommerce_cleaned[Purchase_Date] )          -- Sort Month by MonthNo
Weekday    = FORMAT ( ecommerce_cleaned[Purchase_Date], "ddd" )
WeekdayNo  = WEEKDAY ( ecommerce_cleaned[Purchase_Date], 2 )     -- Sort Weekday by WeekdayNo (Mon = 1)
```

Highlighting a single bar (B, G): Format → Columns → Color → **fx** → Rules, e.g. `Discount (%) = 50` → `#eb6834`, else `#c3c2b7`. Heatmap (E): Matrix → Cell elements → Background color → Gradient, min `#cde2fb`, max `#104281`, and set font color to white for the darkest cells if contrast is low.

## 6. Expected values (to check your build)

| KPI | Value |
|---|---|
| Net Revenue | Rs 757,278 |
| Gross Sales | Rs 932,570 |
| Total Discount | Rs 175,292 (18.8%) |
| Orders | 3,660 |
| AOV | Rs 206.91 |
