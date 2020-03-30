---
title: Creare una macchina virtuale Windows con Azure Image Builder (anteprima)Create a Windows VM with Azure Image Builder (preview)
description: Creare una macchina virtuale Windows con Azure Image Builder.Create a Windows VM with the Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: e82d82dac833f7455e3d83d7e11c0c57c4eea816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238797"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Anteprima: Creare una macchina virtuale Windows con Azure Image BuilderPreview: Create a Windows VM with Azure Image Builder

Questo articolo illustra come creare un'immagine Windows personalizzata usando Azure VM Image Builder.This article is to show you how you can create a customized Windows image using the Azure VM Image Builder. L'esempio in questo articolo usa [gli addetti](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) alla personalizzazione dell'immagine:The example in this article uses customizers for customizing the image:
- PowerShell (ScriptUri): consente di scaricare ed eseguire uno script di [PowerShell.](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)
- Riavvio di Windows: riavvia la macchina virtuale.
- PowerShell (inline) - eseguire un comando specifico. In questo esempio viene creata una `mkdir c:\\buildActions`directory nella macchina virtuale usando .
- File: copia un file da GitHub nella macchina virtuale. Questo esempio [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) copia `c:\buildArtifacts\index.html` index.md nella macchina virtuale.

È inoltre possibile `buildTimeoutInMinutes`specificare un file . Il valore predefinito è 240 minuti ed è possibile aumentare il tempo di compilazione per consentire compilazioni a esecuzione prolungata.

Utilizzeremo un modello .json di esempio per configurare l'immagine. Il file .json che stiamo usando è qui: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="set-variables"></a>Impostare variabili

Utilizzeremo alcune informazioni ripetutamente, quindi creeremo alcune variabili per memorizzare tali informazioni.


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

Creare una variabile per l'ID sottoscrizione. È possibile ottenere `az account show | grep id`questo utilizzando .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Questo gruppo di risorse viene usato per archiviare l'elemento del modello di configurazione dell'immagine e l'immagine.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Impostare le autorizzazioni per il gruppo di risorseSet permissions on the resource group

Concedere a Image Builder l'autorizzazione "collaboratore" per creare l'immagine nel gruppo di risorse. Senza questo, la compilazione dell'immagine avrà esito negativo. 

Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Scaricare l'esempio di modello di configurazione immagineDownload the image configuration template example

È stato creato un modello di configurazione immagine con parametri da provare. Scaricare il file .json di esempio e configurarlo con le variabili impostate in precedenza.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

È possibile modificare questo esempio nel terminale `vi`utilizzando un editor di testo come .

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Per l'immagine di origine, è necessario `latest` [specificare](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)sempre una versione , non è possibile utilizzare .
> Se si aggiunge o si modifica il gruppo di risorse in cui viene distribuita l'immagine, è necessario impostare le [autorizzazioni](#set-permissions-on-the-resource-group) per il gruppo di risorse.
 
## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine al servizio VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Al termine, verrà restituito un messaggio di operazione `Image Builder Configuration Template` riuscita `$imageResourceGroup`alla console e verrà creato un nel file . È possibile visualizzare questa risorsa nel gruppo di risorse nel portale di Azure, se si abilita "Mostra tipi nascosti".

In background, Image Builder creerà anche un gruppo di risorse di gestione temporanea nella sottoscrizione. Questo gruppo di risorse viene usato per la compilazione dell'immagine. Sarà in questo formato:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Non è necessario eliminare direttamente il gruppo di risorse di staging. Eliminare innanzitutto l'elemento del modello di immagine, in modo da eliminare il gruppo di risorse di gestione temporanea.

Se il servizio segnala un errore durante l'invio del modello di configurazione immagine:If the service reports a failure during the image configuration template submission:
-  Rivedere questi passaggi [per la risoluzione dei problemi.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 
- È necessario eliminare il modello, usando il frammento di codice seguente, prima di riprovare a eseguire l'invio.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Avviare la compilazione dell'immagine
Avviare il processo di creazione dell'immagine utilizzando [az resource invoke-action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Attendere il completamento della compilazione. Questa operazione può richiedere circa 15 minuti.

Se si verificano errori, esaminare questi passaggi per [la risoluzione dei problemi.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale usando l'immagine compilata. Sostituire * \<password>* con la `aibuser` propria password per la macchina virtuale.

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

Creare una connessione Desktop remoto alla macchina virtuale usando il nome utente e la password impostati al momento della creazione della macchina virtuale. All'interno della macchina virtuale aprire un prompt dei comandi e digitare:Inside the VM, open a cmd prompt and type:

```console
dir c:\
```

Verranno visualizzate queste due directory create durante la personalizzazione dell'immagine:You should see these two directories created during image customization:
- buildActions
- buildArtifacts (compilazioneArtifacts)

## <a name="clean-up"></a>Eseguire la pulizia

Al termine, eliminare le risorse.

### <a name="delete-the-image-builder-template"></a>Eliminare il modello del generatore di immagini

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Eliminare il gruppo di risorse immagineDelete the image resource group

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui componenti del file .json utilizzati in questo articolo, vedere Informazioni di riferimento sul [modello del generatore](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)di immagini.
