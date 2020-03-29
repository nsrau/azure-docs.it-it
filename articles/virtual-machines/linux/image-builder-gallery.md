---
title: Usare Azure Image Builder con una raccolta immagini per le macchine virtuali Linux (anteprima)Use Azure Image Builder with an image gallery for Linux VMs (preview)
description: Creare immagini di macchine virtuali Linux con Azure Image Builder e Shared Image Gallery.Create Linux VM images with Azure Image Builder and Shared Image Gallery.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945000"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Anteprima: Creare un'immagine Linux e distribuirla a una raccolta immagini condivise 

Questo articolo illustra come usare Azure Image Builder e l'interfaccia della riga di comando di Azure per creare una versione dell'immagine in una [raccolta immagini condivise,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)quindi distribuire l'immagine a livello globale. È anche possibile usare [Azure PowerShell.You](../windows/image-builder-gallery.md)can also do this using Azure PowerShell .


Utilizzeremo un modello .json di esempio per configurare l'immagine. Il file .json che stiamo usando è qui: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Per distribuire l'immagine in una raccolta immagini condivise, il `distribute` modello utilizza [sharedImage](image-builder-json.md#distribute-sharedimage) come valore per la sezione del modello.

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

## <a name="set-variables-and-permissions"></a>Impostare variabili e autorizzazioni 

Utilizzeremo alcune informazioni ripetutamente, quindi creeremo alcune variabili per memorizzare tali informazioni.

Per anteprima, il generatore di immagini supporterà solo la creazione di immagini personalizzate nello stesso gruppo di risorse dell'immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio in modo che sia lo stesso gruppo di risorse dell'immagine gestita di origine.

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

Creare una variabile per l'ID sottoscrizione. È possibile ottenere `az account show | grep id`questo utilizzando .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Creare il gruppo di risorse.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Concedere a Generatore immagini di Azure l'autorizzazione per creare risorse in tale gruppo di risorse. Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Creare una definizione e una raccolta di immagini

Per utilizzare Creazione immagini con una raccolta di immagini condivisa, è necessario disporre di una raccolta di immagini e di una definizione di immagine esistenti. Image Builder non creerà automaticamente la raccolta di immagini e la definizione dell'immagine.

Se non si dispone già di una raccolta e di una definizione di immagine da utilizzare, iniziare creandole. Creare innanzitutto una raccolta immagini.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Quindi, creare una definizione di immagine.

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


## <a name="download-and-configure-the-json"></a>Scaricare e configurare il file .json

Scaricare il modello .json e configurarlo con le variabili.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Creare la versione dell'immagine

La parte successiva creerà la versione dell'immagine nella galleria. 

Inviare la configurazione dell'immagine al servizio Generatore immagini di Azure.Submit the image configuration to the Azure Image Builder service.

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

La creazione dell'immagine e la replica in entrambe le aree possono richiedere un po' di tempo. Attendere il completamento di questa parte prima di passare alla creazione di una macchina virtuale.


## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale dalla versione dell'immagine creata da Azure Image Builder.Create a VM from the image version that was created by Azure Image Builder.

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

Dovresti vedere che l'immagine è stata personalizzata con un *messaggio del giorno* non appena viene stabilita la tua connessione SSH!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole ora provare a ripersonalizzare la versione dell'immagine per creare una nuova versione della stessa immagine, ignorare i passaggi successivi e passare a [Usare Azure Image Builder per creare un'altra versione dell'immagine.](image-builder-gallery-update-image-version.md)


In questo modo verrà eliminata l'immagine creata, insieme a tutti gli altri file di risorse. Assicurarsi di aver completato questa distribuzione prima di eliminare le risorse.

Quando si eliminano le risorse della raccolta immagini, è necessario eliminare tutte le versioni dell'immagine prima di poter eliminare la definizione dell'immagine utilizzata per crearle. Per eliminare una raccolta, è necessario prima aver eliminato tutte le definizioni di immagine nella raccolta.

Eliminare il modello del generatore di immagini.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Ottenere la versione dell'immagine creata da `0.`image builder, questo inizia sempre con , quindi eliminare la versione dell'immagine

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


Eliminare la definizione dell'immagine.

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

Altre informazioni sulle raccolte di immagini condivise di Azure .Learn more about [Azure Shared Image Galleries](shared-image-galleries.md).