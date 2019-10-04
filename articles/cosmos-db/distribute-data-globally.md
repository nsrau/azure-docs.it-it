---
title: Distribuire i dati a livello globale con Azure Cosmos DB
description: Informazioni sulla replica geografica a livello globale, sul multimaster, sul failover e sul ripristino dei dati usando i database globali di Azure Cosmos DB, un servizio database multimodello distribuito a livello globale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 4f17fc7df5aef449c3b0f6dd8d02ae58df959070
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384881"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Informazioni sulla distribuzione globale con Azure Cosmos DB - panoramica

Le applicazioni moderne devono garantire velocità di risposta elevate ed essere sempre online. Per ottenere bassa latenza e disponibilità elevata, è necessario che le istanze di queste applicazioni siano distribuite in data center vicini agli utenti. Queste applicazioni vengono in genere distribuite in più data center e vengono definite distribuite a livello globale. Per le applicazioni distribuite a livello globale è necessario un database distribuito a livello globale, che può replicare in modo trasparente i dati ovunque nel mondo per consentire alle applicazioni di operare su una copia dei dati vicina agli utenti. 

Azure Cosmos DB è un servizio di database distribuito a livello globale e progettato per garantire bassa latenza, scalabilità elastica della velocità effettiva, semantica ben definita per la coerenza dei dati e disponibilità elevata. In breve, se l'applicazione richiede tempi di risposta rapidi in qualsiasi parte del mondo, se è necessario che sia sempre online e necessita di scalabilità illimitata e elastica della velocità effettiva e dello spazio di archiviazione, è necessario compilare l'applicazione in Azure Cosmos DB.

È possibile configurare i propri database in modo che siano distribuiti a livello globale e disponibili in una delle aree di Azure. Per ridurre la latenza, posizionare i dati vicino a quello in cui si trovano gli utenti. La scelta delle aree necessarie dipende la portata globale dell'applicazione e dalla posizione degli utenti. Cosmos DB replica in modo trasparente i dati in tutte le aree associate all'account Cosmos. Offre una singola immagine di sistema per il database e i contenitori Azure Cosmos distribuiti a livello globale, consentendo così all'applicazione di leggere e scrivere in locale. 

Con Azure Cosmos DB è possibile aggiungere o rimuovere le aree associate all'account in qualsiasi momento. Non è necessario sospendere l'esecuzione o ridistribuire l'applicazione per aggiungere o rimuovere un'area. Continua a essere a disponibilità elevata sempre a causa delle funzionalità multihoming fornite dal servizio in modo nativo.

![Topologia di distribuzione a disponibilità elevata](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Vantaggi chiave della distribuzione globale

**Compilare app globali in modalità attiva-attiva.** Con il nuovo protocollo di replica multimaster, ogni area supporta scritture e letture. La funzionalità multimaster consente inoltre di:

- Scalabilità di lettura e scrittura elastica illimitata. 
- Disponibilità in lettura e scrittura pari al 99,999% in tutto il mondo.
- Letture e scritture gestite in meno di 10 millisecondi nel 99% dei casi.

Usando le API multihosting di Azure Cosmos DB, l'applicazione è a conoscenza dell'area più vicina e può inviare richieste a tale area. L'area più vicina viene identificata senza che sia necessario apportare modifiche alla configurazione. Quando si aggiungono e rimuovono aree da e verso l'account Azure Cosmos, l'applicazione non deve essere ridistribuita o sospesa, ma continua a essere a disponibilità elevata in qualsiasi momento.

**Compilare applicazioni a reattività elevata.** L'applicazione può eseguire operazioni di lettura e scrittura quasi in tempo reale su tutte le aree selezionate per il database. Azure Cosmos DB gestisce internamente la replica dei dati tra le aree con garanzie del livello di coerenza del livello selezionato.

**Creare app a disponibilità elevata.** L'esecuzione di un database in più aree in tutto il mondo aumenta la disponibilità di un database. Se un'area non è disponibile, le richieste dell'applicazione vengono gestite automaticamente dalle altre aree. Azure Cosmos DB offre disponibilità del 99,999% in lettura e scrittura per i database in più aree.

**Garantire la continuità operativa in caso di interruzioni a livello di area.** Azure Cosmos DB supporta il [failover automatico](how-to-manage-database-account.md#automatic-failover) in caso di interruzioni del servizio a livello di area. In questi casi, Azure Cosmos DB continua a garantire la conformità ai contratti di servizio per latenza, disponibilità, coerenza e velocità effettiva. Per garantire la disponibilità elevata dell'intera applicazione, Cosmos DB offre un'API di failover manuale per simulare un'interruzione a livello di area. Con questa API è possibile eseguire esercitazioni periodiche per verificare la continuità aziendale.

**Scalabilità globale della velocità effettiva di lettura e scrittura.** Ogni area può essere abilitata per la scrittura e la scalabilità elastica di letture e scritture in tutto il mondo. La velocità effettiva configurata dall'applicazione in un database di Azure Cosmos o un contenitore è garantita per tutte le aree associate all'account Azure Cosmos. La velocità effettiva con provisioning è garantita da contratti di contratto con [supporto finanziario](https://aka.ms/acdbsla).

**Possibilità di scelta tra più modelli di coerenza ben definiti.** Il protocollo di replica di Azure Cosmos DB offre cinque modelli di coerenza ben definiti, pratici e intuitivi. Ogni modello prevede un compromesso ottimale tra coerenza e prestazioni. Usare questi modelli di coerenza per creare con facilità applicazioni distribuite a livello globale.

## <a id="Next Steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione globale sono disponibili negli articoli seguenti:

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Come configurare funzionalità multimaster nelle applicazioni](how-to-multi-master.md)
* [Configurare i client per il multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Aggiungere o rimuovere aree dall'account Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Creare criteri personalizzati per la risoluzione dei conflitti per gli account API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelli di coerenza programmabili in Cosmos DB](consistency-levels.md)
* [Scegliere il livello di coerenza ottimale per la propria applicazione](consistency-levels-choosing.md)
* [Livelli di coerenza nelle API di Azure Cosmos DB](consistency-levels-across-apis.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Come implementare la sincronizzazione personalizzata per ottimizzare la disponibilità e le prestazioni più elevate](how-to-custom-synchronization.md)
