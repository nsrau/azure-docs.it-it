---
title: Creare una VM Linux con il generatore di immagini di Azure (anteprima)
description: Creare una VM Linux con il generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667517"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Anteprima: Creare una VM Linux con il generatore di immagini di Azure

Questo articolo illustra come creare un'immagine Linux personalizzata usando il generatore di immagini di Azure e la CLI di Azure. L'esempio in questo articolo Usa tre diversi [impegnati nella personalizzazione](image-builder-json.md#properties-customize) per personalizzare l'immagine:

- Shell (ScriptUri) - download e viene eseguito un [script della shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - esecuzione dei comandi specifici. In questo esempio, i comandi inline includono la creazione di una directory e l'aggiornamento del sistema operativo.
- File - copia una [file da GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) in una directory nella macchina virtuale.


Si userà un modello JSON di esempio per configurare l'immagine. Il file con estensione JSON si sta usando è qui: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Configurare le variabili di esempio

Verranno usati alcuni tipi di informazioni più volte, in modo che si creerà alcune variabili per archiviare tali informazioni.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Creare una variabile per l'ID sottoscrizione. È possibile ottenere questo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.
Viene utilizzato per archiviare l'elemento di modello di configurazione di immagine e l'immagine.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Impostare le autorizzazioni per il gruppo di risorse
Concedere l'autorizzazione di "Collaboratore" di Image Builder per creare l'immagine nel gruppo di risorse. Senza le autorizzazioni appropriate, la compilazione dell'immagine non riuscirà. 

Il `--assignee` valore è l'ID di registrazione di app per il servizio di Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Scaricare l'esempio di modello

È stato creato un modello di configurazione di immagini di esempio con parametri da usare. Scaricare il file con estensione JSON di esempio e configurarlo con le variabili impostate in precedenza.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

È possibile modificare questo JSON di esempio in base alle esigenze. Ad esempio, è possibile aumentare il valore di `buildTimeoutInMinutes` per consentire le compilazioni in esecuzione più lungo. È possibile modificare il file in Cloud Shell usando `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Per l'immagine di origine, è necessario sempre [specificare la versione](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), non è possibile usare `latest`.
>
> Se si aggiungono o si modifica il gruppo di risorse in cui viene distribuita l'immagine, è necessario assicurarsi che il [le autorizzazioni vengono impostate per il gruppo di risorse](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Inviare la configurazione dell'immagine
Inviare la configurazione dell'immagine per il servizio di generazione di immagini di macchina virtuale

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se completato correttamente, verrà restituito un messaggio di conferma e creare un elemento di modello di image builder configurazione nel $imageResourceGroup. Se si abilita 'Mostra tipi nascosti', è possibile visualizzare il gruppo di risorse nel portale.

Inoltre, in background, Image Builder crea un gruppo di risorse nella sottoscrizione di gestione temporanea. Generatore di immagini utilizza il gruppo di risorse di gestione temporanea per la compilazione dell'immagine. Il nome del gruppo di risorse sarà nel formato seguente: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Non eliminare direttamente il gruppo di risorse di gestione temporanea. Se si elimina l'elemento di modello di immagine, eliminerà automaticamente il gruppo di risorse di gestione temporanea. Per altre informazioni, vedere la [pulire](#clean-up) sezione alla fine di questo articolo.

Se il servizio ha segnalato un errore durante l'invio di modello di configurazione di immagine, vedere la [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) passaggi. È anche necessario eliminare il modello prima di ritentare l'invio della compilazione. Per eliminare il modello:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Avviare la compilazione di immagini

Avviare la compilazione dell'immagine.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Attendere fino a quando la compilazione è stata completata, in questo esempio, potrebbero essere necessari 10-15 minuti.

Se si verificano errori, esaminare questi [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) passaggi.


## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale usando l'immagine che è stata compilata.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Ottenere l'indirizzo IP dall'output di creazione della macchina virtuale e usarlo per la connessione SSH alla macchina virtuale.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Dovrebbe essere che l'immagine è stato personalizzato con un messaggio del giorno, non appena viene stabilita la connessione SSH.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Tipo `exit` al termine chiudere la connessione SSH.

## <a name="check-the-source"></a>Controllare l'origine

Nel modello generatore di immagini, in 'Proprietà', si noterà l'immagine di origine, personalizzazione, creare lo script viene eseguito, e in cui viene distribuita.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Per altre informazioni su questo file con estensione JSON, vedere [riferimento a un modello generatore di immagini](image-builder-json.md)

## <a name="clean-up"></a>Eseguire la pulizia

Al termine, è possibile eliminare le risorse.

Eliminare il modello generatore di immagini.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Eliminare il gruppo di risorse immagine.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file con estensione JSON utilizzato in questo articolo, vedere [riferimenti dei modelli di Image Builder](image-builder-json.md).
