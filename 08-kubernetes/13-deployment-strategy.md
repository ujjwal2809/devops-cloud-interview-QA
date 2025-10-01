## What Deployment Strategy Do You Follow in Your Organization?

### Question  
Explain the deployment strategy your team or organization follows for releasing applications to production. Include the rationale and any tooling used.

### Short explanation of the question  
This question tests your understanding of real-world CI/CD practices and deployment risk mitigation techniques such as blue-green, rolling updates, or canary deployments.

---

### Answer  
In our organization, we primarily follow the **Rolling Update strategy** using **Kubernetes Deployments**, combined with **Canary deployments** via tools like **Argo Rollouts** or **Flagger** for critical services. This allows us to ensure zero downtime while gradually releasing new versions, with automated rollback on failure.

---

### Detailed explanation of the answer for readers’ understanding

---

### 🔁 1. Rolling Update Strategy (Default in Kubernetes)

This is the default strategy in Kubernetes Deployments:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 1
```

- **Old pods are terminated one-by-one**, while **new pods are spun up gradually**.
- Ensures continuous availability with zero downtime.
- Suitable for stateless workloads.

#### Why We Use It:
- Safe and reliable for non-critical changes.
- Works out-of-the-box with minimal setup.
- Easy to observe pod behavior and health during rollout.

---

### 🧪 2. Canary Deployment (for critical changes)

For services that are **business-critical or prone to regression**, we use **Canary deployments** via tools like:

- [Argo Rollouts](https://argo-rollouts.readthedocs.io/)
- [Flagger](https://flagger.app/)
- Istio/Linkerd (in some setups)

Canary strategy slowly shifts traffic like:

```
10% new version ➜ 50% ➜ 100%
```

With checks between each step.

#### Benefits:
- We catch issues early with real traffic.
- Automated rollback if metrics or logs indicate failure.
- Controlled and observable releases.

---

### 🚦 3. Blue-Green Deployment (used less frequently)

In rare cases where instant rollback or migration is needed:

- We deploy v2 alongside v1 in full.
- Switch traffic via load balancer or Ingress.
- Instant rollback is possible.

Downsides: resource-heavy, more infra complexity.

---

### ⚙️ Tooling We Use

| Tool              | Purpose                            |
|------------------|-------------------------------------|
| **ArgoCD**        | GitOps-based deployment management |
| **Argo Rollouts** | Progressive delivery strategies    |
| **Prometheus**    | Monitors health and SLOs           |
| **Helm**          | Templated Kubernetes deployments   |

---

### Real-world Insight

> “During a major upgrade in our user authentication service, we used a **Canary rollout** with Argo Rollouts. We routed 5%, 25%, then 100% traffic after validating performance metrics. Argo Rollouts auto-paused the rollout when latency increased — preventing a major outage.”

---

### Key takeaway  

> "We follow a **Rolling Update** strategy for general use, and **Canary deployments** for critical services. We choose deployment patterns based on service criticality, risk profile, and observability tooling."
