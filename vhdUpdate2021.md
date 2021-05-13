# AWARENESS: VHD Output Change 25th May 2021

As we are finalizing the code for GA, we need to make a small breaking to the VHD Output, where we will not return a SAS URI, but a BLOB URI.

> NOTE: This will only impact builds from the **25th May 2021**.

## Existing behavior
When you specify a VHD output the VHD is placed in the staging resource group, you then query the runOutput:

```bash
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2020-02-14 | grep artifactUri
```
This then would return a SAS URI which could be downloaded, for example:
```bash
https://kqqtojmjy3fid5w2u3zukw1j.blob.core.windows.net/vhds/Tmp1_20210312111014.vhd?se=2021-04-11T19%3A10%3A15Z\u0026sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\u0026sp=r\u0026spr=https\u0026sr=b\u0026sv=2018-03-28
```
## New behavior
Instead of returning the SAS URI, we will just return the BLOB URI:
```bash
https://kqqtojmjy3fid5w2u3zukw1j.blob.core.windows.net/vhds/Tmp1_20210312111014.vhd
```

If you wish to download them, you will need to either:

* Generate a SAS URI as a post build step
* Use an Azure storage client to download the VHD.

## Accomodating the change: example steps to generate a SAS token as a post build step
In this example we will create another resource group and create a managed disk from the VHD URI emitted from image builder. A SAS token is generated from the managed disk.

```bash
# location must be same as AIB service location
diskResGrp=tempdiskResGrp
location=WestUS2
az group create -n $diskResGrp -l $location

# get disk URI from build
imageTemplateName=helloImageTemplateLinux01
vhdResId=$(az resource show --ids /subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName --query 'properties.artifactUri')

# create a disk
# Note! This command will fail with 'Bad Request if a SAS token' is in the $vhdResId, it must be a resourceId.

buildDiskName=buildDisk01
az disk create -g $diskResGrp -n $buildDiskName --source $vhdResId

# get the SAS from the disk
sasURI=$(az disk grant-access --access-level Read --duration-in-seconds 36 --name $buildDiskName --resource-group $diskResGrp --query 'accessSas')

# download the disk
Use the same technique as you have previously done (curl, wget, invoke-webrequest etc..).

# cleanup
az group delete -g $diskResGrp -y

```

