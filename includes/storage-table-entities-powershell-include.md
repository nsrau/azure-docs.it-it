---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179822"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gestione delle entità di tabella

Dopo aver creato una tabella, verranno ora descritte le varie modalità di gestione delle entità o delle righe nella tabella. 

Le entità possono avere fino a 255 proprietà, incluse tre proprietà di sistema: **PartitionKey**, **RowKey**e **timestamp**. L'utente è responsabile dell'inserimento e dell'aggiornamento dei valori di **PartitionKey** e **RowKey**. Il server gestisce il valore **timestamp**, che non può essere modificato. Insieme **PartitionKey** e **RowKey** identificano in modo univoco tutte le entità di una tabella.

* **PartitionKey**: determina la partizione in cui è archiviata l'entità.
* **RowKey**: identifica in modo univoco l'entità all'interno della partizione.

È possibile definire fino a 252 proprietà personalizzate per un'entità. 

### <a name="add-table-entities"></a>Aggiungere le entità di tabella

Aggiungere entità a una tabella usando **Add-AzTableRow**. Questi esempi usano chiavi di partizione con valori `partition1` e e `partition2` chiavi di riga uguali alle abbreviazioni di stato. Le proprietà in ogni entità sono `username` e `userid` . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Eseguire query sulle entità di tabella

È possibile eseguire una query sulle entità in una tabella usando il comando **Get-AzTableRow** .

> [!NOTE]
> I cmdlet **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**e **Get-AzureStorageTableRowByCustomFilter** sono deprecati e verranno rimossi in un aggiornamento futuro della versione.

#### <a name="retrieve-all-entities"></a>Recuperare tutte le entità

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Questo comando restituisce risultati simili a quelli riportati nella tabella seguente:

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Recuperare le entità per una partizione specifica

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

I risultati sono simili a quelli riportati nella tabella seguente:

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Recuperare le entità per un valore specifico di una colonna specifica

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Questa query recupera un record.

|campo|Valore|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Recuperare le entità con un filtro personalizzato 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Questa query recupera un record.

|campo|Valore|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aggiornamento di entità 

Esistono tre passaggi per aggiornare le entità. Il primo passaggio prevede il recupero dell'entità da modificare. Il secondo passaggio prevede l'applicazione delle modifiche. In terzo luogo, eseguire il commit della modifica usando **Update-AzTableRow**.

Aggiornare l'entità con username = 'Jessie' impostando username = 'Jessie2'. Questo esempio mostra anche un altro modo per creare un filtro personalizzato usando i tipi .NET.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

I risultati mostrano il record Jessie2.

|campo|Valore|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Eliminazione delle entità di tabella

È possibile eliminare un'entità o tutte le entità nella tabella.

#### <a name="deleting-one-entity"></a>Eliminazione di un'entità

Per eliminare una singola entità, ottenere un riferimento a tale entità e inviarla tramite pipe a **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Eliminare tutte le entità della tabella

Per eliminare tutte le entità della tabella, recuperarle e inviare tramite pipe i risultati al cmdlet di rimozione. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
