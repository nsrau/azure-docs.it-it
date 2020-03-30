---
title: Creare un'immagine di macchina virtuale e usare un'identità gestita assegnata dall'utente per accedere ai file in Archiviazione di Azure (anteprima)Create a Virtual Machine image and use a user-assigned managed identity to access files in Azure Storage (preview)
description: Creare un'immagine di macchina virtuale usando Azure Image Builder, che può accedere ai file archiviati in Archiviazione di Azure usando l'identità gestita assegnata dall'utente.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060742"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Creare un'immagine e usare un'identità gestita assegnata dall'utente per accedere ai file in Archiviazione di AzureCreate an image and use a user-assigned managed identity to access files in Azure Storage 

Azure Image Builder supporta l'uso di script o la copia di file da più posizioni, ad esempio GitHub e archiviazione di Azure e così via. Per usarli, devono essere stati accessibili esternamente a Generatore di immagini di Azure, ma è possibile proteggere i BLOB di Archiviazione di Azure usando i token di firma di accesso condiviso.

Questo articolo illustra come creare un'immagine personalizzata usando Azure VM Image Builder, in cui il servizio utilizzerà un'identità [gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per accedere ai file nell'archiviazione di Azure per la personalizzazione dell'immagine, senza dover rendere i file accessibili pubblicamente o configurare token di firma di accesso condiviso.

Nell'esempio seguente verranno creati due gruppi di risorse, uno per l'immagine personalizzata e l'altro un account di archiviazione di Azure, che contiene un file di script. In questo modo viene simulato uno scenario di vita reale, in cui potrebbero essere stati utilizzati elementi di compilazione o file di immagine in account di archiviazione diversi, all'esterno di Image Builder.This simulates a real life scenario, where you may have build artifacts, or image files in different storage accounts, outside of Image Builder. Si creerà un'identità assegnata dall'utente, quindi si concederanno le autorizzazioni di lettura per il file di script, ma non si imposterà alcun accesso pubblico a tale file. Si utilizzerà quindi l'addetto alla personalizzazione di Shell per scaricare ed eseguire lo script dall'account di archiviazione.


> [!IMPORTANT]
> Azure Image Builder è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrare le funzioni
Per usare Azure Image Builder durante l'anteprima, è necessario registrare la nuova funzionalità.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Controllare lo stato della registrazione della funzionalità.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Controlla la tua registrazione.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Se non dicono registrati, eseguire quanto segue:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Utilizzeremo alcune informazioni ripetutamente, quindi creeremo alcune variabili per memorizzare tali informazioni.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Creare una variabile per l'ID sottoscrizione. È possibile ottenere `az account show | grep id`questo utilizzando .

```console
subscriptionID=<Your subscription ID>
```

Creare i gruppi di risorse sia per l'immagine che per l'archiviazione degli script.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Creare l'archiviazione e copiarvi lo script di esempio da GitHub.Create the storage and copy the sample script into it from GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Concedere a Image Builder l'autorizzazione per creare risorse nel gruppo di risorse immagine. Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utenteCreate user-assigned managed identity

Creare l'identità e assegnare le autorizzazioni per l'account di archiviazione dello script. Per ulteriori informazioni, vedere [Identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Modificare l'esempio

Scaricare il file .json di esempio e configurarlo con le variabili create.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine al servizio Generatore immagini di Azure.Submit the image configuration to the Azure Image Builder service.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Attendere il completamento del processo di compilazione. Questa operazione può richiedere circa 15 minuti.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale dall'immagine. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Dopo aver creato la macchina virtuale, avviare una sessione SSH con la macchina virtuale.

```console
ssh aibuser@<publicIp>
```

Dovresti vedere che l'immagine è stata personalizzata con un messaggio del giorno non appena viene stabilita la tua connessione SSH!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Eseguire la pulizia

Al termine, è possibile eliminare le risorse se non sono più necessarie.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi con l'uso di Generatore immagini di Azure, vedere [Risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).