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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930652"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Attività di impostazione della variabile in Azure Data Factory

Usare l'attività di impostazione della variabile per impostare il valore di una variabile esistente di tipo String, Bool o Array definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Description | Obbligatoria
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | SÌ
description | Testo che descrive l'attività | no
type | Il tipo di attività è SetVariable | Sì
value | Valore letterale stringa o valore di oggetto espressione usato per impostare la variabile specificata | Sì
variableName | Nome della variabile che verrà impostata da questa attività | Sì


## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Attività di accodamento a variabile](control-flow-append-variable-activity.md)
