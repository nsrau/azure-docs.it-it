---
title: Usare Azure Image Builder con una raccolta immagini per le macchine virtuali Linux (anteprima)
description: Creare immagini Linux con il generatore di immagini di Azure e la raccolta di immagini condivise.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 9fc624ab24cd98d0025fe2a34bf48c29b47c50e9
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695414"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Anteprima: Creare un'immagine Linux e distribuirla in una raccolta di immagini condivise 

Questo articolo illustra come usare il generatore di immagini di Azure per creare una versione di immagine in una [raccolta di immagini condivise](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), quindi distribuire l'immagine a livello globale.


Per configurare l'immagine verrà usato un modello Sample. JSON. Il file con estensione JSON usato è il seguente: [helloImageTemplateforSIG. JSON](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Per distribuire l'immagine in una raccolta di immagini condivise, il modello USA [sharedImage](image-builder-json.md#distribute-sharedimage) come valore per la `distribute` sezione del modello.

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

Le informazioni verranno utilizzate ripetutamente, quindi verranno create alcune variabili per archiviare tali informazioni.

Per l'anteprima, Image Builder supporterà solo la creazione di immagini personalizzate nello stesso gruppo di risorse dell'immagine gestita di origine. Aggiornare il nome del gruppo di risorse in questo esempio in modo che sia lo stesso gruppo di risorse dell'immagine gestita di origine.

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

Creare una variabile per l'ID sottoscrizione. È possibile ottenerlo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Creare il gruppo di risorse.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Assegnare ad Azure Image Builder l'autorizzazione per creare risorse in tale gruppo di risorse. Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore di immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Creare una definizione di immagine e una raccolta

Per usare Image Builder con una raccolta di immagini condivise, è necessario disporre di una raccolta immagini esistente e di una definizione di immagine. Image Builder non creerà la raccolta immagini e la definizione di immagine.

Se non si dispone già di una raccolta e di una definizione di immagine da usare, iniziare creandoli. Per prima cosa, creare una raccolta di immagini.

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


## <a name="download-and-configure-the-json"></a>Scaricare e configurare il file con estensione JSON

Scaricare il modello con estensione JSON e configurarlo con le variabili.

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

Inviare la configurazione dell'immagine al servizio Generatore di immagini di Azure.

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

La creazione dell'immagine e la relativa replica in entrambe le aree possono richiedere alcuni minuti. Attendere il completamento di questa parte prima di procedere alla creazione di una macchina virtuale.


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

Connettersi tramite SSH alla macchina virtuale.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Si noterà che l'immagine è stata personalizzata con un *messaggio del giorno* non appena viene stabilita la connessione SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole provare a ripersonalizzare la versione dell'immagine per creare una nuova versione della stessa immagine, ignorare i passaggi successivi e passare a [usare il generatore di immagini di Azure per creare un'altra versione](image-builder-gallery-update-image-version.md)dell'immagine.


Questa operazione eliminerà l'immagine creata insieme a tutti gli altri file di risorse. Assicurarsi che la distribuzione sia stata completata prima di eliminare le risorse.

Quando si eliminano le risorse della raccolta immagini, è necessario eliminare tutte le versioni dell'immagine prima di poter eliminare la definizione di immagine usata per crearle. Per eliminare una raccolta, è prima di tutto necessario eliminare tutte le definizioni di immagine nella raccolta.

Eliminare il modello di generatore di immagini.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Ottenere la versione dell'immagine creata da Image Builder, che inizia sempre `0.`con e quindi Elimina la versione dell'immagine

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

Altre informazioni sulle [raccolte di immagini condivise di Azure](shared-image-galleries.md).