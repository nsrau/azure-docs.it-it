---
title: Attività di convalida in Azure Data Factory | Microsoft Docs
description: L'attività di convalida non potrà continua l'esecuzione della pipeline fino a quando non viene convalidato il set di dati collegato a determinati criteri, che l'utente specifica.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523027"
---
# <a name="validation-activity-in-azure-data-factory"></a>Attività di convalida in Azure Data Factory
È possibile utilizzare una convalida in una pipeline per assicurarsi che la pipeline continui l'esecuzione solo dopo aver convalidato l'oggetto associato è presente il riferimento di set di dati, che sia conforme ai criteri specificati o viene raggiunto timeout.


## <a name="syntax"></a>Sintassi

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Proprietà del tipo

Proprietà | DESCRIZIONE | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
name | Nome dell'attività "Convalida" | string | Sì |
type | Deve essere impostata su **convalida**. | string | Sì |
dataset | Attività verranno di bloccare l'esecuzione fino a quando la convalida il riferimento di set di dati esista e che soddisfa i criteri specificati, o viene raggiunto timeout. Set di dati fornito deve supportare la proprietà "MinimumSize" o "ChildItems". | Riferimento di set di dati | Sì |
timeout | Specifica il timeout per l'attività da eseguire. Se viene specificato alcun valore, valore predefinito è 7 giorni ("7.00:00:00"). Formato è d.hh:mm:ss | string | No  |
Modalità di sospensione | Un ritardo in secondi tra i tentativi di convalida. Se viene specificato alcun valore, valore predefinito è 10 secondi. | Integer | No  |
childItems | Controlla se la cartella contiene gli elementi figlio. Può essere impostata a true: Verificare che la cartella esista e che disponga di elementi. Blocca fino a quando non è presente nella cartella almeno un elemento o viene raggiunto il valore di timeout.-false: Verificare che la cartella esista e che è vuota. Si blocca fino a cartella è vuota o fino al timeout di valore viene raggiunto. Se viene specificato alcun valore, attività si bloccherà fino a quando la cartella esista o fino a quando non viene raggiunto il timeout. | Boolean | No  |
minimumSize | Dimensioni minime di un file in byte. Se viene specificato alcun valore, valore predefinito è pari a 0 byte | Integer | No  |


## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
