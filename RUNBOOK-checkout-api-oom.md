Runbook: Responding to checkout-api OOMKilled Incidents
Diagnosis
Step 1: Check Pod Status and Restarts
Check if checkout-api pods are restarting repeatedly or showing unexpected status changes:

Bash
kubectl get pods -l app=checkout-api
Verification: Look at the RESTARTS and STATUS columns. A climbing restart count or CrashLoopBackOff status indicates the workload is unstable.

Step 2: Confirm OOMKilled Status
Inspect the detailed state of an affected pod to verify whether memory limits caused the termination:

Bash
kubectl describe pod <pod-name>
(Replace <pod-name> with the exact name of a restarting pod from Step 1.)

Verification: Locate the container section in the command output. Under Last State, verify that Reason: OOMKilled is explicitly listed.

Resolution
Step 1: Revert or Increase Memory Limits
Adjust the memory limit for checkout-api back to a known-good baseline (128Mi) or higher to allow the application to operate normally:

Bash
kubectl set resources deployment/checkout-api -c=checkout-api --limits=memory=128Mi --requests=memory=64Mi
Note: If managing resources via GitOps or manifest files, update resources.limits.memory to 128Mi in the deployment YAML and apply it using kubectl apply -f checkout-api-deployment.yaml.

Verification: Run kubectl get deployment checkout-api to ensure the rollout has triggered and completed.

Step 2: Verify Pod Stability
Monitor the newly created pods to ensure they stabilize and traffic processes without errors:

Bash
kubectl get pods -l app=checkout-api -w
Verification: Ensure all pods transition to STATUS: Running, show READY: 1/1, and that the RESTARTS count stops increasing.
