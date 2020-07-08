---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659530"
---
È ora possibile usare gli [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale nell'insieme di credenziali di Servizi di ripristino. L'endpoint privato usa un indirizzo IP dallo spazio indirizzi della rete virtuale per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali si sposta attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. In questo modo si elimina l'esposizione dalla rete Internet pubblica. È possibile usare gli endpoint privati per eseguire il backup e il ripristino dei database SQL e SAP HANA in esecuzione nelle macchine virtuali di Azure. Possono anche essere usati per i server locali che usano l'agente MARS.

Il backup di macchine virtuali di Azure non richiede la connettività Internet e pertanto non richiede agli endpoint privati di consentire l'isolamento della rete.

Altre informazioni sugli endpoint privati per Backup di Azure sono disponibili [qui](https://docs.microsoft.com/azure/backup/private-endpoints).
