# Bridge the Gap and Save the Cost of GPUs
## Running Ray Jobs on Your Private OpenShift AI from GCP
So, you're living the hybrid cloud dream. Your data processing pipelines are humming along nicely on Google Cloud, but for security, compliance or cost reasons, you may want to run the heavy-duty machine learning jobs on an OpenShift AI cluster that is deployed on private data center. How will you go about it?

It's actually easier than you might think. Let's walk through how you can use the CodeFlare SDK to kick off a Ray job from a simple GCP server straight to your on-prem OpenShift AI instance using an Jupyter notebook. This solution is based on the notebook provided on [CodeFlare github repo](https://github.com/project-codeflare/codeflare-sdk/blob/main/demo-notebooks/additional-demos/remote_ray_job_client.ipynb).

### Before You Begin: The Setup
To make this work, you need a few things in place first:

- A remote OpenShift Cluster: This is your target. It should already have the CodeFlare and KubeRay operators installed.
- Authentication: You'll need credentials to access the cluster. The easiest way is to log in using the oc command-line tool. The SDK is smart and will automatically pick up your login session.
- Python and the SDK: Make sure you have Python (3.8+) and the CodeFlare SDK installed on your machine where you are going to run the notebook from.
- A training job, defined in python, within the working directory.
- A requirements.txt or equivalent file containing any additional packages to install onto the Ray images.


Step 1: Configure Your Connection via RayJobClient
First, we need to tell the CodeFlare SDK client on how to connect to OpenShift AI Ray Cluster
```bash
from codeflare_sdk import RayJobClient
```

Step 2: Provide autentication token and provide ray dashboard url
```bash
auth_token = "XXXX" # Replace with the actual token
header = {
    'Authorization': f'Bearer {auth_token}'
}
```     
Step 3: Initialize the RayJobClient
```bash
client = RayJobClient(address=ray_dashboard, headers=header, verify=True)
```

Step 4: Submit a job using the RayJobClient

```bash
entrypoint_command = "python XXXX" # Replace with the training script name
submission_id = client.submit_job(
    entrypoint=entrypoint_command,
    runtime_env={"working_dir": "./","pip": "requirements.txt"},
)
```

Step 5: Get the job's status and lods
```bash
client.get_job_status(submission_id)
client.get_job_logs(submission_id)
```

And there you have it! You've successfully acted as a remote client to a secure OpenShift cluster, run a distributed Ray job, and retrieved the results, all from your GCP cluster. This powerful workflow bridges the gap between different platforms and enables enterprise-grade remote computing!

Feel free to comment and reach out!
