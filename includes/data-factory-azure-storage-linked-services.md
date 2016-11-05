## Servizio collegato Archiviazione di Azure
Il **servizio collegato ad Archiviazione di Azure** consente di collegare un account di Archiviazione di Azure a Data factory di Azure tramite la **chiave dell'account**. Questo garantisce a Data factory l'accesso globale ad Archiviazione di Azure. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Archiviazione di Azure.

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **AzureStorage** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. |Sì |

Per i passaggi da seguire per visualizzare o copiare la chiave dell'account per Archiviazione di Azure, vedere l'articolo relativo a [visualizzazione, copia e rigenerazione delle chiavi di accesso alle risorse di archiviazione](../articles/storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Esempio:**

    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## Servizio collegato di firma di accesso condiviso Archiviazione di Azure
Una firma di accesso condiviso (SAS) fornisce accesso delegato alle risorse nell'account di archiviazione. Questo significa che è possibile concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato. Per informazioni dettagliate sulle firme di accesso condiviso, vedere [Firme di accesso condiviso, parte 1: conoscere il modello di firma di accesso condiviso](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)

Il servizio collegato di firma di accesso condiviso Archiviazione di Azure consente di collegare un account di Archiviazione di Azure a Data factory di Azure tramite una firma di accesso condiviso. Questo offre a Data factory un accesso con restrizioni o limiti di tempo a tutte le risorse o a risorse specifiche (BLOB/contenitore) nella risorsa di archiviazione. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di firma di accesso condiviso Archiviazione di Azure.

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **AzureStorageSas** |Sì |
| sasUri |Specificare l'URI della firma di accesso condiviso per le risorse di Archiviazione di Azure come BLOB, contenitore o tabella. Vedere le note riportate di seguito per informazioni dettagliate. |Sì |

**Esempio:**

    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Quando si crea un **URI della firma di accesso condiviso** considerare quanto segue:

* Data factory di Azure supporta solo il **servizio di firma di accesso condiviso** e non la firma di accesso condiviso dell'account. Vedere [Tipi di firme di accesso condiviso](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) per informazioni dettagliate su questi due tipi.
* È necessario impostare le **autorizzazioni** appropriate di lettura o scrittura per gli oggetti in base al modo in cui il servizio collegato (lettura, scrittura, lettura/scrittura) viene usato nel Data factory.
* È necessario impostare la **scadenza** in modo appropriato. L'accesso agli oggetti di Archiviazione di Azure non deve scadere nel periodo attivo della pipeline.
* L'URI deve essere creato al giusto livello di contenitore/BLOB o tabella in base alla necessità. Un URI di firma di accesso condiviso per un BLOB di Azure consente al servizio Data factory di accedere a quel particolare BLOB. Un URI di firma di accesso condiviso per un contenitore BLOB di Azure consente al servizio Data factory di eseguire l'iterazione tra i BLOB in quel contenitore. Se si deve consentire l'accesso a più o meno oggetti in un secondo momento o aggiornare l'URI di firma di accesso condiviso, ricordarsi di aggiornare il servizio collegato con il nuovo URI.   

<!---HONumber=AcomDC_0224_2016-->