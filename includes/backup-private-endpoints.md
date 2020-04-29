---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539528"
---
È ora possibile usare [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale all'insieme di credenziali dei servizi di ripristino. L'endpoint privato usa un IP dallo spazio di indirizzi della VNET per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali si sposta attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. In questo modo si elimina l'esposizione dalla rete Internet pubblica. Gli endpoint privati possono essere usati per eseguire il backup e il ripristino dei database SQL e SAP HANA eseguiti nelle macchine virtuali di Azure. Può essere usato anche per i server locali con l'agente MARS.

Il backup di macchine virtuali di Azure non richiede la connettività Internet e pertanto non richiede endpoint privati per consentire l'isolamento rete.

>[!NOTE]
> Questa funzionalità è attualmente in disponibilità limitata ed è disponibile nelle aree Stati Uniti centro-occidentali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2 e Stati Uniti orientali (disponibilità in altre aree di Azure da seguire). Compilare [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e inviare un messaggio di posta azbackupnetsec@microsoft.com elettronica all'indirizzo se si è interessati all'uso di endpoint privati per backup di Azure. La possibilità di usare questa funzionalità è soggetta all'approvazione del servizio backup di Azure.
