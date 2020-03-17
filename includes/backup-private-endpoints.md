---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137025"
---
È ora possibile usare gli [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale nell'insieme di credenziali di Servizi di ripristino. L'endpoint privato usa un indirizzo IP dallo spazio indirizzi della rete virtuale per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali si sposta attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. In questo modo si elimina l'esposizione dalla rete Internet pubblica. È possibile usare gli endpoint privati per eseguire il backup e il ripristino dei database SQL e SAP HANA in esecuzione nelle macchine virtuali di Azure. Possono anche essere usati per i server locali che usano l'agente MARS.

Il backup di macchine virtuali di Azure non richiede la connettività Internet e pertanto non richiede agli endpoint privati di consentire l'isolamento della rete.

>[!NOTE]
> Questa funzionalità è attualmente nelle prime fasi di utilizzo. Compilare [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e inviare un messaggio di posta elettronica all'indirizzoazbackupnetsec@microsoft.com se si è interessati a usare gli endpoint privati per Backup di Azure. La possibilità di usare questa funzionalità è soggetta all'approvazione da parte del servizio Backup di Azure.
