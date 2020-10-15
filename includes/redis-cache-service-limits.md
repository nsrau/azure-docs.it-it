---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "71839210"
---
| Risorsa | Limite |
| --- | --- |
| Dimensioni cache |1,2 TB |
| Database |64 |
| Numero massimo di client connessi |40.000 |
| Repliche della cache di Azure per Redis, per disponibilità elevata |1 |
| Partizioni in una cache premium con il clustering |10 |

I limiti e le dimensioni di Cache Redis di Azure variano per ogni piano tariffario. Per visualizzare i piani tariffari e le dimensioni associate, vedere [Prezzi della cache di Azure per Redis](https://azure.microsoft.com/pricing/details/cache/).

Per altre informazioni sui limiti di configurazione di Cache Redis di Azure, vedere [Configurazione predefinita del server Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Poiché la configurazione e la gestione delle istanze di Cache Redis di Azure vengono eseguite da Microsoft, non tutti i comandi di Redis sono supportati in Cache Redis di Azure. Per altre informazioni, vedere [Comandi di Redis non supportati in Cache Redis di Azure](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

