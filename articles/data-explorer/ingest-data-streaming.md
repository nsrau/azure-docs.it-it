---
title: Usare l'inserimento di flussi per inserire i dati in Azure Esplora dati
description: Informazioni su come inserire (caricare) i dati in Azure Esplora dati usando l'inserimento di flussi.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 712273ddfb8b6f781627e2cc7915a1f538f57b4d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090625"
---
# <a name="streaming-ingestion-preview"></a>Inserimento di flussi (anteprima)

L'inserimento di flussi è destinato a scenari che richiedono una bassa latenza con un tempo di inserimento inferiore a 10 secondi per i vari dati del volume. Viene usato per l'ottimizzazione dell'elaborazione operativa di molte tabelle, in uno o più database in cui il flusso di dati in ogni tabella è relativamente piccolo (pochi record al secondo), ma il volume di inserimento dati complessivo è elevato (migliaia di record al secondo).

Usare l'inserimento classico (bulk) invece dell'inserimento di flussi quando la quantità di dati aumenta fino a un massimo di 1 MB al secondo per tabella. Per ulteriori informazioni sui vari metodi di inserimento, vedere [Cenni preliminari](/azure/data-explorer/ingest-data-overview) sull'inserimento di dati.

> [!NOTE]
> L'inserimento di flussi non supporta le funzionalità seguenti:
> * [Cursori del database](/azure/kusto/management/databasecursor).
> * [Mapping dei dati](/azure/kusto/management/mappings). È supportato solo il mapping dei dati [creato in precedenza](/azure/kusto/management/tables#create-ingestion-mapping) . 

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Accedere all' [interfaccia utente Web](https://dataexplorer.azure.com/).
* Creare [un cluster e un database di Azure Esplora dati](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Abilitare l'inserimento di flussi nel cluster

1. Nel portale di Azure passare a cluster di Esplora dati di Azure. In **Impostazioni**selezionare **configurazioni**. 
1. Nel riquadro **configurazioni** selezionare **attivato per abilitare l'inserimento del** **flusso**.
1. Selezionare **Salva**.
 
    ![inserimento di flussi in](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Nell' [interfaccia utente Web](https://dataexplorer.azure.com/), definire i criteri di inserimento del [flusso](/azure/kusto/concepts/streamingingestionpolicy) per le tabelle o i database che riceveranno i dati in streaming. 

    > [!NOTE]
    > * Se il criterio viene definito a livello di database, tutte le tabelle nel database sono abilitate per l'inserimento di flussi.
    > * I criteri applicati possono fare riferimento solo ai dati appena inseriti e non alle altre tabelle del database.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Usare l'inserimento di flussi per inserire i dati nel cluster

Sono disponibili due tipi di inserimento di flussi supportati:

* [Hub eventi](/azure/data-explorer/ingest-data-event-hub) usato come origine dati
* Per l'inserimento personalizzato è necessario scrivere un'applicazione che usi una delle librerie client di Azure Esplora dati. Per un'applicazione di esempio, vedere esempio di inserimento di [flussi](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) .

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Scegliere il tipo di inserimento flusso appropriato

|   |Hub eventi  |Inserimento personalizzato  |
|---------|---------|---------|
|Ritardo dei dati tra l'avvio dell'inserimento e i dati disponibili per la query   |    ritardo più lungo     |   ritardo più breve      |
|Overhead di sviluppo    |   configurazione rapida e semplice, nessun sovraccarico di sviluppo    |   elevato sovraccarico di sviluppo per l'applicazione per gestire gli errori e assicurare la coerenza dei dati     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Disabilitare l'inserimento di flussi nel cluster

> [!WARNING]
> La disabilitazione dell'inserimento di flussi potrebbe richiedere alcune ore.

1. Elimina i criteri di inserimento dei [flussi](/azure/kusto/concepts/streamingingestionpolicy) da tutte le tabelle e i database pertinenti. La rimozione dei criteri di inserimento dei flussi attiva lo spostamento dei dati di inserimento dallo spazio di archiviazione iniziale all'archiviazione permanente nell'archivio colonne (extent o partizioni). Lo spostamento dei dati può durare tra pochi secondi e alcune ore, a seconda della quantità di dati nell'archiviazione iniziale e dell'utilizzo della CPU e della memoria del cluster.
1. Nel portale di Azure passare a cluster di Esplora dati di Azure. In **Impostazioni**selezionare **configurazioni**. 
1. Nel riquadro **configurazioni** selezionare disattivato per disabilitare l' **inserimento** del **flusso**.
1. Selezionare **Salva**.

    ![Inserimento di flussi disattivato](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Limitazioni

* Le prestazioni e la capacità di inserimento dei flussi vengono ridimensionate con dimensioni maggiori di macchine virtuali e cluster. Per un singolo nodo D14, il carico consigliato è fino a 150 richieste al secondo.
* Attualmente, il supporto è solo per gli SKU con 8 e 16 core (D13, D14, L8 e L16).
* Il limite per le dimensioni dei dati per ogni richiesta di inserimento è 4 MB.
* Gli aggiornamenti dello schema, ad esempio la creazione e la modifica di tabelle e mapping di inserimento, possono richiedere fino a 5 minuti per il servizio di inserimento di flussi.
* L'abilitazione dell'inserimento di flussi in un cluster, anche quando i dati non vengono inseriti tramite lo streaming, usa una parte del disco SSD locale dei computer del cluster per il flusso dei dati di inserimento e riduce lo spazio di archiviazione disponibile per la cache a caldo.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query sui dati in Azure Esplora dati](web-query-data.md)
