---
title: Attività di impostazione della variabile in Azure Data Factory
description: Informazioni su come usare l'attività di impostazione della variabile per configurare il valore di una variabile esistente definita in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930652"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Attività di impostazione della variabile in Azure Data Factory

Usare l'attività di impostazione della variabile per impostare il valore di una variabile esistente di tipo String, Bool o Array definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Obbligatoria
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | Sì
description | Testo che descrive l'attività | no
type | Il tipo di attività è SetVariable | sì
value | Valore letterale stringa o valore di oggetto espressione usato per impostare la variabile specificata | sì
variableName | Nome della variabile che verrà impostata da questa attività | sì


## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Aggiungere un'attività variabile](control-flow-append-variable-activity.md)
