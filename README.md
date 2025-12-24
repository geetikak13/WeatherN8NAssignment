# Daily Weather Automation & Logging System

## Project Overview

This **n8n workflow** serves as an automated pipeline for daily weather monitoring and data persistence . Every 24 hours, the system iterates through a list of configurable cities, fetches real-time weather metrics, evaluates potential weather alerts (Heat, Frost, and Precipitation), logs the records to a Supabase database, and sends a summarized report via email .

---

## 1. Prerequisites & API Setup

OpenWeatherMap (Weather Data) 

1. **Sign up**: Create a free account at [OpenWeatherMap](https://openweathermap.org/).
2. **API Key**: Navigate to the "API Keys" section and generate a new key.
3. **Activation**: Note that new API keys can take up to 2 hours to activate.

Supabase (Database) 
1. **Project**: Create a new project at [Supabase.com](https://supabase.com/).
2. **Table Creation**: Open the **SQL Editor** and run the following script to create the `weather_logs` table with standard and additional metrics :

```sql
CREATE TABLE weather_logs (
  id SERIAL PRIMARY KEY,
  run_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  city TEXT NOT NULL,
  temperature FLOAT,
  feels_like FLOAT,
  pressure INT,
  temperature_unit TEXT DEFAULT 'Celsius',
  condition TEXT,
  humidity INT,
  wind_speed FLOAT,
  alert_type TEXT,
  raw_response JSONB
);

```

3. **Credentials**: Obtain your **Project URL** and **API Key** from **Project Settings > API**.
Email Configuration 

1. **SMTP/Gmail**: Ensure you have an email service n8n can connect to.
2. **Setup**: If using Gmail, generate an **App Password** for the n8n integration.
---

## 2. Functional Features
Multi-City Looping (Bonus) 
The workflow utilizes a **Code Node** at the start to define an array of cities. Because n8n processes items individually, the workflow automatically loops through every city in the configuration, ensuring independent logging and alerting for each city.

Alert Logic Rules 
A centralized processing node evaluates the weather data against the following criteria:
* **Precipitation Alert**: Triggered if the weather condition includes rain, snow, drizzle, storm, or thunder.
* **Heat Alert**: Triggered if the temperature exceeds  ().
* **Frost Alert**: Triggered if the temperature falls below  ().
* **None**: Default state if no thresholds are met.

Advanced Metrics (Bonus) 
The system captures additional data points beyond the standard requirements to provide deeper context:
* **Feels Like**: Apparent temperature based on humidity and wind.
* **Pressure**: Atmospheric pressure at sea level.

**Resilience (Bonus)**

To ensure high availability and handle transient network flickers, **Retry Logic** is enabled on the OpenWeatherMap, Supabase, and Email nodes. Each node is configured for **3 retries** with a **1000ms delay** between attempts.

---

## 3. How to Import and Run 
1. **Import**: In n8n, click the **three dots menu** and select **Import from File**. Upload the provided `.json` file.
2. **Configure Credentials**:
* Edit the **OpenWeatherMap Node** and select/add your API key credentials.
* Edit the **Supabase Node** and input your project URL and API Key.
* Edit the **Email Node** with your SMTP or service details.
3. **Schedule**: The **Cron Trigger** is set to run daily at a fixed time (default 8:00 AM) .
4. **Test**: Click **Execute Workflow** to verify that the data flows correctly into the Supabase `weather_logs` table and that an email is delivered to your inbox.
---

## 4. Deliverables Summary 
* **Workflow JSON**: `weather_automation_final.json` 
* **README**: This documentation file 
* **Screenshot**: A visual snapshot of the n8n canvas showing the node connections 
