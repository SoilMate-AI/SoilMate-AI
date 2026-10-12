# SoilMate AI

SoilMate AI is an end-to-end Smart Agriculture SaaS solution and hybrid artificial intelligence architecture designed to combat inefficient resource usage and yield loss in farming. By combining real-time climate data, satellite vegetation indices, and localized soil characteristics, the system replaces traditional guesswork with data-driven precision optimized for diverse agricultural micro-climates.

Built natively on the **Huawei Cloud** ecosystem and the **MindSpore** deep learning framework, SoilMate AI provides long-term projections and dynamic risk management for smart farming.

### 🏆 Achievements

* **Huawei Developer Competition 2025:** 🥇 First Place in Europe (Winner)
* **Technology Partner:** Developed as part of the Huawei Cloud & MindSpore Ecosystem

---

## 🚀 Project Architecture & Features

SoilMate AI utilizes a robust hybrid infrastructure, splitting responsibilities between edge-level data simulation, automated satellite data extraction, and a high-availability cloud backend.

### 1. Data Acquisition & Processing Pipeline (Google Earth Engine)

The system leverages an automated Google Earth Engine (GEE) pipeline to extract and synthesize rich agricultural data in 4-year chunks from 2000 to 2026. Data extraction is specifically targeted at high-cropland areas (>50% crop share via ESA WorldCover 2021 mask) in the Çukurova region, including Adana Center, Ceyhan, İmamoğlu, Kozan, Tarsus, and Osmaniye.

* **Meteorological Data:** Extracts daily metrics from ERA5-Land (temperature, dewpoint, precipitation, solar radiation, evaporation, and multi-layer soil water), supplemented by CHIRPS precipitation data.


* **Vegetation & Thermal Indices:** Utilizes MODIS imagery to compute NDVI/EVI (MOD13Q1), Land Surface Temperature (MOD11A2), and NDWI (MOD09A1).


* **Static Topographic & Soil Data:** Integrates elevation and slope from USGS SRTM, alongside clay, sand, and pH levels from SoilGrids.


* **Flexible Time-Series Resampling:** The pipeline cleans, interpolates, and resamples the aggregated daily data into dynamic 3, 5, 7, 10, 14, 15, 20, and 30-day (monthly) periods. It also automatically generates cyclical calendar features (`doy_sin`, `doy_cos`) to help the neural networks learn seasonal patterns.



### 2. Cloud Infrastructure (Huawei Cloud)

* **Edge Layer:** In-field data is modeled using a Python simulation (`Sensor_Adana_01`) generating real-time metrics (N, P, K, pH, Moisture).
* **Ingestion (IoTDA):** Sensor data is uploaded via MQTT to Huawei Cloud IoTDA for secure device management and real-time ingestion.
* **Storage (OBS):** Huawei OBS serves as the central data lake, securely storing the extracted master datasets, grouped CSVs, and trained AI model weights.
* **AI Training (ModelArts):** Distributed training of the MindSpore models is conducted in ModelArts, leveraging cloud resources only when needed to optimize inference costs.
* **Application Backend (ECS):** The core compute engine and Streamlit web dashboard are hosted on Elastic Cloud Server (ECS), ensuring 24/7 availability.

### 3. AI Model Architecture

SoilMate AI uses a multi-faceted deep learning approach to process environmental inputs and produce actionable forecasting:

* **HybridModelGen:** A specialized deep learning structure designed for complex temporal patterns: `Bi-Directional LSTM (64 units) -> Dropout (20%) -> Unidirectional LSTM (32 units) -> Dense layers`. It predicts core climatic shifts including Temperature (T), Rainfall (R), and Humidity (H).
* **HybridModelNDVI:** An LSTM-based model (16 units) dedicated to predicting and monitoring vegetation health via NDVI indices.

### 4. Cloud Security & Observability

* **Defense in Depth:** VPC and Security Groups minimize the attack surface by strictly blocking all ports except Port 8501 (Dashboard) and Port 22 (SSH).
* **IAM & Least Privilege:** Root accounts are strictly prohibited; API communication relies on temporary AK/SK credentials.
* **Host Security (HSS):** Continuous vulnerability scanning and brute-force attack detection protect the ECS instances.
* **Proactive Monitoring:** Cloud Eye provides real-time health monitoring, triggering autonomous SMS/Mail alerts if compute usage exceeds 80%.

---

## 📁 Project Structure

The repository is organized to separate raw data extraction, model training, and application logic:

```text
SoilMate-AI/
│
├── SoilMate_Export/                 # Generated via the GEE Data Pipeline
│   ├── Master Data/                 # Raw and merged master CSVs across all sites
│   └── Sample Data/                 # Resampled datasets (3, 5, 7, 10, 14, 15, 20, 30-day)
├── models/                          # Trained LSTM weights and model checkpoints
├── notebooks/                       # Google Colab and Jupyter analysis notebooks
├── src/                             # Backend Python logic and MQTT simulation scripts
└── README.md                        # Project documentation

```

---

## 📊 Performance & Metrics

The system's AI modules are evaluated on dynamic agricultural metrics (temperature, vegetation indices like NDVI/EVI, and soil moisture) using **R² (R-squared), MSE, and MAE**. The system automatically backtests to determine the optimal grouping period (e.g., 7-day vs. 15-day) for forecasting specific agricultural parameters across the 7 distinct micro-climate regions of Turkey.

---

## ⚙️ Installation & Usage

**1. Clone the Repository:**

```bash
git clone https://github.com/berkayilikoba/SoilMate-AI.git
cd SoilMate-AI

```

**2. Data Extraction via Google Colab:**

* Upload the `notebooks/` directory to your Google Drive.
* Open the data acquisition notebook, authenticate your Google Earth Engine account (`ee.Authenticate()`), and execute the cells to generate the regional datasets.


* Move the generated `SoilMate_Export.zip` contents into the project directory.



**3. Run the Dashboard:**

* Configure your Huawei Cloud AK/SK keys within your environment variables.
* Run the Streamlit web application on your local machine or ECS instance.

---

## 🌐 Demo & Live Dashboard

* **Demo Site:** [http://188.239.52.182:8501/](http://188.239.52.182:8501/)
* **Demo Video:** [Insert Link]

## 👥 Team Cukurova

* **Nisa GÜVELOĞLU** — Team Leader & IoT Integration
* **Akın KAFADAR** — Cloud Architect
* **Berkay ILIKOBA** — AI Architect

---

*🏆 **License & Copyright:** This project belongs to the SoilMate AI development team. All rights reserved; protected as a private repository.*
