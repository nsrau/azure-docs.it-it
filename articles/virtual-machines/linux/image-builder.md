---
title: Creare una VM Linux con Azure Image Builder (anteprima)
description: Creare una VM Linux con il generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 1bac04bbb67c7472de92c6da322121bafc20a560
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695439"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Anteprima: Creare una VM Linux con Azure Image Builder

Questo articolo illustra come creare un'immagine Linux personalizzata usando il generatore di immagini di Azure e l'interfaccia della riga di comando di Azure. L'esempio in questo articolo usa tre diversi [personalizzatori](image-builder-json.md#properties-customize) per personalizzare l'immagine:

- Shell (ScriptUri): Scarica ed esegue uno [script della shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline): esegue comandi specifici. In questo esempio, i comandi inline includono la creazione di una directory e l'aggiornamento del sistema operativo.
- File: copia un [file da GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) in una directory nella macchina virtuale.

È anche possibile specificare un `buildTimeoutInMinutes`oggetto. Il valore predefinito è 240 minuti ed è possibile aumentare il tempo di compilazione per consentire le compilazioni a esecuzione prolungata.

Per configurare l'immagine verrà usato un modello Sample. JSON. Il file con estensione JSON usato è il seguente: [helloImageTemplateLinux. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Variabili di esempio di installazione

Le informazioni verranno utilizzate ripetutamente, quindi verranno create alcune variabili per archiviare tali informazioni.


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

Creare una variabile per l'ID sottoscrizione. È possibile ottenerlo usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.
Viene usato per archiviare l'elemento del modello di configurazione dell'immagine e l'immagine.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Impostare le autorizzazioni per il gruppo di risorse
Assegnare l'autorizzazione ' collaboratore ' al generatore immagini per creare l'immagine nel gruppo di risorse. Senza le autorizzazioni appropriate, la compilazione dell'immagine avrà esito negativo. 

Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore di immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Scaricare l'esempio di modello

È stato creato un modello di configurazione dell'immagine di esempio con parametri da usare. Scaricare il file Sample. JSON e configurarlo con le variabili impostate in precedenza.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

È possibile modificare questo esempio. JSON in base alle esigenze. Ad esempio, è possibile aumentare il valore di `buildTimeoutInMinutes` per consentire le compilazioni a esecuzione prolungata. È possibile modificare il file in Cloud Shell usando un editor di testo `vi`come.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Per l'immagine di origine, è sempre necessario [specificare una versione](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), non `latest`è possibile usare.
>
> Se si aggiunge o modifica il gruppo di risorse in cui viene distribuita l'immagine, è necessario assicurarsi che le [autorizzazioni siano impostate per il gruppo di risorse](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Invia la configurazione dell'immagine
Inviare la configurazione dell'immagine al servizio Generatore di immagini VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se viene completata correttamente, viene restituito un messaggio di operazione riuscita e viene creato un elemento del modello di configurazione del generatore di immagini nella $imageResourceGroup. È possibile visualizzare il gruppo di risorse nel portale se si Abilita "Mostra tipi nascosti".

In background, Image Builder crea anche un gruppo di risorse di gestione temporanea nella sottoscrizione. Image Builder usa il gruppo di risorse di staging per la compilazione dell'immagine. Il nome del gruppo di risorse avrà il formato seguente: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Non eliminare direttamente il gruppo di risorse di staging. Se si elimina l'artefatto del modello di immagine, il gruppo di risorse di staging verrà eliminato automaticamente. Per ulteriori informazioni, vedere la sezione relativa alla [pulizia](#clean-up) alla fine di questo articolo.

Se il servizio segnala un errore durante l'invio del modello di configurazione immagine, vedere la procedura di [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . Sarà anche necessario eliminare il modello prima di riprovare a inviare la compilazione. Per eliminare il modello:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Avviare la compilazione dell'immagine

Avviare la compilazione dell'immagine.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Attendere il completamento della compilazione. per questo esempio, l'operazione può richiedere 10-15 minuti.

Se si verificano errori, esaminare le procedure per la [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale usando l'immagine compilata.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Ottenere l'indirizzo IP dall'output della creazione della macchina virtuale e usarlo per SSH nella macchina virtuale.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Si noterà che l'immagine è stata personalizzata con un messaggio del giorno non appena viene stabilita la connessione SSH.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Al `exit` termine, digitare per chiudere la connessione SSH.

## <a name="check-the-source"></a>Controllare l'origine

Nel modello Image Builder, in ' Properties ' (proprietà), verrà visualizzata l'immagine di origine, lo script di personalizzazione che viene eseguito e il percorso in cui viene distribuito.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Per informazioni più dettagliate su questo file con estensione JSON, vedere informazioni di [riferimento sui modelli di Image Builder](image-builder-json.md)

## <a name="clean-up"></a>Eseguire la pulizia

Al termine, è possibile eliminare le risorse.

Eliminare il modello di generatore di immagini.

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

Per altre informazioni sui componenti del file con estensione JSON usato in questo articolo, vedere informazioni di [riferimento sui modelli di generatore di immagini](image-builder-json.md).
