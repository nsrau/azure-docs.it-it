---
title: Distribuire i dati a livello globale con Azure Cosmos DB | Microsoft Docs
description: Informazioni sulla replica geografica a livello globale, sul multimaster, sul failover e sul ripristino dei dati usando i database globali di Azure Cosmos DB, un servizio database multimodello distribuito a livello globale.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 181a8ad7291a8e8a0aa2a8373985c8747bd4569b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446824"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Informazioni sulla distribuzione globale con Azure Cosmos DB

Al giorno d'oggi, le applicazioni devono garantire velocità di risposta elevate ed essere sempre online. Per ottenere bassa latenza e disponibilità elevata, è necessario che le istanze di queste applicazioni siano distribuite in data center vicini agli utenti. Queste applicazioni vengono in genere distribuite in più data center e vengono definite distribuite a livello globale. Per le applicazioni distribuite a livello globale è necessario un database distribuito a livello globale che può replicare in modo trasparente i dati ovunque nel mondo per consentire alle applicazioni di operare su una copia dei dati vicina agli utenti. Azure Cosmos DB è un servizio di database distribuito a livello globale progettato per garantire bassa latenza, scalabilità elastica della velocità effettiva, semantica ben definita per la coerenza dei dati e disponibilità elevata. In breve, se l'applicazione richiede che venga garantito un tempo di risposta rapido in qualsiasi punto nel mondo, se deve essere sempre online e richiede scalabilità illimitata ed elastica della velocità effettiva e dello spazio di archiviazione, è consigliabile valutare la possibilità di compilare le applicazioni con Azure Cosmos DB.

Azure Cosmos DB è un servizio di Azure di base ed è disponibile in tutte le [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/) per impostazione predefinita. Microsoft gestisce data center di Azure in oltre 54 aree geografiche nel mondo e continua a espandere la presenza a livello di area per poter soddisfare le crescenti esigenze dei clienti. Quando si crea un account Azure Cosmos, si decide in quali aree deve essere distribuito il servizio. Il servizio Azure Cosmos DB è operativo 24 ore su 24, 7 giorni su 7. In questo modo, i clienti possono concentrarsi sulle proprie applicazioni.

È possibile configurare i propri database in modo che siano distribuiti a livello globale e disponibili in una delle aree di Azure. Per ridurre la latenza, è consigliabile inserire i dati in un punto più vicino a dove si trovano gli utenti. La scelta delle aree necessarie dipende la portata globale dell'applicazione e dalla posizione degli utenti. Azure Cosmos DB replica in modo trasparente i dati all'interno dell'account in tutte le aree associate all'account. Offre una singola immagine di sistema per il database e i contenitori di Azure Cosmos distribuiti a livello globale, consentendo così all'applicazione di leggere e scrivere in locale. Con Azure Cosmos DB è possibile aggiungere o rimuovere le aree associate all'account in qualsiasi momento. Non è necessario sospendere l'esecuzione o ridistribuire l'applicazione per aggiungere o rimuovere un'area. L'applicazione continua a essere a disponibilità elevata per tutto il tempo grazie alle funzionalità multihoming offerte dal servizio.

## <a name="key-benefits-of-global-distribution"></a>Vantaggi chiave della distribuzione globale

**Compilazione di app globali attiva-attiva**: grazie alla funzionalità multimaster, ogni area è un'area di scrittura (oltre a essere leggibile). La funzionalità multimaster garantisce anche scalabilità di scrittura elastica illimitata, disponibilità di lettura e scrittura al 99,999% in tutto il mondo e operazioni di lettura/scrittura garantite in mendo di 10 millisecondi al 99° percentile.  

Tramite le API multihoming di Azure Cosmos DB l'applicazione sa sempre dove si trova l'area più vicina e può inviare le richieste a tale area. L'area più vicina viene identificata senza che sia necessario apportare modifiche alla configurazione. Quando si aggiungono e rimuovono aree nell'account Azure Cosmos DB, l'applicazione non deve essere ridistribuita e continua a offrire disponibilità elevata.

**Creazione di app altamente reattive**: l'applicazione può essere facilmente progettata per eseguire operazioni di lettura e scrittura quasi in tempo reale, con latenze di pochissimi millisecondi, per tutte le aree scelte per il database.  Azure Cosmos DB gestisce internamente la replica dei dati tra aree in modo che sia garantito il livello di coerenza scelto per l'account Azure Cosmos.

Molte applicazioni potranno trarre vantaggio dai miglioramenti delle prestazioni offerti dalla possibilità di eseguire operazioni di scrittura (locali) in più aree. Alcune applicazioni che richiedono coerenza di alto livello preferiscono indirizzare tutte le operazioni di scrittura verso una singola area. Per supportare queste applicazioni, Azure Cosmos DB supporta sia le configurazioni con singola area che le configurazioni con più aree.

**Creazione di app a disponibilità elevata**: l'esecuzione di un database in più aree ne aumenta la disponibilità. Se un'area non è disponibile, le richieste dell'applicazione verranno gestite automaticamente dalle altre aree. Azure Cosmos DB offre disponibilità del 99,999% in lettura e scrittura per i database in più aree.

**Continuità aziendale durante le interruzioni a livello di area**: Azure Cosmos DB supporta il [failover automatico](how-to-manage-database-account.md#automatic-failover) durante un'interruzione a livello di area. Inoltre, anche in questo caso, Azure Cosmos DB continua a garantire la conformità ai contratti di servizio per latenza, disponibilità, coerenza e velocità effettiva. Per assicurare che l'intera app sia altamente disponibile, Azure Cosmos DB offre un'API di failover manuale per simulare un'interruzione a livello di area. Con questa API è possibile eseguire esercitazioni per verificare la regolare continuità aziendale.

**Scalabilità globale in termini di lettura e scrittura**: con la funzionalità multimaster, è possibile ridimensionare in modo elastico la velocità effettiva in lettura e scrittura in tutto il mondo. La funzionalità multimaster garantisce che la velocità effettiva configurata dall'applicazione in un database Azure Cosmos DB o in un contenitore sia disponibile in tutte le aree e protetta da [contratti di servizio con copertura finanziaria](https://aka.ms/acdbsla).

**Più modelli di coerenza ben definiti**: il protocollo di replica di Azure Cosmos DB è progettato per offrire cinque modelli di coerenza ben definiti, pratici e intuitivi. Ogni modello di coerenza prevede un compromesso tra coerenza e prestazioni. Questi modelli di coerenza consentono di creare con facilità applicazioni distribuite a livello globale.

## <a id="Next Steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione globale sono disponibili negli articoli seguenti:

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Come configurare i client per il multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Come aggiungere o rimuovere aree dall'account Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Come creare criteri personalizzati per la risoluzione dei conflitti per gli account API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)