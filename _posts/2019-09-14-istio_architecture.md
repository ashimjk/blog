---
title: Istio Access Metrics
date: 2019-09-14 11:37:46 +0545
categories: [kubernetes]
tags: [istio]
---

With Istio's insight into how applications communicate, it can generate profound insights into how applications are
working and performance metrics.

## Generate Load

To view the graphs, there first needs to be some traffic. Execute the command below to send requests to the application.

```bash
while true; do
  curl -s https://product.cluster.com/productpage > /dev/null
  echo -n .;
  sleep 0.2
done
```

## Access Dashboards

With the application responding to traffic the graphs will start highlighting what's happening under the covers.

### Grafana

The first is the Istio Grafana Dashboard. The dashboard
`returns the total number of requests currently being processed, along with the number of errors and the response time of each call`.

https://grafana.cluster.com/dashboard/db/istio-mesh-dashboard

As Istio is managing the entire `service-to-service communicate`, the dashboard will highlight the aggregated totals and
the breakdown on an individual service level.

### Jaeger

Jaeger provides `tracing information for each HTTP request`. It shows which calls are made and where the time was spent
within each request.

https://jaeger.cluster.com/

It is an excellent way to identify issues and potential performance bottlenecks.

### Service Graph

As a system grows, it can be hard to visualise the dependencies between services. The Service Graph will
`draw a dependency tree of how the system connects`.

https://service-graph.cluster.com/dotviz