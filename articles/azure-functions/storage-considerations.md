---
title: Considerazioni sull'archiviazione per funzioni di AzureStorage considerations for Azure Functions
description: Informazioni sui requisiti di archiviazione di Funzioni di Azure e sulla crittografia dei dati archiviati.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276582"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerazioni sull'archiviazione per funzioni di AzureStorage considerations for Azure Functions

Funzioni di Azure richiede un account di archiviazione di Azure quando si crea un'istanza dell'app per le funzioni. I seguenti servizi di archiviazione possono essere usati dalla tua app per le funzioni:


|Servizio di archiviazione  | Utilizzo delle funzioni  |
|---------|---------|
| [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md)     | Gestire lo stato delle associazioni e i tasti funzione.  <br/>Utilizzato anche dagli [hub attività in Funzioni durevoli](durable/durable-functions-task-hubs.md). |
| [File di Azure](../storage/files/storage-files-introduction.md)  | Condivisione file utilizzata per archiviare ed eseguire il codice dell'app per le funzioni in un piano di [consumo](functions-scale.md#consumption-plan). |
| [Archiviazione delle code di AzureAzure Queue storage](../storage/queues/storage-queues-introduction.md)     | Utilizzato dagli [hub attività in Funzioni durevoli](durable/durable-functions-task-hubs.md).   |
| [Archiviazione tabelle di AzureAzure Table storage](../storage/tables/table-storage-overview.md)  |  Utilizzato dagli [hub attività in Funzioni durevoli](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Quando si usa il piano di hosting a consumo, i file del codice di funzione e la configurazione di binding vengono archiviati nell'archiviazione file di Azure nell'account di archiviazione principale. Quando si elimina l'account di archiviazione principale, il contenuto viene eliminato e non può essere ripristinato.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure generico che supporti l'archiviazione BLOB, coda e tabelle. Ciò è dovuto al fatto che Funzioni si basa su Archiviazione di Azure per operazioni quali la gestione di trigger e la registrazione delle esecuzioni di funzioni. Alcuni account di archiviazione non supportano code e tabelle. Questi account includono gli account di archiviazione solo BLOB, Archiviazione Premium di Azure e gli account di archiviazione generici con la replica di archiviazione. Questi account non supportati vengono filtrati dal pannello Account di archiviazione durante la creazione di un'app per le funzioni.

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi](../storage/common/storage-introduction.md#azure-storage-services)di archiviazione di Azure.To learn more about storage account types, see Introducing the Azure Storage Services . 

Sebbene sia possibile usare un account di archiviazione esistente con l'app per le funzioni, è necessario assicurarsi che soddisfi questi requisiti. Gli account di archiviazione creati come parte del flusso di creazione dell'app per le funzioni sono garantiti per soddisfare questi requisiti dell'account di archiviazione.  

## <a name="storage-account-guidance"></a>Indicazioni sull'account di archiviazioneStorage account guidance

Ogni app per le funzioni richiede un account di archiviazione per funzionare. Se l'account viene eliminato, l'app per le funzioni non verrà eseguita. Per risolvere i problemi relativi all'archiviazione, vedere [Come risolvere i problemi relativi all'archiviazione](functions-recover-storage-account.md). Le considerazioni aggiuntive seguenti si applicano all'account di archiviazione usato dalle app per le funzioni.

### <a name="storage-account-connection-setting"></a>Impostazione di connessione dell'account di archiviazione

La connessione all'account di archiviazione viene mantenuta [nell'impostazione dell'applicazione AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

La stringa di connessione dell'account di archiviazione deve essere aggiornata quando si rigenerano le chiavi di archiviazione. [Ulteriori informazioni sulla gestione](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)delle chiavi di archiviazione sono disponibili qui .

### <a name="shared-storage-accounts"></a>Account di archiviazione condivisi

È possibile che più app per le funzioni condividano lo stesso account di archiviazione senza problemi. Ad esempio, in Visual Studio è possibile sviluppare più app usando l'emulatore di archiviazione di Azure.For example, in Visual Studio you can develop multiple apps using the Azure Storage Emulator. In questo caso, l'emulatore agisce come un singolo account di archiviazione. Lo stesso account di archiviazione usato dall'app per le funzioni può essere usato anche per archiviare i dati dell'applicazione. Tuttavia, questo approccio non è sempre una buona idea in un ambiente di produzione.

### <a name="optimize-storage-performance"></a>Ottimizzare le prestazioni di archiviazione

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Crittografia dei dati di archiviazione

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivi. Per altre informazioni, vedere Crittografia di Archiviazione di Azure per i [dati inattivi](../storage/common/storage-service-encryption.md).

Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.By default, data is encrypted with Microsoft-managed keys. Per un controllo aggiuntivo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati BLOB e dei file. Queste chiavi devono essere presenti nell'insieme di credenziali delle chiavi di Azure affinché Funzioni sia in grado di accedere all'account di archiviazione. Per altre informazioni, vedere Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure tramite il portale di Azure.To learn more, see [Configure customer-managed keys with Azure Key Vault by using the Azure portal](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Montare condivisioni file (Linux)

È possibile montare le condivisioni File di Azure esistenti nelle app per le funzioni Linux.You can mount existing Azure Files shares to your Linux function apps. Montando una condivisione nell'app per le funzioni Linux, è possibile sfruttare i modelli di apprendimento automatico esistenti o altri dati nelle funzioni. È possibile [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) usare il comando per montare una condivisione esistente nell'app per le funzioni Linux.You can use the command to mount an existing share to your Linux function app. 

In questo `share-name` comando è il nome della condivisione File di Azure esistente e `custom-id` può essere qualsiasi stringa che definisce in modo univoco la condivisione quando viene montata nell'app per le funzioni. `mount-path` È anche il percorso da cui si accede alla condivisione nell'app per le funzioni. `mount-path`deve essere nel `/dir-name`formato e non può `/home`iniziare con .

Per un esempio completo, vedere gli script in [Creare un'app per le funzioni Python e montare una condivisione File](scripts/functions-cli-mount-files-storage-linux.md)di Azure. 

Attualmente, è `storage-type` `AzureFiles` supportato solo un di. È possibile montare solo cinque condivisioni in una determinata app per le funzioni. Il montaggio di una condivisione file può aumentare l'ora di avvio a freddo di almeno 200-300 ms o anche di più quando l'account di archiviazione si trova in un'area diversa.

La condivisione montata è disponibile `mount-path` per il codice della funzione in corrispondenza dell'oggetto specificato. Ad esempio, `mount-path` `/path/to/mount`quando è , è possibile accedere alla directory di destinazione tramite le API del file system, come nell'esempio Python seguente:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle opzioni di hosting di Funzioni di Azure.Learn more about Azure Functions hosting options.

> [!div class="nextstepaction"]
> [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md)


