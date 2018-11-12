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
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238275"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Informazioni sulla distribuzione globale con Azure Cosmos DB

Molte delle applicazioni attuali vengono eseguite o aspirano a essere eseguite su scala globale. Sono sempre attive e sono accessibili agli utenti in tutto il mondo. La gestione della distribuzione globale dei dati usati da queste applicazioni, garantendo disponibilità e prestazioni elevate, non è un problema facile. Cosmos DB è un servizio di database distribuito a livello globale che è stato progettato da zero per far fronte a sfide di questo tipo.

Cosmos DB è un servizio di Azure di base ed è disponibile in tutte le [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/) per impostazione predefinita. Microsoft gestisce data center di Azure in più di 50 aree geografiche nel mondo e continua a espandersi per poter soddisfare le crescenti esigenze dei clienti. Il servizio Cosmos DB è operativo 24 ore su 24, 7 giorni su 7. In questo modo, i clienti possono concentrarsi sulle proprie applicazioni. Quando si crea un account Cosmos DB, si decide in quali aree deve essere distribuito il servizio.

I clienti di Cosmos DB possono configurare i propri database in modo che siano distribuiti a livello globale e disponibili ovunque, in una o più delle oltre 50 aree di Azure. Per ridurre la latenza, è opportuno collocare i dati più vicino alla posizione dell'utente. Pertanto, la decisione riguardo alle aree in cui eseguire il servizio dipende dalla portata globale dell'applicazione e dal luogo in cui si trovano gli utenti. Cosmos DB replica in modo trasparente tutti i dati all'interno dell'account Cosmos in tutte le aree configurate. Cosmos DB offre una singola immagine di sistema per il database e i contenitori Cosmos del cliente, consentendo così all'applicazione di leggere e scrivere in locale. Con Cosmos DB è possibile aggiungere o rimuovere le aree associate all'account in qualsiasi momento. L'applicazione non deve essere messa in pausa né ridistribuita e garantisce costantemente disponibilità elevata, senza interruzioni nella gestione dei dati, grazie alle funzionalità multihoming offerte dal servizio.

## <a name="key-benefits-of-global-distribution"></a>Vantaggi chiave della distribuzione globale

**Possibilità di creare facilmente app globali di tipo attivo-attivo**: con la funzionalità multimaster, ogni area è accessibile in scrittura (oltre che in lettura), consentendo scalabilità elastica illimitata in scrittura, disponibilità del 99,999% in lettura e scrittura, in tutto il mondo e operazioni di lettura e scrittura veloci garantite in meno di 10 millisecondi, al 99° percentile.  

Con le API multihoming di Azure Cosmos DB, l'applicazione sa sempre dove si trova l'area più vicina e invia le richieste a tale area. L'area più vicina viene identificata senza che sia necessario apportare modifiche alla configurazione. Quando si aggiungono e rimuovono aree nell'account Cosmos DB, l'applicazione non deve essere ridistribuita e continua a offrire disponibilità elevata.

**Creazione di app altamente reattive**: l'applicazione può essere facilmente progettata per eseguire operazioni di lettura e scrittura quasi in tempo reale, con latenze di pochissimi millisecondi, per tutte le aree scelte per il database.  Azure Cosmos DB gestisce internamente la replica dei dati tra aree in modo da garantire il livello di coerenza scelto per l'account Cosmos.

Molte applicazioni potranno trarre vantaggio dai miglioramenti delle prestazioni offerti dalla possibilità di eseguire operazioni di scrittura (locali) in più aree. Alcune applicazioni, ad esempio quelle che richiedono coerenza di alto livello, preferiranno indirizzare tutte le operazioni di scrittura verso una singola area. Cosmos DB supporta entrambe le configurazioni, con singola area e con più aree.

**Creazione di app a disponibilità elevata**: l'esecuzione di un database in più aree consente di migliorarne la disponibilità. Se un'area non è disponibile, le richieste dell'applicazione verranno gestite automaticamente dalle altre aree. Azure Cosmos DB offre disponibilità del 99,999% in lettura e scrittura per i database in più aree.

**Continuità aziendale durante le interruzioni a livello di area**: Azure Cosmos DB supporta il [failover automatico](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) durante un'interruzione a livello di area. Inoltre, anche in questo caso, Cosmos DB continua a garantire la conformità ai contratti di servizio per latenza, disponibilità, coerenza e velocità effettiva. Per consentire di verificare che l'intera app rimanga altamente disponibile, Azure Cosmos DB offre un'API di failover manuale per simulare un'interruzione a livello di area. Con questa API è possibile eseguire esercitazioni per verificare la regolare continuità aziendale ed essere così pronti all'implementazione.

**Scalabilità globale in termini di lettura e scrittura**: con la funzionalità multimaster, è possibile ridimensionare in modo elastico la velocità effettiva in lettura e scrittura in tutto il mondo. La funzionalità multimaster garantisce che la velocità effettiva configurata dall'applicazione in un database Azure Cosmos DB o in un contenitore sia disponibile in tutte le aree e protetta da [contratti di servizio con copertura finanziaria](https://aka.ms/acdbsla).

**Più modelli di coerenza ben definiti**: il protocollo di replica di Azure Cosmos DB è progettato per offrire cinque modelli di coerenza ben definiti, pratici e intuitivi, ognuno con un preciso compromesso tra coerenza e prestazioni. Questi modelli di coerenza consentono di creare con facilità applicazioni corrette distribuite a livello globale.

## <a id="Next Steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione globale sono disponibili negli articoli seguenti:

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Come configurare i client per il multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Come aggiungere o rimuovere aree dal database](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Come creare criteri personalizzati per la risoluzione dei conflitti per gli account API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)