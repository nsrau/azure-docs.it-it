---
title: Attività di accodamento a variabile in Azure Data Factory
description: Informazioni su come impostare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679984"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Attività di accodamento a variabile in Azure Data Factory

Usare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | Sì
description | Testo che descrive l'attività | no
type | Il tipo di attività è AppendVariable | Sì
value | Valore letterale stringa o valore di oggetto espressione da accodare a una variabile specificata | Sì
variableName | Nome della variabile che verrà modificata dall'attività, la variabile deve essere di tipo "Array" | Sì

## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Attività di impostazione della variabile](control-flow-set-variable-activity.md)
