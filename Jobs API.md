

---

# agentOS Jobs API Examples

This documentation provides examples of how to interact with the agentOS Jobs API to manage background tasks and scheduling within the **agentOS** framework.

## Create a New Job

To create a new background job, send a `POST` request to the jobs endpoint.

### Using cURL

```bash
curl -X POST http://localhost:3000/api/jobs \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Data Cleanup Task",
    "description": "Cleans up expired cache entries",
    "agentId": "agent-123",
    "schedule": "0 0 * * *",
    "payload": {
      "action": "cleanup",
      "target": "memory"
    }
  }'

```

## List All Jobs

To retrieve a list of all currently scheduled or running jobs in **agentOS**:

```bash
curl http://localhost:3000/api/jobs

```

## Get Job Status

To check the status of a specific job, use the `GET` endpoint with the unique job ID.

```bash
curl http://localhost:3000/api/jobs/your-job-id

```

## Integration Example (TypeScript)

If you are developing directly with the `@agentos/core` package, you can use the internal job manager:

```typescript
import { AgentRuntime } from "@agentos/core";

async function setupJob(runtime: AgentRuntime) {
  const job = await runtime.getService("job_manager").createJob({
    name: "Daily Reminder",
    interval: 86400000, // 24 hours
    callback: async () => {
      console.log("Executing scheduled task in agentOS...");
    }
  });
  
  return job;
}

```

## Delete a Job

To stop and remove an existing job:

```bash
curl -X DELETE http://localhost:3000/api/jobs/your-job-id

```

---

### Implementation Notes

* Ensure your **agentOS** instance is running before making API calls.
* The `agentId` must correspond to a valid agent currently managed by the runtime.
* For cron-based scheduling, **agentOS** follows standard crontab syntax.

Would you like me to create a sample configuration file (JSON) to help you set up these jobs automatically when **agentOS** starts?
