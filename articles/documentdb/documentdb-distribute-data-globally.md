---
title: Distribuire i dati a livello globale con DocumentDB | Documentazione Microsoft
description: Informazioni sulla replica geografica a livello globale, sul failover e sul ripristino dei dati usando i database globali di Azure DocumentDB, un servizio database NoSQL completamente gestito.
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 70ccae77af2ace919e01a595a3e61711be2f760d
ms.openlocfilehash: f7ae58ff1dd500e130aa6a5b362065b1a304b7aa


---
# <a name="distribute-data-globally-with-documentdb"></a>Distribuire i dati a livello globale con DocumentDB

Azure DocumentDB è progettato per soddisfare le esigenze delle applicazioni IoT costituite da milioni di dispositivi distribuiti a livello globale e da applicazioni scalabili Internet che offrono agli utenti di tutto il mondo esperienze a risposta elevata. Questi sistemi di database devono affrontare la sfida di ottenere un accesso a bassa latenza ai dati dell'applicazione da più aree geografiche con garanzie di coerenza e disponibilità di dati ben definite. Come un sistema di database distribuito a livello globale, DocumentDB semplifica la distribuzione globale dei dati, offrendo account di database con più aree e completamente gestiti, che forniscono compromessi espliciti tra coerenza, disponibilità e prestazioni, nonché le relative garanzie. Gli account del database DocumentDB offrono disponibilità elevata, latenze di pochi millisecondi, più [livelli di coerenza ben definiti][consistency], failover regionale trasparente con API multihosting e la possibilità di ridimensionare in modo flessibile la velocità effettiva e le risorse di archiviazione in tutto il mondo. 

Si consiglia di iniziare guardando il video seguente, in cui Karthik Raman spiega la distribuzione geografica con Azure DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]
> 
> 

## <a name="configuring-multi-region-accounts"></a>Configurazione di account in più aree
La configurazione dell'account DocumentDB per la scalabilità in tutto il mondo richiede meno di un minuto con il [portale di Azure](documentdb-portal-global-replication.md). È sufficiente selezionare il livello di coerenza giusto tra i diversi livelli di coerenza ben definiti supportati e associare tutte le aree di Azure desiderate al proprio account di database. I livelli di coerenza di DocumentDB forniscono compromessi espliciti tra le prestazioni e la garanzia di coerenza specifiche. 

![DocumentDB offre numerosi modelli di coerenza, ampi e ben definiti, tra cui scegliere][1]

DocumentDB offre numerosi modelli di coerenza, ampi e ben definiti, tra cui scegliere.

La scelta del livello di coerenza giusto dipende dalla garanzia di coerenza dei dati di cui l'applicazione ha bisogno. DocumentDB replica in automatico i dati in tutte le aree specificate e garantisce la coerenza selezionata per l'account di database. 

## <a name="using-multi-region-failover"></a>Uso del failover in più aree
Azure DocumentDB può effettuare il failover degli account di database in più aree di Azure in modo trasparente. Le nuove [API multihosting][developingwithmultipleregions] garantiscono che l'app possa continuare a usare un endpoint logico senza interruzioni dovute al failover. Il failover è controllato dall'utente, che usufruisce della flessibilità necessaria per spostare l'account di database nel caso in cui si verifichi una delle possibili condizioni di errore, che comprendono errori dell'applicazione, dell'infrastruttura, del servizio o errori relativi all'area (reali o simulati). In caso di errore dell'area di DocumentDB, il servizio eseguirà il failover trasparente dell'account di database e l'applicazione continuerà ad accedere ai dati senza perderne la disponibilità. DocumentDB offre [contratti di servizio con disponibilità del 99,99%][sla], ma è comunque possibile testare le proprietà di disponibilità completa dell'applicazione simulando un errore dell'area, [a livello di codice][arm] o tramite il portale di Azure.

## <a name="scaling-across-the-planet"></a>Scalabilità a livello globale
DocumentDB consente di eseguire il provisioning indipendente della velocità effettiva e dell'uso dell'archiviazione per ogni raccolta di DocumentDB su qualsiasi scala e a livello globale in tutte le aree associate all'account di database. La raccolta di DocumentDB viene automaticamente distribuita a livello globale e gestita in tutte le aree associate all'account di database. Le raccolte nell'account di database possono essere distribuite in tutte le aree di Azure in cui [è disponibile il servizio DocumentDB][serviceregions]. 

La velocità effettiva acquistata e lo spazio di archiviazione usato per ogni raccolta di DocumentDB vengono sottoposti a provisioning automatico e uniforme in tutte le aree. In questo modo è possibile ridimensionare facilmente l'applicazione in tutto il mondo [pagando solo la velocità effettiva e lo spazio di archiviazione usati in ogni ora][pricing]. Se ad esempio è stato eseguito il provisioning di 2 milioni di unità richieste per una raccolta di DocumentDB, ognuna delle aree associate all'account di database ottiene 2 milioni di unità richieste per la raccolta specifica, come illustrato di seguito.

![Esecuzione della scalabilità della velocità effettiva per una raccolta di DocumentDB in quattro aree][2]

DocumentDB assicura latenze di lettura inferiori a 10 ms e latenze di scrittura inferiori a 15 ms in P99. Le richieste di lettura non superano mai i confini dei data center per garantire i [requisiti di coerenza selezionati][consistency]. Le operazioni di scrittura sono sempre sottoposte a un quorum locale prima che vengano confermate ai client. Ogni account di database è configurato con la priorità dell'area di scrittura. L'area designata con priorità più elevata verrà usata come area di scrittura corrente per l'account. Tutti gli SDK indirizzeranno le scritture degli account di database in modo trasparente sull'area di scrittura corrente. 

Infine, dato che DocumentDB è completamente [indipendente dallo schema][vldb], non è necessario preoccuparsi della gestione o dell'aggiornamento degli schemi o degli indici secondari tra i diversi data center. Le [query SQL][sqlqueries] continuano a funzionare nonostante l'evoluzione costante dei modelli applicativi e di dati. 

## <a name="enabling-global-distribution"></a>Abilitazione della distribuzione globale
È possibile distribuire i dati in locale o a livello globale associando l'account di database DocumentDB con una o più aree di Azure. È possibile aggiungere o rimuovere aree all'account di database account in qualsiasi momento. Per abilitare la distribuzione globale tramite il portale, vedere [Come eseguire la replica del database globale di DocumentDB con il portale di Azure](documentdb-portal-global-replication.md). Per abilitare la distribuzione globale a livello di codice, vedere [Sviluppo con account DocumentDB in più aree](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla distribuzione globale dei dati con DocumentDB, vedere gli articoli seguenti:

* [Provisioning della velocità effettiva e dello spazio di archiviazione per una raccolta][throughputandstorage]
* [API multihosting tramite gli SDK REST, .NET, Java, Python e Node][developingwithmultipleregions]
* [Livelli di coerenza in DocumentDB][consistency]
* [Contratti di servizio relativi alla disponibilità][sla]
* [Gestione di un account di database][manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md




<!--HONumber=Dec16_HO3-->


