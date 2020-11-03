---
title: Modifiche importanti previste per il Centro sicurezza di Azure
description: Prossime modifiche al Centro sicurezza di Azure che può essere opportuno conoscere e per le quali potrebbe essere necessaria una pianificazione
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: ba0c68589f7ea4b32970e3ad81fea5175b998dc7
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629154"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Raccomandazioni relative ad Azure Security Benchmark da aggiungere (anteprima)

| Aspetto | Dettagli |
|---------|---------|
|Data dell'annuncio | 26 ottobre 2020  |
|Data della modifica  |  Novembre 2020 |
|Impatto     | Potenzialmente, più raccomandazioni da esaminare.<br>Nessun impatto immediato sul punteggio di sicurezza - Le raccomandazioni in anteprima non influiscono sul punteggio di sicurezza.<br>Nessun impatto immediato sullo stato di integrità delle risorse - Le raccomandazioni in anteprima non contrassegnano una risorsa come "Non integra".|
|  |  |

Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md).

Le 29 nuove raccomandazioni seguenti verranno aggiunte al Centro sicurezza per aumentare la copertura del benchmark.

Le raccomandazioni in anteprima non contrassegnano una risorsa come non integra e non sono incluse nei calcoli del punteggio di sicurezza. Correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima. Per altre informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

- La soluzione Backup di Azure deve essere abilitata per le macchine virtuali
- La conservazione dei controlli per i server SQL deve essere impostata su almeno 90 giorni
- I log di diagnostica devono essere abilitati in Servizio app 
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL
- L'app per le API deve usare FTPS
- L'app per le funzioni deve usare FTPS
- L'app Web deve usare FTPS
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL
- Java deve essere aggiornato all'ultima versione per l'app per le API
- Java deve essere aggiornato all'ultima versione per l'app per le funzioni
- Java deve essere aggiornato all'ultima versione per l'app Web
- Nell'app per le API è necessario usare un'identità gestita
- Nell'app per le funzioni è necessario usare un'identità gestita
- Nell'app Web è necessario usare un'identità gestita
- PHP deve essere aggiornato all'ultima versione per l'app per le API
- PHP deve essere aggiornato all'ultima versione per l'app Web
- L'endpoint privato deve essere abilitato per i server MariaDB
- L'endpoint privato deve essere abilitato per i server MySQL
- L'endpoint privato deve essere abilitato per i server PostgreSQL
- Python deve essere aggiornato all'ultima versione per l'app per le API
- Python deve essere aggiornato all'ultima versione per l'app per le funzioni
- Python deve essere aggiornato all'ultima versione per l'app Web
- TLS deve essere aggiornato all'ultima versione per l'app per le API
- TLS deve essere aggiornato all'ultima versione per l'app per le funzioni
- TLS deve essere aggiornato all'ultima versione per l'app Web
- Le app Web devono richiedere un certificato SSL per tutte le richieste in ingresso

Collegamenti correlati:

- [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Altre informazioni sulle app per le API di Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Altre informazioni sulle app per le funzioni di Azure](../azure-functions/functions-overview.md)
- [Altre informazioni sulle app Web di Azure](../app-service/overview.md)
- [Altre informazioni su Database di Azure per MariaDB](../mariadb/overview.md)
- [Altre informazioni su Database di Azure per MySQL](../mysql/overview.md)
- [Altre informazioni su Database di Azure per PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Passaggi successivi

Per tutte le modifiche recenti al prodotto, vedere [Novità del Centro sicurezza di Azure](release-notes.md).