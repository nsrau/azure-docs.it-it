---
title: Creare una nuova versione dell'immagine di macchina virtuale da una versione di immagine esistente usando il generatore di immagini di Azure (anteprima)
description: Creare una nuova versione dell'immagine di macchina virtuale da una versione di immagine esistente usando Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: d226a7b31dc9f8cf219c6d0d0f886fb5b21741a6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976333"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Anteprima: creare una nuova versione dell'immagine di macchina virtuale da una versione di immagine esistente usando Azure Image Builder

Questo articolo illustra come creare una versione di immagine esistente in una [raccolta di immagini condivise](shared-image-galleries.md), aggiornarla e pubblicarla come nuova versione dell'immagine nella raccolta.

Per configurare l'immagine verrà usato un modello Sample. JSON. Il file con estensione JSON usato è il seguente: [helloImageTemplateforSIGfromSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Registrare le funzionalità
Per usare Azure Image Builder durante l'anteprima, è necessario registrare la nuova funzionalità.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verificare lo stato della registrazione della funzionalità.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Controllare la registrazione.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Se non sono registrati, eseguire le operazioni seguenti:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Impostare variabili e autorizzazioni

Se è stata usata la [creazione di un'immagine e la distribuzione in una raccolta di immagini condivise](image-builder-gallery.md) per creare la raccolta di immagini condivise, sono già state create alcune delle variabili necessarie. In caso contrario, configurare alcune variabili da usare per questo esempio.

Per l'anteprima, Image Builder supporterà solo la creazione di immagini personalizzate nello stesso gruppo di risorse dell'immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio in modo che sia lo stesso gruppo di risorse dell'immagine gestita di origine.


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

Creare una variabile per l'ID sottoscrizione. Questa operazione può essere usata `az account show | grep id`.

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


Se si dispone già di una raccolta di immagini condivise e non è stata seguita l'esempio precedente, sarà necessario assegnare le autorizzazioni per il generatore di immagini per accedere al gruppo di risorse, in modo che possa accedere alla raccolta.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Esempio di modifica di helloImage
È possibile esaminare l'esempio che verrà usato aprendo il file con estensione JSON qui: [helloImageTemplateforSIGfromSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) insieme al [riferimento al modello di generatore di immagini](image-builder-json.md). 


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

Inviare la configurazione dell'immagine al servizio Generatore di immagini VM.

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

Attendere che l'immagine sia stata compilata e replica prima di procedere al passaggio successivo.


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

Si noterà che l'immagine è stata personalizzata con un "messaggio del giorno" non appena viene stabilita la connessione SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digitare `exit` per chiudere la connessione SSH.

È anche possibile elencare le versioni dell'immagine che sono ora disponibili nella raccolta.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file con estensione JSON usato in questo articolo, vedere informazioni di [riferimento sui modelli di generatore di immagini](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).