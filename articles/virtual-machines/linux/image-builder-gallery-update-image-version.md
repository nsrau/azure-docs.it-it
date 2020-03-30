---
title: Creare una nuova versione dell'immagine della macchina virtuale da una versione dell'immagine esistente usando Azure Image Builder (anteprima)Create a new VM image version from an existing image version using Azure Image Builder (preview)
description: Creare una nuova versione dell'immagine della macchina virtuale da una versione dell'immagine esistente usando Azure Image Builder.Create a new VM image version from an existing image version using Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246807"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Anteprima: Creare una nuova versione dell'immagine della macchina virtuale da una versione dell'immagine esistente usando Azure Image BuilderPreview: Create a new VM image version from an existing image version using Azure Image Builder

In questo articolo viene illustrato come creare una versione di immagine esistente in una [raccolta immagini condivise,](shared-image-galleries.md)aggiornarla e pubblicarla come nuova versione dell'immagine nella raccolta.

Utilizzeremo un modello .json di esempio per configurare l'immagine. Il file .json che stiamo usando è qui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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

Se hai usato [Crea un'immagine e distribuisci a una Galleria di immagini condivise](image-builder-gallery.md) per creare la tua Galleria di immagini condivise, hai già creato alcune delle variabili di cui abbiamo bisogno. In caso contrario, impostare alcune variabili da utilizzare per questo esempio.

Per anteprima, il generatore di immagini supporterà solo la creazione di immagini personalizzate nello stesso gruppo di risorse dell'immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio in modo che sia lo stesso gruppo di risorse dell'immagine gestita di origine.


```console
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

Creare una variabile per l'ID sottoscrizione. È possibile ottenere `az account show | grep id`questo utilizzando .

```console
subscriptionID=<Subscription ID>
```

Ottenere la versione dell'immagine che si desidera aggiornare.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Se si dispone già di una raccolta di immagini condivise e non è stato seguito l'esempio precedente, sarà necessario assegnare le autorizzazioni per Il generatore di immagini per accedere al gruppo di risorse, in modo che possa accedere alla raccolta.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Esempio di modifica helloImage
È possibile esaminare l'esempio che stiamo per utilizzare aprendo il file .json qui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) insieme al riferimento al [modello di Generatore](image-builder-json.md)di immagini . 


Scaricare l'esempio .json e configurarlo con le variabili. 

```console
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

Inviare la configurazione dell'immagine al servizio VM Image Builder.Submit the image configuration to the VM Image Builder Service.

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

Attendere che l'immagine sia stata compilata e la replica prima di passare al passaggio successivo.


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

Creare una connessione SSH alla macchina virtuale usando l'indirizzo IP pubblico della macchina virtuale.

```console
ssh azureuser@<pubIp>
```

Dovresti vedere che l'immagine è stata personalizzata con un "Messaggio del giorno" non appena viene stabilita la connessione SSH.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digitare `exit` per chiudere la connessione SSH.

È inoltre possibile elencare le versioni delle immagini ora disponibili nella raccolta.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui componenti del file .json utilizzati in questo articolo, vedere Informazioni di riferimento sul [modello del generatore](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)di immagini.