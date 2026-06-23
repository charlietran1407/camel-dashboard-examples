# Barcode Generator Example

This is a beginner-friendly Apache Camel example that demonstrates how to expose a REST POST endpoint using REST DSL, process a JSON payload, and generate a QR Code barcode image in PNG format using the `barcode` component in Camel YAML DSL.

> [!TIP]
> You can visually design and orchestrate this route (or other complex integrations) using **[Kaoto](https://kaoto.io/)**, the visual designer for Apache Camel, before deploying it here.

## Deployment Workflow using Camel Dashboard

You can deploy and test this route dynamically through the Camel Dashboard UI using the following steps:

### Step 1: Create a Service
1. Go to the **Services** view/page in the sidebar navigation.
2. In the **Add service** card, enter the **Service name** (e.g., `barcode-service`) and a description.
3. Click the **Save service** button. The new service will appear in the **Services** table.

### Step 2: Upload the Route to your Service
1. Go to the **Upload** view/page in the sidebar navigation.
2. Select your newly created service (`barcode-service`) from the **Services** dropdown.
3. Drag & drop the [barcode.camel.yaml](./barcode.camel.yaml) file into the dropzone (labeled *Drop .yaml/.yml files here*), or click to choose/browse it from your device.
4. Click the **Upload all** button. You should see a success toast, and the version will be saved under the history table.

### Step 3: Deploy the Route Version
1. Go to the **Deploy** view/page in the sidebar navigation.
2. Filter or select your service (`barcode-service`) to see its versions.
3. Expand your service name in the Accordion panel.
4. Click the **Deploy** button (paper airplane icon) next to the `barcode.camel.yaml` file (e.g., `v1`).
5. The application will run a static pre-deployment check. Once it passes, it will show a success notification and the version status will update to **Deployed**.

### Step 4: Testing the Endpoint
Once successfully deployed, the REST service exposes a POST endpoint at `(cameldash)/barcode`.

Send an HTTP POST request to:
`http://localhost:8080/(cameldash)/barcode`

**Headers:**
`Content-Type: application/json`

**Body:**
```json
{
  "data": "https://camel.apache.org"
}
```

**Response (200 OK):**
The response will be a PNG image file containing the generated QR Code representing the text `"https://camel.apache.org"`.
