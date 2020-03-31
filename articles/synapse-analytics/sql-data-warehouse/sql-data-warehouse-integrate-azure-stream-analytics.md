---
title: Usare Analisi di flusso di AzureUse Azure Stream Analytics
description: Suggerimenti per l'uso di Analisi di flusso di Azure con il data warehouse in Azure Synapse per lo sviluppo di soluzioni in tempo reale.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: feb7b52c84e5e702202bc668cfda676d291ea82e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350427"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Usare Analisi di flusso di Azure con Analisi synapse di AzureUse Azure Stream Analytics with Azure Synapse Analytics

Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per informazioni di base, vedere [Introduzione ad Analisi di flusso di Azure](../../stream-analytics/stream-analytics-introduction.md). È possibile apprendere come creare una soluzione end-to-end con Analisi di flusso seguendo l’esercitazione [Introduzione all’uso di Analisi di flusso di Azure](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

In questo articolo si apprenderà come usare il data warehouse come sink di output per i processi di Analisi di flusso di Azure.In this article, you will learn how to use your data warehouse as an output sink for your Azure Stream Analytics jobs.

## <a name="prerequisites"></a>Prerequisiti

* Processo di Analisi di flusso di Azure: per creare un processo di Analisi di flusso di Azure, seguire i passaggi dell'esercitazione [Introduzione all'uso](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) di Analisi di flusso di Azure per :Azure Stream Analytics job - To create an Azure Stream Analytics job, follow the steps in the Getstarted using Azure Stream Analytics tutorial to:  

    1. Creare un input dell'hub eventi
    2. Configurare e avviare l'applicazione di generazione di eventi
    3. Eseguire il provisioning di un processo di Analisi dei flussi
    4. Specificare la query e l'input del processo
* Data warehouse del pool SQL di Azure Synapse: per creare un nuovo data warehouse, seguire i passaggi della [Guida introduttiva per creare un nuovo data warehouse.](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Specificare l'output di streaming in modo che punti al data warehouseSpecify streaming output to point to your data warehouse

### <a name="step-1"></a>Passaggio 1

Dal portale di Azure passare al processo analisi di flusso e fare clic su **Output** nel menu **Topologia processo.**

### <a name="step-2"></a>Passaggio 2

Fare clic sul pulsante **Aggiungi** e scegliere **Database SQL** dal menu a discesa.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Passaggio 3

Immettere i valori seguenti:

* *Alias di output*: Immettere un nome descrittivo per l'output del processo.
* *Sottoscrizione*:
  * Se il data warehouse si trova nella stessa sottoscrizione del processo di Analisi di flusso, fare clic su ***Seleziona database SQL dalle sottoscrizioni***.
  * Se il database si trova in una sottoscrizione diversa, fare clic su Fornisci impostazioni database SQL manualmente.
* *Database*: Selezionare il database di destinazione dall'elenco a discesa.
* *Nome utente*: digitare il nome utente di un account con autorizzazioni di scrittura nel database.
* *Password*: fornire la password per l'account utente specificato.
* *Tabella*: specificare il nome della tabella di destinazione nel database.
* fare clic sul pulsante **Salva**

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Passaggio 4

Prima di eseguire un test, è necessario creare la tabella nel data warehouse.  Eseguire lo script di creazione della tabella seguente usando SQL Server Management Studio (SSMS) o lo strumento di query scelto.

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

Nel portale di Azure per il processo di Analisi di flusso fare clic sul nome del processo.  Fare clic sul pulsante ***Test*** nel riquadro ***Dettagli output.***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)Quando la connessione al database ha esito positivo, verrà visualizzata una notifica nel portale.

### <a name="step-6"></a>Passaggio 6

Fare clic sul menu ***Query*** in ***Topologia processo*** e modificare la query in modo da inserire i dati nell'output di Stream creato.  Fare clic sul pulsante ***Test query selezionato*** per testare la query.  Fare clic sul pulsante ***Salva query*** quando il test di query ha esito positivo.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Passaggio 7

Avviare il processo di Analisi di flusso di Azure.Start the Azure Stream Analytics job.  Fare clic sul pulsante ***Start*** nel menu ***Panoramica.***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

fare clic sul pulsante ***Start*** nel riquadro Avvia processo.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica dell'integrazione, vedere [Integrare altri servizi](sql-data-warehouse-overview-integrate.md).
Per ulteriori suggerimenti sullo sviluppo, vedere Decisioni di progettazione e tecniche di [codifica per i data warehouse.](sql-data-warehouse-overview-develop.md)
