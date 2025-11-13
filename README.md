# Maji Ndogo Crop Suitability Analysis

## Introduction
This project analyzes farming data from **Maji Ndogo** to identify the best environmental and soil conditions for planting specific crops.  
By integrating **geographic**, **climatic**, **soil**, and **farm management** data, we can recommend where each crop performs best.

The dataset is stored in a **SQLite** database (`Maji_Ndogo_farm_survey.db`) with multiple tables joined by the common key `Field_ID`.  
The analysis was conducted in **Python** using **Pandas**, **SQLAlchemy**, **Matplotlib**, and **Seaborn**.

---

##  Data Overview

### **1. Geographic Features**
| Column | Description |
|--------|--------------|
| `Field_ID` | Unique identifier for each field |
| `Elevation` | Elevation above sea level (metres) |
| `Latitude`, `Longitude` | Geographic coordinates |
| `Location` | Province name |
| `Slope` | Slope of the land |

### **2. Weather Features**
| Column | Description |
|--------|--------------|
| `Rainfall` | Amount of rainfall (mm) |
| `Min_temperature_C` | Average minimum temperature (°C) |
| `Max_temperature_C` | Average maximum temperature (°C) |
| `Ave_temps` | Average temperature (°C) |

### **3. Soil and Crop Features**
| Column | Description |
|--------|--------------|
| `Soil_fertility` | Fertility level (0 = infertile, 1 = very fertile) |
| `Soil_type` | Soil classification (Text) |
| `pH` | Soil acidity/basicity |

### **4. Farm Management Features**
| Column | Description |
|--------|--------------|
| `Pollution_level` | Pollution index (0–1) |
| `Plot_size` | Size of the farm (hectares) |
| `Chosen_crop` | Crop grown on the plot |
| `Annual_yield` | Total yearly yield |
| `Standard_yield` | Normalized yield per crop type |

---

## Methodology

### **1. Data Import**
All tables were joined using SQLAlchemy and loaded into a Pandas DataFrame:
```python
engine = create_engine('sqlite:///Maji_Ndogo_farm_survey_small.db')
```

sql_query = """
SELECT *  
FROM geographic_features gf  
JOIN weather_features wf  
ON gf.field_ID = wf.field_ID  
JOIN soil_and_crop_features scf  
ON wf.field_ID = scf.field_ID  
JOIN farm_management_features fmf  
ON scf.field_ID = fmf.field_ID;
"""  
  
  ```
  data = pd.read_sql_query(text(sql_query), engine.connect())
  ```
## **2. Data Cleaning**

- Removed duplicate `Field_ID` columns.  
- Fixed column swaps and spelling mistakes in crop names.  
- Replaced negative values in `Elevation` and `Min_temperature_C` with absolute values.  
- Dropped irrelevant columns like `Ave_temps`.  
- Standardized column names to lowercase.  

---

## 3. Defining High Performance

To identify **top-performing farms**, a threshold was computed for each crop based on yield distribution:

**Threshold = (Q₂ + Q₃) / 2**


Where:  
- **Q2** = Median (50th percentile)  
- **Q3** = 75th percentile of `Standard_yield`  

Farms with `Standard_yield ≥ Threshold` were classified as **top performers**.

---

## 4. Key Insights

### Climatic & Geographic Factors

| Crop | Elevation (m) | Rainfall (mm) | Night Temp | Day Temp | Observations |
|------|----------------|---------------|-------------|-----------|---------------|
| **Tea** | 650–800 | 1700–2000 | Cool | 28–33°C | Grows at high altitudes, needs heavy rainfall |
| **Rice** | 400–500 | 1500–1900 | Warm | 30–35°C | Prefers warm, humid, lowlands |
| **Banana** | 600–800 | 1600–2200 | Warm | 30–35°C | Thrives in high rainfall and steady temps |
| **Maize** | 500–900 | 500–800 | 2–7°C | 29–33°C | Adaptable to varied climates |
| **Cassava** | 500–900 | 800–1200 | Slightly warm | 29–33°C | Drought- and pollution-tolerant |
| **Potato** | 500–900 | 500–800 | Warm | 30–33°C | Prefers mid-altitude areas |
| **Wheat** | 500–700 | 200–800 | 4–7°C | 30–33°C | Drought-resistant |
| **Coffee** | 600–900 | 1700–2200 | 5–7°C | 28–34°C | Prefers high rainfall, fertile volcanic soils |

---

### Soil & Environmental Factors

| Factor | Observations |
|---------|---------------|
| **Soil Fertility** | Tea, Coffee, and Rice require very fertile soils. Wheat and Cassava tolerate lower fertility. |
| **Soil pH** | Tea prefers acidic (4.5–5.5); Rice and Maize perform best near neutral (5.5–6.5). |
| **Pollution Tolerance** | Cassava and Wheat perform well even in polluted areas. |
| **Soil Type Preferences** | Tea & Coffee → Volcanic; Rice → Loamy; Cassava & Potato → Loamy/Sandy; Wheat → Loamy. |

---

## 5. Summary Table

| Crop | Altitude (m) | Rainfall (mm) | Night Temp | Day Temp | Soil Type | Soil pH | Fertility | Pollution Tolerance |
|-------|---------------|----------------|-------------|------------|------------|-----------|-------------|----------------|
| **Tea** | 650–800 | 1700–2000 | Cool | 28–33°C | Volcanic | 4.5–5.5 | Very fertile | Low |
| **Rice** | 400–500 | 1500–1900 | Warm | 30–35°C | Loamy | 5.5–6.5 | Very fertile | Low |
| **Banana** | 600–800 | 1600–2200 | Warm | 30–35°C | Volcanic, Loamy | ~6.0 | Moderate | Moderate |
| **Maize** | 500–900 | 500–800 | 2–7°C | 29–33°C | Loamy | 5.5–6.5 | Very fertile | Low |
| **Cassava** | 500–900 | 800–1200 | Slightly warm | 29–33°C | Loamy, Sandy | ~6.0 | Low | High |
| **Potato** | 500–900 | 500–800 | Warm | 30–33°C | Loamy, Sandy | 5.0–6.0 | Moderate | Moderate |
| **Wheat** | 500–700 | 200–800 | 4–7°C | 30–33°C | Loamy | 6.0–7.0 | Average | High |
| **Coffee** | 600–900 | 1700–2200 | 5–7°C | 28–34°C | Silt, Volcanic | 5.0–6.0 | Very fertile | Low |

---

## 6. Visualizations

The analysis includes:
- **Boxplots** — Distribution of climatic and soil variables per crop  
- **KDE Plots** — Comparison between all farms and top-performing farms  
- **Histograms & Correlations** — Overview of numeric relationships  

These visualizations highlight environmental preferences and variability across different crops.

---

## 7. Conclusion

This analysis provides actionable insights for **sustainable, data-driven crop planning** in Maji Ndogo:

- **Tea and Coffee:** Thrive at high altitudes with fertile, volcanic soils.  
- **Rice and Banana:** Favor low-lying, warm, and humid environments.  
- **Cassava and Wheat:** Resilient under pollution and low fertility.  
- **Maize and Potato:** Highly adaptable across different regions.  

Understanding these relationships helps guide **optimal land use**, **improve yields**, and promote **sustainable agriculture** in the region.

---

## 8. Requirements

Install dependencies:
```bash
pip install pandas sqlalchemy matplotlib seaborn
```  

## 9. Author

**Victor Muthii Munene**  
📧 **mv.munene01@gmail.com**

---

## 10. License

This project is licensed under the **[MIT License]**.
