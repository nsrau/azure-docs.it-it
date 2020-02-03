---
title: Considerazioni sull'archiviazione per funzioni di Azure
description: Informazioni sui requisiti di archiviazione di funzioni di Azure e sulla crittografia dei dati archiviati.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 353fdd3bf7775e3bc7a9d017a9e8dd8238b09830
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964982"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerazioni sull'archiviazione per funzioni di Azure

Funzioni di Azure richiede un account di archiviazione di Azure quando si crea un'istanza dell'app per le funzioni. I servizi di archiviazione seguenti possono essere usati dall'app per le funzioni:


|Servizio di archiviazione  | Utilizzo funzioni  |
|---------|---------|
| [Archivio BLOB di Azure](/storage/blobs/storage-blobs-overview.md)     | Gestire lo stato e i tasti funzione delle associazioni.  <br/>Usato anche dagli [Hub attività in Durable Functions](durable/durable-functions-task-hubs.md). |
| [File di Azure](../storage/files/storage-files-introduction.md)  | Condivisione file usata per archiviare ed eseguire il codice dell'app per le funzioni in un [piano a consumo](functions-scale.md#consumption-plan). |
| [Archiviazione code di Azure](../storage/queues/storage-queues-introduction.md)     | Usato dagli [Hub attività in Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Archivio tabelle di Azure](../storage/tables/table-storage-overview.md)  |  Usato dagli [Hub attività in Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Quando si usa il piano di hosting a consumo, i file del codice di funzione e la configurazione di binding vengono archiviati nell'archiviazione file di Azure nell'account di archiviazione principale. Quando si elimina l'account di archiviazione principale, il contenuto viene eliminato e non può essere ripristinato.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, di Accodamento e tabelle. Questo perché le funzioni si basano sull'archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione delle esecuzioni di funzioni. Alcuni account di archiviazione non supportano code e tabelle. Questi account includono gli account di archiviazione solo BLOB, archiviazione Premium di Azure e gli account di archiviazione per utilizzo generico con replica ZRS. Questi account non supportati vengono esclusi dal pannello dell'account di archiviazione quando si crea un'app per le funzioni.

Per altre informazioni sui tipi di account di archiviazione, vedere l'[introduzione ai servizi di Archiviazione di Azure](../storage/common/storage-introduction.md#azure-storage-services). 

Sebbene sia possibile usare un account di archiviazione esistente con l'app per le funzioni, è necessario assicurarsi che soddisfi questi requisiti. Per gli account di archiviazione creati come parte del flusso di creazione dell'app per le funzioni è garantito che soddisfino i requisiti dell'account di archiviazione.  

## <a name="storage-account-guidance"></a>Linee guida per l'account di archiviazione

Ogni app per le funzioni richiede un account di archiviazione per funzionare. Se tale account viene eliminato, l'app per le funzioni non verrà eseguita. Per risolvere i problemi correlati all'archiviazione, vedere [come risolvere](functions-recover-storage-account.md)i problemi relativi all'archiviazione. Le considerazioni aggiuntive seguenti sono valide per l'account di archiviazione usato dalle app per le funzioni.

### <a name="storage-account-connection-setting"></a>Impostazione di connessione dell'account di archiviazione

La connessione dell'account di archiviazione viene mantenuta nell' [impostazione dell'applicazione AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Quando si rigenerano le chiavi di archiviazione, è necessario aggiornare la stringa di connessione dell'account di archiviazione. [Per altre informazioni sulla gestione delle chiavi di archiviazione, vedere qui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Account di archiviazione condivisi

È possibile che più app per le funzioni condividano lo stesso account di archiviazione senza problemi. Ad esempio, in Visual Studio è possibile sviluppare più app usando l'emulatore di archiviazione di Azure. In questo caso, l'emulatore funge da singolo account di archiviazione. È anche possibile usare lo stesso account di archiviazione usato dall'app per le funzioni per archiviare i dati dell'applicazione. Tuttavia, questo approccio non è sempre una scelta ideale in un ambiente di produzione.

### <a name="optimize-storage-performance"></a>Ottimizzare le prestazioni di archiviazione

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Crittografia dei dati di archiviazione

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per altre informazioni, vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md)inattivi.

Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati di BLOB e file. Queste chiavi devono essere presenti in Azure Key Vault affinché le funzioni siano in grado di accedere all'account di archiviazione. Per altre informazioni, vedere [configurare chiavi gestite dal cliente con Azure Key Vault usando il portale di Azure](../storage/common/storage-encryption-keys-portal.md).  

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle opzioni di hosting di funzioni di Azure.

> [!div class="nextstepaction"]
> [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md)


