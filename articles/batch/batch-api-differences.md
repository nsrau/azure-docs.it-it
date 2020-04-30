---
title: Differenze tra API di gestione e API del servizio
description: Le API funzionano sui diversi livelli del servizio Azure Batch.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115398"
---
# <a name="service-level-and-management-level-apis"></a>API del livello di servizio e di gestione

Azure Batch dispone di due set di API, uno per il livello di servizio e uno per il livello di gestione. La denominazione è spesso simile, ma restituiscono risultati diversi. Se si desiderano i log attività, è necessario utilizzare le API di gestione. Le API del livello di servizio ignorano il livello di gestione delle risorse di Azure e non vengono registrate.


Gestione batch e il servizio batch sono entrambi dotati di API per il pool, ad esempio. 
- Questa API per eliminare il pool è destinata direttamente all'account batch:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Questa API per eliminare il https://docs.microsoft.com/rest/api/batchmanagement/pool/delete pool è destinata al livello Management.Azure.com.

