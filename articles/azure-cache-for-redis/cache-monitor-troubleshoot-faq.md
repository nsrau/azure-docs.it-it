---
title: Domande frequenti sulla risoluzione dei problemi e sul monitoraggio di cache di Azure per Redis
description: Informazioni sulle risposte alle domande comuni che consentono di monitorare e risolvere i problemi di cache di Azure per Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 2149d069ce9cb0b636e461d19963f413b162bc9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010857"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Domande frequenti sulla risoluzione dei problemi e sul monitoraggio di cache di Azure per Redis
Questo articolo fornisce le risposte alle domande comuni su come monitorare e risolvere i problemi relativi a cache di Azure per Redis.

## <a name="common-questions-and-answers"></a>Domande e risposte comuni
In questa sezione vengono illustrate le domande frequenti seguenti:

* [In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Perché vengono visualizzati timeout?](#why-am-i-seeing-timeouts)
* [Perché il client è stato disconnesso dalla cache?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?
Le istanze di Cache Redis di Microsoft Azure possono essere monitorate nel [portale di Azure](https://portal.azure.com). È possibile visualizzare le metriche, aggiungere i grafici delle metriche alla Schermata iniziale, personalizzare l'intervallo di data e ora per il monitoraggio dei grafici, aggiungere e rimuovere metriche dai grafici e impostare avvisi per le situazioni in cui vengono soddisfatte determinate condizioni. Per altre informazioni, vedere [How to monitor Azure Cache for Redis](cache-how-to-monitor.md) (Come monitorare Cache Redis di Azure).

Il **Menu risorse** di Cache Redis di Azure include inoltre numerosi strumenti per monitorare e risolvere i problemi delle cache.

* **Diagnostica e risoluzione dei problemi** fornisce informazioni sui problemi comuni e sulle strategie per risolverli.
* **Integrità risorsa** esamina la risorsa e indica se viene eseguita nel modo previsto. Per altre informazioni sul servizio Integrità risorse di Azure, vedere l'articolo sulla [Panoramica su Integrità risorse di Azure](../resource-health/resource-health-overview.md).
* **Nuova richiesta di supporto** fornisce opzioni per aprire una richiesta di supporto per la cache.

Questi strumenti consentono di monitorare l'integrità delle istanze di Cache Redis di Azure e aiutano a gestire le applicazioni di memorizzazione nella cache. Per altre informazioni, vedere la sezione "Supporto e impostazioni di risoluzione dei problemi" in [Come configurare Cache Redis di Azure](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Perché vengono visualizzati timeout?
I timeout si verificano nel client usato per comunicare con Redis. Quando un comando viene inviato al server Redis, il comando viene accodato e il server Redis preleva il comando e lo esegue. È tuttavia possibile che durante questo processo si verifichi il timeout del client e in tale caso viene generata un'eccezione sul lato del chiamante. Per altre informazioni sulla risoluzione dei problemi di timeout, vedere [Risoluzione dei problemi lato client](cache-troubleshoot-client.md) ed [Eccezioni di timeout StackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Perché il client è stato disconnesso dalla cache?
Di seguito sono riportati alcuni motivi comuni per la disconnessione di una cache.

* Cause lato client
  * L'applicazione client è stata ridistribuita.
  * L'applicazione client ha eseguito un'operazione di ridimensionamento.
    * Nel caso dei servizi cloud o delle app Web, ciò potrebbe essere dovuto alla scalabilità automatica.
  * Il livello di rete sul lato client è stato modificato.
  * Si sono verificati errori temporanei nel client o nei nodi di rete tra il client e il server.
  * Sono stati raggiunti i limiti della soglia relativa alla larghezza di banda.
  * Il completamento di operazioni associate alla CPU ha richiesto troppo tempo.
* Cause lato server
  * Nell'offerta di cache standard, il servizio cache di Azure per il servizio Redis ha avviato un failover dal nodo primario al nodo di replica.
  * Azure stava applicando patch all'istanza in cui è stata distribuita la cache.
    * Ciò può riguardare aggiornamenti del server Redis o manutenzione generale delle VM.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio e la risoluzione dei problemi relativi alle istanze di Cache Redis di Azure, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md) e le varie guide per la risoluzione dei problemi.

Informazioni sulle altre [domande frequenti su cache di Azure per Redis](cache-faq.md).
