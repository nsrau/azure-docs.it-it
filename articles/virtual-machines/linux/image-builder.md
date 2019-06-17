---
title: Creare una VM Linux con il generatore di immagini di Azure (anteprima)
description: Creare una VM Linux con il generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159511"
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

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

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
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>Creare l'immagine
Inviare la configurazione dell'immagine per il servizio di generazione di immagini di macchina virtuale

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Attendere fino al completamento della compilazione. L'operazione può richiedere circa 15 minuti.


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

Al termine, eliminare le risorse.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file con estensione JSON utilizzato in questo articolo, vedere [riferimenti dei modelli di Image Builder](image-builder-json.md).