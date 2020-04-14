---
title: Domande frequenti
titleSuffix: Azure Cognitive Search
description: Risposte alle domande comuni sul servizio Ricerca cognitiva di Microsoft Azure, un servizio di ricerca ospitato nel cloud in Microsoft Azure.Get answers to common questions about Microsoft Azure Cognitive Search service, a cloud hosted search service on Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d7ba62c795e23e41a1947def77300ffe5d2cc010
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262452"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Ricerca cognitiva di Azure - domande frequenti (FAQ)Azure Cognitive Search - frequently asked questions (FAQ)

 Risposte alle domande frequenti su concetti, codice e scenari correlati a Ricerca cognitiva di Azure.Find answers to commonly asked questions about concepts, code, and scenarios related to Azure Cognitive Search.

## <a name="platform"></a>Piattaforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>In che modo Ricerca cognitiva di Azure è diversa dalla ricerca full-text nel sistema DBMS?

Ricerca cognitiva di Azure supporta più origini dati, [l'analisi linguistica per molte lingue,](https://docs.microsoft.com/rest/api/searchservice/language-support) [l'analisi personalizzata per input di dati interessanti e insoliti,](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)i controlli di classificazione della ricerca tramite i profili di [punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)e le funzionalità dell'esperienza utente, ad esempio typeahead, evidenziazione dei risultati e esplorazione in base a facet. Include anche altre funzionalità, come i sinonimi e la sintassi di query avanzata, ma queste non sono in genere funzionalità di differenziazione del servizio.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>È possibile sospendere il servizio Ricerca cognitiva di Azure e interrompere la fatturazione?

Non è possibile sospendere il servizio. Quando viene creato il servizio, vengono allocate risorse di calcolo e di archiviazione per l'uso esclusivo. Non è possibile rilasciare e recuperare le risorse su richiesta.

## <a name="indexing-operations"></a>Operazioni di indicizzazione

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Spostare, eseguire il backup e ripristinare gli indici o gli snapshot dell'indice?

Durante la fase di sviluppo, è possibile spostare l'indice tra i servizi di ricerca. Ad esempio, è possibile usare un piano tariffario Basic o Free per sviluppare l'indice e quindi si desidera spostarlo al livello Standard o superiore per l'utilizzo in produzione. 

In alternativa, è possibile eseguire il backup di uno snapshot dell'indice nei file che possono essere utilizzati per ripristinarlo in un secondo momento. 

È possibile eseguire tutte queste operazioni con il codice di esempio **index-backup-restore** in questo repository di esempio di Ricerca cognitiva di [Azure .NET.](https://github.com/Azure-Samples/azure-search-dotnet-samples) 

È anche possibile ottenere una [definizione](https://docs.microsoft.com/rest/api/searchservice/get-index) di indice in qualsiasi momento usando l'API REST di Ricerca cognitiva di Azure.You can also get an index definition at any time using the Azure Cognitive Search REST API.

Nel portale di Azure non è attualmente disponibile alcuna funzionalità predefinita per l'estrazione, lo snapshot o il ripristino degli backup degli indici. Tuttavia, stiamo valutando l'aggiunta della funzionalità di backup e ripristino in una versione futura. Se vuoi mostrare il tuo supporto per questa funzione, vota [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>È possibile ripristinare l'indice o il servizio dopo l'eliminazione?

No, se si elimina un servizio o un indice di Ricerca cognitiva di Azure, non è possibile recuperarlo. Quando si elimina un servizio Ricerca cognitiva di Azure, tutti gli indici nel servizio vengono eliminati in modo permanente. Se si elimina un gruppo di risorse di Azure che contiene uno o più servizi di Ricerca cognitiva di Azure, tutti i servizi vengono eliminati definitivamente.  

Ricreare risorse quali indici, indicizzatori, origini dati e set di competenze richiede la ricreazione dal codice. 

Per ricreare un indice, è necessario reindicizzare i dati da origini esterne. Per questo motivo, è consigliabile mantenere una copia master o un backup dei dati originali in un altro archivio dati, ad esempio Database SQL di Azure o Cosmos DB.

In alternativa, è possibile usare il codice di esempio **index-backup-restore** in questo repository di [esempio .NET di Ricerca cognitiva](https://github.com/Azure-Samples/azure-search-dotnet-samples) di Azure per eseguire il backup di una definizione di indice e di uno snapshot dell'indice in una serie di file JSON. Successivamente, è possibile utilizzare lo strumento e i file per ripristinare l'indice, se necessario.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>È possibile indicizzare da repliche di database SQL (si applica agli [indicizzatori del database SQL di Azure](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))?

Non esistono restrizioni all'utilizzo di repliche primarie o secondarie come origini dati quando si genera un indice da zero. L'aggiornamento di un indice con aggiornamenti incrementali (in base ai record modificati) richiede tuttavia la replica primaria. Questo requisito deriva dal database SQL, che garantisce il rilevamento delle modifiche solo nelle repliche primarie. Se si tenta di usare le repliche secondarie per un carico di lavoro di aggiornamento di indice, non c'è garanzia di ottenere tutti i dati.

## <a name="search-operations"></a>Operazioni di ricerca

### <a name="can-i-search-across-multiple-indexes"></a>È possibile eseguire ricerche tra più indici?

No, questa operazione non è supportata. La ricerca avviene sempre nell'ambito di un singolo indice.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>È possibile limitare l'accesso all'indice di ricerca in base all'identità dell'utente?

È possibile implementare [ filtri di sicurezza](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) con `search.in()` filtro. Il filtro si integra bene con i [servizi di gestione delle identità come Azure Active Directory(AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) per limitare i risultati della ricerca in base all'appartenenza al gruppo utente definita.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Perché vengono trovate zero corrispondenze con termini che sono sicuramente validi?

Non tutti sanno che ogni tipo di query supporta comportamenti di ricerca e livelli di analisi linguistiche diversi. La ricerca full-text, che è il carico di lavoro predominante, include una fase di analisi del linguaggio che suddivide i termini in moduli radice. Questo aspetto di analisi delle query esegue il cast di una rete più ampia alla ricerca di corrispondenze possibili, perché il termine in formato token corrisponde a un maggior numero di varianti.

Le query con caratteri jolly, fuzzy e regex, tuttavia, non vengono analizzate come query di un termine o una frase normale e possono causare un richiamo ridotto se la query non corrisponde alla forma della parola analizzata nell'indice di ricerca. Per ulteriori informazioni sull'analisi e l'analisi delle query, vedere [Architettura delle query](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Le ricerche con caratteri jolly sono lente.

La maggior parte delle query di ricerca con caratteri jolly, ad esempio prefisso, fuzzy e regex, viene riscritta internamente con termini corrispondenti nell'indice di ricerca. Questa elaborazione aggiuntiva di analisi dell'indice di ricerca aumenta la latenza. Inoltre, le query di ricerca ampie che, `a*` ad esempio, dovranno probabilmente essere riscritte con numerosi termini, possono essere molto lente. Per ricerche con caratteri jolly efficienti, valutare la definizione di un [analizzatore personalizzato](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Perché la ricerca classifica un punteggio costante o uguale a 1.0 per ogni occorrenza?

Per impostazione predefinita, ai risultati della ricerca vengono assegnati dei punteggi in base alle [proprietà statistiche dei termini corrispondenti](search-lucene-query-architecture.md#stage-4-scoring) e i risultati vengono ordinati dal punteggio più alto a quello più basso nel set dei risultati. Alcuni tipi di query (con carattere jolly, prefisso, regex) tuttavia contribuiscono sempre con un punteggio costante al punteggio complessivo del documento. Questo comportamento dipende dalla progettazione. Ricerca cognitiva di Azure impone un punteggio costante per consentire l'inserimento nei risultati delle corrispondenze trovate tramite l'espansione delle query, senza influire sulla classificazione.

Si supponga, ad esempio, che un input di "tour" in una ricerca con caratteri jolly produca corrispondenze in "tour", "tourettes" e "tourmaline". Data la natura di questi risultati, non è possibile dedurre ragionevolmente quali termini risultino più utili rispetto ad altri. Per questo motivo, verranno ignorate le frequenze di termini durante l'assegnazione dei punteggi dei risultati nelle query di tipo con caratteri jolly, prefisso e regex. Ai risultati della ricerca basati su un input parziale viene assegnato un punteggio costante per evitare la distorsione verso risultati potenzialmente imprevisti.

## <a name="design-patterns"></a>Modelli di progettazione

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Qual è l'approccio migliore per l'implementazione della ricerca localizzata?

La maggior parte dei clienti sceglie campi dedicati da una raccolta quando si tratta di supportare impostazioni locali (lingue) differenti nello stesso indice. I campi specifici delle impostazioni locali consentono di assegnare un analizzatore appropriato, ad esempio l'analizzatore francese di Microsoft a un campo contenente stringhe in francese. Viene anche semplificata l'applicazione dei filtri. Se si sa che una query viene avviata in una pagina fr-fr, è possibile limitare i risultati della ricerca a questo campo. In alternativa, è possibile creare un [profilo di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per assegnare al campo più peso relativo. Ricerca cognitiva di Azure supporta oltre [50 analizzatori linguistici](https://docs.microsoft.com/azure/search/search-language-support) tra cui scegliere.

## <a name="next-steps"></a>Passaggi successivi

La domanda riguarda una funzione o una funzionalità mancante? Richiedere la funzionalità sul [sito Web User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Vedere anche

 [StackOverflow: Azure Cognitive Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)  
 [Che cos'è la ricerca cognitiva di Azure?](search-what-is-azure-search.md)
