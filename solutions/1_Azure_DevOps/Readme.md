# The Azure VM Image Builder DevOps Task  

Welcome to the Azure DevOps Task for Image Builder

## V1 Design Purpose
This task is designed to take your repo/build artifacts, and inject them into a VM image, so you can install, configure your application, and OS.
 
## [Documentation](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/image-builder-devops-task)
Details on the inputs for the task.

## [End to End Example (with video guide!!)](./BuildaPipeline.md)
Step by step guide on how to create a custom image build pipeline, where you can bake your apps into an image, configure the OS, and then distribute globally.

## DevOps Task versions
There are two Azure VM Image Builder (AIB) DevOps Tasks:

* ['Stable' AIB Task](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), this allows us to put in the latest updates and features, allow customers to test them, before we promote it to the 'stable' task, approx 1 week later. 
Current task version: v1.0.33

* ['Unstable' AIB Task](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary), this allows us to put in the latest updates and features, allow customers to test them, before we promote it to the 'stable' task. If there are no reported issues, and our telemetry shows no issues, approx 1 week later, we will promote the task code to 'stable'. Current task version: v1.0.8

* Unstable DevOps Task Updates v1.0.8
    * Support has been added to the task to support user identity
        * You will need to [create the user identity and ensure the right permissions](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/image-builder-overview#permissions) are applied.
    * Mutliple Bug fixes to address source custom images
        * Windows custom images would not get the files injected into the image
        * Some Linux custom images would not extract the files inject the image
        * This will support the new API


