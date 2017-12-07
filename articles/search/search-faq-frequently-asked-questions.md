---
title: Domande frequenti su Ricerca di Azure | Microsoft Docs
description: Risposte alle domande frequenti sul servizio Ricerca di Microsoft Azure
services: search
author: HeidiSteen
manager: jhubbard
ms.service: search
ms.technology: search
ms.topic: article
ms.date: 08/03/2017
ms.author: heidist
ms.openlocfilehash: dcd66991375a9f063345cda20c69f6a41c512ed2
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Ricerca di Azure: Domande frequenti
 
 Questo articolo offre le risposte alle domande frequenti sui concetti, il codice e gli scenari correlati a Ricerca di Azure.

## <a name="platform"></a>Piattaforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>In cosa si distingue Ricerca di Azure dalla ricerca full-text nel sistema di gestione di database (DBMS)?

Ricerca di Azure supporta più origini dati, [l'analisi linguistica per molti linguaggi](https://docs.microsoft.com/rest/api/searchservice/language-support), [l'analisi personalizzata per input di dati interessanti e insoliti](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), i controlli di classificazione delle ricerche mediante i [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) e le funzionalità di esperienza utente, ad esempio i completamenti automatici, l'evidenziazione dei riscontri e l'esplorazione in base a facet. Include anche altre funzionalità, come i sinonimi e la sintassi di query avanzata, ma queste non sono in genere funzionalità di differenziazione del servizio.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Qual è la differenza tra Ricerca di Azure ed Elasticsearch?

Quando si confrontano le tecnologie di ricerca, i clienti spesso chiedono informazioni sulle specifiche di Ricerca di Azure in confronto a Elasticsearch. I clienti che preferiscono Ricerca di Azure a Elasticsearch per i propri progetti di applicazioni di ricerca lo fanno in genere per usufruire di un'attività chiave che è stata semplificata o perché necessitano dell'integrazione incorporata con altre tecnologie Microsoft:

+ Ricerca di Azure è un servizio cloud completamente gestito con contratti di servizio (SLA) al 99,9% quando offerto in provisioning con una ridondanza sufficiente (2 repliche per l'accesso in lettura, 3 repliche per l'accesso in lettura e scrittura).
+ I [processori del linguaggio naturale](https://docs.microsoft.com/rest/api/searchservice/language-support) Microsoft offrono funzioni di analisi linguistica all'avanguardia.  
+ Gli [indicizzatori di Ricerca di Azure](search-indexer-overview.md) possono eseguire ricerche di un'ampia gamma di origini dati di Azure per l'indicizzazione iniziale e incrementale.
+ Se si desidera una risposta rapida alle fluttuazioni nei volumi di query o di indicizzazione, è possibile usare i [dispositivi di scorrimento](search-manage.md#scale-up-or-down) nel portale di Azure o eseguire uno [script di PowerShell](search-manage-powershell.md), evitando direttamente la gestione delle partizioni.  
+ Le [funzionalità di assegnazione di punteggi e di ottimizzazione](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) forniscono i mezzi per influenzare i punteggi della classificazione delle ricerche, oltre a ciò che il semplice motore di ricerca può offrire. 

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>È possibile sospendere il servizio Ricerca di Azure e interrompere la fatturazione?

Non è possibile sospendere il servizio. Quando viene creato il servizio, vengono allocate risorse di calcolo e di archiviazione per l'uso esclusivo. Non è possibile rilasciare e recuperare le risorse su richiesta. 

## <a name="indexing-operations"></a>Operazioni di indicizzazione

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>È possibile eseguire il backup e il ripristino (o il download e lo spostamento) degli indici o degli snapshot dell'indice?

Sebbene sia possibile [ottenere una definizione di indice](https://docs.microsoft.com/rest/api/searchservice/get-index) in qualsiasi momento, non vi è alcuna funzionalità di estrazione indice, di snapshot di indice, di backup o ripristino per scaricare un indice *popolato* in esecuzione nel cloud in un sistema locale o spostarlo in un altro servizio Ricerca di Azure. 

Gli indici vengono compilati e popolati dal codice che si scrive e vengono eseguiti solo in Ricerca di Azure nel cloud. I clienti che desiderano spostare un indice in un altro servizio, in genere devono modificare il codice in modo da poter usare un nuovo endpoint e quindi eseguire di nuovo l'indicizzazione. Se si desidera avere la possibilità di acquisire uno snapshot o eseguire il backup di un indice, esprimere un voto in [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>È possibile indicizzare da repliche di database SQL (si applica agli [indicizzatori del database SQL di Azure](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))?

 Non esistono restrizioni all'utilizzo di repliche primarie o secondarie come origini dati quando si genera un indice da zero. L'aggiornamento di un indice con aggiornamenti incrementali (in base ai record modificati) richiede tuttavia la replica primaria. Questo requisito deriva dal database SQL, che garantisce il rilevamento delle modifiche solo nelle repliche primarie. Se si tenta di usare le repliche secondarie per un carico di lavoro di aggiornamento di indice, non c'è garanzia di ottenere tutti i dati.

## <a name="search-operations"></a>Operazioni di ricerca

### <a name="can-i-search-across-multiple-indexes"></a>È possibile eseguire ricerche tra più indici?

No, questa operazione non è supportata. La ricerca avviene sempre nell'ambito di un singolo indice.

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>È possibile limitare l'accesso ai dati delle ricerche in base all'identità dell'utente?

Ricerca di Azure non dispone di un modello di sicurezza basato sui ruoli che consenta l'accesso ai dati in base all'utente. L'autenticazione è con diritti completi o di sola lettura a livello di servizio. Alcuni clienti hanno implementato soluzioni basate sul [parametro di ricerca `$filter`](https://docs.microsoft.com/rest/api/searchservice/search-documents), ma è solo un espediente. Se si desidera questa funzionalità, esprimere un voto in [User Voice](https://feedback.azure.com/forums/263029-azure-search/category/86074-security).

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Perché vengono trovate zero corrispondenze con termini che sono sicuramente validi?

Non tutti sanno che ogni tipo di query supporta comportamenti di ricerca e livelli di analisi linguistiche diversi. La ricerca full-text, che è il carico di lavoro predominante, include una fase di analisi del linguaggio che scompone i termini in forme radice. Questo aspetto di analisi delle query esegue il cast di una rete più ampia alla ricerca di corrispondenze possibili, perché il termine in formato token corrisponde a un maggior numero di varianti.

Se si richiamano [altri tipi di query](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (ricerca con caratteri jolly, ricerca di corrispondenze parziali, ricerca per prossimità, suggerimenti, per citarne alcuni), non vi è alcuna analisi linguistica. I termini vengono aggiunti alla struttura di query così come sono. 

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Perché la ricerca classifica un punteggio costante o uguale a 1.0 per ogni occorrenza?

Per impostazione predefinita, ai risultati della ricerca vengono assegnati dei punteggi in base alle [proprietà statistiche dei termini corrispondenti](search-lucene-query-architecture.md#stage-4-scoring) e i risultati vengono ordinati dal punteggio più alto a quello più basso nel set dei risultati. Alcuni tipi di query (con carattere jolly, prefisso, regex) tuttavia contribuiscono sempre con un punteggio costante al punteggio complessivo del documento. Questo comportamento dipende dalla progettazione. Ricerca di Azure impone un punteggio costante per permettere alle corrispondenze trovate tramite l'espansione della query di essere incluse nei risultati, ma senza influire sulla classifica. 

Si supponga ad esempio che un input "viaggi*" in una ricerca con caratteri jolly produca corrispondenze come "viaggio", "viaggiatore" e "viaggiare". Data la natura di questi risultati, non è possibile dedurre ragionevolmente quali termini risultino più utili rispetto ad altri. Per questo motivo, verranno ignorate le frequenze di termini durante l'assegnazione dei punteggi dei risultati nelle query di tipo con caratteri jolly, prefisso e regex. Ai risultati della ricerca basati su un input parziale viene assegnato un punteggio costante per evitare la distorsione verso risultati potenzialmente imprevisti.

## <a name="design-patterns"></a>Modelli di progettazione

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Qual è l'approccio migliore per l'implementazione della ricerca localizzata?

La maggior parte dei clienti sceglie campi dedicati da una raccolta quando si tratta di supportare impostazioni locali (lingue) differenti nello stesso indice. I campi specifici delle impostazioni locali consentono di assegnare un analizzatore appropriato, ad esempio l'analizzatore francese di Microsoft a un campo contenente stringhe in francese. Viene anche semplificata l'applicazione dei filtri. Se si sa che una query viene avviata in una pagina fr-fr, è possibile limitare i risultati della ricerca a questo campo. In alternativa, è possibile creare un [profilo di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per assegnare al campo più peso relativo.

## <a name="next-steps"></a>Passaggi successivi

La domanda riguarda una funzione o una funzionalità mancante? Richiedere la funzionalità sul [sito Web User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Vedere anche

 [StackOverflow: Ricerca di Azure](https://stackoverflow.com/questions/tagged/azure-search)   
 [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md)  
 [Che cos'è la Ricerca di Azure?](search-what-is-azure-search.md)

 
