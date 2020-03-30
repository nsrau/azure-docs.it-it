---
title: Creare una nuova versione dell'immagine da una versione di immagine esistente usando Azure Image Builder (anteprima)Create a new image version from an existing image version using Azure Image Builder (preview)
description: Creare una nuova versione dell'immagine della macchina virtuale da una versione dell'immagine esistente usando Azure Image Builder.Create a new VM image version from an existing image version using Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 60a37588ddcac339d9545fb6f597ef7bdc17ccb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247368"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Anteprima: Creare una nuova versione dell'immagine della macchina virtuale da una versione dell'immagine esistente usando Azure Image BuilderPreview: Create a new VM image version from an existing image version using Azure Image Builder

In questo articolo viene illustrato come creare una versione di immagine esistente in una [raccolta immagini condivise,](shared-image-galleries.md)aggiornarla e pubblicarla come nuova versione dell'immagine nella raccolta.

Utilizzeremo un modello .json di esempio per configurare l'immagine. Il file .json che stiamo usando è qui: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Se non dicono registrati, eseguire quanto segue:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Impostare variabili e autorizzazioni

Se hai usato [Crea un'immagine e distribuisci a una Galleria di immagini condivise](image-builder-gallery.md) per creare la tua Galleria di immagini condivise, hai già creato le variabili di cui abbiamo bisogno. In caso contrario, impostare alcune variabili da utilizzare per questo esempio.

Per anteprima, il generatore di immagini supporterà solo la creazione di immagini personalizzate nello stesso gruppo di risorse dell'immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio in modo che sia lo stesso gruppo di risorse dell'immagine gestita di origine.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Creare una variabile per l'ID sottoscrizione. È possibile ottenere `az account show | grep id`questo utilizzando .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Ottenere la versione dell'immagine che si desidera aggiornare.

```azurecli-interactive
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
È possibile esaminare l'esempio che stiamo per utilizzare aprendo il file .json qui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) insieme al riferimento al [modello di Generatore](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)di immagini . 


Scaricare l'esempio .json e configurarlo con le variabili. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine al servizio VM Image Builder.Submit the image configuration to the VM Image Builder Service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Attendere che l'immagine sia stata compilata e la replica prima di passare al passaggio successivo.


## <a name="create-the-vm"></a>Creare la VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Verificare la personalizzazione
Creare una connessione Desktop remoto alla macchina virtuale usando il nome utente e la password impostati al momento della creazione della macchina virtuale. All'interno della macchina virtuale aprire un prompt dei comandi e digitare:Inside the VM, open a cmd prompt and type:

```console
dir c:\
```

Dovrebbero ora essere visualizzate due directory:
- `buildActions`creato nella prima versione dell'immagine.
- `buildActions2`che è stato creato come parte verso l'alto aggiornando la prima versione dell'immagine per creare la seconda versione dell'immagine.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui componenti del file .json utilizzati in questo articolo, vedere Informazioni di riferimento sul [modello del generatore](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)di immagini.