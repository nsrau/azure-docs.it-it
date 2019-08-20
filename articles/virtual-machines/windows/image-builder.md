---
title: Creare una macchina virtuale Windows con il generatore di immagini di Azure (anteprima)
description: Creare una VM Windows con il generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 9dc4909db5560be6eb082dbad85d4b2d42113bdd
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828709"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Anteprima: Creare una VM Windows con Azure Image Builder

Questo articolo illustra come creare un'immagine di Windows personalizzata usando il generatore di immagini di VM di Azure. L'esempio in questo articolo usa i [personalizzatori](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) per personalizzare l'immagine:
- PowerShell (ScriptUri): scaricare ed eseguire uno [script di PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Riavvio di Windows-riavvia la macchina virtuale.
- PowerShell (inline): eseguire un comando specifico. In questo esempio viene creata una directory nella macchina virtuale usando `mkdir c:\\buildActions`.
- File: copiare un file da GitHub nella macchina virtuale. Questo esempio copia [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) `c:\buildArtifacts\index.html` in nella macchina virtuale.

È anche possibile specificare un `buildTimeoutInMinutes`oggetto. Il valore predefinito è 240 minuti ed è possibile aumentare il tempo di compilazione per consentire le compilazioni a esecuzione prolungata.

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

## <a name="set-variables"></a>Impostare variabili

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
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Questo gruppo di risorse viene usato per archiviare l'artefatto del modello di configurazione dell'immagine e l'immagine.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Impostare le autorizzazioni per il gruppo di risorse

Assegnare l'autorizzazione ' collaboratore ' al generatore immagini per creare l'immagine nel gruppo di risorse. In assenza di questa operazione, la compilazione dell'immagine avrà esito negativo. 

Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore di immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Scaricare l'esempio di modello di configurazione immagine

È stato creato un modello di configurazione dell'immagine con parametri da provare. Scaricare il file example. JSON e configurarlo con le variabili impostate in precedenza.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

È possibile modificare questo esempio nel terminale usando un editor di testo come `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Per l'immagine di origine, è sempre necessario [specificare una versione](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), non è `latest`possibile utilizzare.
> Se si aggiunge o modifica il gruppo di risorse in cui viene distribuita l'immagine, è necessario [impostare le autorizzazioni](#set-permissions-on-the-resource-group) per il gruppo di risorse.
 
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

Al termine, viene restituito un messaggio di operazione riuscita alla console e viene creato un oggetto `Image Builder Configuration Template` `$imageResourceGroup`in. È possibile visualizzare questa risorsa nel gruppo di risorse nel portale di Azure, se si Abilita "Mostra tipi nascosti".

In background, Image Builder creerà anche un gruppo di risorse di staging nella sottoscrizione. Questo gruppo di risorse viene usato per la compilazione dell'immagine. Il formato sarà il seguente:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Non è necessario eliminare direttamente il gruppo di risorse di staging. Per prima cosa eliminare l'artefatto del modello di immagine, in modo che il gruppo di risorse di staging venga eliminato.

Se il servizio segnala un errore durante l'invio del modello di configurazione immagine:
-  Esaminare le procedure per la [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . 
- È necessario eliminare il modello, usando il frammento di codice seguente, prima di riprovare l'invio.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Avviare la compilazione dell'immagine
Avviare il processo di compilazione dell'immagine usando [AZ Resource Invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Attendere il completamento della compilazione. Questa operazione può richiedere circa 15 minuti.

Se si verificano errori, esaminare le procedure per la [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


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

### <a name="delete-the-image-builder-template"></a>Eliminare il modello di generatore di immagini
```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Eliminare il gruppo di risorse immagine
```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file con estensione JSON usato in questo articolo, vedere informazioni di [riferimento sui modelli di generatore di immagini](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
