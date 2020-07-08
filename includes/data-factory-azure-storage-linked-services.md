---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75468331"
---
### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Il **servizio collegato ad Archiviazione di Azure** consente di collegare un account di Archiviazione di Azure a una data factory di Azure tramite la **chiave dell'account**, che fornisce alla data factory l'accesso globale ad Archiviazione di Azure. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Archiviazione di Azure.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà Type deve essere impostata su: **AzureStorage** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. |Sì |

Per altre informazioni su come recuperare le chiavi di accesso dell'account di archiviazione, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../articles/storage/common/storage-account-keys-manage.md).

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
Una firma di accesso condiviso (SAS) fornisce accesso delegato alle risorse nell'account di archiviazione. Consente di concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato. Per altre informazioni sulla firma di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../articles/storage/common/storage-sas-overview.md).

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

* Impostare le **autorizzazioni** di lettura/scrittura appropriate per gli oggetti in base alla modalità di utilizzo del servizio collegato (lettura, scrittura, lettura/scrittura) nell'data factory.
* Impostare **Ora di scadenza** in modo appropriato. L'accesso agli oggetti di Archiviazione di Azure non deve scadere nel periodo attivo della pipeline.
* L'URI deve essere creato al giusto livello di contenitore/BLOB o tabella in base alla necessità. Un URI di firma di accesso condiviso per un BLOB di Azure consente al servizio Data factory di accedere a quel particolare BLOB. Un URI di firma di accesso condiviso per un contenitore BLOB di Azure consente al servizio Data factory di eseguire l'iterazione tra i BLOB in quel contenitore. Se si deve consentire l'accesso a più o meno oggetti in un secondo momento o aggiornare l'URI di firma di accesso condiviso, ricordarsi di aggiornare il servizio collegato con il nuovo URI.   

