---
title: Eseguire attività del flusso di dati in Azure Data Factory | Microsoft Docs
description: L'attività del flusso di dati execute esegue flussi di dati.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557573"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Eseguire attività del flusso di dati in Azure Data Factory
Usare l'attività del flusso di dati execute per eseguire il flusso di dati di Azure Data factory in esecuzioni di pipeline debug (sandbox) e nelle esecuzioni di pipeline attivata.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Sintassi

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Proprietà del tipo

* ```dataflow``` è il nome dell'entità del flusso di dati che si desidera eseguire
* ```compute``` Descrive l'ambiente di esecuzione di Spark
* ```coreCount``` è il numero di core da assegnare a questa esecuzione dell'attività del flusso di dati

![Eseguire il flusso di dati](media/data-flow/activity-data-flow.png "eseguire il flusso di dati")

### <a name="run-on"></a>Esegui in

Scegliere l'ambiente di calcolo per l'esecuzione del flusso di dati. Il valore predefinito è il Runtime di integrazione predefinita di risoluzione automatica di Azure. Questa scelta verrà eseguito il flusso di dati nell'ambiente di Spark nella stessa area di data factory. Il tipo di calcolo sarà un cluster dei processi, ovvero che l'ambiente di calcolo potrebbe richiedere alcuni minuti per l'avvio.

### <a name="debugging-pipelines-with-data-flows"></a>Debug di pipeline con i flussi di dati

![Eseguire il debug sul pulsante](media/data-flow/debugbutton.png "sul pulsante di Debug")

Usare i dati del flusso di Debug di usare un cluster warmed per il test in modo interattivo i flussi di dati in un'esecuzione del debug di pipeline. Usare l'opzione di Debug della pipeline per testare i flussi di dati all'interno di una pipeline.

### <a name="compute-type"></a>Tipo di calcolo

È possibile scegliere generico, calcolo ottimizzato o in base ai requisiti del flusso di dati ottimizzati per la memoria.

### <a name="core-count"></a>Numero di core

Scegliere il numero di core che si desidera assegnare al processo. Per i processi più piccoli, meno core funzionerà meglio.

### <a name="staging-area"></a>Area di gestione temporanea

Se si elaborano i dati in Azure Data Warehouse, è necessario scegliere un percorso di gestione temporanea per il caricamento in batch Polybase.

## <a name="parameterized-datasets"></a>Set di dati con parametri

Se si usa i set di dati con parametri, assicurarsi di impostare i valori dei parametri.

![Eseguire i parametri del flusso di dati](media/data-flow/params.png "parametri")

### <a name="debugging-parameterized-data-flows"></a>Debug dei flussi di dati con parametri

È possibile solo eseguire il debug di flussi di dati con set di dati con parametri di Debug di Pipeline di esecuzione usando l'attività flusso di dati execute. Le sessioni di debug interattivo nel flusso di dati di Azure Data factory attualmente non funzionano con i set di dati con parametri. Le esecuzioni di pipeline ed esecuzioni di debug funzionerà con i parametri.

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
