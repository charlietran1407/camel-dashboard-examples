# Weather Forecast Integration Example

This is a beginner-friendly Apache Camel example that demonstrates how to fetch current weather data from a public REST API (Open-Meteo), parse the JSON response using `jsonpath`, and log a formatted report.

> [!TIP]
> You can visually design and orchestrate this route (or other complex integrations) using **[Kaoto](https://kaoto.io/)**, the visual designer for Apache Camel, before deploying it here.

## Deployment Workflow using Camel Dashboard

You can deploy and monitor this route dynamically through the Camel Dashboard UI using the following steps:

### Step 1: Create a Service
1. Go to the **Services** view/page in the sidebar navigation.
2. In the **Add service** card, enter the **Service name** (e.g., `weather-forecast-service`) and a description.
3. Click the **Save service** button. The new service will appear in the **Services** table.

### Step 2: Upload the Route to your Service
1. Go to the **Upload** view/page in the sidebar navigation.
2. Select your newly created service (`weather-forecast-service`) from the **Services** dropdown.
3. Drag & drop the [weather.camel.yaml](./weather.camel.yaml) file into the dropzone, or click to choose/browse it from your device.
4. Click the **Upload all** button.

### Step 3: Deploy the Route Version
1. Go to the **Deploy** view/page in the sidebar navigation.
2. Expand your service name `weather-forecast-service` in the Accordion panel.
3. Click the **Deploy** button (paper airplane icon) next to the `weather.camel.yaml` file (e.g., `v1`).

### Step 4: Verify the Execution
Since this route is triggered automatically by a `timer` component when it starts, you do not need to send any external requests to test it.

1. Go to the **Routes** page in the sidebar navigation to confirm that your route `weather-demo` is listed as **Running**.
2. Check your backend console logs. You should see the formatted weather report for Hanoi printed in the output console:
   ```text
   Current Weather Report for Hanoi:
   - Temperature: 28.5 °C
   - Humidity: 82 %
   ```

---

## Technical Details

### 1. Route Trigger
The route is triggered by a `timer` component:
* `timerName`: `weatherTimer`
* `delay`: `1000` (fires 1 second after route start)
* `repeatCount`: `1` (runs only once)

### 2. HTTP Request
The route calls a public weather API using the `http` component:
* **Endpoint**: `http://api.open-meteo.com/v1/forecast?latitude=21.0245&longitude=105.8412&current=temperature_2m,relative_humidity_2m&timezone=Asia/Bangkok`
* This requests the current temperature and relative humidity for Hanoi, Vietnam (latitude `21.0245`, longitude `105.8412`).

### 3. Parsing JSON via JSONPath
* `convertBodyTo`: Converts the response body to `java.lang.String` to allow string-based processors/expressions.
* `setHeader`:
  * Extracts the current temperature using JSONPath: `$.current.temperature_2m`
  * Extracts the relative humidity using JSONPath: `$.current.relative_humidity_2m`

### 4. Setting & Logging Body
* A formatted multi-line string is set as the message body using the `simple` language:
  ```text
  Current Weather Report for Hanoi:
  - Temperature: ${header.temp} °C
  - Humidity: ${header.humid} %
  ```
* Finally, the body is logged using the `log` step.
