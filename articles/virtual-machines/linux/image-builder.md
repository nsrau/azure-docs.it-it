---
title: Creare una macchina virtuale Linux con Azure Image Builder (anteprima)Create a Linux VM with Azure Image Builder (preview)
description: Creare una macchina virtuale Linux con Azure Image Builder.Create a Linux VM with the Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066675"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Anteprima: Creare una macchina virtuale Linux con Azure Image BuilderPreview: Create a Linux VM with Azure Image Builder

Questo articolo illustra come creare un'immagine Linux personalizzata usando Azure Image Builder e l'interfaccia della riga di comando di Azure.This article shows you how you can create a customized Linux image using the Azure Image Builder and the Azure CLI. L'esempio in questo articolo usa tre diverse [opzioni di personalizzazione](image-builder-json.md#properties-customize) per la personalizzazione dell'immagine:The example in this article uses three different customizers for customizing the image:

- Shell (ScriptUri) - scarica ed esegue uno [script della shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - esegue comandi specifici. In questo esempio, i comandi inline includono la creazione di una directory e l'aggiornamento del sistema operativo.
- File: copia un [file da GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) in una directory nella macchina virtuale.

È inoltre possibile `buildTimeoutInMinutes`specificare un file . Il valore predefinito è 240 minuti ed è possibile aumentare il tempo di compilazione per consentire compilazioni a esecuzione prolungata.

Utilizzeremo un modello .json di esempio per configurare l'immagine. Il file .json che stiamo usando è qui: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Variabili di esempio di installazione

Utilizzeremo alcune informazioni ripetutamente, quindi creeremo alcune variabili per memorizzare tali informazioni.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Creare una variabile per l'ID sottoscrizione. È possibile ottenere `az account show | grep id`questo utilizzando .

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.
Viene utilizzato per archiviare l'elemento del modello di configurazione dell'immagine e l'immagine.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Impostare le autorizzazioni per il gruppo di risorseSet permissions on the resource group
Concedere a Image Builder l'autorizzazione "collaboratore" per creare l'immagine nel gruppo di risorse. Senza le autorizzazioni appropriate, la compilazione dell'immagine avrà esito negativo. 

Il `--assignee` valore è l'ID di registrazione dell'app per il servizio Generatore immagini. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Scaricare l'esempio di modelloDownload the template example

È stato creato un modello di configurazione dell'immagine di esempio con parametri da usare. Scaricare il file .json di esempio e configurarlo con le variabili impostate in precedenza.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

È possibile modificare questo esempio .json in base alle esigenze. Ad esempio, è possibile `buildTimeoutInMinutes` aumentare il valore di per consentire compilazioni a esecuzione prolungata. È possibile modificare il file in Cloud `vi`Shell utilizzando un editor di testo come .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Per l'immagine di origine, è necessario `latest` [specificare](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)sempre una versione , non è possibile utilizzare .
>
> Se si aggiunge o si modifica il gruppo di risorse in cui viene distribuita l'immagine, è necessario assicurarsi che le [autorizzazioni siano impostate per il gruppo](#set-permissions-on-the-resource-group)di risorse.


## <a name="submit-the-image-configuration"></a>Inviare la configurazione dell'immagine
Inviare la configurazione dell'immagine al servizio VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se viene completato correttamente, restituirà un messaggio di operazione riuscita e creerà un elemento del modello di configurazione del generatore di immagini nel $imageResourceGroup. È possibile visualizzare il gruppo di risorse nel portale se si abilita "Mostra tipi nascosti".

Inoltre, in background, Image Builder crea un gruppo di risorse di gestione temporanea nella sottoscrizione. Image Builder usa il gruppo di risorse di staging per la compilazione dell'immagine. Il nome del gruppo di risorse `IT_<DestinationResourceGroup>_<TemplateName>`sarà nel seguente formato: .

> [!IMPORTANT]
> Non eliminare direttamente il gruppo di risorse di staging. Se si elimina l'elemento del modello di immagine, verrà eliminato automaticamente il gruppo di risorse di gestione temporanea. Per altre informazioni, vedere la sezione [Pulire](#clean-up) alla fine di questo articolo.

Se il servizio segnala un errore durante l'invio del modello di configurazione dell'immagine, vedere la procedura di [risoluzione dei problemi.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) Sarà inoltre necessario eliminare il modello prima di riprovare a inviare la compilazione. Per eliminare il modello:

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

Attendere il completamento della compilazione, per questo esempio, possono essere completati 10-15 minuti.

Se si verificano errori, esaminare questi passaggi per [la risoluzione dei problemi.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


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

Ottenere l'indirizzo IP dall'output della creazione della macchina virtuale e usarlo in SSH per la macchina virtuale.

```bash
ssh azureuser@<pubIp>
```

Dovresti vedere che l'immagine è stata personalizzata con un messaggio del giorno non appena viene stabilita la tua connessione SSH!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Al `exit` termine, digitare la connessione SSH.

## <a name="check-the-source"></a>Controllare l'origine

Nel modello Di creazione immagini, in "Proprietà", verranno visualizzati l'immagine di origine, lo script di personalizzazione che viene eseguito e dove viene distribuito.

```bash
cat helloImageTemplateLinux.json
```

Per informazioni più dettagliate su questo file .json, vedere Informazioni di riferimento sul modello di generatore di immaginiFor more detailed information about this .json [file,](image-builder-json.md) see Image builder template reference

## <a name="clean-up"></a>Eseguire la pulizia

Al termine, è possibile eliminare le risorse.

Eliminare il modello del generatore di immagini.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Eliminare il gruppo di risorse immagine.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui componenti del file .json utilizzati in questo articolo, vedere Riferimento ai [modelli di generatore di immagini.](image-builder-json.md)
