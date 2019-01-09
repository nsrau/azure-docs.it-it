---
title: Informazioni su Cache Redis di Azure | Microsoft Docs
description: Informazioni su Cache Redis di Azure e sugli utilizzi più comuni.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: eab4365b6b86a82f29dbd1a79d4fda6dc9b30e0e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971325"
---
# <a name="what-is-azure-cache-for-redis"></a>Informazioni su Cache Redis di Azure

Cache Redis di Azure si basa sul noto software [Redis](https://redis.io/). In genere viene usato come cache per aumentare le prestazioni e la scalabilità dei sistemi che si basano su archivi dati back-end. Le prestazioni vengono aumentate copiando temporaneamente i dati usati di frequente all'interno di una risorsa di archiviazione rapida vicina all'applicazione. Con [Cache Redis di Azure ](https://redis.io/), questa risorsa di archiviazione rapida è situata all'interno della memoria invece di essere caricata dal disco da un database.

Cache Redis di Azure può essere usato anche come archivio della struttura dei dati interno alla memoria, database non relazionale distribuito e broker di messaggi. Le prestazioni dell'applicazione vengono aumentate grazie alle prestazioni a bassa latenza ed elevata velocità effettiva del motore Redis.

Cache Redis di Azure consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft, ospitata in Azure e accessibile dall'interno o dall'esterno di Azure.

## <a name="why-use-azure-cache-for-redis"></a>Perché usare Cache Redis di Azure

Esistono numerosi modelli comuni in cui viene usato Cache Redis di Azure per supportare l'architettura dell'applicazione o per aumentare le prestazioni dell'applicazione. Alcuni dei più comuni sono:

| Modello      | Descrizione                                        |
| ------------ | -------------------------------------------------- |
| [Cache-aside](cache-web-app-cache-aside-leaderboard.md) | Dal momento che un database può essere di dimensioni elevate, il caricamento di un intero database in una cache non è un approccio consigliato. È comune usare il modello [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) per caricare gli elementi di dati nella cache solo in base alle esigenze. Quando il sistema apporta modifiche ai dati di back-end, può anche aggiornare la cache, che viene distribuita con altri client. Inoltre, il sistema può impostare una scadenza per gli elementi di dati oppure usare criteri di rimozione per fare in modo che gli aggiornamenti di dati vengano ricaricati nella cache.|
| [Memorizzazione di contenuti nella cache](cache-aspnet-output-cache-provider.md) | La maggior parte delle pagine Web viene generata da modelli con intestazioni, piè di pagina, barre degli strumenti, menu e così via. In realtà queste pagine non cambiano spesso e non devono essere generate in modo dinamico. L'uso di una cache interna alla memoria, come Cache Redis di Azure, fornirà ai server Web l'accesso rapido a questo tipo di contenuti statici rispetto agli archivi dati back-end. Questo modello riduce il tempo di elaborazione e il carico del server che sarebbero necessari per generare i contenuti in modo dinamico. Ciò consente una maggiore reattività dei server Web e può consentire di ridurre il numero di server necessari per gestire i carichi. Cache Redis di Azure fornisce il provider di cache di output per Redis per supportare questo modello con ASP.NET.|
| [Usare la memorizzazione nella cache di una sessione](cache-aspnet-session-state-provider.md) | Questo modello viene comunemente usato con i carrelli della spesa e altre informazioni sui tipi di cronologia utente che un'applicazione Web potrebbe voler associare ai cookie dell'utente. L'archiviazione di troppi dati in un cookie può avere un impatto negativo sulle prestazioni poiché le dimensioni dei cookie aumentano, vengono superate e convalidate con ogni richiesta. Una tipica soluzione consiste nell'usare il cookie come chiave per eseguire query sui dati in un database back-end. L'uso di una cache interna alla memoria, come Cache Redis di Azure, per associare informazioni a un utente risulta molto più rapido rispetto all'interazione con un database relazionale completo. |
| Accodamento di messaggi e processi | Quando le applicazioni ricevono richieste, spesso l'esecuzione di operazioni associate alla richiesta impiega più tempo. Si tratta di un modello comune per rinviare le operazioni la cui esecuzione richiede più tempo aggiungendole a una coda, che verrà elaborata in un secondo momento e forse da un altro server. Questo metodo di rinviare il lavoro viene detto accodamento delle attività. Esistono molti componenti software progettati per supportare le code di attività. Cache Redis di Azure viene usato anche a questo scopo come coda distribuita.|
| Transazioni distribuite | Un requisito comune per le applicazioni è essere in grado di eseguire una serie di comandi in un archivio di dati back-end come operazione singola (atomica). Tutti i comandi devono avere esito positivo oppure è necessario eseguire il rollback allo stato iniziale. Cache Redis di Azure supporta l'esecuzione di un batch di comandi come singola operazione sotto forma di [transazioni](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Offerte disponibili per Cache Redis di Azure

Cache Redis di Azure è disponibile nei livelli seguenti:

| Livello | Descrizione |
|---|---|
Basic | Una cache con un nodo singolo. Questo livello supporta varie dimensioni di memoria (250 MB - 53 GB). È ideale per sviluppo/test e carichi di lavoro non critici. Il livello Basic non dispone di alcun contratto di servizio |
| Standard | Una cache replicata in una configurazione a due nodi primario/secondario gestita da Microsoft, con un contratto di servizio che garantisce disponibilità elevata (99,9%) |
| Premium | Il livello Premium è pronto per le aziende. Le cache di livello Premium supportano più funzionalità e hanno una velocità effettiva superiore con latenze inferiori. Le cache di livello Premium sono distribuite su un hardware più potente e che offre prestazioni migliori rispetto al livello Standard o Basic. Questo vantaggio significa che la velocità effettiva per una cache delle stesse dimensioni sarà superiore nel livello Premium rispetto al livello Standard |

> [!TIP]
> Per altre informazioni su dimensioni, velocità effettiva e larghezza di banda con le cache Premium, vedere [Domande frequenti su Cache Redis di Azure](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

È possibile aumentare le prestazioni della cache fino a un livello superiore, a patto che sia già stato creato. Il passaggio a un piano inferiore non è supportato. Per istruzioni dettagliate sul ridimensionamento, vedere [Come ridimensionare Cache Redis di Azure](cache-how-to-scale.md) e [Come automatizzare un'operazione di ridimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Confronto delle funzionalità

Nella pagina [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/) è presente un confronto dettagliato dei livelli disponibili. La tabella seguente descrive alcune delle funzionalità supportate in base al livello:

| Descrizione della funzionalità | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistenza dei dati Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Sicurezza tramite le regole del firewall](cache-configure.md#firewall) |✔|✔|✔|
| [Isolamento e protezione avanzata con rete virtuale](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importazione/Esportazione](cache-how-to-import-export-data.md) |✔|-|-|
| [Pianificare gli aggiornamenti](cache-administration.md#schedule-updates) |✔|-|-|
| [Replica geografica](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva all'app Web ASP.NET](cache-web-app-howto.md): creare una semplice app Web ASP.NET che usa Cache Redis di Azure.
* [Guida introduttiva a .NET](cache-dotnet-how-to-use-azure-redis-cache.md): creare un'app .NET che usa Cache Redis di Azure.
* [Guida introduttiva a .NET Core](cache-dotnet-core-quickstart.md): creare un'app .NET Core che usa Cache Redis di Azure.
* [Guida introduttiva a Node.js](cache-nodejs-get-started.md): creare una semplice app Node.js che usa Cache Redis di Azure.
* [Guida introduttiva a Java](cache-java-get-started.md): creare una semplice app Java che usa Cache Redis di Azure.
* [Guida introduttiva a Python](cache-python-get-started.md): creare un'app Python che usa Cache Redis di Azure.
