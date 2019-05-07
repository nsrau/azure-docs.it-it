---
title: Distribuire i dati a livello globale con Azure Cosmos DB
description: Informazioni sulla replica geografica a livello globale, sul multimaster, sul failover e sul ripristino dei dati usando i database globali di Azure Cosmos DB, un servizio database multimodello distribuito a livello globale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: b90e495e0901c0caa14d9451f365e17f6c075e2b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070797"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Informazioni sulla distribuzione globale con Azure Cosmos DB - panoramica

Le applicazioni moderne devono garantire velocità di risposta elevate ed essere sempre online. Per ottenere bassa latenza e disponibilità elevata, è necessario che le istanze di queste applicazioni siano distribuite in data center vicini agli utenti. Queste applicazioni vengono in genere distribuite in più data center e vengono definite distribuite a livello globale. Per le applicazioni distribuite a livello globale è necessario un database distribuito a livello globale, che può replicare in modo trasparente i dati ovunque nel mondo per consentire alle applicazioni di operare su una copia dei dati vicina agli utenti. 

Azure Cosmos DB è un servizio di database distribuito a livello globale e progettato per garantire bassa latenza, scalabilità elastica della velocità effettiva, semantica ben definita per la coerenza dei dati e disponibilità elevata. In breve, se l'applicazione richiede ovunque tempi di risposta garantito nel mondo, se è necessario essere sempre online e richiede la scalabilità illimitata ed elastica della velocità effettiva e archiviazione, è necessario compilare l'applicazione in Azure Cosmos DB.

È possibile configurare i propri database in modo che siano distribuiti a livello globale e disponibili in una delle aree di Azure. Per ridurre la latenza, inserire i dati più vicino agli utenti. La scelta delle aree necessarie dipende la portata globale dell'applicazione e dalla posizione degli utenti. COSMOS DB replica in modo trasparente i dati in tutte le aree associate all'account Cosmos. Offre una singola immagine di sistema per il database e i contenitori Azure Cosmos distribuiti a livello globale, consentendo così all'applicazione di leggere e scrivere in locale. 

Con Azure Cosmos DB è possibile aggiungere o rimuovere le aree associate all'account in qualsiasi momento. Non è necessario sospendere l'esecuzione o ridistribuire l'applicazione per aggiungere o rimuovere un'area. Continua a essere a disponibilità elevata di tutto il tempo a causa delle funzionalità multihosting che il servizio fornisce in modo nativo.

![Topologia di distribuzione a disponibilità elevata](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Vantaggi chiave della distribuzione globale

**Compilare app globali in modalità attiva-attiva.** Con il relativo protocollo di replica multimaster novel, ogni area supporta letture e scritture. La funzionalità multimaster consente inoltre:

- Elastic illimitato scrivere e leggere la scalabilità. 
- Disponibilità in lettura e scrittura pari al 99,999% in tutto il mondo.
- Letture e scritture gestite in meno di 10 millisecondi nel 99% dei casi.

Con il multihoming di Azure Cosmos DB API, l'applicazione riconosca l'area più vicina e può inviare richieste per quell'area. L'area più vicina viene identificata senza che sia necessario apportare modifiche alla configurazione. Quando si aggiungono e rimuovono aree da e verso l'account Azure Cosmos, l'applicazione non è necessario essere ridistribuito o messo in pausa, continua a essere a disponibilità elevata in qualsiasi momento.

**Compilare applicazioni a reattività elevata.** L'applicazione può eseguire quasi in tempo reale letture e scritture per tutte le aree di cui si è scelto per il database. Azure Cosmos DB gestisce internamente la replica dei dati tra le aree con garanzie del livello di coerenza del livello selezionato.

**Creare app a disponibilità elevata.** Esecuzione di un database in più aree in tutto il mondo aumenta la disponibilità di un database. Se un'area non è disponibile, le richieste dell'applicazione vengono gestite automaticamente dalle altre aree. Azure Cosmos DB offre disponibilità del 99,999% in lettura e scrittura per i database in più aree.

**Garantire la continuità operativa in caso di interruzioni a livello di area.** Azure Cosmos DB supporta il [failover automatico](how-to-manage-database-account.md#automatic-failover) in caso di interruzioni del servizio a livello di area. In questi casi, Azure Cosmos DB continua a garantire la conformità ai contratti di servizio per latenza, disponibilità, coerenza e velocità effettiva. Per assicurarsi che l'intera applicazione è a disponibilità elevata, Cosmos DB offre un failover manuale per simulare un'interruzione di area API. Con questa API è possibile eseguire esercitazioni periodiche per verificare la continuità aziendale.

**Scalabilità globale della velocità effettiva di lettura e scrittura.** È possibile abilitare ogni area accessibile in scrittura e scalabilità elastica e letture e scritture in tutto il mondo. La velocità effettiva che consente di configurare l'applicazione in un database Cosmos Azure o in un contenitore è garantita a essere recapitati in tutte le aree associate all'account Azure Cosmos. La velocità effettiva con provisioning comportassero iscrizione [contratti di servizio con supporto finanziario](https://aka.ms/acdbsla).

**Possibilità di scelta tra più modelli di coerenza ben definiti.** Il protocollo di replica di Azure Cosmos DB offre cinque modelli di coerenza ben definiti, pratici e intuitivi. Ogni modello prevede un compromesso ottimale tra coerenza e prestazioni. Usare questi modelli di coerenza per creare con facilità applicazioni distribuite a livello globale.

## <a id="Next Steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione globale sono disponibili negli articoli seguenti:

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Come configurare funzionalità multimaster nelle applicazioni](how-to-multi-master.md)
* [Configurare i client per il multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Aggiungere o rimuovere aree dall'account Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Creare criteri personalizzati per la risoluzione dei conflitti per gli account API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)