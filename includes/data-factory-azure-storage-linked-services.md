### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Il **servizio collegato ad Archiviazione di Azure** consente di collegare un account di Archiviazione di Azure a una data factory di Azure tramite la **chiave dell'account**, che fornisce alla data factory l'accesso globale ad Archiviazione di Azure. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Archiviazione di Azure.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **AzureStorage** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. |Sì |

Per i passaggi da seguire per visualizzare o copiare la chiave dell'account per Archiviazione di Azure, vedere [Visualizzare, copiare e rigenerare le chiave di accesso alle risorse di archiviazione](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

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
Una firma di accesso condiviso (SAS) fornisce accesso delegato alle risorse nell'account di archiviazione. Consente di concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato. Per informazioni dettagliate sulle firme di accesso condiviso, vedere [Firme di accesso condiviso, parte 1: conoscere il modello di firma di accesso condiviso](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory supporta attualmente solo il **servizio di firma di accesso condiviso**, ma non la firma di accesso condiviso dell'account. Vedere [Tipi di firme di accesso condiviso](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) per informazioni dettagliate su questi due tipi e sulla modalità di costruzione. Si noti che l'URL di firma di accesso condiviso generabile dal portale di Azure o da Storage Explorer è una firma di accesso condiviso dell'account, che non è supportata.

> [!TIP]
> È possibile eseguire i comandi PowerShell seguenti per generare una firma di accesso condiviso del servizio per l'account di archiviazione. Sostituire i segnaposto e concedere l'autorizzazione necessaria: `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Il servizio collegato di firma di accesso condiviso Archiviazione di Azure consente di collegare un account di Archiviazione di Azure a Data factory di Azure tramite una firma di accesso condiviso. Offre a Data factory un accesso con restrizioni o limiti di tempo a tutte le risorse o a risorse specifiche (BLOB/contenitore) nella risorsa di archiviazione. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di firma di accesso condiviso Archiviazione di Azure. 

| Proprietà | DESCRIZIONE | Obbligatoria |
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

