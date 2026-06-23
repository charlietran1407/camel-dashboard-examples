# RSS to Database Example

This is a beginner-friendly Apache Camel example that demonstrates how to consume RSS feeds using the `rss` component, sort and process them using `groovy` expressions, split the articles, and save them into a database using the `sql` component.

> [!TIP]
> You can visually design and orchestrate this route (or other complex integrations) using **[Kaoto](https://kaoto.io/)**, the visual designer for Apache Camel, before deploying it here.

## Prerequisites

### 1. Database Table Creation
Ensure the target database has the following table structure before running the route (see [1.init_database.sql](./1.init_database.sql)):

```sql
CREATE TABLE world_news (
    id SERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    link TEXT NOT NULL,
    pub_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    description TEXT, 
    source TEXT
);
```

### 2. Configure DataSource Bean
Ensure your Camel integration environment has a pre-configured DataSource bean named `newsWorldDb` pointing to your target database.

## Deployment Workflow using Camel Dashboard

You can deploy and monitor this route dynamically through the Camel Dashboard UI using the following steps:

### Step 1: Create a Service
1. Go to the **Services** view/page in the sidebar navigation.
2. In the **Add service** card, enter the **Service name** (e.g., `rss-to-db-service`) and a description.
3. Click the **Save service** button. The new service will appear in the **Services** table.

### Step 2: Upload the Route to your Service
1. Go to the **Upload** view/page in the sidebar navigation.
2. Select your newly created service (`rss-to-db-service`) from the **Services** dropdown.
3. Drag & drop the [rss-to-db.camel.yaml](./rss-to-db.camel.yaml) file into the dropzone, or click to choose/browse it from your device.
4. Click the **Upload all** button.

### Step 3: Deploy the Route Version
1. Go to the **Deploy** view/page in the sidebar navigation.
2. Expand your service name `rss-to-db-service` in the Accordion panel.
3. Click the **Deploy** button (paper airplane icon) next to the `rss-to-db.camel.yaml` file (e.g., `v1`).

---

## Technical Details

### 1. Polling RSS Feeds
The route polls the target RSS feed at regular intervals using the `rss` component:
* `feedUri`: The URL of the RSS feed (e.g., `https://tuoitre.vn/rss/the-gioi.rss`).
* `splitEntries`: Set to `false` so the entire feed is received as a single message body containing all entries, allowing us to perform sorting and filtering globally before processing.

### 2. Sorting & Selecting Entries
A `groovy` expression is used to sort all entries by their publication date in descending order (newest first) and take only the top 5 articles:
```groovy
request.body.entries.sort { a, b -> b.publishedDate <=> a.publishedDate }.take(5)
```

> [!WARNING]
> ROME (the library behind Camel RSS component) parses dates strictly. If the RSS feed uses a non-standard date format (not matching RFC 822/W3C, or containing special locale spaces like narrow no-break space `\u202f`), `publishedDate` will be evaluated as `null`. When `publishedDate` is null, the sorting comparator will evaluate to equal (`0`) and the database will insert the default `CURRENT_TIMESTAMP`.

### 3. Splitting and Saving to Database
* The route splits the sorted list of 5 entries and handles each entry individually.
* Title, link, publication date, and description are extracted into exchange headers.
* The `sql` component executes an insert query to store the articles into the `world_news` table.
