---
title: Distribuire i dati a livello globale con Azure Cosmos DB
description: Informazioni sulla replica geografica a livello globale, sul multimaster, sul failover e sul ripristino dei dati usando i database globali di Azure Cosmos DB, un servizio database multimodello distribuito a livello globale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 3599875f96c6bd79ecace1d59c3580027fab3168
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040354"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Informazioni sulla distribuzione globale con Azure Cosmos DB

Le applicazioni moderne devono garantire velocità di risposta elevate ed essere sempre online. Per ottenere bassa latenza e disponibilità elevata, è necessario che le istanze di queste applicazioni siano distribuite in data center vicini agli utenti. Queste applicazioni vengono in genere distribuite in più data center e vengono definite distribuite a livello globale. Per le applicazioni distribuite a livello globale è necessario un database distribuito a livello globale, che può replicare in modo trasparente i dati ovunque nel mondo per consentire alle applicazioni di operare su una copia dei dati vicina agli utenti. 

Azure Cosmos DB è un servizio di database distribuito a livello globale e progettato per garantire bassa latenza, scalabilità elastica della velocità effettiva, semantica ben definita per la coerenza dei dati e disponibilità elevata. In breve, se l'applicazione richiede tempi di risposta rapidi in qualsiasi punto del mondo, se deve essere sempre online e richiede scalabilità illimitata ed elastica della velocità effettiva e dello spazio di archiviazione, valutare la possibilità di compilare le applicazioni con Azure Cosmos DB.

È possibile configurare i propri database in modo che siano distribuiti a livello globale e disponibili in una delle aree di Azure. Per ridurre la latenza, inserire i dati in un punto più vicino a quello in cui si trovano gli utenti. La scelta delle aree necessarie dipende la portata globale dell'applicazione e dalla posizione degli utenti. Azure Cosmos DB replica in modo trasparente i dati all'interno dell'account in tutte le aree associate all'account. Offre una singola immagine di sistema per il database e i contenitori Azure Cosmos distribuiti a livello globale, consentendo così all'applicazione di leggere e scrivere in locale. 

Con Azure Cosmos DB è possibile aggiungere o rimuovere le aree associate all'account in qualsiasi momento. Non è necessario sospendere l'esecuzione o ridistribuire l'applicazione per aggiungere o rimuovere un'area. L'applicazione continua a essere a disponibilità elevata in ogni momento grazie alle funzionalità multihoming offerte dal servizio.

## <a name="key-benefits-of-global-distribution"></a>Vantaggi chiave della distribuzione globale

**Compilare app globali in modalità attiva-attiva.** Con la funzionalità multimaster ogni area è un'area di scrittura. Inoltre è supportata la lettura. La funzionalità multimaster garantisce anche quanto segue:

- Scalabilità elastica in scrittura illimitata. 
- Disponibilità in lettura e scrittura pari al 99,999% in tutto il mondo.
- Letture e scritture gestite in meno di 10 millisecondi nel 99% dei casi.

Con il multihoming delle API Azure Cosmos, l'applicazione rileva l'area più vicina. Quindi può inviare richieste a quell'area. L'area più vicina viene identificata senza che sia necessario apportare modifiche alla configurazione. Quando si aggiungono e rimuovono aree nell'account Azure Cosmos DB non è necessario ridistribuire l'applicazione. L'applicazione continua a offrire disponibilità elevata.

**Compilare applicazioni a reattività elevata.** L'applicazione può essere impostata facilmente per l'esecuzione di operazioni di lettura e scrittura near real time. Può garantire latenze di pochi millisecondi in tutte le aree scelte per il database. Azure Cosmos DB gestisce internamente la replica dei dati tra le aree. Di conseguenza il livello di coerenza selezionato per l'account Azure Cosmos DB è garantito.

I miglioramenti delle prestazioni derivanti dalla possibilità di eseguire operazioni di scrittura (locali) in più aree offrono vantaggi per molte applicazioni. Alcune applicazioni che richiedono coerenza di alto livello preferiscono indirizzare tutte le operazioni di scrittura verso una singola area. Per queste applicazioni, Azure Cosmos DB supporta sia le configurazioni ad area singola che le configurazioni con più aree.

**Creare app a disponibilità elevata.** L'esecuzione di un database in più aree ne aumenta la disponibilità. Se un'area non è disponibile, le richieste dell'applicazione vengono gestite automaticamente dalle altre aree. Azure Cosmos DB offre disponibilità del 99,999% in lettura e scrittura per i database in più aree.

**Garantire la continuità operativa in caso di interruzioni a livello di area.** Azure Cosmos DB supporta il [failover automatico](how-to-manage-database-account.md#automatic-failover) in caso di interruzioni del servizio a livello di area. In questi casi, Azure Cosmos DB continua a garantire la conformità ai contratti di servizio per latenza, disponibilità, coerenza e velocità effettiva. Per assicurare che l'intera app sia a disponibilità elevata, Azure Cosmos DB offre un'API di failover manuale che consente di simulare un'interruzione a livello di area. Con questa API è possibile eseguire esercitazioni periodiche per verificare la continuità aziendale.

**Scalabilità globale della velocità effettiva di lettura e scrittura.** Con la funzionalità multimaster è possibile ridimensionare in modo elastico la velocità effettiva in lettura e scrittura in tutto il mondo. La funzionalità multimaster garantisce che la velocità effettiva configurata dall'applicazione in un database Azure Cosmos DB o in un contenitore sia disponibile in tutte le aree. La velocità effettiva è anche protetta da [contratti di servizio con copertura finanziaria](https://aka.ms/acdbsla).

**Possibilità di scelta tra più modelli di coerenza ben definiti.** Il protocollo di replica di Azure Cosmos DB offre cinque modelli di coerenza ben definiti, pratici e intuitivi. Ogni modello prevede un compromesso ottimale tra coerenza e prestazioni. Usare questi modelli di coerenza per creare con facilità applicazioni distribuite a livello globale.

## <a id="Next Steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione globale sono disponibili negli articoli seguenti:

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Configurare i client per il multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Aggiungere o rimuovere aree dall'account Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Creare criteri personalizzati per la risoluzione dei conflitti per gli account API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)