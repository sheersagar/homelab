## Steps to Follow

The very first step is to review all the pods and deployment and look for the pods crashlooping.

![Error picture](../Images/crashloop.png)

## Steps to Follow

The very first step is to review all the pods and deployment and look for the pods crashlooping.

![Error picture](../Images/crashloop.png)

# Step-by-Step Troubleshoorting Framework
1. #### Why it is failing
    * #### Describe the Pod
    ```bash
     kubectl describe pod -n <name-space> <pod-name>
    ```
    Now go to the event section of the output and look for errors.
    
    ![Alt text](../Images/describe-crashloop.png)

    