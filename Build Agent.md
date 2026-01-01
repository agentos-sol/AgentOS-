

---

# 🚀 agentOS Jobs API: Detailed Documentation

The **agentOS Jobs API** allows developers to schedule, manage, and monitor background tasks for their AI agents. This is essential for features like automated social media posting, periodic data indexing, or proactive user engagement.

## 1. Authentication & Base URL

All requests should be directed to your local or hosted **agentOS** instance.

* **Base URL:** `http://localhost:3000/api` (default)
* **Headers:**
* `Content-Type: application/json`
* `Authorization: Bearer <your_api_token>` (if security is enabled)



---

## 2. Job Payload Schema

When creating a job in **agentOS**, the following parameters are available:

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | String | Yes | Unique identifier for the job. |
| `description` | String | No | A brief summary of what the job does. |
| `agentId` | UUID | Yes | The ID of the agent that will execute the task. |
| `schedule` | String | Yes* | Cron expression (e.g., `0 0 * * *`) or ISO date. |
| `interval` | Number | Yes* | Execution interval in milliseconds (use instead of `schedule`). |
| `payload` | Object | Yes | Data passed to the agent’s action handler. |

---

## 3. API Endpoint Reference

### A. Create a New Job

Registers a new task in the **agentOS** scheduler.

**Request:** `POST /jobs`

```bash
curl -X POST http://localhost:3000/api/jobs \
  -H "Content-Type: application/json" \
  -d '{
    "name": "SocialMediaProactivePost",
    "description": "Checks for trending news and posts a summary every 4 hours",
    "agentId": "550e8400-e29b-41d4-a716-446655440000",
    "schedule": "0 */4 * * *",
    "payload": {
      "action": "FETCH_TRENDS_AND_POST",
      "platform": "twitter",
      "topics": ["AI", "Blockchain"]
    }
  }'

```

### B. List All Active Jobs

Returns an array of all jobs currently registered in the **agentOS** runtime.

**Request:** `GET /jobs`

```bash
curl http://localhost:3000/api/jobs

```

### C. Retrieve Specific Job Details

Fetch the status, last run time, and next scheduled run for a specific job.

**Request:** `GET /jobs/:jobId`

```bash
curl http://localhost:3000/api/jobs/job-12345

```

### D. Update an Existing Job

Modify the schedule or the payload of an existing job without deleting it.

**Request:** `PATCH /jobs/:jobId`

```bash
curl -X PATCH http://localhost:3000/api/jobs/job-12345 \
  -d '{ "schedule": "0 12 * * *" }'

```

### E. Delete/Cancel a Job

Stops the schedule and removes the job from the **agentOS** database.

**Request:** `DELETE /jobs/:jobId`

```bash
curl -X DELETE http://localhost:3000/api/jobs/job-12345

```

---

## 4. Advanced Integration (TypeScript)

If you are developing custom plugins for **agentOS**, use the `IAgentRuntime` service directly to manage jobs programmatically.

```typescript
import { IAgentRuntime, JobStatus } from "@agentos/core";

export class CustomTaskPlugin {
  async initialize(runtime: IAgentRuntime) {
    const jobManager = runtime.getService("job_manager");

    // Programmatic job creation
    const myJob = await jobManager.createJob({
      name: "AutonomousReflexion",
      interval: 3600000, // 1 hour
      callback: async () => {
        const memory = await runtime.getMemoryManager().getLatestMemories();
        console.log("agentOS is reflecting on recent interactions...");
        // Logic for internal agent processing
      }
    });

    console.log(`Job started with ID: ${myJob.id}`);
  }
}

```

---

## 5. Error Handling

**agentOS** uses standard HTTP status codes for the Jobs API:

* `201 Created`: Job successfully scheduled.
* `400 Bad Request`: Invalid Cron syntax or missing `agentId`.
* `404 Not Found`: The specified `jobId` does not exist.
* `500 Internal Server Error`: Issue with the underlying **agentOS** scheduler.

> **Note:** If a job fails during execution, **agentOS** will log the error in the `logs/` directory and attempt a retry if the `retryPolicy` is defined in your `agentos.config.json`.

---

