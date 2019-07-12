---
title: Creare una nuova versione dell'immagine da una versione di immagine esistente utilizzando Generatore di immagini di Azure (anteprima)
description: Creare una nuova versione dell'immagine da una versione di immagine esistente utilizzando Generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 9155f6fc1243f0d2e4d63f2718ccfd6846ebbc50
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671491"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Anteprima: Creare una nuova versione dell'immagine da una versione di immagine esistente utilizzando Generatore di immagini di Azure

Questo articolo illustra come eseguire una versione di immagine esistente in un [raccolta di immagini condivise](shared-image-galleries.md), aggiornarlo e pubblicarlo come una nuova versione dell'immagine nella raccolta.

Si userà un modello JSON di esempio per configurare l'immagine. Il file con estensione JSON si sta usando è qui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Registrare la funzionalità
Per usare Azure Image Builder durante l'anteprima, è necessario registrare la nuova funzionalità.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Controllare lo stato della registrazione di funzionalità.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verificare la registrazione.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Se non si dice registrato, eseguire le operazioni seguenti:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Impostare le variabili e le autorizzazioni

Se è stata usata [creare un'immagine e distribuire in una raccolta di immagini condivise](image-builder-gallery.md) per creare la raccolta di immagini condivise, è già stato creato alcune delle variabili di cui abbiamo bisogno. In caso contrario, installare alcune variabili da usare per questo esempio.

Per l'anteprima di generatore di immagini supporta solo la creazione di immagini personalizzate nello stesso gruppo di risorse come immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio sia il gruppo di risorse stesso come l'immagine gestita di origine.


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Creare una variabile per l'ID sottoscrizione. È possibile ottenere questo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Ottenere la versione dell'immagine che si desidera aggiornare.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Se si dispone già una propria raccolta di immagini condivise e non è stata seguita l'esempio precedente, è necessario assegnare le autorizzazioni per la generazione di immagini accedere al gruppo di risorse, in modo che possa accedere alla raccolta.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modificare l'esempio helloImage
È possibile esaminare l'esempio si sta tentando di usare aprendo il file con estensione JSON qui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) con il [riferimenti dei modelli di Image Builder](image-builder-json.md). 


Scaricare l'esempio con estensione JSON e configurarlo con le variabili. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine per il servizio di generatore di immagini di macchina virtuale.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Attendere fino a quando l'immagine è stata compilata e la replica prima di procedere al passaggio successivo.


## <a name="create-the-vm"></a>Creare la VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Creare una connessione SSH alla VM usando l'indirizzo IP pubblico della macchina virtuale.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Verrà visualizzata che l'immagine è stato personalizzato con un "messaggio di Day", non appena viene stabilita la connessione SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Tipo `exit` per chiudere la connessione SSH.

È anche possibile elencare le versioni delle immagini sono disponibili nella raccolta.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file con estensione JSON utilizzato in questo articolo, vedere [riferimento a un modello generatore di immagini](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).