---
title: Creare un'immagine di macchina virtuale e usare un'identità gestita assegnata dall'utente per accedere ai file in archiviazione di Azure (anteprima)
description: Creare un'immagine di macchina virtuale con Azure Image Builder, che può accedere ai file archiviati in archiviazione di Azure usando l'identità gestita assegnata dall'utente.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: f5734d4b1871dd285fc83a72631f7d645e0b72ff
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307263"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Creare un'immagine e usare un'identità gestita assegnata dall'utente per accedere ai file in archiviazione di Azure 

Il generatore di immagini di Azure supporta l'uso di script o la copia di file da più posizioni, ad esempio GitHub e archiviazione di Azure e così via. Per usarli, è necessario che siano accessibili esternamente ad Azure Image Builder, ma è possibile proteggere i BLOB di archiviazione di Azure usando i token SAS.

Questo articolo illustra come creare un'immagine personalizzata usando il generatore di immagini di VM di Azure, in cui il servizio userà un' [identità gestita assegnata dall'utente](../../active-directory/managed-identities-azure-resources/overview.md) per accedere ai file in archiviazione di Azure per la personalizzazione delle immagini, senza che sia necessario rendere i file accessibili pubblicamente o configurare token SAS.

Nell'esempio seguente vengono creati due gruppi di risorse, uno per l'immagine personalizzata e l'altro ospita un account di archiviazione di Azure, che contiene un file di script. Questa operazione simula uno scenario reale, in cui è possibile avere artefatti di compilazione o file di immagine in account di archiviazione diversi, al di fuori di Image Builder. Si creerà un'identità assegnata dall'utente e quindi si concederanno le autorizzazioni di lettura per il file di script, ma non verrà impostato alcun accesso pubblico al file. Si userà quindi la shell verbi per scaricare ed eseguire lo script dall'account di archiviazione.


> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrare le funzionalità
Per usare Azure Image Builder durante l'anteprima, è necessario registrare la nuova funzionalità.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verificare lo stato della registrazione della funzionalità.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verificare la registrazione.


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

Se non visualizzano Registered, eseguire le operazioni seguenti:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Alcune informazioni verranno usate ripetutamente, pertanto verranno create alcune variabili in cui archiviarle.


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

Creare una variabile per l'ID di sottoscrizione. Per ottenere questo risultato, usare `az account show | grep id`.

```console
subscriptionID=<Your subscription ID>
```

Creare i gruppi di risorse per l'immagine e l'archiviazione di script.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Creare un'identità assegnata dall'utente e impostare le autorizzazioni per il gruppo di risorse.

Il generatore di immagini userà l' [identità utente](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) specificata per inserire l'immagine nel gruppo di risorse. In questo esempio verrà creata una definizione di ruolo di Azure con le azioni granulari per eseguire la distribuzione dell'immagine. La definizione del ruolo verrà quindi assegnata all'identità utente.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Creare la risorsa di archiviazione e copiare lo script di esempio da GitHub.

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

Assegnare a Image Builder l'autorizzazione per creare risorse nel gruppo di risorse immagine. Il `--assignee` valore è l'ID identità utente.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Modificare l'esempio

Scaricare il file example. JSON e configurarlo con le variabili create.

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

Inviare la configurazione dell'immagine al servizio Image Builder di Azure.

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

Si noterà che l'immagine viene personalizzata con un messaggio del giorno non appena viene stabilita la connessione SSH.

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

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante l'uso di Azure Image Builder, vedere [risoluzione dei problemi](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).
