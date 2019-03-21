---
title: Attività di accodamento a variabile in Azure Data Factory | Microsoft Docs
description: Informazioni su come impostare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575155"
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
