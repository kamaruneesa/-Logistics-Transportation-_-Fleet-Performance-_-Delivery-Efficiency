# -Logistics-Transportation-_-Fleet-Performance-_-Delivery-Efficiency
 Logistics &amp; Transportation- Fleet Performance &amp; Delivery Efficiency
# 🚚 Logistics & Transportation: Fleet Performance & Delivery Efficiency



---

## 📂 Dataset Overview
The dataset (`logistics_project_dataset 2_2.xlsx`) consists of two tables:
1. **`Trip_Data`**: 50 trip records containing `Trip_ID`, `Vehicle_ID`, `Driver_ID`, `Origin`, `Destination`, `Distance_km`, `Fuel_Consumed_L`, `Delivery_Status` (`On-Time` / `Late`), and `Delivery_Date`.
2. **`Vehicle_Master`**: Master details for 7 fleet vehicles (`V01`–`V07`) including `Vehicle_ID`, `Vehicle_Type` (`Van`, `Truck`, `Mini-Truck`), `Capacity_kg`, and `Maintenance_Cost`.

---

## 🛠️ Step-by-Step Workflow

### 1. Data Cleaning   (Microsoft Excel)
* **Fixing Corrupted `Trip_ID` Records (Rows 1–12):**
  * The first column was mislabeled as `Projects`, and the first 12 records contained unrelated project descriptions and blank cells instead of sequential trip identifiers.
  * Renamed the column header to **`Trip_ID`** and standardized the missing/corrupted entries sequentially from **`T001` to `T012`** (matching the existing `T013`–`T050` format).
* **Correcting `Distance_km` Data Anomaly (`T043`):**
  * Identified a text/expression anomaly (`116+666` / malformed numeric string) in the `Distance_km` column for trip **`T043`** (`V06`, `Hyderabad -> Bangalore`, `2023-01-06`), which prevented numeric aggregation.
  * Cleaned the record to a valid numeric value and formatted the entire `Distance_km` column as **Number**.
* **Relating Trips with Vehicle Master (`XLOOKUP`):**
  * Used Excel's `XLOOKUP` function to pull `Vehicle_Type`, `Capacity_kg`, and `Maintenance_Cost` from `Vehicle_Master` into the trip table using `Vehicle_ID`.
  * =XLOOKUP([@[Vehicle_ID]],Vehicle_Master_2[Vehicle_ID],Vehicle_Master_2[Vehicle_Type])


### 2. Missing Value Imputation via Group By (Excel Power Query)
* Loaded the cleaned trip table into **Excel Power Query** to handle missing/blank `Fuel_Consumed_L` values.
* Created an aggregated table (`Avg_Fuel_Table`) using **Group By** on `Vehicle_Type` to calculate average fuel consumption per vehicle category.
* Merged the grouped averages back into the main query (`Trip_Data_21Merge2`) and imputed missing fuel values with the corresponding `Vehicle_Type` average.

### 3. Data Modeling (Power BI Desktop)
Imported the transformed tables into **Power BI Desktop** and configured **Many-to-One (`*:1`)** relationships in Model View:
* **`Trip_Data_21Merge2` ➔ `Vehicle_Master_22`** (linked via `Vehicle_ID`)
* **`Trip_Data_21Merge2` ➔ `Avg_Fuel_Table1`** (linked via `Vehicle_Type`)

---

## 📐 DAX Measures Created (Power BI)
*(Note: Assumed fuel price is set at **₹95 per liter** for cost calculations).*
* **Route Standardization:new column**
  * Combined `Origin` and `Destination` (`Origin -> Destination`) to create a **`Route`** column for route-level performance analysis.
  * Route = Trip_Data_21Merge2[Origin] & "--> "& Trip_Data_21Merge2[Destination] 
  * **new measure**

    * **Fuel Efficiency=**
    * Fuel_ Efficiency = DIVIDE(SUM(Trip_Data_21Merge2[Distance_km]),SUM(Trip_Data_21Merge2[Fuel_Consumed_L]))
    * **On-Time Delivery % =**
    * On-Time Delivery % = DIVIDE(CALCULATE(COUNT(Trip_Data_21Merge2[Delivery_Status]),Trip_Data_21Merge2[Delivery_Status]="On-Time"),COUNT(Trip_Data_21Merge2[Trip ID]),0)*100
    * **Cost per km =**
    * Cost per km =DIVIDE(SUM(Trip_Data_21Merge2[Fuel_Consumed_L])* 95  + SUM(Trip_Data_21Merge2[Maintenance_Cost]),SUM(Trip_Data_21Merge2[Distance_km]),0)
      

---

## 📊 Visualizations Included

### 1. Card Visualizations (KPI Summary)
* **Sum of Fuel_Consumed_L:** Displays total fuel consumed across trips .
* **On-Time Delivery %:** Tracks the share of trips delivered on schedule.
* **Cost per km:** Shows total operating cost per kilometer (fuel cost + maintenance cost divided by total distance).

### 2. Interactive Slicers
* **`Vehicle_Type` Slicer:** Filters visuals by `Mini-Truck`, `Truck`, and `Van`.
* **`Delivery_Status` Slicer:** Filters visuals by `Late` and `On-Time` trips.

 ### 3. Clustered Bar Chart (`On-Time Delivery % by Route`)


### 4. Line Chart (`Fuel Efficiency by Month`)


### 5. Map Visual (`Sum of Distance_km by Origin and Delivery_Status`)


---
