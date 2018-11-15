---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 01d6d933474d4a9c1e428b8df89fc766c9b40f20
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572284"
---
| Risorsa | Limite |
| --- | --- |
| Dimensioni cache |530 GB |
| Database |64 |
| Numero massimo di client connessi |40.000 |
| Repliche di Cache Redis (per la disponibilità elevata) |1 |
| Partizioni in una cache premium con il clustering |10 |

I limiti e le dimensioni di Cache Redis di Azure variano per ciascun livello di prezzo. Per visualizzare i livelli di prezzo e le relative dimensioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).

Per altre informazioni sui limiti di configurazione di Cache Redis di Azure, vedere [Configurazione predefinita del server Redis](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

Poiché la configurazione e gestione delle istanze di Cache Redis di Azure viene eseguita tramite Microsoft, non tutti i comandi Redis sono supportati nella Cache Redis di Azure. Per altre informazioni, vedere [Comandi di Redis non supportati in Cache Redis di Azure](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache).

