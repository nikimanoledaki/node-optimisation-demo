# Node Optimisation Demo

The open source [Telemetry-Aware Scheduling (TAS)](https://github.com/intel/platform-aware-scheduling/tree/master/telemetry-aware-scheduling) tool can be used to optimise how Pods are scheduled onto Nodes in an energy-efficient manner.

TAS uses a metrics pipeline, Policies, and [labeling strategies](https://github.com/intel/platform-aware-scheduling/tree/master/telemetry-aware-scheduling#strategies), which are currently the following:
- scheduleonmetric
- dontschedule
- deschedule
- labeling

TAS can be used to schedule Pods on Nodes based on various metrics such as a Node’s CPU utilization.
According to ["Energy-Proportional Computing: A new Definition"](https://ieeexplore.ieee.org/document/7999152), the optimal CPU utilization is 70-80% on average.

The following manifest can be used with TAS:
```yaml
scheduleonmetric:
  rules:
  - metricname: CPUUtilization
    operator: GreaterThan
dontschedule:
  rules:
  - metricname: CPUUtilization
    operator: GreaterThan
    target: 0.8
deschedule:
  rules
  - metricname: CPUUtilization
    operator: GreaterThan
    target: 0.8
```

This is saying to TAS:
- To schedule a Pod, look at the CPUUtilization for each node, sort the values in descending order and pick a Node. The Node with capacity that is the closest to 70% will be chosen. If there is no capacity on the Node, the Horizontal Pod autoscaler should reschedule this Pod to a different Node.
- New Pods won’t be scheduled on any Nodes where CPUUtilization is > 80%.
- If you have existing pods that are on Nodes with CPUUtlization > 80%, mark them for descheduling. Then, we need the descheduler to actually deschedule the workloads.
