---
title: Indirizzare gli eventi di archiviazione BLOB di Azure a un endpoint Web personalizzato (anteprima) | Microsoft Docs
description: Usare la Griglia di eventi di Azure per sottoscrivere eventi di archiviazione BLOB.
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/19/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 50a6126f065b1b4d851f53b5cb3096c130314450
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato (anteprima)

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per sottoscrivere eventi di archiviazione BLOB e attivare l'evento per visualizzare il risultato. 

In genere, si inviano eventi a un endpoint che risponde all'evento, ad esempio un webhook o una funzione di Azure. Per semplificare l'esempio mostrato in questo articolo, gli eventi vengono inviati a un URL che raccoglie semplicemente i messaggi. L'URL viene creato con [RequestBin](https://requestb.in/), uno strumento open source di terze parti.

> [!NOTE]
> **RequestBin** è uno strumento open source non destinato all'utilizzo a velocità effettiva elevata. L'uso dello strumento in questo esempio è esclusivamente dimostrativo. Se si esegue il push di più di un evento alla volta, è possibile che non vengano visualizzati tutti gli eventi nello strumento.

Al termine della procedura descritta in questo articolo, si potrà notare che i dati dell'evento sono stati inviati a un endpoint.

![Dati dell'evento](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione più recente dell'interfaccia della riga di comando di Azure (2.0.24 o versione successiva). Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

Se non si usa Cloud Shell, prima è necessario accedere usando `az login`.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti della griglia di eventi sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). 

L'esempio seguente crea un gruppo di risorse denominato `<resource_group_name>` nella località *westcentralus*.  Sostituire `<resource_group_name>` con un nome univoco per il gruppo di risorse.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>Creare un account di archiviazione BLOB

Per usare Archiviazione di Azure, è necessario un account di archiviazione.  Gli eventi di archiviazione BLOB sono attualmente disponibili solo negli account di archiviazione BLOB.

Gli account di archiviazione BLOB sono account di archiviazione specializzati per l'archiviazione dei dati non strutturati come BLOB (oggetti) in Archiviazione di Azure. Gli account di archiviazione BLOB sono simili agli account di archiviazione di uso generico esistenti e condividono tutte le straordinarie funzionalità di durabilità, disponibilità, scalabilità e prestazioni già usate, inclusa la coerenza API al 100% per i BLOB in blocchi e i BLOB di aggiunta. Per applicazioni che richiedono solo archivi BLOB in blocchi o BLOB di aggiunta, è consigliabile usare account di archiviazione BLOB.

> [!NOTE]
> Griglia di eventi è attualmente in anteprima ed è disponibile solo per gli account di archiviazione delle aree **westcentralus** e **westus2**.

Sostituire `<storage_account_name>` con un nome univoco per l'account di archiviazione e `<resource_group_name>` con il gruppo di risorse creato in precedenza.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere eventi dall'account di archiviazione BLOB, creare l'endpoint per il messaggio dell'evento. Anziché scrivere codice per rispondere all'evento, creare un endpoint che raccoglie i messaggi per poterli visualizzare. RequestBin è uno strumento open source di terze parti che consente di creare un endpoint e visualizza le richieste che gli vengono inviate. Passare a [RequestBin](https://requestb.in/) e fare clic su **Create a RequestBin** (Crea un RequestBin).  Copiare l'URL del contenitore, necessario per sottoscrivere l'argomento.

## <a name="subscribe-to-your-blob-storage-account"></a>Sottoscrivere l'account di archiviazione BLOB

Si sottoscrive un argomento per indicare alla griglia di eventi gli eventi di cui si vuole tenere traccia. L'esempio seguente sottoscrive l'account di archiviazione BLOB creato e passa l'URL da RequestBin come endpoint per la notifica dell'evento. Sostituire `<event_subscription_name>` con un nome univoco per la sottoscrizione all'evento e `<URL_from_RequestBin>` con il valore specificato nella sezione precedente. Se durante la sottoscrizione si specifica un endpoint, la griglia di eventi gestisce il routing degli eventi verso tale endpoint. Per `<resource_group_name>` e `<storage_account_name>` usare i valori creati in precedenza. 

```azurecli-interactive
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint <URL_from_RequestBin>
```

## <a name="trigger-an-event-from-blob-storage"></a>Attivare un evento dall'archiviazione BLOB

A questo punto, attivare un evento per vedere come la griglia di eventi distribuisce il messaggio nell'endpoint. Configurare prima di tutto il nome e la chiave per l'account di archiviazione, quindi creare un contenitore e creare e caricare un file. Anche in questo caso, usare i valori per `<storage_account_name>` e `<resource_group_name>` creati in precedenza.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

È stato attivato l'evento e la griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Passare all'URL di RequestBin creato in precedenza. In alternativa, fare clic su Aggiorna nel browser di RequestBin aperto. Verrà visualizzato l'evento appena inviato. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Pulire le risorse
Se si intende continuare a usare questo account di archiviazione e questa sottoscrizione di eventi, non è necessario pulire le risorse create in questo articolo. Se non si intende continuare, usare il comando seguente per eliminare le risorse create con questo articolo.

Sostituire `<resource_group_name>` con il gruppo di risorse creato in precedenza.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi, è possibile approfondire le operazioni possibili con gli eventi di archiviazione BLOB e con la Griglia di eventi:

- [Reazione agli eventi di archiviazione BLOB](storage-blob-event-overview.md)
- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
