# Detailed Lab Notes — Monitor Resources with Google Cloud Observability

## Lab Objective

Use Google Cloud Monitoring to keep an eye on Compute Engine resources by building dashboards, tracking CPU activity, creating alerts, grouping related resources, and setting up uptime checks.

## Focus Areas

Cloud Monitoring, dashboards, CPU metrics, alerting, resource groups, uptime checks, notification channels, and general resource monitoring.

## Purpose

Get hands-on experience with how Google Cloud Monitoring works and learn how it can be used to spot performance issues, organize resources, and keep track of availability.

---

## Task 1 — Verify Monitoring Resources

**Action:** Checked the Compute Engine VM Instances page to make sure the three lab VMs were running and ready to monitor.

**Resources**
- `nginxstack-1`
- `nginxstack-2`
- `nginxstack-3`

**Observation:** All three VMs were running in `us-east1-b` and each had an internal and external IP address.

**What I learned:** The resources did not have to be created from inside Cloud Monitoring. Existing Compute Engine instances could already be monitored once they were running in the project.

**Security relevance:** Before setting up monitoring, it helps to know exactly which systems are in scope. If a resource is not being watched, problems with that system could be missed.

---

## Task 2 — Created a Custom Monitoring Dashboard

**Action:** Created a dashboard called `My Dashboard` and added a line chart to track CPU utilization across the three VM instances.

**Configuration**
- Dashboard: `My Dashboard`
- Widget type: Line chart
- Chart title: `My Chart`
- Resource: VM Instance
- Metric: CPU utilization

**Observation:** The chart showed a separate CPU utilization line for each of the three `nginxstack` VMs, which made it easy to compare what each VM was doing over the same time period.

**What I learned:** A custom dashboard gave me one place to look at resource activity instead of opening each VM separately. It also made spikes or differences between systems much easier to notice.

**Security relevance:** CPU activity by itself does not mean there is a security problem, but unexpected spikes can be a reason to look deeper, especially when they line up with alerts, logs, or other unusual activity.

---

## Task 3 — Create a Multi-Condition Alerting Policy

### Task 3A — Configured CPU Usage Condition

**Action:** Created the first alert condition using the CPU usage metric for VM instances.

**Configuration**
- Resource: VM Instance
- Metric: CPU usage
- Rolling window: 1 minute
- Threshold position: Above threshold
- Threshold value: `20`

**Observation:** Cloud Monitoring showed the current CPU usage for each of the three VMs along with the threshold line I configured.

**What I learned:** An alert condition checks a metric over a set period of time and compares it against a threshold. If the metric crosses that threshold, the condition can be considered met.

**Security relevance:** Threshold alerts can help point out behavior that is outside of what I expect. They do not automatically mean something is wrong, but they give me a reason to investigate.

### Task 3B — Added CPU Utilization Condition

**Action:** Added a second condition using CPU utilization and set the policy so both conditions had to be met before an alert could trigger.

**Configuration**
- Resource: VM Instance
- Metric: CPU utilization
- Rolling window: 1 minute
- Threshold position: Above threshold
- Threshold value: `20%`
- Multi-condition trigger: **All conditions are met**

**Observation:** The policy now checked two CPU-related conditions instead of relying on a single metric.

**What I learned:** Cloud Monitoring lets me combine more than one condition into the same alert policy. This makes it possible to build alerts that are more specific about what should actually trigger a notification.

**Security relevance:** Using more than one condition can help cut down on unnecessary alerts. Instead of reacting to every single metric change, I can require more than one signal before deciding something needs attention.

### Task 3C — Configured Notification Channel and Created Policy

**Action:** Added an email notification channel and created the policy as `My Alert Policy`.

**Observation:** The policy was created successfully and the email notification channel was attached. There were no active incidents when I reviewed the policy.

**What I learned:** An alerting policy is more than just a threshold. It combines the metric being watched, the condition that needs to happen, how the conditions work together, and where the notification should be sent.

**Security relevance:** Monitoring is only useful if someone knows when something needs attention. Notification channels make sure an alert can actually reach the person responsible for looking into it.

**Key takeaway:** Cloud Monitoring alerts can use multiple conditions instead of relying on a single metric. Requiring both conditions to be met makes the alert more specific and can help reduce unnecessary notifications.

---

## Task 4 — Created a Resource Group

**Action:** Created a Cloud Monitoring group called `VM instances` and used `nginx` as the filter so the three NGINX VMs would be grouped together.

**Configuration**
- Group name: `VM instances`
- Filter value: `nginx`

**Observation:** The group pulled in `nginxstack-1`, `nginxstack-2`, and `nginxstack-3`. The group page gave me one place to view the VMs and related resources instead of looking at each instance separately.

**What I learned:** Groups are useful when several resources belong to the same application or workload and I want to monitor them together.

**Security relevance:** From a security and operations standpoint, grouping related systems makes it easier to notice when several resources are having the same problem at the same time.

---

## Task 5 — Created an Uptime Check

**Action:** Created an uptime check in Cloud Monitoring for the `VM instances` group so I could test whether the NGINX resources were reachable over HTTP.

**Configuration**
- Protocol: HTTP
- Resource type: Instance
- Applies to: Group
- Group: `VM instances`
- Check frequency: 1 minute
- Notification channel: Email
- Uptime check name: `My Uptime check`

**Observation:** Before creating the check, I ran the built-in test and Cloud Monitoring successfully reached the target with an HTTP `200 OK` response. After creating it, `My Uptime check` appeared in the Uptime Checks page with monitoring locations across multiple regions.

**What I learned:** An uptime check actively tests whether a service can be reached, which is different from only looking at CPU or other performance metrics.

**Security relevance:** A system can look normal from a resource-usage perspective and still be unavailable to users. Uptime monitoring adds another layer of visibility by checking whether the service is actually responding.

---

## Task 6 — Disabled the Alert Policy

**Action:** Turned off `My Alert Policy` after finishing the lab.

**Observation:** The alert policy stayed in Cloud Monitoring, but it was no longer active or evaluating the CPU conditions.

**What I learned:** An alert policy can be disabled without deleting it, which is useful when I want to stop notifications but still keep the configuration.

**Security relevance:** Old or unused alerts can create noise and confusion. Turning off alerts that are no longer needed helps keep the monitoring environment cleaner and makes active alerts easier to trust.

**Evidence note:** I did not preserve a separate screenshot of the disabled policy.

**Key takeaway:** Monitoring rules need to be maintained just like the systems they watch. Disabling alerts that are no longer needed helps avoid unnecessary noise.
