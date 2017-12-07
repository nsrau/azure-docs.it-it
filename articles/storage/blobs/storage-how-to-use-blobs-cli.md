---
title: Eseguire operazioni nell'archiviazione BLOB di Azure (oggetto archiviazione) con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: "Informazioni su come caricare e scaricare i BLOB nell'archiviazione BLOB di Azure, nonché creare una firma di accesso condiviso (SAS) per gestire l'accesso a un BLOB nell'account di archiviazione."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: tamram
ms.openlocfilehash: 0c3fc2d73a0caf0e0331cb9073bfcc0574240dac
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Eseguire operazioni di archiviazione BLOB con l'interfaccia della riga di comando di Azure

Archivio BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati oggetto non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Questa esercitazione illustra le operazioni di base nell'archiviazione BLOB di Azure, ad esempio il caricamento, il download e l'eliminazione dei BLOB. Si apprenderà come:

> [!div class="checklist"]
> * Creare un contenitore
> * Caricare un file (BLOB) in un contenitore
> * Elencare i BLOB in un contenitore
> * Scaricare un BLOB da un contenitore
> * Copiare un BLOB tra account di archiviazione
> * Eliminare un BLOB
> * Visualizzare e modificare metadati e proprietà dei BLOB
> * Gestire la sicurezza con una firma di accesso condiviso (SAS)

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Creare un contenitore

I contenitori sono simili alle directory nel computer e, di fatto, è possibile organizzare i gruppi di BLOB in un contenitore così come si organizzano i file in una directory. Un account di archiviazione può includere un numero qualsiasi di contenitori. È possibile archiviare fino a 500 TB di dati BLOB in un contenitore, ovvero la quantità massima di dati in un account di archiviazione.

Creare un contenitore per l'archiviazione di BLOB con il comando [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-). Per altre informazioni sulla denominazione dei BLOB e dei contenitori, vedere l'articolo relativo alla [denominazione e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="enable-public-read-access-for-a-container"></a>Abilitare l'accesso in lettura pubblico per un contenitore

Un contenitore appena creato è privato per impostazione predefinita. Vale a dire che nessun utente può accedere al contenitore senza una [firma di accesso condiviso](#create-a-shared-access-signature-sas) o le chiavi di accesso per l'account di archiviazione. Usando l'interfaccia della riga di comando di Azure, è possibile modificare questo comportamento impostando le autorizzazioni del contenitore su uno dei tre livelli seguenti:

| | |
|---|---|
| `--public-access off` | (Impostazione predefinita) Nessun accesso in lettura pubblico |
| `--public-access blob` | Accesso in lettura pubblico solo per i BLOB |
| `--public-access container` | Accesso in lettura pubblico ai BLOB, con la possibilità di elencare i BLOB nel contenitore |

Quando si imposta l'accesso pubblico a `blob` o `container`, si abilita l'accesso di sola lettura per tutti gli utenti in Internet. Se ad esempio si desidera visualizzare le immagini archiviate come BLOB nel proprio sito Web, è necessario abilitare l'accesso in lettura pubblico. Se invece si desidera abilitare l'accesso in lettura/scrittura, è necessario usare una [firma di accesso condiviso (SAS)](#create-a-shared-access-signature-sas).

Abilitare l'accesso in lettura pubblico per il contenitore con il comando [az storage container set-permission](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Caricare un BLOB in un contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi sono il tipo più comune di BLOB archiviati in Archiviazione di Azure. I BLOB di aggiunta vengono usati quando è necessario aggiungere dati a un BLOB esistente senza modificarne il contenuto, ad esempio nelle operazioni di registrazione. I BLOB di pagine supportano i file con estensione VHD delle macchine virtuali IaaS.

In questo esempio si carica un BLOB nel contenitore creato nel passaggio precedente con il comando [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Questa operazione crea il BLOB, se non esiste, o lo sovrascrive se esiste già. Caricare più file in modo da visualizzare più voci quando si elencano i BLOB nel passaggio successivo.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB nel contenitore con il comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Output di esempio:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Scaricare un BLOB

Scaricare il BLOB caricato in un passaggio precedente tramite il comando [az storage blob download](/cli/azure/storage/blob#download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Copiare un BLOB tra account di archiviazione

È possibile copiare i BLOB tra aree e account di archiviazione in modo asincrono.

Nell'esempio seguente viene illustrato come copiare BLOB da un account di archiviazione a un altro. Prima di tutto si crea un contenitore nell'account di archiviazione di origine, specificando l'accesso in lettura pubblico per i suoi BLOB. Quindi si carica un file nel contenitore e infine si copia il BLOB dal contenitore in un contenitore nell'account di archiviazione di destinazione.

In questo esempio, perché l'operazione di copia riesca, il contenitore di destinazione deve esistere già nell'account di archiviazione di destinazione. Inoltre, il BLOB di origine specificato nell'argomento `--source-uri` deve includere un token di firma di accesso condiviso oppure essere accessibile pubblicamente, come in questo esempio.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Eliminare un BLOB

Eliminare il BLOB dal contenitore usando il comando [az storage blob delete](/cli/azure/storage/blob#delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="set-the-content-type"></a>Impostare il tipo di contenuto

Il tipo di contenuto, detto anche tipo MIME, identifica il formato dei dati nel BLOB. I browser e altri software usano il tipo di contenuto per determinare come devono essere elaborati i dati. L'esempio seguente imposta il tipo di contenuto su `image/png`.

```azurecli-interactive
# Set the content type
az storage blob update
    --container-name mystoragecontainer 
    --name blobName 
    --content-type image/png
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Visualizzare e modificare metadati e proprietà dei BLOB

Ogni BLOB presenta diverse proprietà definite dal servizio che è possibile visualizzare con il comando [az storage blob show](/cli/azure/storage/blob#show) e che includono nome, tipo, lunghezza e altre proprietà. È inoltre possibile configurare un BLOB con proprietà personalizzate e i rispettivi valori usando il comando [az storage blob metadata update](/cli/azure/storage/blob/metadata#update).

In questo esempio vengono prima visualizzate le proprietà di un BLOB definite dal servizio, quindi viene aggiornato il BLOB con due proprietà dei metadati personalizzate. Infine vengono visualizzati le proprietà dei metadati del BLOB e i relativi valori con il comando [az storage blob metadata show](/cli/azure/storage/blob/metadata#show).

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Creare una firma di accesso condiviso (SAS)

Una firma di accesso condiviso costituisce uno strumento per concedere l'accesso limitato agli oggetti del proprio account di archiviazione senza dover esporre la chiave di accesso di tale account. Per produrre un URI che consenta l'accesso a una risorsa privata, creare il token di firma di accesso condiviso con le autorizzazioni desiderate e la finestra di validità (la durata effettiva) e aggiungerlo come stringa di query all'URL di una risorsa, in modo da formare il relativo URI SAS completo. Tutti gli utenti dotati di questo URI SAS (URL della risorsa cui viene aggiunto il token di firma di accesso condiviso) possono quindi accedere durante il periodo di validità del token di firma di accesso condiviso. È ad esempio possibile concedere l'accesso in lettura a un BLOB privato per due minuti per consentire a un utente di visualizzarlo.

Nei passaggi seguenti si disabiliterà l'accesso pubblico al contenitore, si sottoporrà a test l'accesso solo privato, quindi verrà generato un URI SAS.

### <a name="disable-container-public-access"></a>Disabilitare l'accesso pubblico al contenitore

Impostare il livello di accesso del contenitore su `off`. In questo modo si stabilisce che non è possibile accedere al contenitore o ai relativi BLOB senza una firma di accesso condiviso o una chiave di accesso dell'account di archiviazione.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Verificare l'accesso privato

Per verificare che non vi sia alcun accesso in lettura pubblico ai BLOB nel contenitore, ottenere l'URL di uno dei relativi BLOB con il comando [az storage blob url](/cli/azure/storage/blob#url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Passare all'URL del BLOB in una finestra del browser privata. Viene visualizzato un errore `ResourceNotFound` perché il BLOB è privato e non è stata inclusa una firma di accesso condiviso.

### <a name="create-a-sas-uri"></a>Creare un URI SAS

A questo punto creeremo un URI SAS che consente l'accesso al BLOB. Nell'esempio seguente si popolerà una variabile con l'URL del BLOB usando il comando [az storage blob url](/cli/azure/storage/blob#url), quindi si popolerà un'altra variabile con un token SAS generato usando il comando [az storage blob generate-sas](/cli/azure/storage/blob#generate-sas). Infine verrà eseguito l'output dell'URI SAS completo del BLOB mediante la concatenazione dei due elementi separati dal separatore di stringa di query `?`.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Eseguire il test dell'URI SAS

In una finestra del browser privata passare all'URI SAS completo visualizzato con il comando `echo` nel frammento di codice precedente. Questa volta non viene visualizzato un errore ed è possibile visualizzare o scaricare il BLOB.

Attendere il tempo necessario perché l'URL scada (in questo esempio due monuti), quindi passare all'URI in un'altra finestra del browser privata. Adesso viene visualizzato un errore `AuthenticationFailed` poiché il token SAS è scaduto.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non sono più necessarie le risorse del gruppo di risorse, inclusi l'account di archiviazione creato ed eventuali BLOB caricati in questa esercitazione, eliminare il gruppo di risorse con il comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base relative all'uso dei BLOB in Archiviazione di Azure:

> [!div class="checklist"]
> * Creare un contenitore
> * Caricare un file (BLOB) in un contenitore
> * Elencare i BLOB in un contenitore
> * Scaricare un BLOB da un contenitore
> * Copiare un BLOB tra account di archiviazione
> * Eliminare un BLOB
> * Visualizzare e modificare metadati e proprietà dei BLOB
> * Gestire la sicurezza con una firma di accesso condiviso (SAS)

Le risorse seguenti forniscono ulteriori informazioni sull'uso dell'interfaccia della riga di comando di Azure, nonché sull'uso delle risorse nell'account di archiviazione.

* Interfaccia della riga di comando di Azure
  * [Accedere con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/authenticate-azure-cli) -Informazioni sui diversi metodi di autenticazione con l'interfaccia della riga di comando di Azure, tra cui l'accesso interattivo tramite l'[entità servizio](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) per l'esecuzione automatica degli script dell'interfaccia della riga di comando di Azure 2.0.
  * [Riferimento ai comandi dell'interfaccia della riga di comando di Azure 2.0](/cli/azure/)
* Microsoft Azure Storage Explorer
  * [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente i dati di Archiviazione di Azure in Windows, macOS e Linux.
