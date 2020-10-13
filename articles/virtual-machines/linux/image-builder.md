---
title: Usare Azure Image Builder con una raccolta immagini per macchine virtuali Linux (anteprima)
description: Creare immagini di macchina virtuale Linux con Azure Image Builder e la Raccolta immagini condivise.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: d4715bd8b7a13a5ab53d254ac853ac324440b403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502614"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery-by-using-azure-cli"></a>Anteprima: creare un'immagine Linux e distribuirla in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come usare Image Builder e l'interfaccia della riga di comando di Azure per creare una versione dell'immagine in una [Raccolta immagini condivise](../windows/shared-image-galleries.md), quindi come distribuire l'immagine globalmente. Questa operazione può essere eseguita anche tramite [Azure PowerShell](../windows/image-builder-gallery.md).


Per configurare l'immagine si userà un modello di esempio con estensione json. Il file con estensione json usato è il seguente: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Per distribuire l'immagine in una Raccolta immagini condivise, il modello usa [sharedImage](image-builder-json.md#distribute-sharedimage) come valore per la sezione `distribute`.

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

## <a name="set-variables-and-permissions"></a>Impostare variabili e autorizzazioni 

Alcune informazioni verranno usate ripetutamente, pertanto si creano variabili per archiviare tali informazioni.

Per l'anteprima, Image Builder supporta la creazione di immagini personalizzate solo nello stesso gruppo di risorse dell'immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio in modo che sia uguale a quello del gruppo di risorse dell'immagine gestita di origine.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Creare una variabile per l'ID di sottoscrizione. Per ottenere questo risultato, usare `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Creare il gruppo di risorse.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Creare un'identità assegnata dall'utente e impostare autorizzazioni per il gruppo di risorse
Image Builder userà l'[identità utente](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) specificata per inserire l'immagine nella Raccolta immagini condivise di Azure. In questo esempio si creerà una definizione di ruolo di Azure che include azioni granulari per eseguire la distribuzione dell'immagine a SIG. La definizione del ruolo verrà quindi assegnata all'identità utente.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
identityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $identityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Creare una definizione dell'immagine e una raccolta

Per usare Image Builder con una raccolta di immagini condivise, è necessario avere una raccolta immagini esistente e una definizione di immagine. Image Builder non crea autonomamente la raccolta immagini e la definizione di immagine.

Se non si ha già una raccolta e una definizione di immagine da usare, iniziare creando questi elementi. In primo luogo creare un raccolta di immagini.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Creare una definizione dell'immagine.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Scaricare e configurare il file con estensione json

Scaricare il modello con estensione json e configurarlo con le variabili.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Creare la versione di immagine

In questa parte viene creata la versione dell'immagine nella raccolta. 

Inviare la configurazione dell'immagine al servizio Image Builder di Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

La creazione dell'immagine e la sua replica in entrambe le aree possono richiedere un certo tempo. Attendere il completamento di questa parte prima di procedere alla creazione di una macchina virtuale.


## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale dalla versione dell'immagine creata da Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Accedere tramite SSH alla macchina virtuale.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Si noterà che l'immagine viene personalizzata con un *messaggio del giorno* non appena viene stabilita la connessione SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole provare a ripersonalizzare la versione dell'immagine per creare una nuova versione della stessa immagine, ignorare i passaggi successivi e passare a [Usare Azure Image Builder per creare un'altra versione dell'immagine](image-builder-gallery-update-image-version.md).


Questa operazione eliminerà l'immagine creata e tutti gli altri file di risorse. Assicurarsi che la distribuzione sia stata completata prima di eliminare le risorse.

Quando si eliminano risorse della raccolta immagini, è necessario eliminare tutte le versioni dell'immagine prima di poter eliminare la definizione immagine usata per creare le risorse. Per eliminare una raccolta, è prima di tutto necessario eliminare tutte le definizioni immagine nella raccolta.

Eliminare il modello di generatore di immagini.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Eliminare assegnazioni di autorizzazioni, ruoli e identità
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Ottenere la versione dell'immagine creata da Image Builder, che inizia sempre con `0.`, quindi eliminare la versione dell'immagine

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Eliminare la definizione immagine.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Eliminare la raccolta.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Eliminare il gruppo di risorse.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [raccolte di immagini condivise di Azure](shared-image-galleries.md).
