---
title: Attività di impostazione della variabile in Azure Data Factory
description: Informazioni su come usare l'attività di impostazione della variabile per configurare il valore di una variabile esistente definita in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879267"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Impostare l'attività delle variabili in Azure Data FactorySet variable activity in Azure Data Factory

Usare l'attività di impostazione della variabile per impostare il valore di una variabile esistente di tipo String, Bool o Array definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Obbligatoria
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | sì
description | Testo che descrive l'attività | no
type | Deve essere impostato su **SetVariable** | sì
Valore | Valore letterale stringa o oggetto espressione a cui verrà assegnata la variabile | sì
variableName | Nome della variabile che verrà impostata da questa attività | sì

## <a name="incrementing-a-variable"></a>Incremento di una variabile

Uno scenario comune che coinvolge variabili in Azure Data Factory usa una variabile come iteratore all'interno di un'attività fino a quando o foreach. In un'attività di impostazione variabile non `value` è possibile fare riferimento alla variabile impostata nel campo. Per risolvere questa limitazione, impostare una variabile temporanea e quindi creare una seconda attività di variabile impostata. La seconda attività della variabile set imposta il valore dell'iteratore sulla variabile temporanea. 

Di seguito è riportato un esempio di questo modello:Below is an example of this pattern:

![Incrementare una variabile](media/control-flow-set-variable-activity/increment-variable.png "Incrementare una variabile")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Aggiungere un'attività variabile](control-flow-append-variable-activity.md)
