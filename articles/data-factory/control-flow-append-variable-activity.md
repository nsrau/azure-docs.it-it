---
title: Attività di accodamento a variabile in Azure Data Factory | Microsoft Docs
description: Informazioni su come impostare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: e904075908fe7108c0566856b25fe03be0b7fd86
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023808"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Attività di accodamento a variabile in Azure Data Factory

Usare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | DESCRIZIONE | Obbligatoria
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | Yes
description | Testo che descrive l'attività | no
type | Il tipo di attività è AppendVariable | Sì
value | Valore letterale stringa o valore di oggetto espressione da accodare a una variabile specificata | Sì
variableName | Nome della variabile che verrà modificata dall'attività, la variabile deve essere di tipo "Array" | Sì

## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Attività di impostazione della variabile](control-flow-set-variable-activity.md)
