---
title: Usare l'inserimento di streaming per inserire dati in Azure Data ExplorerUse streaming ingestion to ingest data into Azure Data Explorer
description: Informazioni su come inserire (caricare) dati in Azure Data Explorer usando l'inserimento nello streaming.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297085"
---
# <a name="streaming-ingestion-preview"></a>Inserimento in streaming (anteprima)Streaming ingestion (Preview)

Usare l'inserimento in streaming quando è necessaria una bassa latenza con un tempo di inserimento inferiore a 10 secondi per dati di volume diversi. Viene utilizzato per ottimizzare l'elaborazione operativa di molte tabelle, in uno o più database, in cui il flusso di dati in ogni tabella è relativamente piccolo (pochi record al secondo), ma il volume di inserimento dati complessivo è elevato (migliaia di record al secondo). 

Usare l'inserimento bulk anziché l'inserimento nello streaming quando la quantità di dati aumenta fino a più di 1 MB al secondo per tabella. Per altre informazioni sui vari metodi di inserimento, [leggere Panoramica dell'inserimento](/azure/data-explorer/ingest-data-overview) dei dati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Accedere [all'interfaccia utente Web.](https://dataexplorer.azure.com/)
* Creare un cluster e un database di [Azure Data ExplorerCreate an Azure Data Explorer cluster and database](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Abilitare l'inserimento nello streaming nel clusterEnable streaming ingestion on your cluster

> [!WARNING]
> Si prega di rivedere le [limitazioni](#limitations) prima di consentire l'ingestione di fumifu.

1. Nel portale di Azure passare a cluster di Esplora dati di Azure. In **Impostazioni**selezionare **Configurazioni**. 
1. Nel riquadro **Configurazioni** selezionare **Attivato** per abilitare **l'inserimento in streaming.**
1. Selezionare **Salva**.
 
    ![ingestione in streaming su](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. [Nell'interfaccia utente Web](https://dataexplorer.azure.com/)definire i criteri di [inserimento](/azure/kusto/management/streamingingestionpolicy) dello streaming nelle tabelle o nei database che riceveranno i dati di streaming. 

    > [!NOTE]
    > * Se il criterio è definito a livello di database, tutte le tabelle nel database vengono abilitate per l'inserimento nel flusso.
    > * Il criterio applicato può fare riferimento solo ai dati appena ingeriti e non ad altre tabelle del database.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Usare l'inserimento in streaming per l'inserimento di dati nel clusterUse streaming ingestion to ingest data to your cluster

Esistono due tipi di inserimento di streaming supportati:There are two supported streaming ingestion types:


* [**Hub eventi**](/azure/data-explorer/ingest-data-event-hub), utilizzato come origine dati
* **L'inserimento personalizzato** richiede la scrittura di un'applicazione che usa una delle librerie client di Azure Data Explorer.Custom ingestion requires you to write an application that uses one of the Azure Data Explorer client libraries. Vedere [l'esempio](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) di inserimento in streaming per un'applicazione di esempio.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Scegliere il tipo di inserimento di streaming appropriato

|   |Hub eventi  |Inserimento personalizzato  |
|---------|---------|---------|
|Ritardo dei dati tra l'avvio dell'inserimento e i dati disponibili per la queryData delay between ingestion initiation and the data available for query   |    Ritardo più lungo     |   Ritardo più breve      |
|Sovraccarico di sviluppo    |   Configurazione semplice e veloce, nessun sovraccarico di sviluppo    |   Elevato sovraccarico di sviluppo per le applicazioni per gestire gli errori e garantire la coerenza dei dati     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Disabilitare l'inserimento nello streaming nel clusterDisable streaming ingestion on your cluster

> [!WARNING]
> La disabilitazione dell'inserimento nello streaming potrebbe richiedere alcune ore.

1. Eliminare i criteri di [inserimento](/azure/kusto/management/streamingingestionpolicy) dello streaming da tutte le tabelle e i database pertinenti. La rimozione dei criteri di inserimento in streaming attiva lo spostamento dei dati di inserimento in streaming dall'archiviazione iniziale all'archiviazione permanente nell'archivio colonne (estensioni o partizioni). Lo spostamento dei dati può durare da pochi secondi a poche ore, a seconda della quantità di dati nello spazio di archiviazione iniziale e del modo in cui la CPU e la memoria vengono utilizzate dal cluster.
1. Nel portale di Azure passare a cluster di Esplora dati di Azure. In **Impostazioni**selezionare **Configurazioni**. 
1. Nel riquadro **Configurazioni** selezionare **Disattivato** per disabilitare **l'inserimento in streaming**.
1. Selezionare **Salva**.

    ![Ingestione in streaming off](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Limitazioni

* L'inserimento nello streaming non supporta [i cursori di database](/azure/kusto/management/databasecursor) o il mapping dei [dati.](/azure/kusto/management/mappings) È supportato solo il mapping dei dati [creato in precedena.](/azure/kusto/management/create-ingestion-mapping-command) 
* Prestazioni e capacità di inserimento dello streaming con dimensioni crescenti di macchine virtuali e cluster. Le ingestione simultanee sono limitate a sei inserimenti per core. Ad esempio, per 16 SKU core, ad esempio D14 e L16, il carico supportato massimo è 96 inserimenti simultanei. Per due SKU core, ad esempio D11, il carico supportato massimo è 12 inserimenti simultanei.
* Il limite di dimensione dei dati per ogni richiesta di inserimento è 4 MB.
* Gli aggiornamenti dello schema, ad esempio la creazione e la modifica di tabelle e mapping di inserimento, possono richiedere fino a cinque minuti per il servizio di inserimento di streaming.
* L'abilitazione dell'inserimento dello streaming in un cluster, anche quando i dati non vengono ingeriti tramite streaming, usa parte del disco SSD locale dei computer del cluster per lo streaming dei dati di inserimento e riduce lo spazio di archiviazione disponibile per la hot cache.
* [I tag](/azure/kusto/management/extents-overview#extent-tagging) di estensione non possono essere impostati sui dati di inserimento dello streaming.

## <a name="next-steps"></a>Passaggi successivi

* [Query data in Azure Data Explorer](web-query-data.md)
