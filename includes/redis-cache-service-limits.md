---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238643"
---
| Resource | Limite |
| --- | --- |
| Dimensioni cache |530 GB |
| Database |64 |
| Numero massimo di client connesso |40.000 |
| Cache di Azure per le repliche di Redis, per la disponibilità elevata |1 |
| Partizioni in una cache premium con il clustering |10 |

I limiti e le dimensioni di Cache Redis di Azure variano per ogni piano tariffario. Per visualizzare i piani tariffari e le relative dimensioni, vedere [Cache di Azure per Redis prezzi](https://azure.microsoft.com/pricing/details/cache/).

Per altre informazioni sui limiti di configurazione di Cache Redis di Azure, vedere [Configurazione predefinita del server Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Poiché la configurazione e la gestione delle istanze di Cache Redis di Azure vengono eseguite da Microsoft, non tutti i comandi di Redis sono supportati in Cache Redis di Azure. Per altre informazioni, vedere [Comandi di Redis non supportati in Cache Redis di Azure](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

