---
title: Failover regionali in Azure DocumentDB | Documentazione Microsoft
description: "Informazioni sulla modalità di funzionamento di failover manuali e automatici con Azure DocumentDB."
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 0921464c10d5ca3d426a535d434eab6cf02013e6
ms.openlocfilehash: c234958f5fc1ba0dbcb727e18e733d13ad0c7e71


---
# <a name="regional-failovers-in-azure-documentdb"></a>Failover regionali in Azure DocumentDB
Azure DocumentDB semplifica la distribuzione globale dei dati, offrendo [account di database con più aree](documentdb-distribute-data-globally.md) e completamente gestiti, che forniscono compromessi espliciti tra coerenza, disponibilità e prestazioni, il tutto con le relative garanzie. Gli account DocumentDB offrono disponibilità elevata, latenze di pochi millisecondi, più [livelli di coerenza ben definiti](documentdb-consistency-levels.md), failover regionale trasparente con API multihosting e la possibilità di ridimensionare in modo flessibile la velocità effettiva e le risorse di archiviazione in tutto il mondo. 

Azure DocumentDB supporta sia failover espliciti sia failover basati su criteri che consentono di controllare il comportamento del sistema end-to-end in caso di errori. Questo articolo analizza i seguenti aspetti:

* Come funzionano i failover manuali in DocumentDB?
* Come funzionano i failover automatici in DocumentDB?
* In che modo è possibile usare i failover manuali in architetture applicative?

In questo video di Azure Friday, con Scott Hanselman e Karthik Raman, Principal Engineering Manager, sono disponibili altre informazioni sui failover regionali.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a name="a-idconfiguremultiregionapplicationsaconfiguring-multi-region-applications"></a><a id="ConfigureMultiRegionApplications"></a>Configurazione di applicazioni in più aree
Prima di approfondire le modalità di failover, esaminiamo in che modo è possibile configurare un'applicazione per sfruttare i vantaggi della disponibilità di più aree e assicurare la resilienza in caso di failover regionali.

* In primo luogo, distribuire l'applicazione in più aree
* Per garantire l'accesso a bassa latenza da ogni area in cui l'applicazione viene distribuita, configurare [l'elenco delle aree preferite](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) per ogni area tramite uno degli SDK supportati.

Il frammento seguente mostra come inizializzare un'applicazione in più aree. In questo caso l'account DocumentDB `contoso.documents.azure.com` è configurato con due aree: Stati Uniti occidentali ed Europa settentrionale. 

* L'applicazione viene distribuita nell'area Stati Uniti occidentali (usando ad esempio Servizi app di Azure) 
* Configurato con `West US` come la prima area preferita per letture a bassa latenza
* Configurato con `North Europe` come la seconda area preferita (per disponibilità elevata in caso di errori a livello di area)

In .NET questa configurazione è simile al frammento seguente:

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

L'applicazione viene distribuita anche nell'area Europa settentrionale con l'ordine di preferenza delle aree invertito. Ciò significa che l'Europa settentrionale viene specificata come prima area per le letture a bassa latenza e Stati Uniti occidentali come la seconda area preferita per disponibilità elevata in caso di errori a livello di area.

Il diagramma dell'architettura seguente illustra la distribuzione di un'applicazione in più aree in cui DocumentDB e l'applicazione sono configurati per essere disponibili in quattro aree geografiche di Azure.  

![Distribuzione globale di un'applicazione con Azure DocumentDB](./media/documentdb-regional-failovers/app-deployment.png)

Ora esaminiamo in che modo il servizio DocumentDB gestisce errori a livello di area tramite i failover automatici. 

## <a name="a-idautomaticfailoversaautomatic-failovers"></a><a id="AutomaticFailovers"></a>Failover automatici
Nel raro caso di un'interruzione del servizio di Azure a livello di area, DocumentDB attiva automaticamente i failover di tutti gli account DocumentDB presenti nell'area interessata. 

**Cosa accade se si verifica un'interruzione del servizio in un'area di lettura?**

Gli account DocumentDB con un'area di lettura in una delle aree interessate vengono automaticamente disconnessi dalla rispettiva area di scrittura e contrassegnati come offline. Gli SDK di DocumentDB implementano un protocollo di individuazione area che consente di rilevare automaticamente quando un'area è disponibile e reindirizzare le chiamate di lettura all'area successiva disponibile nell'elenco delle aree preferite. Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente. Non sono necessarie modifiche nel codice dell'applicazione per gestire i failover regionali. Durante l'intero processo, DocumentDB continua a rispettare garanzie di coerenza.

![Errori di un'area di lettura in Azure DocumentDB](./media/documentdb-regional-failovers/read-region-failures.png)

Dopo il ripristino dell'area interessata da un'interruzione del servizio, tutti gli account DocumentDB interessati nell'area vengono ripristinati automaticamente dal servizio. Gli account DocumentDB con un'area di lettura nell'area interessata si sincronizzano quindi automaticamente con l'area di scrittura corrente e passano in modalità online. Gli SDK di DocumentDB rilevano la disponibilità della nuova area e valutano se deve essere selezionata come l'area di lettura corrente in base all'elenco delle aree preferite configurato dall'applicazione. Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione.

**Cosa accade se si verifica un'interruzione del servizio in un'area di scrittura?**

Se l'area interessata è l'area di scrittura corrente per un determinato account Azure DocumentDB, questa viene automaticamente contrassegnata come offline. Viene quindi promossa un'area alternativa come area di scrittura per ogni account DocumentDB interessato. È possibile controllare completamente l'ordine di selezione dell'area per gli account DocumentDB tramite il portale di Azure o [a livello di codice](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Priorità di failover per Azure DocumentDB](./media/documentdb-regional-failovers/failover-priorities.png)

Durante i failover automatici, DocumentDB sceglie automaticamente l'area di scrittura successiva per un determinato account Azure DocumentDB in base all'ordine di priorità specificato. 

![Errori di un'area di scrittura in Azure DocumentDB](./media/documentdb-regional-failovers/write-region-failures.png)

Dopo il ripristino dell'area interessata da un'interruzione del servizio, tutti gli account DocumentDB interessati nell'area vengono ripristinati automaticamente dal servizio. 

* Gli account DocumentDB con l'area di scrittura precedente nell'area interessata rimangono in modalità offline, con disponibilità di lettura anche dopo il ripristino dell'area. 
* È possibile eseguire una query su questa area per calcolare eventuali scritture non replicate durante l'interruzione del servizio tramite il confronto con i dati disponibili nell'area di scrittura corrente. In base alle esigenze dell'applicazione, è possibile completare l'unione e/o la risoluzione dei conflitti ed eseguire il writeback del set finale di modifiche nell'area di scrittura corrente. 
* Dopo aver completato l'unione delle modifiche, è possibile ripristinare la modalità online dell'area interessata rimuovendola e aggiungendola nuovamente all'account DocumentDB. Dopo aver aggiunto di nuovo l'area, è possibile riconfigurarla come l'area di scrittura eseguendo un failover manuale tramite il portale di Azure o [a livello di codice](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

## <a name="a-idmanualfailoversamanual-failovers"></a><a id="ManualFailovers"></a>Failover manuali

Oltre ai failover automatici, è possibile modificare manualmente l'area di scrittura corrente di un determinato account DocumentDB in modo dinamico in una delle aree di lettura esistenti. I failover manuali possono essere avviati tramite il portale di Azure o [a livello di codice](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

I failover manuali assicurano una **perdita di dati pari a zero** e **nessuna perdita di disponibilità** e trasferiscono correttamente lo stato di scrittura dalla vecchia alla nuova area di scrittura per l'account DocumentDB specificato. Come nei failover automatici, durante i failover manuali Azure DocumentDB SDK gestisce automaticamente le modifiche dell'area di scrittura e garantisce che le chiamate vengano reindirizzate in automatico alla nuova area di scrittura. La gestione dei failover non richiede alcuna modifica del codice o della configurazione dell'applicazione. 

![Failover manuali in Azure DocumentDB](./media/documentdb-regional-failovers/manual-failovers.png)

Alcuni degli scenari comuni in cui il failover manuale può essere utile sono:

**Seguire il modello di orologio**: se le applicazioni dispongono di modelli di traffico prevedibili in base all'ora del giorno, è possibile modificare periodicamente lo stato di scrittura nell'area geografica più attiva in base all'ora del giorno.

**Aggiornamento del servizio**: in alcuni casi la distribuzione globale di un'applicazione può comportare il reinstradamento del traffico a un'area diversa tramite Gestione traffico durante l'aggiornamento del servizio pianificato. Tale distribuzione può ora usare il failover manuale per mantenere lo stato di scrittura nell'area in cui ci sarà traffico attivo durante la finestra di aggiornamento del servizio.

**Esercitazioni di continuità aziendale e ripristino di emergenza (BCDR)**: la maggior parte delle applicazioni aziendali include test di continuità aziendale come parte del processo di sviluppo e rilascio. I test BCDR sono spesso un passaggio importante nelle certificazioni di conformità e garantiscono la disponibilità del servizio in caso di interruzioni del servizio a livello dell'area. È possibile verificare la preparazione BCDR delle applicazioni che usano DocumentDB per l'archiviazione tramite attivazione di un failover manuale del proprio account DocumentDB e/o aggiunta e rimozione dinamica di un'area.

In questo articolo abbiamo esaminato la modalità di funzionamento di failover manuali e automatici in Azure DocumentDB e analizzato in che modo è necessario configurare l'account DocumentDB e le applicazioni affinché siano disponibili a livello globale. Tramite l'uso del supporto per replica globale di Azure DocumentDB, è possibile migliorare la latenza end-to-end e assicurare la disponibilità elevata anche in caso di errori di un'area. 

## <a name="a-idnextstepsanext-steps"></a><a id="NextSteps"></a>Passaggi successivi
* Altre informazioni sul modo in cui DocumentDB supporta la [distribuzione globale](documentdb-distribute-data-globally.md)
* Altre informazioni sulla [coerenza globale con DocumentDB](documentdb-consistency-levels.md)
* Sviluppare in più aree usando [Azure DocumentDB SDK](documentdb-developing-with-multiple-regions.md)
* Altre informazioni su come compilare [architetture di scrittura in più aree](documentdb-multi-region-writers.md) con Azure DocumentDB




<!--HONumber=Feb17_HO2-->


