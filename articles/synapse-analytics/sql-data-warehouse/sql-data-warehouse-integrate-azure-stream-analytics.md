---
title: Usare analisi di flusso di Azure
description: Suggerimenti per l'uso di analisi di flusso di Azure con le data warehouse in sinapsi di Azure per lo sviluppo di soluzioni in tempo reale.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 3ead3393218255808eb67983251fcf9f2561c82c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020181"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Usare Analisi di flusso di Azure con Azure Synapse Analytics

Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per informazioni di base, vedere [Introduzione ad Analisi di flusso di Azure](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). È possibile apprendere come creare una soluzione end-to-end con Analisi di flusso seguendo l’esercitazione [Introduzione all’uso di Analisi di flusso di Azure](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

In questo articolo si apprenderà come usare il data warehouse come sink di output per l'inserimento di dati con velocità effettiva elevata con i processi di analisi di flusso di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Processo di analisi di flusso di Azure: per creare un processo di analisi di flusso di Azure, seguire la procedura descritta nell'esercitazione [Introduzione all'uso di analisi di flusso di Azure](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) per:  

    1. Creare un input dell'hub eventi
    2. Configurare e avviare l'applicazione di generazione di eventi
    3. Eseguire il provisioning di un processo di Analisi dei flussi
    4. Specificare la query e l'input del processo
* Pool SQL dedicato a sinapsi per la data warehouse: per creare una nuova data warehouse, seguire i passaggi nella [Guida introduttiva per creare un nuovo data warehouse](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Specificare l'output del flusso in modo che punti al data warehouse

### <a name="step-1"></a>Passaggio 1

Dal portale di Azure passare al processo di analisi di flusso e fare clic su **output** nel menu **topologia processo** .

### <a name="step-2"></a>Passaggio 2

Fare clic sul pulsante **Aggiungi** e scegliere **Azure sinapsi Analytics** dal menu a discesa.

![Scegliere Azure sinapsi Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Passaggio 3

Immettere i valori seguenti:

* *Alias di output*: immettere un nome descrittivo per l'output del processo.
* *Sottoscrizione*:
  * Se il data warehouse si trova nella stessa sottoscrizione del processo di analisi di flusso, fare clic su ***selezionare Azure sinapsi Analytics dalle sottoscrizioni** _.
  _ Se il data warehouse si trova in una sottoscrizione diversa, fare clic su fornire le impostazioni di Azure sinapsi Analytics manualmente.
* *Database*: selezionare il database di destinazione dall'elenco a discesa.
* *Nome utente*: digitare il nome utente di un account con autorizzazioni di scrittura nel database.
* *Password*: fornire la password per l'account utente specificato.
* *Tabella*: specificare il nome della tabella di destinazione nel database.
* fare clic sul pulsante **Salva** .

![Modulo di analisi sinapsi di Azure completato](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Passaggio 4

Prima di poter eseguire un test, è necessario creare la tabella nella data warehouse.  Eseguire lo script di creazione tabella seguente usando SQL Server Management Studio (SSMS) o lo strumento di query scelto.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Passaggio 5

Nel portale di Azure per il processo di analisi di flusso, fare clic sul nome del processo.  Fare clic sul pulsante **_test_* _ nel riquadro _*_Dettagli output_*_ .

![Pulsante test sui dettagli dell'outbroncio ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) quando la connessione al database ha esito positivo, nel portale viene visualizzata una notifica.

### <a name="step-6"></a>Passaggio 6

Fare clic sul menu _*_query_*_ in _*_topologia processo_*_ e modificare la query per inserire i dati nell'output del flusso creato.  Fai clic sul pulsante _*_test selezionato_*_ per testare la query.  Quando il test di query ha esito positivo, fare clic sul pulsante _*_Salva query_*_ .

![Salvare la query](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Passaggio 7

Avviare il processo di analisi di flusso di Azure.  Fare clic sul pulsante _*_Start_*_ nel menu _*_Overview (panoramica_*_ ).

![Avviare il processo di Analisi di flusso](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Fare clic sul pulsante _ *_Avvia_** nel riquadro Avvia processo.

![Fare clic su Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica dell'integrazione, vedere [integrazione di altri servizi](sql-data-warehouse-overview-integrate.md).
Per altri suggerimenti sullo sviluppo, vedere [decisioni di progettazione e tecniche di codifica per i data warehouse](sql-data-warehouse-overview-develop.md).
