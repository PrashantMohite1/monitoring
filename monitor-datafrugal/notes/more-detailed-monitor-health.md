To monitor whether your application is **up and running**, you can use a combination of **health checks**, **Prometheus metrics**, and **Grafana alerts**. Here’s how you can set up each part to determine the status of your application.

### 1. **Health Checks in Your Application**

To ensure that your application is **up**, you need to expose **health check endpoints**. These health checks will allow Prometheus (and any monitoring system) to verify if the application is alive.

#### a. **Spring Boot Health Check**

Spring Boot provides a built-in `health` endpoint as part of its **Actuator** module, which can give you information about the health of your application, such as database connectivity, memory usage, and more.

1. Add the **Spring Boot Actuator** dependency to your `pom.xml` (for Maven) or `build.gradle` (for Gradle) if it’s not already added:

   **Maven:**
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   ```

   **Gradle:**
   ```groovy
   implementation 'org.springframework.boot:spring-boot-starter-actuator'
   ```

2. Enable the health check endpoint in `application.properties` or `application.yml`:
   ```properties
   management.endpoints.web.exposure.include=health
   ```

   This will expose the health check endpoint at `/actuator/health`, which you can query to get the status of the application.

3. Customize the health check if needed (e.g., add checks for database, messaging queues, etc.) in your application:
   ```properties
   management.health.db.enabled=true
   management.health.diskspace.enabled=true
   ```

Now, the health check can be accessed via:
```
http://<your-spring-boot-app>:8080/actuator/health
```

It will return a status of `UP` if everything is healthy, or `DOWN` if there’s a problem.

#### b. **Keycloak Health Check**

Keycloak also provides a health check endpoint. You can access it via:
```
http://<keycloak-server>:8080/realms/master/protocol/openid-connect/token
```

Alternatively, you can use:
```
http://<keycloak-server>:8080/auth/realms/master/health
```

This endpoint will return a `200 OK` status if Keycloak is running properly.

#### c. **React (Frontend) Health Check**

For the React frontend, you can create a simple health check by making an HTTP request to a known endpoint or use **window.performance** to measure load times.

If you have an API endpoint from your backend, you can query it and check if it responds. For example, in a React app, you could have:
```javascript
fetch('/api/health')
  .then(response => response.json())
  .then(data => {
    if (data.status === 'UP') {
      console.log('Frontend is healthy');
    }
  })
  .catch(error => console.log('Error: ', error));
```

You can even create a custom health endpoint in the backend that responds with a simple JSON object like `{ "status": "UP" }`.

---

### 2. **Scrape Health Checks with Prometheus**

Prometheus can scrape these health endpoints to track the status of your services.

#### a. **Configure Prometheus to Scrape Health Endpoints**

Add your health check endpoints to the `prometheus.yml` file so Prometheus can scrape them periodically.

```yaml
scrape_configs:
  - job_name: 'spring_boot_health'
    static_configs:
      - targets: ['<spring-boot-url>:8080/actuator/health']

  - job_name: 'keycloak_health'
    static_configs:
      - targets: ['<keycloak-url>:8080/auth/realms/master/health']

  - job_name: 'frontend_health'
    static_configs:
      - targets: ['<frontend-url>/health']  # If your frontend exposes a health check
```

Prometheus will now scrape the health status of all these services periodically.

---

### 3. **Setting Up Alerts in Grafana**

Now that Prometheus is scraping health check data, you can create **alerts** in Grafana to notify you when your application is **down**.

#### a. **Create a Grafana Dashboard**

1. Open Grafana (`http://<grafana-url>:3000`).
2. Create a new dashboard or open an existing one.
3. Add a **Panel** with a **Prometheus Query**.

For example, to check if the Spring Boot service is **UP**, use the following query in Grafana:
```prometheus
up{job="spring_boot_health"} == 0
```

This query checks if the health check for the Spring Boot service is down (`0` indicates an unhealthy state). If it’s `1`, it means the service is healthy.

#### b. **Set Up Alerts in Grafana**

1. In your Grafana panel, go to the **Alert** tab.
2. Click **Create Alert**.
3. Set the condition to trigger when the service is down:
   - Set the **Evaluate every** field (e.g., every 1 minute).
   - Set the **For** field to ensure the alert is triggered after the condition is sustained (e.g., 1 minute).
   - Set the condition to be `up{job="spring_boot_health"} == 0` for the health check query.
4. Set up your **Alert Notifications** (e.g., Slack, Email, etc.).

You can do the same for **Keycloak** and **React** health checks, by using similar queries:
```prometheus
up{job="keycloak_health"} == 0
up{job="frontend_health"} == 0
```

Now, Grafana will send you an alert whenever any of your services (Spring Boot, Keycloak, or React) is **down**.

---

### 4. **(Optional) Use Blackbox Exporter for More Complex Monitoring**

For more advanced health checks (e.g., checking if a page loads properly or if specific APIs are reachable), you can use **Prometheus Blackbox Exporter**.

- Set up the **Blackbox Exporter** to ping HTTP endpoints and check if they respond correctly.
- Add the Blackbox Exporter as a target in `prometheus.yml`:
  ```yaml
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
        - http://<spring-boot-url>/actuator/health
        - http://<keycloak-url>/auth/realms/master/health
    relabel_configs:
      - source_labels: [__param_target]
        target_label: target
      - target_label: __param_module
        replacement: http_2xx
      - target_label: job
        replacement: blackbox
  ```

Blackbox Exporter will try to probe the given targets and give you more detailed results on HTTP response codes, latency, etc.

---

### Summary

To monitor if your application is up:
- **Health Checks**: Expose `/actuator/health` for Spring Boot, `/health` for Keycloak, and a custom health check for your React frontend.
- **Prometheus**: Configure Prometheus to scrape these health endpoints.
- **Grafana**: Create dashboards and alerts to monitor the status of the services and be notified when any service goes down.

By using health checks, Prometheus scraping, and Grafana alerts, you'll be able to effectively monitor the health and uptime of your application.