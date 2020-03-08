---
title: Differenze tra API di gestione e API del servizio-Azure Batch | Microsoft Docs
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672767"
---
# <a name="service-level-and-management-level-apis"></a>API del livello di servizio e di gestione

Azure Batch dispone di due set di API, uno per il livello di servizio e uno per il livello di gestione. La denominazione è spesso simile, ma restituiscono risultati diversi. Se si desiderano i log attività, è necessario utilizzare le API di gestione. Le API del livello di servizio ignorano il livello di gestione delle risorse di Azure e non vengono registrate.


Gestione batch e il servizio batch sono entrambi dotati di API per il pool, ad esempio. 
- Questa API per eliminare il pool è destinata direttamente all'account batch: https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Questa API per eliminare https://docs.microsoft.com/rest/api/batchmanagement/pool/delete del pool è destinata al livello management.azure.com.

