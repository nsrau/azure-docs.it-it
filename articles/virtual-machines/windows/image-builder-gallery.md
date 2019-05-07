---
title: Usare generatore di immagini di Azure con una raccolta di immagini per le macchine virtuali di Windows (anteprima)
description: Creare immagini di Windows con Azure Image Builder e raccolta di immagini condivise.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-widows
manager: jeconnoc
ms.openlocfilehash: 2453d37720bcf48b95b428cf78c6186de40b31aa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160111"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Anteprima: Creare un'immagine di Windows e distribuirlo a una raccolta di immagini condivise 

In questo articolo è illustrare come è possibile usare il generatore di immagini di Azure per creare una versione di immagine in un [raccolta di immagini condivise](shared-image-galleries.md), quindi distribuire l'immagine a livello globale.

Si userà un modello JSON per configurare l'immagine. Il file con estensione JSON si sta usando è qui: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Per distribuire l'immagine in una raccolta di immagini condivise, il modello Usa [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) come valore per il `distribute` sezione del modello.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Se non si dice registrato, eseguire le operazioni seguenti:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Impostare le variabili e le autorizzazioni 

Verranno usati alcuni tipi di informazioni più volte, in modo che si creerà alcune variabili per archiviare tali informazioni. Sostituire i valori per le variabili, ad esempio `username` e `vmpassword`, con le proprie informazioni.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Creare una variabile per l'ID sottoscrizione. È possibile ottenere questo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
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
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Scaricare e configurare il JSON

Scaricare il modello JSON e configurarlo con le variabili.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Creare la versione dell'immagine

Questa parte successiva creerà la versione dell'immagine nella raccolta. 

Inviare la configurazione dell'immagine per il servizio Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Creazione dell'immagine e la replica in entrambe le aree possono richiedere un po' di tempo. Attendere fino a quando questa parte è terminata prima di procedere alla creazione di una macchina virtuale.


## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale dalla versione di immagine che è stata creata dal generatore di immagini di Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Verificare la personalizzazione
Creare una connessione Desktop remoto alla VM usando il nome utente e la password impostati durante la creazione della macchina virtuale. All'interno della VM, aprire un prompt dei comandi e digitare:

```console
dir c:\
```

Dovrebbe essere una directory denominata `buildActions` che è stato creato durante la personalizzazione delle immagini.


## <a name="clean-up-resources"></a>Pulire le risorse
Se si tenta nuovamente personalizzazione la versione dell'immagine per creare una nuova versione della stessa immagine, a questo punto **ignorare questo passaggio** e andare al [Usa il generatore di immagini di Azure per creare un'altra versione dell'immagine](image-builder-gallery-update-image-version.md).


Questa operazione eliminerà l'immagine che è stato creato, insieme a tutti gli altri file di risorse. Assicurarsi che al termine con la distribuzione prima di eliminare le risorse.

Quando si elimina le risorse della raccolta immagini, è necessario eliminare tutte le versioni immagine prima di poter eliminare la definizione dell'immagine utilizzata per crearli. Per eliminare una raccolta, è necessario innanzitutto avere eliminato tutte le definizioni di immagine nella raccolta.

Eliminare il modello generatore di immagini.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
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

## <a name="next-steps"></a>Fasi successive

Per informazioni su come aggiornare la versione dell'immagine è stato creato, vedere [Usa il generatore di immagini di Azure per creare un'altra versione dell'immagine](image-builder-gallery-update-image-version.md).