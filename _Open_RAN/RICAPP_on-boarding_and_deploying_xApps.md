---

---



# On-boarding and Deploying xApps

![onboard.jpg](https://wiki.o-ran-sc.org/download/attachments/20873294/onboard.jpg?version=1&modificationDate=1585246135010&api=v2)

1. xApp Onboarding:

- To onboard an xApp, the xApp descriptor and its schema are submitted to the xApp onboard.



2. Helm Chart Generation and Distribution:

- The xApp onboarder generates Helm charts and distributes them to the local Helm repository in the RIC platform instance.



3. xApp Deployment:

- The operator triggers the xApp deployment.



4. (Optional) Downloading and Modifying xApp configuration file:

- Through the RIC dashboard, download a 'values.yaml' file that contains the default xApp configuration parameters. Modify the xApp configuration parameters, if necessary.



5. Configuration Update and Application:

- Upload the new configuration to appmgr. Appmgr combines the xApp Helm charts from the local Helm repo and the new configuration.



6. Creating an xApp Instance:

- Appmgr creates an xApp instance.