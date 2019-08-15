---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: ee368b58195d61a1c6792a3a3655122af7104d58
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012166"
---
### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Il **servizio collegato ad Archiviazione di Azure** consente di collegare un account di Archiviazione di Azure a una data factory di Azure tramite la **chiave dell'account**, che fornisce alla data factory l'accesso globale ad Archiviazione di Azure. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Archiviazione di Azure.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **AzureStorage** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. |Sì |

Vedere la sezione seguente per i passaggi per visualizzare o copiare la chiave dell'account per un archivio di Azure: [Chiavi di accesso](../articles/storage/common/storage-account-manage.md#access-keys).

**Esempio:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Servizio collegato di firma di accesso condiviso Archiviazione di Azure
Una firma di accesso condiviso (SAS) fornisce accesso delegato alle risorse nell'account di archiviazione. Consente di concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato. Per altre informazioni sulla firma di accesso [condiviso, vedere concedere l'accesso limitato alle risorse di archiviazione di Azure usando le firme di accesso condiviso (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory supporta attualmente solo il **servizio di firma di accesso condiviso**, ma non la firma di accesso condiviso dell'account. Si noti che l'URL di firma di accesso condiviso generabile dal portale di Azure o da Storage Explorer è una firma di accesso condiviso dell'account, che non è supportata.

> [!TIP]
> È possibile eseguire i comandi PowerShell seguenti per generare una firma di accesso condiviso del servizio per l'account di archiviazione. Sostituire i segnaposto e concedere l'autorizzazione necessaria: `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Il servizio collegato di firma di accesso condiviso Archiviazione di Azure consente di collegare un account di Archiviazione di Azure a Data factory di Azure tramite una firma di accesso condiviso. Offre a Data factory un accesso con restrizioni o limiti di tempo a tutte le risorse o a risorse specifiche (BLOB/contenitore) nella risorsa di archiviazione. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di firma di accesso condiviso Archiviazione di Azure. 

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **AzureStorageSas** |Sì |
| sasUri |Specificare l'URI della firma di accesso condiviso per le risorse di Archiviazione di Azure come BLOB, contenitore o tabella.  |Sì |

**Esempio:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Quando si crea un **URI della firma di accesso condiviso**considerare quanto segue:  

* Impostare le **autorizzazioni** appropriate di lettura o scrittura per gli oggetti in base al modo in cui il servizio collegato (lettura, scrittura, lettura/scrittura) viene usato nella data factory.
* Impostare **Ora di scadenza** in modo appropriato. L'accesso agli oggetti di Archiviazione di Azure non deve scadere nel periodo attivo della pipeline.
* L'URI deve essere creato al giusto livello di contenitore/BLOB o tabella in base alla necessità. Un URI di firma di accesso condiviso per un BLOB di Azure consente al servizio Data factory di accedere a quel particolare BLOB. Un URI di firma di accesso condiviso per un contenitore BLOB di Azure consente al servizio Data factory di eseguire l'iterazione tra i BLOB in quel contenitore. Se si deve consentire l'accesso a più o meno oggetti in un secondo momento o aggiornare l'URI di firma di accesso condiviso, ricordarsi di aggiornare il servizio collegato con il nuovo URI.   

