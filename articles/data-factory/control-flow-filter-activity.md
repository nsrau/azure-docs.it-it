---
title: Attività filtro in Azure Data Factory
description: L'attività filtro consente di filtrare gli input.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: 2c8ada22b4492bef0239b6f811d5a7bd58e58510
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417216"
---
# <a name="filter-activity-in-azure-data-factory"></a>Attività filtro in Azure Data Factory
È possibile usare un'attività filtro in una pipeline per applicare un'espressione filtro a una matrice di input. 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="syntax"></a>Sintassi

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività `Filter`. | string | Sì
type | Deve essere impostato su **filter**. | string | Sì
condizione | Condizione da usare per il filtraggio dell'input. | Expression | Sì
items | Matrice di input a cui deve essere applicato il filtro. | Expression | Sì

## <a name="example"></a>Esempio

In questo esempio, la pipeline contiene due attività: **Filter** e **ForEach**. L'attività filtro è configurata per filtrare la matrice di input per gli elementi con un valore maggiore di 3. L'attività ForEach scorre quindi i valori filtrati e imposta il **test** della variabile sul valore corrente.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Eseguire l'attività di pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
