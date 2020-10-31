---
title: Offerta senza server basata sul consumo in Azure Cosmos DB
description: Scopri di più sull'offerta senza server basata sul consumo di Azure Cosmos DB.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 75d22a5021c7c8ae3a12f25644f2875e0ccf8cdd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098757"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB senza server (anteprima)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT]
> Azure Cosmos DB senza server è attualmente in fase di anteprima. Questa versione di anteprima viene fornita senza una Contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere le [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB senza server consente di usare l'account Azure Cosmos in modo basato sul consumo, in cui vengono addebitate solo le unità di richiesta utilizzate dalle operazioni del database e la risorsa di archiviazione utilizzata dai dati. Non è previsto alcun addebito minimo quando si usa Azure Cosmos DB in modalità senza server.

> [!IMPORTANT] 
> Ci sono commenti e suggerimenti su senza server? Vogliamo sentirci! È possibile rilasciare un messaggio al team Azure Cosmos DB senza server: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Quando si utilizza Azure Cosmos DB, tutte le operazioni di database hanno un costo espresso in [unità richiesta](request-units.md). Il modo in cui viene addebitato questo costo dipende dal tipo di account Azure Cosmos usato:

- In modalità di [velocità effettiva con provisioning](set-throughput.md) , è necessario eseguire il commit di una determinata quantità di velocità effettiva (espressa in unità richiesta al secondo) di cui viene eseguito il provisioning nei database e nei contenitori. Il costo delle operazioni del database viene quindi detratto dal numero di unità richiesta disponibili ogni secondo. Alla fine del periodo di fatturazione, viene addebitata la quantità di velocità effettiva di cui è stato effettuato il provisioning.
- In modalità senza server non è necessario effettuare il provisioning di una velocità effettiva durante la creazione di contenitori nell'account Azure Cosmos. Alla fine del periodo di fatturazione, viene addebitato il numero di unità richiesta utilizzate dalle operazioni del database.

## <a name="use-cases"></a>Casi d'uso

Azure Cosmos DB gli scenari più adatti per i server in cui ci si aspetta:

- **Traffico leggero** : poiché la capacità di provisioning in tali situazioni non è necessaria e può essere dispendiosa in termini di costi
- Velocità di espansione **moderata** : poiché i contenitori senza server possono fornire fino a 5.000 unità richiesta al secondo
- **Prestazioni moderate** : poiché i contenitori senza server hanno [caratteristiche specifiche delle prestazioni](#performance)

Per questi motivi, è necessario prendere in considerazione Azure Cosmos DB senza server per i seguenti tipi di carico di lavoro:

- Sviluppo
- Test
- Creazione di prototipi
- Modello di verifica
- Applicazione non critica con traffico leggero

Per altre indicazioni su come scegliere l'offerta più adatta ai casi d'uso, vedere l'articolo [come scegliere tra velocità effettiva con provisioning e senza server](throughput-serverless.md) .

## <a name="using-serverless-resources"></a>Uso di risorse senza server

Senza server è un nuovo tipo di account Azure Cosmos, il che significa che è necessario scegliere tra la **velocità effettiva con provisioning** e senza **Server** quando si crea un nuovo account. Per iniziare a usare senza server, è necessario creare un nuovo account senza server. Durante la versione di anteprima, l'unico modo supportato per creare un nuovo account senza server consiste nell' [usare la portale di Azure](create-cosmosdb-resources-portal.md). La migrazione degli account esistenti da e verso la modalità senza server non è attualmente supportata.

> [!NOTE]
> Il server non è attualmente supportato nell'API Azure Cosmos DB Core (SQL).

Qualsiasi contenitore creato in un account senza server è un contenitore senza server. I contenitori senza server espongono le stesse funzionalità dei contenitori creati in modalità di velocità effettiva con provisioning, in modo da leggere, scrivere ed eseguire query sui dati esattamente allo stesso modo. Tuttavia, anche gli account senza server e i contenitori hanno caratteristiche specifiche:

> [!IMPORTANT]
> Alcune di queste limitazioni possono essere attenuate o rimosse quando il server diventa disponibile a livello generale e **i commenti e suggerimenti** ci aiuteranno a decidere. Scopri di più sull'esperienza senza server: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Un account senza server può essere eseguito solo in una singola area di Azure. Non è possibile aggiungere altre aree di Azure a un account senza server dopo averlo creato.
- Non è possibile abilitare la funzionalità di [anteprima del collegamento sinapsi](synapse-link.md) in un account senza server.
- La velocità effettiva del provisioning non è necessaria per i contenitori senza server, pertanto sono applicabili le seguenti istruzioni:
    - Non è possibile passare una velocità effettiva quando si crea un contenitore senza server e in questo modo viene restituito un errore.
    - Non è possibile leggere o aggiornare la velocità effettiva in un contenitore senza server e in questo modo viene restituito un errore.
    - Non è possibile creare un database di velocità effettiva condivisa in un account senza server e in questo modo viene restituito un errore.
- I contenitori senza server possono fornire un'espansione massima della velocità effettiva di 5.000 unità richiesta al secondo.
- I contenitori senza server possono archiviare un massimo di 50 GB di dati e indici.

## <a name="monitoring-your-consumption"></a>Monitoraggio del consumo

Se in precedenza è stata Azure Cosmos DB usata la modalità di velocità effettiva con provisioning, si noterà che il server non è più conveniente quando il traffico non giustifica la capacità con provisioning. Il compromesso consiste nel fatto che i costi diventeranno meno prevedibili perché vengono addebitati in base al numero di richieste elaborate dal database. Per questo motivo, è importante tenere sotto controllo il consumo corrente.

Quando si Esplora il riquadro **metriche** dell'account, si troverà un grafico denominato **unità richiesta utilizzate** nella scheda **Panoramica** . Questo grafico mostra il numero di unità richiesta utilizzate dall'account:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Grafico che mostra le unità richiesta utilizzate" border="false":::

È possibile trovare lo stesso grafico quando si usa monitoraggio di Azure, come descritto [qui](monitor-request-unit-usage.md). Si noti che monitoraggio di Azure consente di configurare gli [avvisi](../azure-monitor/platform/alerts-metric-overview.md), che possono essere usati per notificare quando il consumo delle unità richiesta ha superato una determinata soglia.

## <a name="performance"></a><a id="performance"></a>Prestazioni

Le risorse senza server producono caratteristiche specifiche delle prestazioni diverse da quelle offerte dalle risorse della velocità effettiva con provisioning:

- **Disponibilità** : dopo che l'offerta senza server diventa disponibile a livello generale, la disponibilità di contenitori senza server sarà coperta da un contratto di servizio (SLA) del 99,9% quando non vengono usati zone di disponibilità (ridondanza della zona). Il contratto di contratto è 99,99% quando si utilizzano zone di disponibilità.
- **Latenza** : dopo che l'offerta senza server diventa disponibile a livello generale, la latenza dei contenitori senza server sarà coperta da un obiettivo del livello di servizio (SLO) di 10 millisecondi o meno per le scritture di punti e 30 millisecondi o meno per le Scritture. Un'operazione di lettura di punti consiste nel recupero di un singolo elemento in base al relativo ID e valore della chiave di partizione.
- Espansione **: dopo** che l'offerta senza server diventa disponibile a livello generale, l'espansione dei contenitori senza server sarà coperta da un obiettivo del livello di servizio (SLO) del 95%. Ciò significa che è possibile ottenere la massima espansione per almeno il 95% del tempo.

> [!NOTE]
> Come qualsiasi anteprima di Azure, Azure Cosmos DB senza server è escluso dai contratti di servizio (SLA). Le caratteristiche di prestazioni menzionate in precedenza sono fornite come anteprima dell'offerta che verrà garantita dall'offerta quando disponibile a livello generale.

## <a name="next-steps"></a>Passaggi successivi

Introduzione a senza server con gli articoli seguenti:

- [Unità richiesta in Azure Cosmos DB](request-units.md)
- [Scegliere tra le modalità Provisioning velocità effettiva e Serverless](throughput-serverless.md)
- [Modello di determinazione dei prezzi di Azure Cosmos DB](how-pricing-works.md)
