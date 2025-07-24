# 🚨 K8s Pod Monitoring & Alerting with OpenTelemetry

## 📌 Problem Statement
Monitor Kubernetes pods and send an alert if a pod is stuck in `Pending` state for over **5 minutes** using **OpenTelemetry Collector**. Metrics should be visualized in **SigNoz** and alerts delivered via **Slack**.

---

## ✅ Prerequisites

- ✅ [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- ✅ [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
- ✅ [kubectx & kubens](https://github.com/ahmetb/kubectx)
- ✅ Slack webhook URL
- ✅ SigNoz account + access token

---

## 🧰 Tools Used
| Tool | Use |
|------|-----|
| Minikube | Local K8s cluster |
| OpenTelemetry Collector | Pod phase metric observer |
| SigNoz Cloud | Metric ingestion & alerting |
| Slack Webhook | Alert delivery |

---

## 📁 Folder Structure
```bash
signoz-k8s-alerting/
├── deployments/
│   └── nginx-deployments.yaml
├── otel/
│   ├── otel-rbac.yaml
│   ├── otel-collector-config.yaml
│   └── otel-collector-deployment.yaml
├── screenshots/
│   ├── pending-pod.png
│   ├── alert-query.png
│   └── slack-alert.png
│   └── setup.png
└── README.md
```

---

## 🧭 High-Level Flow

1. Start Minikube and create required namespaces (`signoz`, `opentelemetry`).
2. Deploy two nginx pods - one runs normally, the other is stuck in `Pending` via `nodeSelector` mismatch.
3. Deploy OpenTelemetry Collector configured with:
   - `k8s_observer` extension: Discovers Kubernetes objects like pods.
   - `k8s_cluster` receiver: Exposes core Kubernetes metrics like `k8s.pod.phase`, which is required for alerting.
4. Send metrics to SigNoz using the `otlp` exporter.
5. In SigNoz, set up an alert rule for `k8s.pod.phase == Pending` persisting for 5+ minutes.
6. Send alert to a Slack channel via webhook.

---

## 🛠️ Key Commands

```bash
# Start minikube
minikube start

# Create and switch to namespace
kubectl create ns signoz
kubens signoz

# Apply nginx deployments
kubectl apply -f deployments/nginx-deployments.yaml

# Create opentelemetry namespace
kubectl create ns opentelemetry

# Deploy OTEL Collector RBAC and config
kubectl apply -f otel/otel-rbac.yaml
kubectl apply -f otel/otel-collector-config.yaml
kubectl apply -f otel/otel-collector-deployment.yaml
```

---

## 📊 Visualize Metrics in SigNoz

1. Open [Sample Dashboard](https://svey-5nsw.in.signoz.cloud/dashboard/01983dbc-cb7a-7895-b491-92d32eb449b2?relativeTime=30m)
2. Create dashboard or use Explorer
3. Query `k8s.pod.phase` filtered by `Pending`

---

## 🔔 Alerting Steps

1. Go to **Alerts > Create Alert**
2. Metric: `k8s.pod.phase`
3. Condition: `Pending count > 0 for 5 minutes`
4. Slack integration: Add webhook URL

---

## 🖼️ Screenshots

- 📸 `screenshots/pending-pod.png`: Pod stuck in `Pending`
- 📸 `screenshots/signoz-dashboard.png`: SigNoz showing metric
- 📸 `screenshots/slack-alert.png`: Slack message after 5 minutes

---

## ✅ Expected Outcome

- Metrics are correctly ingested into SigNoz
- Alert fires after 5 minutes if pod remains pending
- Slack receives the alert message

---

## 🧑‍💻 Author Notes

This demo showcases how to integrate **Kubernetes**, **OpenTelemetry**, and **SigNoz** to detect real-time infrastructure issues and alert on them effectively.

---

## 📎 Resources

- [OpenTelemetry K8s Observer Extension](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/extension/observer/k8sobserver/README.md)
- [SigNoz Cloud](https://signoz.io)
- [Slack Incoming Webhooks](https://api.slack.com/messaging/webhooks)
