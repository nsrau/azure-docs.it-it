---
title: Attività di impostazione della variabile in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'attività di impostazione della variabile per configurare il valore di una variabile esistente definita in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 71abfdff629f36b278488851b546c7371353a4d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60767966"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Attività di impostazione della variabile in Azure Data Factory

Usare l'attività di impostazione della variabile per impostare il valore di una variabile esistente di tipo String, Bool o Array definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | Yes
description | Testo che descrive l'attività | no
type | Il tipo di attività è SetVariable | sì
value | Valore letterale stringa o valore di oggetto espressione usato per impostare la variabile specificata | sì
variableName | Nome della variabile che verrà impostata da questa attività | sì


## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Attività di accodamento a variabile](control-flow-append-variable-activity.md)
