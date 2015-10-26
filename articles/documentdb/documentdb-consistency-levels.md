<properties 
	pageTitle="Livelli di coerenza in DocumentDB | Microsoft Azure" 
	description="Esaminare il modo in cui DocumentDB ha quattro livelli di coerenza con livelli di prestazioni associati per consentire agli sviluppatori di applicazioni di compensare in modo prevedibile coerenza, disponibilità e latenza." 
	keywords="eventual consistency, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2015" 
	ms.author="mimig"/>

# Utilizzo dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni di DocumentDB

Gli sviluppatori devono spesso far fronte alla sfida posta dalla scelta tra due estremi: coerenza assoluta e coerenza finale. In realtà vi sono diversi stadi intermedi di coerenza tra questi due estremi. Nella maggior parte degli scenari reali, le applicazioni traggono vantaggio dai compromessi granulari tra coerenza, disponibilità e latenza. DocumentDB offre quattro livelli di coerenza ben definiti, a cui sono associati dei livelli di prestazioni. Ciò consente agli sviluppatori di applicazioni di compensare in modo prevedibile coerenza, disponibilità e latenza.
 
Tutte le risorse di sistema, inclusi gli account di database, i database, le raccolte, gli utenti e le autorizzazioni, sono sempre fortemente coerenti per letture e query. I livelli di coerenza si applicano unicamente alle risorse definite dall'utente. Per query e operazioni di lettura sulle risorse definite dall'utente, tra cui documenti, allegati, stored procedure, trigger e funzioni definite dall'utente, DocumentDB offre quattro livelli di coerenza distinti:

 - Coerenza assoluta
 - Coerenza obsolescenza limitata
 - Coerenza di sessione
 - Coerenza finale

Questi livelli di coerenza granulari e ben definiti permettono di ottenere compromessi efficaci tra coerenza, disponibilità e prestazioni e sono supportati da livelli di prestazioni prevedibili, che garantiscono risultati coerenti per l'applicazione.

## Livelli di coerenza per i database

È possibile configurare un livello di coerenza predefinito per l'account di database che si applica a tutte le raccolte (in tutti i database) nel proprio account di database. Per impostazione predefinita, tutte le letture e le query eseguite sulle risorse definite dall'utente useranno il livello di coerenza predefinito che è stato specificato nell'account di database. Tuttavia, è possibile abbassare il livello di coerenza di una richiesta di lettura/query specifica specificando l'intestazione della richiesta [x-ms-coerenza-level]. Il protocollo di replica di DocumentDB supporta quattro tipi di livelli di coerenza, descritti in breve qui di seguito.

>[AZURE.NOTE]L'override del livello di coerenza predefinito per le singole raccolte potrebbe essere supportato in una versione futura.

**Sicuro**: la coerenza assoluta garantisce come un'operazione di scrittura sia visibile solo dopo che viene eseguito il commit permanente per il quorum di maggioranza delle repliche. Per quanto riguarda la scrittura, o ne viene eseguito il commit in modo sincrono dalla replica primaria e dalla maggioranza delle repliche secondarie oppure viene interrotta. Una lettura viene sempre confermata dal quorum di lettura di maggioranza: un client non potrà mai rilevare una scrittura parziale o di cui non è stato eseguito il commit e garantisce sempre la lettura della scrittura confermata più recente.
 
La coerenza assoluta offre la garanzia totale sulla coerenza dei dati, ma il livello più basso di prestazioni in lettura e scrittura.

**Delimitata obsolescenza**: Coerenza obsolescenza delimitata garantisce il totale dell'ordine di propagazione delle operazioni di scrittura con la possibilità che legga il ritardo dietro le scrittura al massimo con i prefissi K. La lettura viene sempre confermata da un quorum di maggioranza di repliche. La risposta a una richiesta di lettura ne specifica il relativo aggiornamento (in termini di K).

L'obsolescenza associata offre un comportamento più prevedibile per la coerenza delle letture, con latenza minima per le scritture. Poiché le letture vengono confermate da un quorum di maggioranza, la latenza di lettura non è quella minima offerta dal sistema.

>[AZURE.NOTE]L’obsolescenza vincolata garantisce solo le letture monotona esplicite richieste di lettura. La risposta del server visualizzati per le richieste di scrittura non offre garanzie obsolescenza vincolato.

**Sessione**: a differenza dei modelli di coerenza globale offerti dai livelli di coerenza obsolescenza sicuri e vincolati, la coerenza di "sessione" è progettata per una sessione client specifico. La coerenza di sessione è di solito sufficiente in quanto fornisce letture e scritture monotoniche garantite, oltre alla capacità di leggere le proprie scritture. Una richiesta di lettura per la coerenza di sessione viene rilasciata a fronte di una replica che può gestire la versione richiesta dal cliente (parte del cookie di sessione).

La coerenza di sessione offre una coerenza prevedibile dei dati delle letture per una sessione, con latenza minima per le scritture. Anche la latenza delle letture è bassa, tranne in rari casi in cui la lettura viene gestita da una singola replica.

**Eventual**: l’eventuale coerenza è la coerenza più debole in cui un client possa ottenere i valori che sono meno recenti rispetto a quelli ha visto in precedenza, nel corso del tempo. In assenza di ulteriori scritture, alla fine le repliche all'interno del gruppo convergeranno. La richiesta di lettura viene gestita da qualsiasi indice secondario.

La coerenza finale rappresenta il livello più debole, ma offre la latenza più bassa sia per le letture sia le per scritture.

### Modifica del livello di coerenza del database

1.  Nel [portale di anteprima di Azure](https://portal.azure.com/), nell’indice fare clic su **Account DocumentDB**.

2. Nel pannello **DocumentDB account**, selezionare il database di account da modificare.

3. Nel pannello dell’account, nella sezione **configurazione**, fare clic sui**Uniformità predefinita**riquadro.

4. Nel pannello **Coerenza predefinita** selezionare il nuovo livello di coerenza e fare clic su **Salva**.

	![Cattura di schermata evidenziando il riquadro uniformità predefinita, le impostazioni di coerenza ed il pulsante Salva](./media/documentdb-consistency-levels/database-consistency-level.png)

## Livelli di coerenza per le query

Per impostazione predefinita, per le risorse definite dall'utente, il livello di coerenza delle query è identico a quello delle letture. Per impostazione predefinita, l'indice viene aggiornato in modo sincrono a ogni inserimento, sostituzione o eliminazione di un documento nella raccolta. Ciò permette alle query di rispettare lo stesso livello di coerenza delle letture di documenti. Benché DocumentDB sia ottimizzato per la scrittura e supporti volumi elevati di scritture di documenti, oltre a offrire la manutenzione sincrona dell'indice e la gestione di query coerenti, è possibile configurare determinate raccolte per l'aggiornamento differito dell'indice. L'indicizzazione differita migliora ulteriormente le prestazioni di scrittura ed è ideale per scenari di inserimento in blocco quando un carico di lavoro presenta principalmente un'intensa attività di lettura.

Modalità di indicizzazione|	Letture|	Query  
-------------|-------|---------
Coerente (impostazione predefinita)|	Selezionare tra assoluta, con obsolescenza associata, sessione o finale|	Selezionare tra assoluta, con obsolescenza associata, sessione o finale|
Differita|	Selezionare tra assoluta, con obsolescenza associata, sessione o finale|	Finale  

Come per le richieste di lettura, è possibile abbassare il livello di coerenza per una particolare richiesta di query specificando l'intestazione di richiesta x-ms-consistency-level.

## Passaggi successivi

Se si desidera eseguire ulteriori informazioni sui livelli di coerenza e i compromessi, è consigliabile che le risorse seguenti:

-	Doug Terry. Coerenza dei dati replicati illustrati attraverso il baseball.[http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Garanzie di sessione per i dati replicati con tipizzazione debole e coerente.[http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi. Svantaggi della coerenza di moderne distribuite Progettazione sistemi di Database: CAP è solo una parte del brano". [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) 
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistica delimitata obsolescenza (PBS) per pratiche quorum parziale.[http://vldb.org/pvldb/vol5/p776\_peterbailis\_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Eventuale coerente, in particolare.[http://allthingsdistributed.com/2008/12/eventually\_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
 

<!---HONumber=Oct15_HO3-->