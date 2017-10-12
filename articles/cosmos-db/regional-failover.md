---
title: Failover a livello di area in Azure Cosmos DB | Microsoft Docs
description: "Informazioni sulla modalità di funzionamento di failover manuali e automatici con Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d8ba08bc9f99cb77c9f03949fc5db299eb222c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Failover a livello di area automatici per la continuità aziendale in Azure Cosmos DB
Azure Cosmos DB semplifica la distribuzione globale dei dati, offrendo [account di database con più aree](distribute-data-globally.md) e completamente gestiti, che forniscono compromessi espliciti tra coerenza, disponibilità e prestazioni, il tutto con le relative garanzie. Gli account Cosmos DB offrono disponibilità elevata, latenze di pochi millisecondi, più [livelli di coerenza ben definiti](consistency-levels.md), failover a livello di area trasparente con API multihosting e la possibilità di ridimensionare in modo flessibile la velocità effettiva e le risorse di archiviazione in tutto il mondo. 

Cosmos DB supporta sia failover espliciti che failover basati su criteri che consentono di controllare il comportamento del sistema end-to-end in caso di errori. Questo articolo analizza i seguenti aspetti:

* Come funzionano i failover manuali in Cosmos DB?
* Come funzionano i failover automatici in Cosmos DB e che cosa accade quando un data center smette di funzionare?
* In che modo è possibile usare i failover manuali in architetture applicative?

In questo video di Azure Friday, con Scott Hanselman e Karthik Raman, Principal Engineering Manager, sono disponibili altre informazioni sui failover regionali.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a id="ConfigureMultiRegionApplications"></a>Configurazione di applicazioni in più aree
Prima di approfondire le modalità di failover, esaminiamo in che modo è possibile configurare un'applicazione per sfruttare i vantaggi della disponibilità di più aree e assicurare la resilienza in caso di failover regionali.

* In primo luogo, distribuire l'applicazione in più aree
* Per garantire l'accesso a bassa latenza da ogni area in cui l'applicazione viene distribuita, configurare [l'elenco delle aree preferite](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) per ogni area tramite uno degli SDK supportati.

Il frammento seguente mostra come inizializzare un'applicazione in più aree. In questo caso l'account Azure Cosmos DB `contoso.documents.azure.com` è configurato con due aree: Stati Uniti occidentali ed Europa settentrionale. 

* L'applicazione viene distribuita nell'area Stati Uniti occidentali (usando ad esempio Servizi app di Azure) 
* Configurato con `West US` come la prima area preferita per letture a bassa latenza
* Configurato con `North Europe` come la seconda area preferita (per disponibilità elevata in caso di errori a livello di area)

Nell'API di DocumentDB questa configurazione è simile al frammento seguente:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
    usConnectionPolicy);
```

L'applicazione viene distribuita anche nell'area Europa settentrionale con l'ordine di preferenza delle aree invertito. Ciò significa che l'Europa settentrionale viene specificata come prima area per le letture a bassa latenza e Stati Uniti occidentali come la seconda area preferita per disponibilità elevata in caso di errori a livello di area.

Il diagramma dell'architettura seguente illustra la distribuzione di un'applicazione in più aree in cui Cosmos DB e l'applicazione sono configurati per essere disponibili in quattro aree geografiche di Azure.  

![Distribuzione globale di un'applicazione con Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Ecco come il servizio Cosmos DB gestisce errori a livello di area tramite i failover automatici. 

## <a id="AutomaticFailovers"></a>Failover automatici
Nel raro caso di un'interruzione a livello di area di Azure o del data center, Cosmos DB attiva automaticamente i failover di tutti gli account Cosmos DB presenti nell'area interessata. 

**Cosa accade se si verifica un'interruzione del servizio in un'area di lettura?**

Gli account Cosmos DB con un'area di lettura in una delle aree interessate vengono automaticamente disconnessi dalla rispettiva area di scrittura e contrassegnati come offline. Gli SDK di Cosmos DB implementano un protocollo di individuazione area che consente di rilevare automaticamente quando un'area è disponibile e reindirizzare le chiamate di lettura all'area successiva disponibile nell'elenco delle aree preferite. Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente. Non sono necessarie modifiche nel codice dell'applicazione per gestire i failover regionali. Durante l'intero processo, Cosmos DB continua a rispettare garanzie di coerenza.

![Errori di un'area di lettura in Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

Dopo il ripristino dell'area interessata da un'interruzione del servizio, tutti gli account Cosmos DB interessati nell'area vengono ripristinati automaticamente dal servizio. Gli account Cosmos DB con un'area di lettura nell'area interessata vengono quindi sincronizzati automaticamente con l'area di scrittura corrente e passano in modalità online. Gli SDK di Cosmos DB rilevano la disponibilità della nuova area e valutano se deve essere selezionata come area di lettura corrente in base all'elenco delle aree preferite configurato dall'applicazione. Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione.

**Cosa accade se si verifica un'interruzione del servizio in un'area di scrittura?**

Se l'area interessata è l'area di scrittura corrente per un determinato account Cosmos DB, questa viene automaticamente contrassegnata come offline. Viene quindi promossa un'area alternativa come area di scrittura per ogni account Cosmos DB interessato. È possibile controllare completamente l'ordine di selezione dell'area per gli account Cosmos DB tramite il portale di Azure o [a livello di codice](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Priorità di failover per Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Durante i failover automatici, Cosmos DB sceglie automaticamente l'area di scrittura successiva per un determinato account Cosmos DB in base all'ordine di priorità specificato. 

![Errori di un'area di scrittura in Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

Dopo il ripristino dell'area interessata da un'interruzione del servizio, tutti gli account Cosmos DB interessati nell'area vengono ripristinati automaticamente dal servizio. 

* Gli account Cosmos DB con l'area di scrittura precedente nell'area interessata rimangono in modalità offline, con disponibilità di lettura anche dopo il ripristino dell'area. 
* È possibile eseguire una query su questa area per calcolare eventuali scritture non replicate durante l'interruzione del servizio tramite il confronto con i dati disponibili nell'area di scrittura corrente. In base alle esigenze dell'applicazione, è possibile completare l'unione e/o la risoluzione dei conflitti ed eseguire il writeback del set finale di modifiche nell'area di scrittura corrente. 
* Dopo aver completato l'unione delle modifiche, è possibile ripristinare la modalità online dell'area interessata rimuovendola e aggiungendola nuovamente all'account Cosmos DB. Dopo aver aggiunto di nuovo l'area, è possibile riconfigurarla come l'area di scrittura eseguendo un failover manuale tramite il portale di Azure o [a livello di codice](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

## <a id="ManualFailovers"></a>Failover manuali

Oltre ai failover automatici, è possibile modificare manualmente l'area di scrittura corrente di un determinato account Cosmos DB in modo dinamico in una delle aree di lettura esistenti. I failover manuali possono essere avviati tramite il portale di Azure o [a livello di codice](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

I failover manuali assicurano una **perdita di dati pari a zero** e **nessuna perdita di disponibilità** e trasferiscono correttamente lo stato di scrittura dalla vecchia alla nuova area di scrittura per l'account Cosmos DB specificato. Come nei failover automatici, durante i failover manuali Cosmos DB SDK gestisce automaticamente le modifiche dell'area di scrittura e garantisce che le chiamate vengano reindirizzate automaticamente alla nuova area di scrittura. La gestione dei failover non richiede alcuna modifica del codice o della configurazione dell'applicazione. 

![Failover manuali in Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Alcuni degli scenari comuni in cui il failover manuale può essere utile sono:

**Seguire il modello di orologio**: se le applicazioni dispongono di modelli di traffico prevedibili in base all'ora del giorno, è possibile modificare periodicamente lo stato di scrittura nell'area geografica più attiva in base all'ora del giorno.

**Aggiornamento del servizio**: in alcuni casi la distribuzione globale di un'applicazione può comportare il reinstradamento del traffico a un'area diversa tramite Gestione traffico durante l'aggiornamento del servizio pianificato. Tale distribuzione può ora usare il failover manuale per mantenere lo stato di scrittura nell'area in cui ci sarà traffico attivo durante la finestra di aggiornamento del servizio.

**Continuità aziendale e ripristino di emergenza, BCDR, e Disponibilità elevata e ripristino di emergenza, HADR**: la maggior parte delle applicazioni aziendali include test di continuità aziendale come parte del processo di sviluppo e rilascio. I test BCDR e HADR sono spesso un passaggio importante nelle certificazioni di conformità e garantiscono la disponibilità del servizio in caso di interruzioni del servizio a livello regionale. Per testare la conformità BCDR delle applicazioni che usano Cosmos DB per l'archiviazione, attivare un failover manuale dell'account Cosmos DB e/o aggiungendo e rimuovendo un'area in modo dinamico.

In questo articolo è stato illustrato come funzionano i failover manuali e automatici in Cosmos DB e come è possibile configurare gli account Cosmos DB e le applicazioni in modo che siano disponibili a livello globale. Usando il supporto per la replica globale di Cosmos DB, è possibile migliorare la latenza end-to-end e assicurare la disponibilità elevata anche in caso di errori di un'area. 

## <a id="NextSteps"></a>Passaggi successivi
* Informazioni su come Cosmos DB supporta la [distribuzione globale](distribute-data-globally.md)
* Informazioni sulla [coerenza globale con Azure Cosmos DB](consistency-levels.md)
* Sviluppare in più aree usando l'[API DocumentDB](../cosmos-db/tutorial-global-distribution-documentdb.md) di Azure Cosmos DB
* Altre informazioni su come compilare [architetture di scrittura in più aree](multi-region-writers.md) con Azure DocumentDB

