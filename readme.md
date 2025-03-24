

node_exporter - is needs to be installed on machine which we want to monitor

graphana dashboards 

Great! Since you’ve installed **Prometheus** and are now looking to visualize its collected metrics in Grafana dashboards, here’s a step-by-step guide to set it up:

### Step 1: Add Prometheus as a Data Source in Grafana
1. **Log into Grafana**:
   - Open Grafana in your browser (usually `http://localhost:3000`).
   - Log in using your credentials (default is `admin`/`admin`).

2. **Add Prometheus Data Source**:
   - In Grafana, go to the left sidebar and click on the **gear icon (Configuration)**.
   - Click **Data Sources** under the Configuration menu.
   - Click **Add data source**.
   - From the list of available data sources, choose **Prometheus**.

3. **Configure the Prometheus Data Source**:
   - In the Prometheus settings page, enter the URL of your Prometheus server. If Prometheus is running on the same machine as Grafana, the URL will typically be `http://localhost:9090`.
   - Click **Save & Test** to verify the connection between Grafana and Prometheus. If the connection is successful, you'll see a green "Data source is working" message.


To import a pre-configured dashboard in Grafana, you can follow these steps. This is especially useful when you want to quickly set up a dashboard for Prometheus or another data source without having to manually create it from scratch.

### Step-by-Step Guide to Import a Dashboard in Grafana

#### Step 1: Get the Dashboard JSON or ID
1. **Find a pre-built dashboard**:
   - Grafana has a large collection of **public dashboards** for various data sources, including Prometheus. You can explore and download them from the **Grafana Dashboards Repository** at [https://grafana.com/grafana/dashboards](https://grafana.com/grafana/dashboards).
   - You can search for dashboards by data source or specific metrics. For example, searching for **Prometheus** will show dashboards specifically designed for Prometheus metrics.
   - Once you find a dashboard you like, note the **Dashboard ID** or **download the JSON file**.

   Example: Dashboard ID for **Prometheus Node Exporter Full**: `1860` (this is a popular Prometheus + Node Exporter dashboard).

#### Step 2: Import the Dashboard in Grafana
1. **Log into Grafana**:
   - Open your Grafana instance in a web browser (e.g., `http://localhost:3000`) and log in.

2. **Go to the Import Page**:
   - In the left sidebar, click on the **"+"** icon and then select **Import**.

3. **Enter the Dashboard ID or JSON**:
   - **Option 1: Import by Dashboard ID**:
     - In the **Import via grafana.com** section, type or paste the **Dashboard ID** (e.g., `1860` for Prometheus Node Exporter Full).
     - Grafana will automatically fetch the dashboard configuration from the Grafana repository.
   - **Option 2: Import via JSON file**:
     - If you’ve downloaded the dashboard JSON file (you can do this from the Grafana dashboard repository), click **Upload .json File**.
     - Select the **JSON file** you downloaded from your local machine.

4. **Configure Data Source**:
   - After you provide the Dashboard ID or upload the JSON, Grafana will ask you to select a data source. For example, if you’re using **Prometheus**, ensure that Prometheus is selected as the data source.
   - If the dashboard was designed with a different data source, you'll need to make sure your data source is correctly configured in Grafana.

5. **Import the Dashboard**:
   - After selecting the data source, click the **Import** button to load the dashboard.

#### Step 3: View the Imported Dashboard
1. **Go to your Dashboard**:
   - After the dashboard is imported, Grafana will redirect you to the newly created dashboard. You can now see the panels and metrics being pulled from Prometheus (or your configured data source).

2. **Customize the Dashboard** (Optional):
   - If needed, you can tweak the panels, queries, and visualization settings according to your requirements.

