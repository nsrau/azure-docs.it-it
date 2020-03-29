---
title: Differenze tra le API di gestione e le API del servizio - Azure Batch Documenti Microsoft
description: Le API funzionano sui diversi livelli del servizio Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672767"
---
# <a name="service-level-and-management-level-apis"></a>API a livello di servizio e di gestioneService level and management level APIs

Azure Batch include due set di API, uno per il livello di servizio e uno per il livello di gestione. La denominazione è spesso simile, ma restituisce risultati diversi. Se si desidera che i log attività, è necessario usare le API di gestione. Le API del livello di servizio ignorano il livello di gestione delle risorse di Azure e non vengono registrate.


La gestione batch e il servizio Batch dispongono entrambi di API per il pool, ad esempio. 
- Questa API per eliminare il pool è destinata direttamente all'account batch:This API to delete pool is targeted directly on the batch account:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Questa API per https://docs.microsoft.com/rest/api/batchmanagement/pool/delete eliminare il pool è destinata al livello management.azure.com.

