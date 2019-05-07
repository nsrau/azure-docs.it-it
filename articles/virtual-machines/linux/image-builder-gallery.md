---
title: Usare generatore di immagini di Azure con una raccolta di immagini per le macchine virtuali Linux (anteprima)
description: Creare le immagini Linux con Azure Image Builder e raccolta di immagini condivise.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e9a8a30d9f5f170073c0ad671a248703b1078864
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159496"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Anteprima: Creare un'immagine Linux e distribuirlo a una raccolta di immagini condivise 

Questo articolo illustra come è possibile usare il generatore di immagini di Azure per creare una versione di immagine in un [raccolta di immagini condivise](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/shared-image-galleries), quindi distribuire l'immagine a livello globale.


Si userà un modello JSON di esempio per configurare l'immagine. Il file con estensione JSON si sta usando è qui: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Per distribuire l'immagine in una raccolta di immagini condivise, il modello Usa [sharedImage](image-builder-json.md#distribute-sharedimage) come valore per il `distribute` sezione del modello.

> [!IMPORTANT]
> Azure Image Builder è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Verranno usati alcuni tipi di informazioni più volte, in modo che si creerà alcune variabili per archiviare tali informazioni.

Per l'anteprima di generatore di immagini supporta solo la creazione di immagini personalizzate nello stesso gruppo di risorse come immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio sia il gruppo di risorse stesso come l'immagine gestita di origine.

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

Creare una variabile per l'ID sottoscrizione. È possibile ottenere questo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Creare il gruppo di risorse.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Concedere l'autorizzazione di Azure Image Builder per creare risorse in tale gruppo di risorse. Il `--assignee` valore è l'ID di registrazione di app per il servizio di Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Creare una definizione dell'immagine e raccolta

Creare una raccolta di immagini. 

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


## <a name="download-and-configure-the-json"></a>Scaricare e configurare il JSON

Scaricare il modello JSON e configurarlo con le variabili.

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

Questa parte successiva creerà la versione dell'immagine nella raccolta. 

Inviare la configurazione dell'immagine per il servizio Azure Image Builder.

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

Creazione dell'immagine e la replica in entrambe le aree possono richiedere un po' di tempo. Attendere fino a quando questa parte è terminata prima di procedere alla creazione di una macchina virtuale.


## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale dalla versione di immagine che è stata creata dal generatore di immagini di Azure.

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

Dovrebbe essere l'immagine è stato personalizzato con un *messaggi del giorno* , non appena viene stabilita la connessione SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole ora provare nuovamente la personalizzazione la versione dell'immagine per creare una nuova versione della stessa immagine, ignorare i passaggi e andare al [Usa il generatore di immagini di Azure per creare un'altra versione dell'immagine](image-builder-gallery-update-image-version.md).


Questa operazione eliminerà l'immagine che è stato creato, insieme a tutti gli altri file di risorse. Assicurarsi che al termine con la distribuzione prima di eliminare le risorse.

Quando si elimina le risorse della raccolta immagini, è necessario eliminare tutte le versioni immagine prima di poter eliminare la definizione dell'immagine utilizzata per crearli. Per eliminare una raccolta, è necessario innanzitutto avere eliminato tutte le definizioni di immagine nella raccolta.

Eliminare il modello generatore di immagini.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Ottenere la versione dell'immagine creata dal generatore di immagini, questa inizia sempre con `0.`e quindi eliminare la versione dell'immagine

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

Altre informazioni sulle [raccolte di immagini di Azure condiviso](shared-image-galleries.md).