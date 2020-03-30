---
title: Attività di convalida in Azure Data FactoryValidation activity in Azure Data Factory
description: L'attività di convalida non continua l'esecuzione della pipeline finché non convalida il set di dati associato con determinati criteri specificati dall'utente.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678357"
---
# <a name="validation-activity-in-azure-data-factory"></a>Attività di convalida in Azure Data FactoryValidation activity in Azure Data Factory
È possibile usare una convalida in una pipeline per garantire che la pipeline continui l'esecuzione solo dopo aver convalidato l'esiguo riferimento al set di dati associato, che soddisfi i criteri specificati o che sia stato raggiunto il timeout.


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

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività 'Convalida' | string | Sì |
type | Deve essere impostato su **Convalida**. | string | Sì |
dataset | L'attività bloccherà l'esecuzione fino a quando non viene convalidato l'esistente del riferimento al set di dati e che soddisfa i criteri specificati o non è stato raggiunto il timeout. Il set di dati fornito deve supportare la proprietà "MinimumSize" o "ChildItems". | Informazioni di riferimento sui set di dati | Sì |
timeout | Specifica il timeout per l'attività da eseguire. Se non viene specificato alcun valore, il valore predefinito è 7 giorni ("7.00:00:00"). Il formato è d.hh:mm:ss | string | No |
sleep | Ritardo in secondi tra i tentativi di convalida. Se non viene specificato alcun valore, il valore predefinito è 10 secondi. | Integer | No |
childItems | Controlla se la cartella contiene elementi figlio. Può essere impostato su-true: verificare che la cartella esista e che continda elementi. Blocchi fino a quando non è presente almeno un elemento nella cartella o viene raggiunto il valore di timeout.-false: verificare che la cartella esista e che sia vuota. Si blocca fino a quando la cartella non è vuota o fino a quando non viene raggiunto il valore di timeout. Se non viene specificato alcun valore, l'attività si bloccherà fino a quando la cartella non esiste o fino al raggiungito timeout. | Boolean | No |
Minimumsize | Dimensione minima di un file in byte. Se non viene specificato alcun valore, il valore predefinito è 0 byte | Integer | No |


## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività Esegui pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere l'attività dei metadatiGet Metadata Activity](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
