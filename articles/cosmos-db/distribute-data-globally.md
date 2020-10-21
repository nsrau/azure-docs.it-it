---
title: Distribuire i dati a livello globale con Azure Cosmos DB
description: Informazioni sulla replica geografica a livello globale, le Scritture in più aree, il failover e il ripristino dei dati usando i database globali da Azure Cosmos DB, un servizio di database multimodello distribuito a livello globale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 5ea5ee2dfe89b36fce78c369100224718eb5864f
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278771"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Distribuire i dati a livello globale con Azure Cosmos DB

Le applicazioni moderne devono garantire velocità di risposta elevate ed essere sempre online. Per ottenere bassa latenza e disponibilità elevata, è necessario che le istanze di queste applicazioni siano distribuite in data center vicini agli utenti. Queste applicazioni vengono in genere distribuite in più data center e vengono definite distribuite a livello globale. Per le applicazioni distribuite a livello globale è necessario un database distribuito a livello globale, che può replicare in modo trasparente i dati ovunque nel mondo per consentire alle applicazioni di operare su una copia dei dati vicina agli utenti. 

Azure Cosmos DB è un servizio di database distribuito a livello globale e progettato per garantire bassa latenza, scalabilità elastica della velocità effettiva, semantica ben definita per la coerenza dei dati e disponibilità elevata. In breve, se l'applicazione richiede tempi di risposta rapidi in qualsiasi parte del mondo, se è necessario che sia sempre online e necessiti di scalabilità illimitata e elastica della velocità effettiva e dello spazio di archiviazione, è necessario compilare l'applicazione in Azure Cosmos DB.

È possibile configurare i propri database in modo che siano distribuiti a livello globale e disponibili in una delle aree di Azure. Per ridurre la latenza, posizionare i dati vicino a quello in cui si trovano gli utenti. La scelta delle aree necessarie dipende la portata globale dell'applicazione e dalla posizione degli utenti. Cosmos DB replica in modo trasparente i dati in tutte le aree associate all'account Cosmos. Offre una singola immagine di sistema per il database e i contenitori Azure Cosmos distribuiti a livello globale, consentendo così all'applicazione di leggere e scrivere in locale. 

Con Azure Cosmos DB è possibile aggiungere o rimuovere le aree associate all'account in qualsiasi momento. Non è necessario sospendere l'esecuzione o ridistribuire l'applicazione per aggiungere o rimuovere un'area. Continua a essere a disponibilità elevata sempre a causa delle funzionalità multihoming fornite dal servizio in modo nativo.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Topologia di distribuzione a disponibilità elevata" border="false":::

## <a name="key-benefits-of-global-distribution"></a>Vantaggi chiave della distribuzione globale

**Crea app attive globali attive.** Con il nuovo protocollo di replica per scritture in più aree, ogni area supporta scritture e letture. La funzionalità di scrittura in più aree Abilita anche:

- Scalabilità di lettura e scrittura elastica illimitata.
- Disponibilità in lettura e scrittura pari al 99,999% in tutto il mondo.
- Letture e scritture gestite in meno di 10 millisecondi nel 99% dei casi.

Usando le API multihosting di Azure Cosmos DB, l'applicazione è a conoscenza dell'area più vicina e può inviare richieste a tale area. L'area più vicina viene identificata senza che sia necessario apportare modifiche alla configurazione. Quando si aggiungono e rimuovono aree da e verso l'account Azure Cosmos, l'applicazione non deve essere ridistribuita o sospesa, ma continua a essere a disponibilità elevata in qualsiasi momento.

**Compilare applicazioni a reattività elevata.** L'applicazione può eseguire operazioni di lettura e scrittura quasi in tempo reale su tutte le aree selezionate per il database. Azure Cosmos DB gestisce internamente la replica dei dati tra le aree con garanzie del livello di coerenza del livello selezionato.

**Creare app a disponibilità elevata.** L'esecuzione di un database in più aree in tutto il mondo aumenta la disponibilità di un database. Se un'area non è disponibile, le richieste dell'applicazione vengono gestite automaticamente dalle altre aree. Azure Cosmos DB offre disponibilità del 99,999% in lettura e scrittura per i database in più aree.

**Mantenere la continuità aziendale durante le interruzioni dell'area.** Azure Cosmos DB supporta il [failover automatico](how-to-manage-database-account.md#automatic-failover) in caso di interruzioni del servizio a livello di area. In questi casi, Azure Cosmos DB continua a garantire la conformità ai contratti di servizio per latenza, disponibilità, coerenza e velocità effettiva. Per garantire la disponibilità elevata dell'intera applicazione, Cosmos DB offre un'API di failover manuale per simulare un'interruzione a livello di area. Con questa API è possibile eseguire esercitazioni periodiche per verificare la continuità aziendale.

**Ridimensionare la velocità effettiva di lettura e scrittura a livello globale.** Ogni area può essere abilitata per la scrittura e la scalabilità elastica di letture e scritture in tutto il mondo. Il provisioning della velocità effettiva configurata dall'applicazione in un database di Azure Cosmos o in un contenitore viene effettuato in tutte le aree associate all'account Azure Cosmos. La velocità effettiva con provisioning è garantita da contratti di contratto con [supporto finanziario](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

**Scegliere tra diversi modelli di coerenza ben definiti.** Il protocollo di replica di Azure Cosmos DB offre cinque modelli di coerenza ben definiti, pratici e intuitivi. Ogni modello prevede un compromesso ottimale tra coerenza e prestazioni. Usare questi modelli di coerenza per creare con facilità applicazioni distribuite a livello globale.

## <a name="next-steps"></a><a id="Next Steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione globale sono disponibili negli articoli seguenti:

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Come configurare le Scritture in più aree nelle applicazioni](how-to-multi-master.md)
* [Configurare i client per il multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Aggiungere o rimuovere aree dall'account Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Creare criteri personalizzati per la risoluzione dei conflitti per gli account API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelli di coerenza programmabili in Cosmos DB](consistency-levels.md)
* [Scegliere il livello di coerenza ottimale per la propria applicazione](consistency-levels-choosing.md)
* [Livelli di coerenza nelle API di Azure Cosmos DB](consistency-levels-across-apis.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)

