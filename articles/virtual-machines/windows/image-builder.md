---
title: Creare una macchina virtuale Windows con il generatore di immagini di Azure (anteprima)
description: Creare una VM Windows con il generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 103ec3c9ee4bd6b3b83408b0f9958a22d3a22ae1
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261053"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Anteprima: Creare una VM Windows con Azure Image Builder

Questo articolo illustra come creare un'immagine di Windows personalizzata usando il generatore di immagini di VM di Azure. L'esempio in questo articolo usa tre diversi [personalizzatori](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) per personalizzare l'immagine:
- PowerShell (ScriptUri): scaricare ed eseguire uno [script di PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Riavvio di Windows-riavvia la macchina virtuale.
- PowerShell (inline): eseguire un comando specifico. In questo esempio viene creata una directory nella macchina virtuale usando `mkdir c:\\buildActions`.
- File: copiare un file da GitHub nella macchina virtuale. Questo esempio copia [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) `c:\buildArtifacts\index.html` in nella macchina virtuale.

Per configurare l'immagine verrà usato un modello Sample. JSON. Il file con estensione JSON usato è il seguente: [helloImageTemplateWin. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Le informazioni verranno utilizzate ripetutamente, quindi verranno create alcune variabili per archiviare tali informazioni.

```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Creare una variabile per l'ID sottoscrizione. È possibile ottenerlo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Creare il gruppo di risorse.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Assegnare a Image Builder l'autorizzazione per creare risorse in tale gruppo di risorse. Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore di immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Scaricare l'esempio JSON

Scaricare il file example. JSON e configurarlo con le variabili create.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine al servizio Generatore di immagini VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Attendere il completamento della compilazione. Questa operazione può richiedere circa 15 minuti.

## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale usando l'immagine compilata. `aibuser`  *Sostituire\<password >* con la propria password per nella macchina virtuale.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verificare la personalizzazione

Creare una connessione Desktop remoto alla macchina virtuale usando il nome utente e la password impostati al momento della creazione della macchina virtuale. All'interno della macchina virtuale aprire un prompt dei comandi e digitare:

```console
dir c:\
```

Verranno visualizzate le due directory create durante la personalizzazione delle immagini:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Eseguire la pulizia

Al termine, eliminare le risorse.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file con estensione JSON usato in questo articolo, vedere informazioni di [riferimento sui modelli di generatore di immagini](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

