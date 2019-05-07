---
title: Creare una macchina virtuale Windows con il generatore di immagini di Azure (anteprima)
description: Creare una macchina virtuale Windows con il generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: 01109aa83c12bda9b1d21ec25784d663f8abf700
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159721"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Anteprima: Creare una macchina virtuale Windows con il generatore di immagini di Azure

Questo articolo si descrive come creare un'immagine Windows personalizzata usando il generatore di immagini di macchina virtuale di Azure. L'esempio in questo articolo Usa tre diversi [impegnati nella personalizzazione](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) per personalizzare l'immagine:
- PowerShell (ScriptUri) - scaricare ed eseguire un' [script di PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Riavvio di Windows - riavvia la macchina virtuale.
- PowerShell (inline) - eseguire un comando specifico. In questo esempio viene creata una directory della macchina virtuale con `mkdir c:\\buildActions`.
- File - copiare un file da GitHub alla macchina virtuale. Questo esempio viene copiato [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) a `c:\buildArtifacts\index.html` nella macchina virtuale.

Si userà un modello JSON di esempio per configurare l'immagine. Il file con estensione JSON si sta usando è qui: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Verranno usati alcuni tipi di informazioni più volte, in modo che si creerà alcune variabili per archiviare tali informazioni.

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

Creare una variabile per l'ID sottoscrizione. È possibile ottenere questo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Creare il gruppo di risorse.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Concedere l'autorizzazione di Image Builder per creare risorse in tale gruppo di risorse. Il `--assignee` valore è l'ID di registrazione di app per il servizio di Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Scaricare l'esempio con estensione JSON

Scaricare il file con estensione JSON di esempio e configurarlo con le variabili di cui che è stato creato.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine per il servizio di generazione di immagini di macchina virtuale

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

Attendere fino al completamento della compilazione. L'operazione può richiedere circa 15 minuti.

## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale usando l'immagine che è stata compilata. Sostituire *<password>* con la propria password per il `aibuser` nella macchina virtuale.

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

Creare una connessione Desktop remoto alla VM usando il nome utente e la password impostati durante la creazione della macchina virtuale. All'interno della VM, aprire un prompt dei comandi e digitare:

```console
dir c:\
```

Dovrebbe essere questi due directory create durante la personalizzazione delle immagini:
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

Per altre informazioni sui componenti del file con estensione JSON utilizzato in questo articolo, vedere [riferimento a un modello generatore di immagini](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

