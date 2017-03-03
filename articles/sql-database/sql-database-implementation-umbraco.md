---
title: 'Case study sul database SQL di Azure: Umbraco | Microsoft Docs'
description: Altre informazioni su come Umbraco usa il database SQL per eseguire rapidamente il provisioning e ridimensionare i servizi per migliaia di tenant nel cloud
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5243d31e-3241-4cb0-9470-ad488ff28572
ms.service: sql-database
ms.custom: app development case study
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 187954f3ddafdbc17e341ce41f5b109cb95f8a24
ms.openlocfilehash: 774d5ac6f3d5d9d97120ab895157677e4a92bb05
ms.lasthandoff: 01/12/2017


---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco usa il database SQL di Azure per eseguire rapidamente il provisioning e il ridimensionamento di servizi per migliaia di tenant nel cloud.
![Logo di Umbraco](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco è un noto sistema di gestione di contenuti (CMS) open source che può eseguire qualsiasi contenuto, da siti per brochure o piccole campagne ad applicazioni complesse per siti Web di social media globali e aziende nella classifica Fortune 500. 

> "Un'ampia community di sviluppatori usa il sistema, con più di 100.000 sviluppatori nei nostri forum e oltre 350.000 siti attivi che eseguono il sistema Umbraco".
> 
> - Morten Christensen, responsabile tecnico, Umbraco
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

Per semplificare le distribuzioni ai clienti, Umbraco ha aggiunto Umbraco-as-a-Service (UaaS), un'offerta di Software-as-a-Service (SaaS) che esclude la necessità di distribuzioni locali, fornisce un ridimensionamento predefinito ed elimina il sovraccarico di gestione, consentendo agli sviluppatori di concentrarsi sull'innovazione dei prodotti anziché sulla gestione della soluzione. Umbraco offre tutti questi vantaggi basandosi sul modello Platform-as-a-Service (PaaS) flessibile offerto da Microsoft Azure.

Il servizio UaaS consente ai clienti SaaS di usare funzionalità del sistema Umbraco CMS in precedenza non accessibili. Per questi clienti viene eseguito il provisioning di un ambiente CMS di lavoro con un database di produzione. I clienti possono aggiungere fino ad altri due database per gli ambienti di sviluppo e di gestione temporanea, a seconda dei requisiti. Quando è necessario un nuovo ambiente, al cliente viene assegnato automaticamente un database tramite un processo automatizzato. Il nuovo database è pronto in pochi secondi perché il provisioning è stato eseguito preventivamente da Umbraco tramite un pool elastico di database disponibili Azure (figura 1).

![Ciclo di vita del provisioning Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Ciclo di vita del provisioning per Umbraco-as-a-Service (UaaS)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Automazione e pool elastici di Azure per semplificare le distribuzioni
Con il database SQL di Azure e altri servizi di Azure, i clienti Umbraco possono eseguire autonomamente il provisioning dei propri ambienti e Umbraco può monitorare e gestire facilmente i database come parte di un flusso di lavoro intuitivo:

1. Eseguire il provisioning
   
   Umbraco gestisce 200 database disponibili, il cui provisioning è stato eseguito preventivamente da pool elastici. Quando un nuovo cliente esegue l'iscrizione per il servizio UaaS, Umbraco gli fornisce un nuovo ambiente CMS in tempo pressoché reale, assegnando un database dal pool di disponibilità.
   
   Quando viene raggiunta la soglia di un pool di disponibilità, viene creato un nuovo pool elastico e viene eseguito preventivamente il provisioning di nuovi database da assegnare ai clienti in base alle esigenze.
   
   L'implementazione è completamente automatizzata tramite le librerie di gestione in C# e le code del bus di servizio.
2. Uso
   
   I clienti usano da uno a tre ambienti, per la produzione, la gestione temporanea e/o lo sviluppo, ognuno con un proprio database. I database dei clienti si trovano in pool elastici, che consentono a Umbraco di fornire un ridimensionamento efficace evitando il provisioning eccessivo.
   
   ![Panoramica del progetto Umbraco](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Dettagli del progetto Umbraco](./media/sql-database-implementation-umbraco/figure3.png)
   
   Figura 2. Sito Web di clienti con Umbraco-as-a-Service (UaaS) che mostra una panoramica e i dettagli del progetto
   
   Il database SQL di Azure usa le unità di transazioni di database (DTU) per esprimere la potenza relativa necessaria per le transazioni di database reali. Per i clienti UaaS, la potenza dei database è in genere di circa 10 DTU, ma la flessibilità di ogni database consente la scalabilità su richiesta. I clienti possono quindi disporre delle risorse necessarie, anche durante le ore di punta. Ad esempio, nel corso di un recente evento sportivo notturno di domenica, un cliente del servizio UaaS ha riscontrato un picco del database fino a 100 DTU per la durata dell'evento. Grazie ai pool elastici di Azure, il sistema Umbraco è stato in grado di supportare questa richiesta elevata senza compromettere le prestazioni.
3. Monitoraggio
   
   Umbraco monitora l'attività dei database tramite i dashboard del portale di Azure e agli avvisi di posta elettronica personalizzati.
4. Ripristino di emergenza
   
   Azure offre due opzioni di ripristino di emergenza: il ripristino a livello geografico e la replica geografica attiva. L'opzione adatta ad ogni azienda dipende dagli [obiettivi di continuità aziendale](sql-database-business-continuity.md)specifici.
   
   La replica geografica attiva assicura il livello di risposta più veloce in caso di tempi di inattività. Con la replica geografica attiva è possibile creare fino a quattro database secondari leggibili su server in aree diverse e quindi avviare il failover in uno qualsiasi dei database secondari in caso di errore.
   
   Umbraco non richiede la replica geografica, tuttavia la replica geografica di Azure offre il vantaggio di tempi di inattività minimi in caso di interruzione. Il ripristino geografico si basa sui backup di database nell'archiviazione di Azure con ridondanza geografica. Gli utenti possono quindi eseguire il ripristino da una copia di backup in caso di interruzione del servizio nell'area primaria.
5. Deprovisioning
   
   Quando viene eliminato l'ambiente di un progetto, tutti i database associati (di sviluppo, di gestione temporanea o live) vengono rimossi durante la pulizia della coda del bus di servizio. Questo processo automatizzato ripristina i database inutilizzati nel pool di disponibilità di database elastici Umbraco, rendendoli disponibili per attività di provisioning future ottimizzando il livello di utilizzo.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>I pool elastici consentono un facile ridimensionamento del servizio UaaS
Grazie all'uso di pool elastici di Azure, Umbraco è in grado di ottimizzare le prestazioni per i clienti senza ricorrere al provisioning eccessivo o insufficiente. Umbraco ha attualmente quasi 3.000 database in 19 pool elastici, assicurando un facile ridimensionamento in linea con le esigenze di uno qualsiasi dei 325.000 clienti esistenti o dei nuovi clienti disponibili a distribuire un sistema CMS nel cloud.

Secondo Morten Christensen, responsabile tecnico Umbraco, "Ogni giorno circa 30 nuovi clienti scelgono il servizio UaaS. I clienti apprezzano in particolare la possibilità di eseguire il provisioning di nuovi progetti in pochi secondi, pubblicare immediatamente gli aggiornamenti nei propri siti live da un ambiente di sviluppo tramite le "distribuzioni con un clic" e apportare modifiche altrettanto rapidamente qualora vengano rilevati errori".

Se un cliente non ha più necessità di un secondo e/o un terzo ambiente, è possibile rimuovere semplicemente questi ambienti. In questo modo vengono liberate risorse che possono essere usate per altri clienti come parte del pool di disponibilità di database elastici Umbraco.

![Architettura di distribuzione Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Architettura per la distribuzione di UaaS in Microsoft Azure

## <a name="the-path-from-datacenter-to-cloud"></a>Percorso dal data center al cloud
Al momento di adottare inizialmente la decisione di migrare a un modello SaaS, gli sviluppatori Umbraco sono consapevoli del fatto che necessitano di un modo conveniente e scalabile per distribuire il servizio.

> "I pool elastici sono perfetti per l'offerta SaaS perché consentono di aumentare o ridurre la capacità in base alle esigenze. Il provisioning è semplice e grazie alla configurazione è possibile ottimizzare il livello di utilizzo".
> 
> - Morten Christensen, responsabile tecnico, Umbraco
> 
> 

"Il nostro obiettivo era quello di dedicare tempo prezioso alla risoluzione dei problemi dei clienti, non alla gestione dell'infrastruttura. Lo scopo era quello di mettere i clienti nella condizione migliore per ottenere il massimo valore" afferma Niels Hartvig, fondatore di Umbraco. "Inizialmente abbiamo valutato l'ipotesi di eseguire autonomamente l'hosting dei server, ma la pianificazione della capacità avrebbe rappresentato un incubo". Non a caso, Umbraco non impiega amministratori di database, aspetto che evidenzia l'uso di UaaS come proposta di valore chiave.

Un obiettivo importante perseguito dagli sviluppatori Umbraco è stato quello di offrire ai clienti UaaS un modo per eseguire il provisioning di ambienti rapidamente e senza limiti di capacità. Fornire tuttavia un servizio ospitato dedicato nei data center Umbraco avrebbe richiesto una capacità in eccesso elevata per gestire i picchi in fase di elaborazione. Ciò avrebbe comportato l'aggiunta di una notevole infrastruttura di calcolo che sarebbe stata frequentemente sottoutilizzata.

Inoltre, il team di sviluppo Umbraco desiderava una soluzione che consentisse di riutilizzare quanto più possibile il codice esistente. Come afferma Mikkel Madsen, sviluppatore Umbraco, "Eravamo soddisfatti degli strumenti di sviluppo Microsoft con cui avevamo già familiarità, come Microsoft SQL Server, il database SQL di Microsoft Azure, ASP.net e Internet Information Services (IIS). Prima di investire su una soluzione cloud IaaS o PaaS, desideravamo assicurarci che supportasse gli strumenti e le piattaforme Microsoft in modo che non fosse necessario apportare modifiche sostanziali al codice base".

Per soddisfare tutti i criteri, Umbraco ha cercato un partner per il cloud con le caratteristiche seguenti:

* Affidabilità e capacità adeguata
* Supporto per gli strumenti di sviluppo Microsoft per non costringere i tecnici Umbraco a ricostruire completamente l'ambiente di sviluppo
* Presenza in tutti i mercati geografici in cui il servizio UaaS opera come competitor: le aziende hanno bisogno di garantire il rapido accesso ai dati e l'archiviazione dei dati in una posizione in linea con i requisiti normativi internazionali

## <a name="why-umbraco-chose-azure-for-uaas"></a>Perché Umbraco ha scelto Azure per UaaS
Secondo Morten Christensen "Dopo un'attenta valutazione di tutte le opzioni, abbiamo scelto Azure perché soddisfa tutti i criteri, dalla gestibilità e scalabilità alla familiarità e al rapporto costi/benefici. Abbiamo configurato gli ambienti nelle VM Azure e ogni ambiente ha una propria istanza del database SQL di Azure, con tutte le istanze nei pool elastici. Separando i database tra gli ambienti di sviluppo, di gestione temporanea e live, siamo in grado di offrire ai clienti un livello avanzato di isolamento delle prestazioni abbinato alla scalabilità conseguendo un successo notevole".

Morten afferma anche: "Prima era necessario eseguire manualmente il provisioning di server per i database Web. Ora non teniamo più in considerazione questo aspetto. Tutte le operazioni sono automatizzate, dal provisioning alle operazioni di eliminazione".

Morten è soddisfatto anche delle funzionalità di ridimensionamento disponibili in Azure. "I pool elastici sono perfetti per l'offerta SaaS perché consentono di aumentare o ridurre la capacità in base alle esigenze. Il provisioning è semplice e grazie alla configurazione è possibile ottimizzare il livello di utilizzo". Morten afferma "La semplicità dei pool elastici, con la garanzia di DTU basate sui livelli di servizio, offre la possibilità di eseguire il provisioning di nuovi pool di risorse su richiesta. Uno dei nostri clienti più importanti ha raggiunto recentemente il picco di 100 DTU nell'ambiente live. Usando Azure, i pool elastici hanno fornito ai database dei clienti le risorse necessarie in tempo reale senza la necessità di prevedere in anticipo i requisiti di DTU. In poche parole, i clienti ottengono il tempo di risposta che si aspettano e possiamo soddisfare i contratti di servizio a livello di prestazioni".

Mikkel Madsen ribadisce "Abbiamo adottato il potente algoritmo di Azure che connette uno scenario SaaS comune, come l'acquisizione di nuovi clienti in tempo reale su larga scala, al modello applicativo, come il provisioning preventivo di database, sia di sviluppo sia live, insieme alla tecnologia sottostante usando le code del bus di servizio Azure in combinazione con il database SQL di Azure".

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Con Azure il servizio UaaS supera le aspettative dei clienti
Da quando ha scelto Azure come partner cloud, Umbraco è stato in grado di fornire ai clienti UaaS prestazioni ottimali in termini di gestione dei contenuti, senza dover investire su risorse IT, come sarebbe stato necessario con una soluzione indipendente. Come afferma Morten, "Apprezziamo in particolare i vantaggi e la scalabilità che Azure mette a disposizione degli sviluppatori e i clienti sono particolarmente soddisfatti di funzionalità e affidabilità. Il successo è stato grande da ogni punto di vista".

## <a name="more-information"></a>Altre informazioni
* Per altre informazioni sui pool elastici, vedere [pool elastici](sql-database-elastic-pool.md).
* Per altre informazioni sul bus di servizio di Azure, vedere [Bus di servizio](https://azure.microsoft.com/services/service-bus/).
* Per altre informazioni sui ruoli Web e i ruoli di lavoro, vedere l'argomento relativo ai [ruoli di lavoro](../fundamentals-introduction-to-azure.md#compute).    
* Per altre informazioni sulla rete virtuale, vedere [Rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Per altre informazioni su backup e ripristino, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).    
* Per altre informazioni sul monitoraggio di pool, vedere [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md).    
* Per altre informazioni su Umbraco-as-a-Service, vedere [Umbraco](https://umbraco.com/cloud).


