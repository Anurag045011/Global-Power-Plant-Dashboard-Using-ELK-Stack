# **Global Power Plant Dashboard Using ELK Stack**

![image](https://github.com/user-attachments/assets/97dda784-3c5a-4478-bcd1-e00ac6b2abe5)

---

## **Objective**  
The primary objective of this project is to build an **interactive energy generation dashboard** using the **ELK Stack (Elasticsearch, Logstash, and Kibana)**.  
The dashboard aims to provide insights into global power generation facilities, focusing on production trends, fuel type distributions, and regional capacity, enabling stakeholders to derive data-driven decisions regarding **energy infrastructure, renewable investments, and sustainability**.

---

## **Dataset Description**  
The dataset used in this project is the **Global Power Plant Database**, which provides comprehensive information about **power generation facilities worldwide**.

### **Key Features of the Dataset**  
- **Diverse Fuel Types**: Includes renewable energy sources such as **solar, wind, hydro**, and conventional fuel types like **coal** and **gas**.  
- **Global Coverage**: Data spans multiple continents, covering power plants across over 100 countries.  
- **Detailed Power Generation Data**: Information about:  
   - Plant **capacity (MW)**.  
   - **Estimated energy generation** (GWh) for the years 2013 to 2017.  
   - Plant **geolocation** (latitude and longitude).  
- **Environmental Impact**: Data facilitates understanding of power generation's environmental footprint (emissions and fuel dependencies).

---

### **Dataset Columns and Descriptions**  

| **Column Name**               | **Description**                                                                                 |
|-------------------------------|---------------------------------------------------------------------------------------------|
| `country`                     | 3-letter ISO country code of the power plant's location.                                      |
| `country_long`                | Full name of the country.                                                                     |
| `name`                        | Name of the power plant.                                                                      |
| `gppd_idnr`                   | Unique identifier for each power plant.                                                       |
| `capacity_mw`                 | Power plant's capacity in megawatts (MW).                                                     |
| `latitude`                    | Latitude coordinate of the plant's location.                                                  |
| `longitude`                   | Longitude coordinate of the plant's location.                                                 |
| `primary_fuel`                | Primary fuel type used for power generation (e.g., Solar, Wind, Hydro).                       |
| `source`                      | Source of the data (e.g., governmental agency or power mapping service).                      |
| `geolocation_source`          | Source of the plant's geolocation data.                                                       |
| `estimated_generation_gwh_2013` | Estimated power generation in gigawatt-hours (GWh) for the year 2013.                         |
| `estimated_generation_gwh_2014` | Estimated power generation (GWh) for 2014.                                                   |
| `estimated_generation_gwh_2015` | Estimated power generation (GWh) for 2015.                                                   |
| `estimated_generation_gwh_2016` | Estimated power generation (GWh) for 2016.                                                   |
| `estimated_generation_gwh_2017` | Estimated power generation (GWh) for 2017.                                                   |
| `estimated_generation_note_*` | Notes or comments regarding the estimation method for each respective year.                   |

---

## **Process Workflow**

The process of building this dashboard involves multiple steps:

### **1. Data Collection**  
- The dataset was sourced from the **Global Power Plant Database**, which includes energy generation data, capacity values, and geolocation details.

---

### **2. Data Transformation and Ingestion**  
To ingest the CSV dataset into Elasticsearch, we used **Logstash**. The pipeline processes data, converts necessary columns, and pushes the clean data into the `energy` index in Elasticsearch.

### **Logstash Configuration File Explanation**  

Here is the `logstash.conf` file used in this project:

```plaintext
input {
  file {
    path => "/home/ashok/Documents/elk/5.streaming_data/sample/sample_main.csv"  
    start_position => "beginning"  
    sincedb_path => "/dev/null"  
  }
}

filter {
  csv {
      separator => ","
      skip_header => "true"
      columns => ["country","country_long","name","gppd_idnr","capacity_mw","latitude","longitude","primary_fuel","source","geolocation_source","estimated_generation_gwh_2013","estimated_generation_gwh_2014","estimated_generation_gwh_2015","estimated_generation_gwh_2016","estimated_generation_gwh_2017","estimated_generation_note_2013","estimated_generation_note_2014","estimated_generation_note_2015","estimated_generation_note_2016","estimated_generation_note_2017"]
  }

  mutate {
        convert => { 
                     "capacity_mw" => "float"
                     "latitude" => "float"
                     "longitude" => "float"
                     "estimated_generation_gwh_2013" => "float"
                     "estimated_generation_gwh_2014" => "float"
                     "estimated_generation_gwh_2015" => "float"
                     "estimated_generation_gwh_2016" => "float"
                     "estimated_generation_gwh_2017" => "float"
                   }
        remove_field => ["message","path","host","@version"]
  }
}

output {
   elasticsearch {
     hosts => "http://localhost:9200"  
     index => "energy"  
  }
  stdout {}  
}
```

#### **Code Breakdown**:
- **Input Section**:  
   - Reads the dataset file from the specified path.  
   - `start_position => "beginning"` ensures the file is read from the start.  
   - `sincedb_path => "/dev/null"` disables tracking file position to prevent skipping records.  

- **Filter Section**:  
   - **`csv` filter**: Parses the CSV file, defining each column explicitly.  
   - **`mutate` filter**: Converts relevant fields (e.g., `capacity_mw` and `generation_gwh`) to `float` for accurate querying.  
   - Unnecessary fields (e.g., `path`, `message`) are removed for cleaner indexing.  

- **Output Section**:  
   - Sends the processed data to the Elasticsearch index named `energy`.  
   - Outputs data to the console (`stdout`) for debugging purposes.

---

### **3. Visualization in Kibana**  
Once the data is ingested into Elasticsearch, we created visualizations using **Kibana**. Each visualization focuses on **different insights** from the dataset.

---

### **4. Dashboard Development**  
The visualizations were organized into a **comprehensive Kibana dashboard**. The key components of the dashboard are:

---


## **Dashboard Insights**

The dashboard consists of multiple visualizations, each highlighting specific aspects of global power generation. Below are detailed descriptions and insights for each graph.

---

![ELK_Dashboard](https://github.com/user-attachments/assets/53947b1c-b74e-4a41-8762-a017899c09f4)

---

## Video


https://github.com/user-attachments/assets/e9b79018-8290-4c2e-b274-4e2fa1617589



### **1. Average Production in 2013, 2014, 2015, 2016, and 2017**  
- **Graph Type**: Metric (Top Row)  
- **Insights**:  
   - Average energy production **dropped slightly** between 2013 (207.797 GWh) and 2014 (203.228 GWh).  
   - Energy production remained steady through **2015 (204.477 GWh)** and **2016 (207.465 GWh)** but declined again in **2017 (197.738 GWh)**.  
   - This suggests potential **underutilization** of power plants or issues like aging infrastructure, fuel shortages, or policy changes.  

![Screenshot 2024-12-18 145810](https://github.com/user-attachments/assets/27f94acf-707c-472c-8d8b-ae8630bb1ec4)

![Screenshot 2024-12-18 150943](https://github.com/user-attachments/assets/d1041485-4aef-41e8-9e5d-8562d39b8863)
---

### **2. Average Capacity by Fuel Type Across Countries**  
- **Graph Type**: Bar Charts (Middle Row - Solar, Hydro, Wind)  
- **Insights**:  
   - **Solar Energy**: Kazakhstan and South Africa lead in solar power capacity. China and Brazil follow closely, while other countries have relatively lower contributions.  
   - **Hydro Energy**: Taiwan dominates in average hydro power capacity, followed by Italy and South Korea.  
   - **Wind Energy**: The United States of America leads, followed by Spain and the Netherlands.  
   - **Key Insight**:  
     - Countries like **Taiwan** (Hydro) and **USA** (Wind) rely heavily on specific renewable sources for their power needs.  
     - Renewable energy investments vary significantly by country.
    
![Screenshot 2024-12-17 134248](https://github.com/user-attachments/assets/da8871ea-e362-4dff-800f-162bb6cf9d32)

![image](https://github.com/user-attachments/assets/df9721ad-d0f5-446e-a729-0ac36ea2514a)


---

### **3. Top Generating Power Plants by Country**  
- **Graph Type**: Table (Middle Row)  
- **Insights**:  
   - The **highest generating power plants** are located in **Canada** with a capacity of **5,428 MW** (Churchill Falls).  
   - Other top plants include **Vietnam (2,400 MW)** and **Brazil (1,710 MW)**.  
   - Taiwan, China, and Indonesia also feature plants with significant capacities.  
   - **Key Insight**: Large-scale power plants are concentrated in North America and Asia, reflecting regional energy demands.

![image](https://github.com/user-attachments/assets/5f1533dc-cf4f-4058-90cf-910d02dc5ea4)

![image](https://github.com/user-attachments/assets/51f53bd4-7fa2-40f9-b208-868013e3b374)


---

### **4. Share of Power Generation by Country in 2015**  
- **Graph Type**: Pie Chart (Lower Row - Left)  
- **Insights**:  
   - **Taiwan** accounts for the **largest share (36.83%)** of global power generation in 2015.  
   - Ethiopia, Canada, and Colombia also contribute significantly with shares of **18.15%, 15.15%, and 14.58%** respectively.  
   - **Key Insight**:  
     - Taiwan's dominance may stem from its investment in large hydro power plants, ensuring consistent energy output.  
     - Countries like Ethiopia have significant shares despite lower infrastructure, highlighting a focus on renewable energy.

![image](https://github.com/user-attachments/assets/93e30898-c48f-4b04-a166-04570944e101)


---

### **5. Average Power Generated by Top 3 Fuel Types**  
- **Graph Type**: Bar Chart (Lower Row - Right)  
- **Insights**:  
   - **Hydro** is the leading fuel type for power generation, producing over **2,500 GWh**.  
   - **Wind and Solar** follow, but with significantly lower contributions.  
   - **Key Insight**:  
     - Hydro remains the most dominant fuel type globally.  
     - Investments in solar and wind need to be accelerated to compete with hydro energy output.  


![image](https://github.com/user-attachments/assets/1cda1918-e0a8-4acd-82f5-649c4a8ae833)

---

### **6. Fuel Type Distribution**  
- **Graph Type**: Donut Chart (Lower Row - Left Center)  
- **Insights**:  
   - **Solar** leads the global fuel type distribution with a **49.37%** share.  
   - **Hydro** follows with a **40.75%** contribution.  
   - **Wind energy** holds a **9.87%** share.  
   - **Key Insight**:  
     - Solar energy has surpassed other renewables, signaling a major shift toward **solar power generation** worldwide.  
     - Hydro remains a reliable source but may face geographical limitations.

![image](https://github.com/user-attachments/assets/b1ce19eb-a198-4723-8a27-3197ee8025e8)


---

### **7. Power Plant Capacity by Country**  
- **Graph Type**: Line Chart (Lower Row - Right)  
- **Insights**:  
   - Countries like **Taiwan** and **Ethiopia** have the **highest median plant capacities**, indicating large-scale infrastructure.  
   - Capacity decreases for other countries, suggesting smaller, decentralized energy facilities.  
   - **Key Insight**:  
     - Countries with larger plant capacities (e.g., Taiwan) are better positioned to meet energy demands.
    
![image](https://github.com/user-attachments/assets/a09d44c1-2e9e-4b91-baec-e866f4c6f64d)


---

### **8. Geographic Energy Production Insights**  
- **Graph Type**: World Maps (Bottom Row)  
- **Insights**:  
   - **Maximum Capacity by Country**:  
     - North America and parts of Asia lead in maximum energy production capacity.  
     - Africa and South America show lower energy production, indicating gaps in infrastructure.  
   - **Average Energy Production in 2013 vs 2017**:  
     - While North America maintains high production, some regions in Africa and Europe display **declining trends** between 2013 and 2017.  
   - **Key Insight**:  
     - Geographic disparities exist, with developing regions (e.g., Africa) lagging behind in energy production.  


![Screenshot 2024-12-18 150647](https://github.com/user-attachments/assets/cf51bc05-72e6-4e7f-9409-9e828eae0658)

![Screenshot 2024-12-17 134002](https://github.com/user-attachments/assets/064a1285-b3a2-48af-9890-4b53d344de4f)


---

## **Managerial Insights**  

1. **Global Energy Transition**:  
   - Solar energy now leads global fuel distribution, indicating a shift toward renewables.  
   - Hydro remains dominant but is geographically constrained.

2. **Production Decline**:  
   - Average energy production declined from 207.8 GWh in 2013 to 197.7 GWh in 2017. This suggests potential **underutilization** or aging infrastructure.

3. **Geographic Inequality**:  
   - Countries in **Africa** and parts of **Europe** face energy shortages due to lower infrastructure capacities.  
   - North America, Taiwan, and Brazil lead in production capacities, showcasing better energy planning.

4. **Top Generating Countries**:  
   - Taiwan, Canada, and Ethiopia emerge as leaders in power generation. Investments in large plants ensure higher outputs.  

5. **Focus on Renewables**:  
   - Wind and solar energy require **accelerated investments** to diversify fuel dependencies and reduce reliance on hydro energy.

---

## **Learning Outcomes**

1. **Building Data Pipelines**:  
   - Gained experience in developing data pipelines using **Logstash** for structured data ingestion.  

2. **Visualization with Kibana**:  
   - Learned to build **interactive dashboards** using Kibana for actionable insights.

3. **Energy Insights**:  
   - Analyzed trends in renewable energy adoption (solar and wind) and geographic production disparities.

4. **Data Transformation**:  
   - Used Logstash filters to clean and transform data for Elasticsearch ingestion.

5. **Real-World Applications**:  
   - Learned how energy production data can help policymakers identify investment priorities.

---

## **Conclusion**  

This project provides a detailed understanding of global power generation trends, highlighting the shift toward **solar energy**, regional inequalities, and the need for infrastructure improvements. The interactive ELK dashboard allows decision-makers to:  

- Monitor energy production trends.  
- Identify underperforming regions or fuel types.  
- Make informed decisions on energy investments and sustainability efforts.  

---
