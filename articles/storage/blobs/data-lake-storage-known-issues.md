---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e sui problemi noti relativi ad Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f635360c5a6da19d60f3992878a8950b03c5f748
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513874"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo elenca le funzionalità e gli strumenti non ancora supportati o solo parzialmente supportati con gli account di archiviazione che hanno uno spazio dei nomi gerarchico (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>API di archiviazione BLOB

Le API di archiviazione BLOB sono disabilitate per evitare problemi di operatività delle funzionalità che possono verificarsi perché le API di archiviazione BLOB non sono ancora interoperabili con Azure Data Lake API Gen2.

> [!NOTE]
> Con l'anteprima pubblica dell'accesso a più protocolli su Data Lake Storage, le API BLOB e Data Lake Storage Gen2 API possono operare sugli stessi dati. Per altre informazioni, vedere [l'articolo relativo all'accesso a più protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Cosa fare con gli strumenti, le applicazioni e i servizi esistenti

Se uno di questi usa API BLOB e si vuole usarli per lavorare con tutto il contenuto dell'account, sono disponibili due opzioni.

* **Opzione 1**: non abilitare uno spazio dei nomi gerarchico nell'account di archiviazione BLOB finché non è disponibile a [livello generale l'accesso a più protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md) e le API blob diventano completamente interoperabili con Azure Data Lake API Gen2. [L'accesso a più protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md) è attualmente disponibile in anteprima pubblica.  L'uso di un account di archiviazione **senza** uno spazio dei nomi gerarchico significa che non si avrà accesso a data Lake storage Gen2 funzionalità specifiche, ad esempio elenchi di controllo di accesso di directory e contenitori.

* **Opzione 2**: abilitare gli spazi dei nomi gerarchici. Con l'anteprima pubblica dell' [accesso a più protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md), gli strumenti e le applicazioni che chiamano le API blob, nonché le funzionalità di archiviazione BLOB, ad esempio i log di diagnostica, possono usare gli account che hanno uno spazio dei nomi gerarchico. Assicurarsi di consultare questo articolo per i problemi noti e le limitazioni.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Cosa fare se le API BLOB sono state usate per caricare i dati prima della disabilitazione delle API BLOB

Se sono state usate queste API per caricare i dati prima che venissero disabilitate e occorre accedere ai dati per motivi di produzione, contattare il supporto tecnico Microsoft con le informazioni seguenti:

> [!div class="checklist"]
> * ID sottoscrizione (il GUID, non il nome).
> * Nome/i dell'account di archiviazione.
> * Se si è interessati attivamente alla produzione e in caso affermativo, per quali account di archiviazione?
> * Anche se non si è coinvolti in modo attivo nella produzione, indicare se è necessario che i dati vengano copiati in un altro account di archiviazione per qualche motivo e in tal caso, perché?

In queste circostanze è possibile ripristinare l'accesso all'API BLOB per un periodo di tempo limitato, in modo che sia possibile copiare i dati in un account di archiviazione in cui la funzionalità degli spazi dei nomi gerarchici non è abilitata.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problemi e limitazioni dell'uso di API BLOB su account con uno spazio dei nomi gerarchico

Con l'anteprima pubblica dell'accesso a più protocolli su Data Lake Storage, le API BLOB e Data Lake Storage Gen2 API possono operare sugli stessi dati.

Questa sezione descrive i problemi e le limitazioni dell'uso delle API BLOB e delle API Data Lake Storage Gen2 per operare sugli stessi dati.

* Non è possibile usare sia API BLOB sia API Data Lake Storage per scrivere nella stessa istanza di un file.

* Se si scrive in un file usando Data Lake Storage Gen2 API, i blocchi del file non saranno visibili alle chiamate all'API blob [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) .

* È possibile sovrascrivere un file usando API Data Lake Storage Gen2 o API blob. Questa operazione non influirà sulle proprietà del file.

* Quando si usa l'operazione [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) senza specificare un delimitatore, i risultati includeranno sia le directory che i BLOB.

  Se si sceglie di utilizzare un delimitatore, utilizzare solo una barra (`/`). Questo è l'unico delimitatore supportato.

* Se si usa l'API [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) per eliminare una directory, tale directory verrà eliminata solo se è vuota.

  Ciò significa che non è possibile usare le directory di eliminazione dell'API BLOB in modo ricorsivo.

Queste API REST BLOB non sono supportate:

* [Put Blob (pagina)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Ottenere gli intervalli di pagine](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB di copia incrementale](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Inserisci pagina dall'URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Accoda blocco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Problemi relativi ai dischi delle macchine virtuali (VM) non gestiti

I dischi delle macchine virtuali non gestiti non sono supportati negli account che dispongono di uno spazio dei nomi gerarchico. Se si vuole abilitare uno spazio dei nomi gerarchico in un account di archiviazione, collocare i dischi delle macchine virtuali non gestiti in un account di archiviazione in cui non è abilitata la funzionalità di spazio dei nomi gerarchico.


## <a name="support-for-other-blob-storage-features"></a>Supporto per altre funzionalità di archiviazione BLOB

La tabella seguente elenca tutte le altre funzionalità e gli strumenti non ancora supportati o solo parzialmente supportati con gli account di archiviazione che hanno uno spazio dei nomi gerarchico (Azure Data Lake Storage Gen2).

| Funzionalità/strumento    | Altre informazioni    |
|--------|-----------|
| **API per gli account di archiviazione Data Lake Storage Gen2** | Supporto parziale <br><br>L'accesso a più protocolli su Data Lake Storage è attualmente disponibile in anteprima pubblica. Questa anteprima consente di usare le API blob negli SDK .NET, Java e Python con account che dispongono di uno spazio dei nomi gerarchico.  Gli SDK non contengono ancora API che consentono di interagire con le directory o impostare elenchi di controllo di accesso (ACL). Per eseguire queste funzioni, è possibile usare Data Lake Storage Gen2 le API **Rest** . |
| **AzCopy** | Supporto specifico della versione <br><br>Usare solo la versione più recente di AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Le versioni precedenti di AzCopy, ad esempio AzCopy v 8.1, non sono supportate.|
| **Criteri di gestione del ciclo di vita dell'archiviazione BLOB di Azure** | Supportato dall'accesso con più [protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md) Preview. I livelli di accesso cool e Archive sono supportati solo dalla versione di anteprima. L'eliminazione degli snapshot BLOB non è ancora supportata. |
| **Rete per la distribuzione di contenuti (CDN) di Azure** | Non ancora supportato|
| **Ricerca di Azure** |Supportato dall'accesso con più [protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md) Preview.|
| **Azure Storage Explorer** | Supporto specifico della versione <br><br>Utilizzare solo la versione `1.6.0` o successiva. <br>La versione `1.6.0` è disponibile come [download gratuito](https://azure.microsoft.com/features/storage-explorer/).|
| **ACL del contenitore BLOB** |Non ancora supportato|
| **Blobfuse** |Non ancora supportato|
| **Domini personalizzati** |Non ancora supportato|
| **Esplora file System** | Supporto limitato |
| **Registrazione diagnostica** |I log di diagnostica sono supportati dall' [accesso a più protocolli in data Lake storage](data-lake-storage-multi-protocol-access.md) anteprima. <br><br>L'abilitazione dei log nell'portale di Azure non è attualmente supportata. Di seguito è riportato un esempio di come abilitare i log usando PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Assicurarsi di specificare `Blob` come valore del parametro `-ServiceType`, come illustrato in questo esempio. <br><br>Attualmente non è possibile usare Azure Storage Explorer per la visualizzazione dei log di diagnostica. Per visualizzare i log, usare AzCopy o gli SDK.
| **Archiviazione non modificabile** |Non ancora supportato <br><br>L'archiviazione non modificabile offre la possibilità di archiviare i dati in un [worm (scrivere una sola volta, leggere molti)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) stato.|
| **Livelli a livello di oggetto** |I livelli di accesso sporadico e archivio sono supportati dall' [accesso a più protocolli in data Lake storage](data-lake-storage-multi-protocol-access.md) anteprima. <br><br> Tutti gli altri livelli di accesso non sono ancora supportati.|
| **Supporto di PowerShell e CLI** | Funzionalità limitate <br><br>Sono supportate le operazioni di gestione, ad esempio la creazione di un account. Le operazioni del piano dati, ad esempio il caricamento e il download di file, sono in anteprima pubblica come parte dell'accesso a più [protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md). L'utilizzo delle directory e l'impostazione degli elenchi di controllo di accesso (ACL) non sono ancora supportati. |
| **Siti web statici** |Non ancora supportato <br><br>In particolare, la possibilità di gestire i file nei [siti web statici](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Applicazioni di terze parti** | Supporto limitato <br><br>Le applicazioni di terze parti che usano le API REST per lavorare continueranno a funzionare se vengono usate con Data Lake Storage Gen2. <br>Le applicazioni che chiamano API blob probabilmente funzioneranno con l'anteprima pubblica dell' [accesso a più protocolli su data Lake storage](data-lake-storage-multi-protocol-access.md). |
|**eliminazione temporanea** |Non ancora supportato|
| **Funzionalità di controllo delle versioni** |Non ancora supportato <br><br>Sono incluse l' [eliminazione](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)temporanea e altre funzionalità di controllo delle versioni, ad esempio gli [snapshot](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


