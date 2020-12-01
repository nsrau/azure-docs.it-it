---
title: Usare l'inventario di archiviazione di Azure per gestire i dati BLOB (anteprima)
description: L'inventario di archiviazione di Azure è uno strumento che consente di ottenere una panoramica di tutti i dati BLOB in un account di archiviazione.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 11/04/2020
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 149fb9c888c54ea45d273890f3fe2cd59730fa01
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355008"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Usare l'inventario BLOB di archiviazione di Azure per gestire i dati BLOB (anteprima)

La funzionalità di inventario BLOB di archiviazione di Azure offre una panoramica dei dati BLOB in un account di archiviazione. Usare il report inventario per comprendere le dimensioni totali dei dati, l'età, lo stato della crittografia e così via. Il report fornisce una panoramica dei dati per i requisiti aziendali e di conformità. Una volta abilitato, viene creato automaticamente un report di inventario giornaliero.

## <a name="availability"></a>Disponibilità

L'inventario BLOB è supportato per gli account per utilizzo generico versione 2 (GPv2), archiviazione BLOB in blocchi Premium e Azure datalake storage Gen2 (ADLS Gen2).

### <a name="preview-regions"></a>Aree di anteprima

L'anteprima dell'inventario BLOB è disponibile negli account di archiviazione nelle aree seguenti:

- Francia centrale
- Canada centrale
- Canada orientale

### <a name="pricing-and-billing"></a>Prezzi e fatturazione

La tariffa per i report di inventario non viene addebitata durante il periodo di anteprima. I prezzi verranno determinati quando questa funzionalità è disponibile a livello generale.

## <a name="enable-inventory-reports"></a>Abilita report inventario

Abilitare i report inventario BLOB aggiungendo un criterio all'account di archiviazione. Aggiungere, modificare o rimuovere un criterio usando il [portale di Azure](https://portal.azure.com/).

1. Passare al [portale di Azure](https://portal.azure.com/)
1. Selezionare uno degli account di archiviazione
1. In **servizio BLOB** selezionare **inventario BLOB**
1. Verificare che sia selezionato **inventario BLOB abilitato**
1. Selezionare **Aggiungi una regola**
1. Assegnare un nome alla nuova regola
1. Selezionare i **tipi di BLOB** per il report di inventario
1. Aggiungere una corrispondenza di prefisso per filtrare il report di inventario
1. Consente di scegliere se **includere le versioni BLOB** e **includere gli snapshot** nel report di inventario. Per salvare una nuova regola con l'opzione corrispondente abilitata, è necessario abilitare le versioni e gli snapshot nell'account.
1. Selezionare **Salva**

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Screenshot che illustra come aggiungere una regola di inventario BLOB usando il portale di Azure":::

I criteri di inventario vengono letti o scritti completamente. Gli aggiornamenti parziali non sono supportati.

> [!IMPORTANT]
> Se si abilitano le regole del firewall per l'account di archiviazione, le richieste di inventario potrebbero essere bloccate. È possibile sbloccare queste richieste fornendo eccezioni per i servizi Microsoft attendibili. Per altre informazioni, vedere la sezione Eccezioni in [Configurare i firewall e le reti virtuali](../common/storage-network-security.md#exceptions).

Un'esecuzione dell'inventario BLOB viene automaticamente pianificata ogni giorno. Il completamento dell'esecuzione di un inventario può richiedere fino a 24 ore. Un report di inventario viene configurato aggiungendo un criterio di inventario con una o più regole.

## <a name="inventory-policy"></a>Criteri di inventario

Un criterio di inventario è una raccolta di regole in un documento JSON.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Visualizzare il codice JSON per i criteri di inventario selezionando la scheda **visualizzazione codice** nella sezione **inventario BLOB** della portale di Azure.

| Nome parametro | Tipo di parametro        | Note | Necessaria? |
|----------------|-----------------------|-------|-----------|
| destination    | string                | Contenitore di destinazione in cui verranno generati tutti i file di inventario. Il contenitore di destinazione deve essere già esistente. | Sì |
| Enabled        | Boolean               | Utilizzato per disabilitare l'intero criterio. Se impostato su **true**, il campo abilitato a livello di regola esegue l'override di questo parametro. Se disabilitato, l'inventario per tutte le regole verrà disabilitato. | Sì |
| regole          | Matrice di oggetti Rule | È necessario almeno una regola in un criterio. Sono supportate fino a 10 regole. | Sì |

## <a name="inventory-rules"></a>Regole di inventario

Una regola acquisisce le condizioni di filtro e i parametri di output per la generazione di un report di inventario. Ogni regola crea un report di inventario. Le regole possono avere prefissi sovrapposti. Un BLOB può essere visualizzato in più di un inventario a seconda delle definizioni delle regole.

Ogni regola all'interno del criterio presenta diversi parametri:

| Nome parametro | Tipo di parametro                 | Note | Necessaria? |
|----------------|--------------------------------|-------|-----------|
| name           | string                         | Un nome di regola può includere fino a 256 caratteri alfanumerici con distinzione tra maiuscole e minuscole. Il nome deve essere univoco all'interno di un criterio. | Sì |
| Enabled        | Boolean                        | Flag che consente di abilitare o disabilitare una regola. Il valore predefinito è **true**. | Sì |
| Definizione     | Definizione della regola di inventario JSON | Ogni definizione è costituita da un set di filtri di regole. | Sì |

Il flag Global **BLOB Inventory Enabled** ha la precedenza sul parametro *Enabled* in una regola.

### <a name="rule-filters"></a>Filtri della regola

Per personalizzare un report inventario BLOB sono disponibili diversi filtri:

| Nome filtro         | Tipo di filtro                     | Note | Necessaria? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Matrice di valori enum predefiniti | I valori validi sono `blockBlob` e `appendBlob` per gli account gerarchici abilitati per gli spazi dei nomi e `blockBlob` , e per gli `appendBlob` `pageBlob` altri account. | Sì |
| prefixMatch         | Matrice di un massimo di 10 stringhe per la corrispondenza dei prefissi. Un prefisso deve iniziare con un nome di contenitore, ad esempio, "container1/foo" | Se non si definisce *prefixMatch* o si specifica un prefisso vuoto, la regola si applica a tutti i BLOB all'interno dell'account di archiviazione. | No |
| includeSnapshots    | Boolean                         | Specifica se l'inventario deve includere snapshot. Il valore predefinito è **false**. | No |
| includeBlobVersions | Boolean                         | Specifica se l'inventario deve includere le versioni dei BLOB. Il valore predefinito è **false**. | No |

Visualizzare il codice JSON per le regole di inventario selezionando la scheda **visualizzazione codice** nella sezione **inventario BLOB** della portale di Azure. I filtri sono specificati all'interno di una definizione di regola.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Output inventario

Ogni esecuzione di inventario genera un set di file in formato CSV nel contenitore di destinazione dell'inventario specificato. L'output dell'inventario viene generato nel percorso seguente: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` dove:

- *AccountName* è il nome dell'account di archiviazione BLOB di Azure
- *Inventory-Destination-container* è il contenitore di destinazione specificato nei criteri di inventario
- *Aaaa/mm/gg/hh-mm-SS* è l'ora di inizio dell'esecuzione dell'inventario

### <a name="inventory-files"></a>File di inventario

Ogni esecuzione dell'inventario genera i seguenti file:

- **Inventario file CSV**: file con valori delimitati da virgole (CSV) per ogni regola di inventario. Ogni file contiene gli oggetti corrispondenti e i relativi metadati. La prima riga in ogni file con formato CSV è sempre la riga dello schema. Nell'immagine seguente viene illustrato un file CSV di inventario aperto in Microsoft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Screenshot di un file CSV di inventario aperto in Microsoft Excel":::

- **File manifesto**: un manifest.jssu un file contenente i dettagli dei file di inventario generati per ogni regola di tale esecuzione. Il file manifesto acquisisce anche la definizione della regola fornita dall'utente e il percorso dell'inventario per la regola.

- **File di checksum**: file manifest. checksum contenente il checksum MD5 del contenuto di manifest.jssu file. La generazione del file manifest. checksum contrassegna il completamento di un'esecuzione di inventario.

## <a name="inventory-completed-event"></a>Inventario completato evento

Sottoscrivere l'evento inventario completato per ricevere una notifica al termine dell'esecuzione dell'inventario. Questo evento viene generato quando viene creato il file di checksum del manifesto. L'evento inventario completato si verifica anche in caso di errore dell'esecuzione dell'inventario prima dell'avvio dell'esecuzione. Ad esempio, un criterio non valido o un errore del contenitore di destinazione non presente attiverà l'evento. L'evento viene pubblicato nell'argomento inventario BLOB.

Evento di esempio:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Passaggi successivi

[Gestire il ciclo di vita dell'archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
