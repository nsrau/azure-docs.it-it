<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gestione delle entità di tabella

Dopo aver creato una tabella, verranno ora descritte le varie modalità di gestione delle entità o delle righe nella tabella. 

Un'entità può contenere fino a 255 proprietà, incluse 3 proprietà di sistema: **PartitionKey**, **RowKey** e **Timestamp**. L'utente è responsabile dell'inserimento e dell'aggiornamento dei valori di **PartitionKey** e **RowKey**. Il server gestisce il valore **Timestamp**, che non può essere modificato. Insieme **PartitionKey** e **RowKey** identificano in modo univoco tutte le entità di una tabella.

* **PartitionKey**: determina la partizione in cui è archiviata l'entità.
* **RowKey**: identifica in modo univoco l'entità all'interno della partizione.

È possibile definire fino a 252 proprietà personalizzate per un'entità. 

### <a name="add-table-entities"></a>Aggiungere le entità di tabella

Aggiungere entità a una tabella con **Add-StorageTableRow**. Questi esempi usano chiavi di partizione impostate sui valori "partition1" e "partition2" e chiavi di riga impostate sulle abbreviazioni di stato. Le proprietà di ciascuna entità sono username e userid. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Eseguire query sulle entità di tabella

Esistono diversi modi per eseguire query sulle entità di una tabella.

#### <a name="retrieve-all-entities"></a>Recuperare tutte le entità

Per recuperare tutte le entità, usare **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Questo comando restituisce risultati simili a quelli riportati nella tabella seguente:

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Recuperare le entità per una partizione specifica

Per recuperare tutte le entità di una partizione specifica, usare **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
I risultati sono simili a quelli riportati nella tabella seguente:

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Recuperare le entità per un valore specifico di una colonna specifica

Per recuperare le entità in cui il valore di una specifica colonna è uguale a un valore specifico, usare **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Questa query recupera un record.

|campo|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Recuperare le entità con un filtro personalizzato 

Per recuperare le entità con un filtro personalizzato, usare **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq '1')"
```

Questa query recupera un record.

|campo|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aggiornamento di entità 

Esistono tre passaggi per aggiornare le entità. Il primo passaggio prevede il recupero dell'entità da modificare. Il secondo passaggio prevede l'applicazione delle modifiche. Nel terzo viene eseguito il commit delle modifiche tramite **Update-AzureStorageTableRow**.

Aggiornare l'entità con username = 'Jessie' impostando username = 'Jessie2'. Questo esempio mostra anche un altro modo per creare un filtro personalizzato usando i tipi .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

I risultati mostrano il record Jessie2.

|campo|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Eliminazione delle entità di tabella

È possibile eliminare un'entità o tutte le entità nella tabella.

#### <a name="deleting-one-entity"></a>Eliminazione di un'entità

Per eliminare una singola entità, ottenere un riferimento a tale entità e inviarlo tramite pipe a **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Eliminare tutte le entità della tabella 

Per eliminare tutte le entità della tabella, recuperarle e inviare tramite pipe i risultati al cmdlet di rimozione. 

```powershell
# Get all rows and pipe it into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```