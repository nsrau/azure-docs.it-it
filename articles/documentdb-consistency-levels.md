<properties title="Consistency levels in DocumentDB" pageTitle="Livelli di coerenza in DocumentDB" description="DocumentDB ha quattro livelli di coerenza con livelli di prestazioni associati per consentire agli sviluppatori di applicazioni di compensare in modo prevedibile coerenza, disponibilità e latenza." metaKeywords="Optional" services="documentdb" solutions="data-management" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="Optional" scriptId="Optional" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Livelli di coerenza in DocumentDB
Gli sviluppatori devono spesso far fronte alla sfida posta dalla scelta tra due estremi: coerenza assoluta e coerenza finale. La realtà è che esistono più stadi intermedi di coerenza tra questi due estremi. Nella maggior parte degli scenari reali, le applicazioni traggono vantaggio dai compromessi granulari tra consistenza, disponibilità e latenza. DocumentDB offre quattro livelli di coerenza ben definiti, a cui sono associati dei livelli di prestazioni. Ciò consente agli sviluppatori di applicazioni di compensare in modo prevedibile coerenza, disponibilità e latenza.  
 
Tutte le risorse di sistema, inclusi gli account di database, i database, le raccolte, gli utenti e le autorizzazioni, sono sempre fortemente coerenti per letture e query. I livelli di coerenza si applicano unicamente alle risorse definite dall'utente. Per query e operazioni di lettura sulle risorse definite dall'utente, tra cui documenti, allegati, stored procedure, trigger e UDF, DocumentDB offre quattro livelli di coerenza distinti, ovvero assoluta, con obsolescenza associata, sessione e finale. Questi livelli di coerenza granulari e ben definiti permettono di ottenere compromessi efficaci tra coerenza, disponibilità e latenza e sono supportati da livelli di prestazioni prevedibili, che garantiscono risultati coerenti per l'applicazione.   

##Livelli di coerenza
È possibile configurare il livello di coerenza predefinito sull'account di database che si applica a tutte le raccolte (in tutti i database) nel proprio account di database. Per impostazione predefinita, tutte le letture e le query inviate a fronte delle risorse definite dall'utente useranno il livello di coerenza predefinito che è stato specificato nell'account di database. È comunque possibile ridurre il livello di coerenza di una specifica richiesta di lettura/query specificando l'intestazione di richiesta [x-ms-consistency-level]. Il protocollo di replica di DocumentDB supporta quattro tipi di livelli di coerenza, descritti in breve qui di seguito. 

>[AZURE.NOTE] L'override del livello di coerenza predefinito per le singole raccolte potrebbe essere supportato in una versione futura.  

**Assoluta**: la coerenza assoluta garantisce che una scrittura sia visibile solo dopo che ne è stato eseguito il commit in modo permanente dal quorum di maggioranza delle repliche. Per quanto riguarda la scrittura, o ne viene eseguito il commit in modo sincrono dalla replica primaria e dal quorum di maggioranza delle repliche secondarie, oppure questa viene interrotta. Una lettura viene sempre confermata dal quorum di lettura di maggioranza: un client non potrà mai rilevare una scrittura parziale o di cui non è stato eseguito il commit e garantisce sempre la lettura della scrittura confermata più recente.   
 
La coerenza assoluta offre la garanzia totale sulla coerenza dei dati, ma il livello più basso di prestazioni in lettura e scrittura.  

**Obsolescenza associata**: La coerenza con obsolescenza associata garantisce l'ordine totale di propagazione delle scritture, ma comporta un potenziale ritardo delle letture in corrispondenza di gran parte dei prefissi K. La lettura viene sempre confermata da un quorum di maggioranza di repliche. La risposta a una richiesta di lettura ne specifica il relativo aggiornamento (in termini di KB).  

L'obsolescenza associata offre un comportamento molto più prevedibile per la coerenza della lettura pur consentendo scritture con latenza minima. Poiché le letture vengono confermate da un quorum di maggioranza, la latenza di lettura non è quella minima offerta dal sistema.    

**Sessione**: a differenza dei modelli di coerenza globale offerti dai livelli di coerenza assoluta e con obsolescenza associata, la coerenza di "sessione" è personalizzata per una specifica sessione del client. La coerenza di sessione è di solito sufficiente in quanto fornisce letture e scritture monotoniche garantite, oltre alla capacità di leggere le proprie scritture. Una richiesta di lettura per la coerenza di sessione viene rilasciata a fronte di una replica che può gestire la versione richiesta dal cliente (parte del cookie di sessione).  

La sessione offre una coerenza prevedibile dei dati di lettura pur consentendo scritture con latenza minima. Anche la latenza delle letture è bassa, tranne in rari casi in cui la lettura viene gestita da una singola replica.  

**Finale**: la coerenza finale è la forma più debole di coerenza, in cui un client può ottenere nel tempo valori obsoleti rispetto a quelli già visualizzati in passato. In assenza di ulteriori scritture, alla fine le repliche all'interno del gruppo convergeranno. La richiesta di lettura viene gestita da qualsiasi replica secondaria.  

La coerenza di lettura finale rappresenta il livello più debole, ma offre la latenza più bassa sia per le letture sia le per scritture. 

##Coerenza delle query
Per impostazione predefinita, per le risorse definite dall'utente, il livello di coerenza delle query è identico a quello delle letture. Per impostazione predefinita, l'indice è aggiornato in modo sincrono a ogni inserimento, sostituzione o eliminazione di un documento nella raccolta. Ciò permette alle query di rispettare lo stesso livello di coerenza delle letture di documenti. Benché DocumentDB sia ottimizzato per la scrittura e supporti volumi elevati di scritture di documenti, oltre a offrire la manutenzione sincrona dell'indice e la gestione di query coerenti, è possibile configurare determinate raccolte per l'aggiornamento differito dell'indice. L'indicizzazione differita migliora ulteriormente le prestazioni di scrittura ed è ideale per scenari di inserimento in blocco quando si ha un carico di lavoro con intensa attività di lettura.  

Modalità di indicizzazione|	Letture|	Query  
-------------|-------|---------
Coerente (impostazione predefinita)|	Selezionare tra assoluta, con obsolescenza associata, sessione o finale|	Selezionare tra assoluta, con obsolescenza associata, sessione o finale|
Differita|	Selezionare tra assoluta, con obsolescenza associata, sessione o finale|	Finale  

Come per le richieste di lettura, è possibile ridurre il livello di coerenza di una specifica richiesta di query specificando l'intestazione di richiesta [x-ms-consistency-level].  

##Riferimenti
-	Doug Terry. Replicated Data Consistency explained through baseball.   
[http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Session Guarantees for Weakly Consistent Replicated Data.   
[http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html](http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html)
-	Daniel Abadi. Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story".   
[http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) 
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums.   
[http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Eventual Consistent - Revisited.    
[http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
