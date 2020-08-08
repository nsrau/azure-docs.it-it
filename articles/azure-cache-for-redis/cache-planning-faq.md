---
title: Domande frequenti sulla pianificazione di cache di Azure per Redis
description: Informazioni sulle risposte alle domande comuni che consentono di pianificare la cache di Azure per Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 13ba529dd3067ae16167f0d9c14c8f72b982f52c
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010841"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Domande frequenti sulla pianificazione di cache di Azure per Redis

Questo articolo fornisce le risposte alle domande comuni su come pianificare la cache di Azure per Redis.

## <a name="common-questions-and-answers"></a>Domande e risposte comuni
In questa sezione vengono illustrate le domande frequenti seguenti:

* [Prestazioni di Cache Redis di Azure](#azure-cache-for-redis-performance)
* [In quale area è consigliabile posizionare la cache?](#in-what-region-should-i-locate-my-cache)
* [Dove si trovano i dati memorizzati nella cache?](#where-do-my-cached-data-reside)
* [Quali sono i costi addebitati per Cache Redis di Azure?](#how-am-i-billed-for-azure-cache-for-redis)
* [È possibile usare cache di Azure per Redis con Azure Government cloud, Azure China 21Vianet cloud o Microsoft Azure Germania?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Prestazioni di Cache Redis di Azure
La tabella seguente mostra i valori massimi per la larghezza di banda osservati durante il test di diverse dimensioni di cache Standard e Premium usando `redis-benchmark.exe` da una macchina virtuale IaaS nell'endpoint di Cache Redis di Azure. Per la velocità effettiva TLS, si usa redis-benchmark con stunnel per connettersi all'endpoint di Cache Redis di Azure.

>[!NOTE] 
>Questi valori non sono garantiti e che non è disponibile alcun contratto di servizio per questi numeri, che dovrebbero essere tuttavia tipici. È consigliabile testare l'applicazione per determinare le dimensioni di cache ottimali per l'applicazione specifica.
>Questi numeri possono cambiare man mano che si pubblicano periodicamente risultati più recenti.
>

Da questa tabella è possibile trarre le seguenti conclusioni:

* La velocità effettiva per cache con dimensioni simili è superiore nel Premium rispetto al livello Standard. Ad esempio, con una cache di 6 GB, la velocità effettiva di P1 è 180.000 richieste al secondo (RPS) rispetto a 100.000 RPS per C3.
* Con il clustering di Redis, la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni (nodi) nel cluster. Ad esempio, se si crea un cluster P4 di 10 partizioni, la velocità effettiva disponibile sarà 400.000 * 10 = 4 milioni di RPS.
* La velocità effettiva per dimensioni maggiori di chiave è superiore nel Premium rispetto al livello Standard.

| Piano tariffario | Dimensione | Core CPU | Larghezza di banda disponibile | Dimensioni del valore di 1 KB | Dimensioni del valore di 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Dimensioni della cache livello Standard** | | |**Megabit al secondo (Mb/s) / Megabyte al secondo (MB/s)** |**Richieste al secondo (RPS) non SSL** |**Richieste al secondo (RPS) SSL** |
| C0 | 250 MB | Condiviso | 100 / 12,5  |  15.000 |   7\.500 |
| C1 |   1 GB | 1      | 500 / 62,5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500 / 62,5  |  41.000 |  37.000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100,000 |  90.000 |
| C4 |  13 GB | 2      | 500 / 62,5  |  60.000 |  55.000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126.000 | 120.000 |
| **Dimensioni della cache livello Premium** | |**Core CPU per partizione** | **Megabit al secondo (Mb/s) / Megabyte al secondo (MB/s)** |**Richieste al secondo (RPS) non SSL, per partizione** |**Richieste al secondo (RPS) SSL, per partizione** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172.000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400.000 | 373.000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400.000 | 373.000 |

Per istruzioni sulla configurazione di stunnel o sul download degli strumenti Redis, ad esempio `redis-benchmark.exe` , vedere [come è possibile eseguire i comandi di redis?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>In quale area è consigliabile posizionare la cache?
Per ottenere le prestazioni migliori e la latenza più bassa, Cache Redis di Azure deve trovarsi nella stessa area in cui si trova l'applicazione client della cache.

### <a name="where-do-my-cached-data-reside"></a>Dove si trovano i dati memorizzati nella cache?
Cache di Azure per Redis archivia i dati dell'applicazione nella RAM della VM o delle VM, a seconda del livello, che ospita la cache. I dati risiedono esclusivamente nell'area di Azure selezionata per impostazione predefinita. Esistono due casi in cui i dati possono lasciare un'area:
* Quando si Abilita la persistenza nella cache, cache di Azure per Redis eseguirà il backup dei dati in un account di archiviazione di Azure di cui si è proprietari. Se l'account di archiviazione fornito si trova in un'altra area, verrà eseguita una copia dei dati.
* Se si configura la replica geografica e la cache secondaria si trova in un'area diversa, come avviene normalmente, i dati verranno replicati in tale area.

Per usare queste funzionalità, è necessario configurare in modo esplicito cache di Azure per Redis. Si ha anche il controllo completo sull'area in cui si trova l'account di archiviazione o la cache secondaria.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Quali sono i costi addebitati per Cache Redis di Azure?
I prezzi di Cache Redis di Azure sono elencati in [questa pagina](https://azure.microsoft.com/pricing/details/cache/) e indicati La pagina dei prezzi elenca i prezzi come tariffa oraria e mensile. Le cache vengono fatturate a minuto dal momento in cui la cache viene creata fino a quando viene eliminata. Non è disponibile alcuna opzione per interrompere o sospendere la fatturazione di una cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>È possibile usare cache di Azure per Redis con Azure Government cloud, Azure China 21Vianet cloud o Microsoft Azure Germania?
Sì, è possibile usare Cache Redis di Azure con il cloud Azure per enti pubblici, il cloud di Azure Cina e Microsoft Azure Germania. Gli URL per l'accesso e la gestione di Cache Redis di Azure in questi cloud sono diversi da quelli del cloud pubblico di Azure.

| Cloud   | Suffisso DNS per Redis            |
|---------|---------------------------------|
| Pubblico  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Germania | *.redis.cache.cloudapi.de       |
| Cina   | *.redis.cache.chinacloudapi.cn  |

Per altre informazioni sulle considerazioni relative all'uso di Cache Redis di Azure con altri cloud, vedere i collegamenti seguenti.

- [Database di Azure per enti pubblici - Cache Redis di Azure](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Cloud di Azure Cina - Cache Redis di Azure](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germania](https://azure.microsoft.com/overview/clouds/germany/)

Per informazioni sull'uso di Cache Redis di Azure con PowerShell nel cloud di Azure per enti pubblici, nel cloud di Azure Cina e in Microsoft Azure Germania, vedere [Come connettersi ad altri cloud - PowerShell con Cache Redis di Azure](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre [domande frequenti su cache di Azure per Redis](cache-faq.md).
