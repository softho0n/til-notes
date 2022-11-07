# Cordon 

## What is `cordon` ?

- Let us assume we have k8s cluster that have 4 nodes.
- There are master node, gpu1 node, gpu2 node and gpu3 node.
- But if someone want to use gpu3 node for stadalone training processing, we need to set k8s cluster so that no tasks that use gpu resources are assigned to gpu3. Right?
- In this situation, we can use `cordon` instruction in `kubectl`
- First, Let us check node status using below instruction.
    
    ```bash
    $ kubectl get nodes
    
    NAME     STATUS                     ROLES                  AGE   VERSION
    gpu1     Ready                      <none>                 11d   v1.21.12
    gpu2     Ready                      <none>                 11d   v1.21.12
    gpu3     Ready                      <none>                 10d   v1.21.12
    master   Ready                      control-plane,master   11d   v1.21.12
    ```
    
- We will set gpu3 status from `Ready` to `SchedulingDisabled` using `cordon` instruction.
    
    ```bash
    $ kubectl cordon gpu3
    
    $ kubectl get nodes
    
    NAME     STATUS                     ROLES                  AGE   VERSION
    gpu1     Ready                      <none>                 11d   v1.21.12
    gpu2     Ready                      <none>                 11d   v1.21.12
    gpu3     Ready,SchedulingDisabled   <none>                 11d   v1.21.12
    master   Ready                      control-plane,master   11d   v1.21.12
    ```
    

## How can be changed Pod status in cordoned node?

- There are many running pod in node before cordon. right?
- If we execute cordon instuction to node,
    - the pod that already is running status will not be changed.
- But, the additional pod scheduling will be rejected after executing cordon instruction.