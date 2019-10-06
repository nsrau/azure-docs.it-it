---
title: Attività flusso di dati in Azure Data Factory | Microsoft Docs
description: Come eseguire i flussi di dati dall'interno di una pipeline data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: makromer
ms.openlocfilehash: 7db410e97046b6d251eb73e754e40eab09a2ee64
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981787"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Attività flusso di dati in Azure Data Factory

Utilizzare l'attività flusso di dati per trasformare e spostare i dati tramite il mapping di flussi di dati. Se non si ha familiarità con i flussi di dati, vedere [Panoramica del flusso di dati di mapping](concepts-data-flow-overview.md)

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Sintassi

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Richiesto
-------- | ----------- | -------------- | --------
Dataflow | Riferimento al flusso di dati in esecuzione | DataFlowReference | Yes
integrationRuntime | Ambiente di calcolo in cui viene eseguito il flusso di dati | IntegrationRuntimeReference | Yes
staging. linkedService | Se si usa un'origine o un sink di SQL DW, l'account di archiviazione usato per la gestione temporanea di base | LinkedServiceReference | Solo se il flusso di dati legge o scrive in SQL DW
staging. folderPath | Se si usa un'origine o un sink di SQL DW, il percorso della cartella nell'account di archiviazione BLOB usato per la gestione temporanea di base | Stringa | Solo se il flusso di dati legge o scrive in SQL DW

Esegui flusso di dati(media/data-flow/activity-data-flow.png "esecuzione") ![flusso di dati]

### <a name="data-flow-integration-runtime"></a>Runtime di integrazione del flusso di dati

Scegliere la Integration Runtime da usare per l'esecuzione dell'attività flusso di dati. Per impostazione predefinita, Data Factory userà il runtime di integrazione di Azure con quattro core di lavoro e nessuna durata (TTL). Questo IR ha un tipo di calcolo per utilizzo generico e viene eseguito nella stessa area della factory. È possibile creare runtime di integrazione di Azure personalizzati che definiscono aree specifiche, tipo di calcolo, conteggi core e TTL per l'esecuzione dell'attività flusso di dati.

Per le esecuzioni di pipeline, il cluster è un cluster di processi che richiede alcuni minuti per l'avvio prima dell'avvio dell'esecuzione. Se non viene specificato alcun valore TTL, questo tempo di avvio è necessario per ogni esecuzione della pipeline. Se si specifica un valore TTL, un pool di cluster caldo resterà attivo per il tempo specificato dopo l'ultima esecuzione, ottenendo tempi di avvio più brevi. Se, ad esempio, si dispone di un valore TTL di 60 minuti ed è necessario eseguirvi un flusso di dati una volta all'ora, il pool di cluster resterà attivo. Per altre informazioni, vedere [runtime di integrazione di Azure](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> La Integration Runtime selezione nell'attività flusso di dati si applica solo alle *esecuzioni attivate* della pipeline. Il debug della pipeline con flussi di dati viene eseguito nel cluster specificato nella sessione di debug.

### <a name="polybase"></a>PolyBase

Se si usa un Azure SQL Data Warehouse come sink o origine, è necessario scegliere un percorso di gestione temporanea per il carico batch di base. La polibase consente il caricamento batch in blocco anziché il caricamento dei dati riga per riga. La polibase riduce drasticamente il tempo di caricamento in SQL DW.

## <a name="parameterizing-data-flows"></a>Flussi di dati parametrizzazione

### <a name="parameterized-datasets"></a>Set di impostazioni con parametri

Se il flusso di dati utilizza set di dati con parametri, impostare i valori dei parametri nella scheda **Impostazioni** .

(media/data-flow/params.png "Parametri") di ![esecuzione dei parametri del flusso di dati]

### <a name="parameterized-data-flows"></a>Flussi di dati con parametri

Se il flusso di dati è parametrizzato, impostare i valori dinamici dei parametri del flusso di dati nella scheda **parametri** . È possibile utilizzare il linguaggio delle espressioni della pipeline di ADF (solo per i tipi di stringa) o il linguaggio delle espressioni del flusso di dati per assegnare valori di parametri dinamici o letterali. Per ulteriori informazioni, vedere [parametri del flusso di dati](parameters-data-flow.md).

(media/data-flow/parameter-example.png "Esempio") di parametro dell' ![esempio di parametro flusso di dati]

## <a name="pipeline-debug-of-data-flow-activity"></a>Debug della pipeline dell'attività flusso di dati

Per eseguire una pipeline di debug eseguita con un'attività flusso di dati, è necessario attivare la modalità di debug del flusso di dati tramite il dispositivo di scorrimento **debug del flusso di dati** nella barra superiore. La modalità di debug consente di eseguire il flusso di dati in un cluster Spark attivo. Per altre informazioni, vedere [modalità di debug](concepts-data-flow-debug-mode.md).

Pulsante debug ![pulsante](media/data-flow/debugbutton.png "debug")

La pipeline di debug viene eseguita sul cluster di debug attivo, non sull'ambiente di Integration runtime specificato nelle impostazioni dell'attività flusso di dati. È possibile scegliere l'ambiente di calcolo di debug quando si avvia la modalità di debug.

## <a name="monitoring-the-data-flow-activity"></a>Monitoraggio dell'attività flusso di dati

L'attività flusso di dati offre un'esperienza di monitoraggio speciale in cui è possibile visualizzare le informazioni sul partizionamento, sulla fase temporale e sulla derivazione dei dati. Aprire il riquadro Monitoraggio usando l'icona degli occhiali in **azioni**. Per altre informazioni, vedere [monitoraggio dei flussi di dati](concepts-data-flow-monitoring.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere attività del flusso di controllo supportate da Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
