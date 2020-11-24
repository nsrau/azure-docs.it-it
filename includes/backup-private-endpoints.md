---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561566"
---
È ora possibile usare gli [endpoint privati](../articles/private-link/private-endpoint-overview.md) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale nell'insieme di credenziali di Servizi di ripristino. L'endpoint privato usa un indirizzo IP dallo spazio indirizzi della rete virtuale per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali si sposta attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. In questo modo si elimina l'esposizione dalla rete Internet pubblica. È possibile usare gli endpoint privati per eseguire il backup e il ripristino dei database SQL e SAP HANA in esecuzione nelle macchine virtuali di Azure. Possono anche essere usati per i server locali che usano l'agente MARS.

Il backup di macchine virtuali di Azure non richiede la connettività Internet e pertanto non richiede agli endpoint privati di consentire l'isolamento della rete.

Altre informazioni sugli endpoint privati per Backup di Azure sono disponibili [qui](../articles/backup/private-endpoints.md).