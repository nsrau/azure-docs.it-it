---
title: Eseguire attività del flusso di dati in Azure Data Factory | Microsoft Docs
description: Come eseguire dati flussi all'interno di una pipeline di data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: e1d4ce355f34014d5099c4b46f4420d032363fce
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236684"
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

Usare i dati del flusso di Debug di usare un cluster warmed per il test in modo interattivo i flussi di dati in un'esecuzione del debug di pipeline. Usare l'opzione di eseguire il Debug di Pipeline per testare i flussi di dati all'interno di una pipeline.

### <a name="run-on"></a>Esegui in

Si tratta di un campo obbligatorio che definisce quale Runtime di integrazione da usare per l'esecuzione dell'attività flusso di dati. Per impostazione predefinita, Data Factory userà il runtime di integrazione di Azure predefinito risoluzione automatica. Tuttavia, è possibile creare il proprio runtime di integrazione di Azure che definiscono aree specifiche, tipo di conteggi core e durata (TTL) di calcolo per l'esecuzione di attività del flusso di dati.

L'impostazione predefinita per le esecuzioni del flusso di dati è 8 core di calcolo generale con un valore TTL di 60 minuti.

### <a name="staging-area"></a>Area di gestione temporanea

Se si elaborano i dati in Azure Data Warehouse, è necessario scegliere un percorso di gestione temporanea per il caricamento in batch Polybase.

## <a name="parameterized-datasets"></a>Set di dati con parametri

Se si usa i set di dati con parametri, assicurarsi di impostare i valori dei parametri.

![Eseguire i parametri del flusso di dati](media/data-flow/params.png "parametri")

### <a name="debugging-parameterized-data-flows"></a>Debug dei flussi di dati con parametri

È possibile solo eseguire il debug di flussi di dati con set di dati con parametri di Debug di Pipeline di esecuzione usando l'attività flusso di dati execute. Le sessioni di debug interattivo nel flusso di dati di Azure Data factory attualmente non funzionano con i set di dati con parametri. Le esecuzioni di pipeline ed esecuzioni di debug funzionerà con i parametri.

Una procedura consigliata consiste nel compilare il flusso di dati con un set di dati statici in modo da poter propagazione di colonna di metadati completi disponibile in fase di progettazione. Sostituire quindi il set di dati statico con un set di dati con parametri dinamici quando si rende operativo il pipeline di flusso di dati.

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
