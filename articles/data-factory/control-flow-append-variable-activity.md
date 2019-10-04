---
title: Attività di accodamento a variabile in Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: 24808c9ed4bda5e8d3b0ce23ab93bc59eb260374
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141733"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Attività di accodamento a variabile in Azure Data Factory

Usare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | Yes
description | Testo che descrive l'attività | no
type | Il tipo di attività è AppendVariable | sì
value | Valore letterale stringa o valore di oggetto espressione da accodare a una variabile specificata | sì
variableName | Nome della variabile che verrà modificata dall'attività, la variabile deve essere di tipo "Array" | sì

## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Attività di impostazione della variabile](control-flow-set-variable-activity.md)
