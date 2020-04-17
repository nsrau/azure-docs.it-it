---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539528"
---
È ora possibile utilizzare [gli endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale all'insieme di credenziali di Servizi di ripristino. L'endpoint privato utilizza un indirizzo IP dello spazio di indirizzi VNET per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali passa attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. Questo elimina l'esposizione da internet pubblico. Gli endpoint privati possono essere usati per eseguire il backup e il ripristino dei database SQL e SAP HANA eseguiti all'interno delle macchine virtuali di Azure.Private Endpoints can be used for backing up and restoring your SQL and SAP HANA databases that run inside your Azure VMs. Può essere utilizzato anche per i server locali utilizzando l'agente MARS.

Il backup delle macchine virtuali di Azure non richiede la connettività Internet e pertanto non richiede endpoint privati per consentire l'isolamento della rete.

>[!NOTE]
> Questa funzionalità è attualmente a disponibilità limitata ed è disponibile negli Stati Uniti centrali occidentali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2 e Stati Uniti orientali (disponibilità in altre aree di Azure da seguire). Compilare [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e azbackupnetsec@microsoft.com inviarci un messaggio di posta elettronica in caso di posta elettronica se si è interessati all'utilizzo di endpoint privati per Backup di Azure.Please fill out this survey and email us at if you are interested in using Private Endpoints for Azure Backup. La possibilità di usare questa funzionalità è soggetta all'approvazione del servizio Backup di Azure.The ability to use this feature is subject to approval from the Azure Backup service.
