<properties 
	pageTitle="Indicizzazione automatica in DocumentDB | Microsoft Azure" 
	description="Informazioni sul funzionamento dell’indicizzazione automatica in Azure DocumentDB." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="arramac"/>
	
# L'indicizzazione automatica in Azure DocumentDB

Questo articolo è un estratto del documento ["Indicizzazione senza schema con Azure DocumentDB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), che verrà presentato alla [41 Conferenza Internazionale VLDB](http://www.vldb.org/2015/) tra il 31 agosto e il 4 settembre 2015 e costituisce un’introduzione al funzionamento dell’indicizzazione in Azure DocumentDB.

Dopo la lettura di questo articolo, si sarà in grado di rispondere alle domande seguenti:

- In che modo DocumentDB deduce lo schema da un documento JSON?
- In che modo DocumentDB crea un indice di documenti diversi?
- In che modo DocumentDB esegue l'indicizzazione automatica su larga scala?

##<a id="HowDocumentDBIndexingWorks"></a> Come funziona l'indicizzazione di DocumentDB

[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) è un database privo di schema creato per JSON. Non prevede o richiede nessuno schema ne definizioni di indice secondario per indicizzare i dati su larga scala. Questa caratteristica consente di definire rapidamente modelli di dati di applicazioni ed eseguirne l'iterazione utilizzando DocumentDB. DocumentDB indicizza automaticamente tutte le proprietà dei documenti aggiunti a una raccolta in modo da renderle disponibili per l'esecuzione di query. L’indicizzazione automatica consente di archiviare documenti che appartengono a schemi completamente arbitrari senza doversi preoccupare degli schemi o degli indici secondari.

Con l'obiettivo di eliminare la mancata corrispondenza tra il database e i modelli di programmazione di applicazioni, DocumentDB sfrutta la semplicità di JSON e la sua mancanza di una definizione dello schema. Non fa ipotesi sui documenti e consente ai documenti all'interno di una raccolta di DocumentDB di variare nello schema, oltre ai valori specifici di istanza. A differenza di altri database di documenti, il motore di database di DocumentDB opera direttamente a livello di grammatica JSON, rimanendo agnostico al concetto di uno schema di documento e unendo valori di struttura e d’istanza dei documenti. Questo, a sua volta, gli consente di indicizzare automaticamente documenti senza schema o indici secondari.

L'indicizzazione di DocumentDB sfrutta il fatto che la grammatica JSON consente la **rappresentazione dei documenti come strutture ad albero**. Per rappresentare un documento JSON come struttura ad albero, è necessario creare un nodo principale fittizio come elemento padre per il resto dei nodi sottostanti nel documento. Ogni etichetta che include gli indici di matrice in un documento JSON diventa un nodo dell'albero. La figura seguente illustra un documento JSON di esempio e la rappresentazione ad albero corrispondente.

>[AZURE.NOTE]Poiché JSON è autodescrittivo ovvero ogni documento include schema (metadati) e dati, ad esempio `{"locationId": 5, "city": "Moscow"}` rivela che sono disponibili due proprietà `locationId` e `city` che esse hanno valori numerici e di proprietà di stringa. DocumentDB è in grado di dedurre lo schema dei documenti e indicizzarli quando vengono inseriti o sostituiti, senza dover definire indici secondari o schemi di indice.


**Documenti JSON come strutture ad albero:**

![Documenti come strutture ad albero](media/documentdb-indexing/DocumentsAsTrees.png)

Ad esempio, nell'esempio illustrato in precedenza:

- La proprietà JSON `{"headquarters": "Belgium"}` nell'esempio precedente corrisponde al percorso /headquarters/Belgium.
- La matrice JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` corrisponde ai percorsi `/exports/[]/city/Moscow` e `/exports/[]/city/Athens`.

Con l'indicizzazione automatica, (1) ogni percorso in un albero di documenti viene indicizzato (a meno che lo sviluppatore abbia configurato in modo esplicito i criteri di indicizzazione per escludere determinati modelli di percorso). (2) ogni aggiornamento di un documento per una raccolta di DocumentDB comporta l'aggiornamento della struttura dell'indice (ad esempio, causa aggiunta o rimozione di nodi). Uno dei requisiti principali dell’indicizzazione automatica dei documenti è garantire che il costo per indicizzare e eseguire la query di un documento con struttura di nidificazione, ad esempio 10 livelli, sia uguale a quello di un documento JSON flat costituito da coppie chiave-valore a solo un livello di profondità. Pertanto una rappresentazione in forma normalizzata del percorso è la base su cui vengono creati sottosistemi di indicizzazione automatica e di query.

Un'importante implicazione del trattamento di entrambi i valori di schema e d'istanza in modo uniforme in termini di percorsi è che, logicamente come i singoli documenti, un indice dei due documenti ha mostrato che mantiene una mappa tra percorsi e gli ID di documento che contengono tale percorso possono anche essere rappresentati come una struttura ad albero. DocumentDB utilizza questo fatto per creare una struttura di indice ad albero che viene costruita unendo tutte le strutture ad albero che rappresentano i singoli documenti all'interno della raccolta. La struttura dell'indice nelle collezioni di DocumentDB aumenta nel tempo man mano che nuovi documenti vengono aggiunti o aggiornati alla raccolta.


**Indice di DocumentDB come struttura ad albero:**

![Indice come una struttura ad albero](media/documentdb-indexing/IndexAsTree.png)

Nonostante sia privo di schema, l’SQL di DocumentDB e i linguaggi di query di JavaScript forniscono proiezioni relazionali e filtri, navigazione gerarchica tra documenti e la chiamata di funzioni definite dall'utente interamente scritte in JavaScript. La fase di esecuzione delle query di DocumentDB è in grado di supportare tali query, poiché può operare direttamente sulla rappresentazione della struttura di indice dei dati.

I criteri di indicizzazione predefiniti automaticamente indicizzano tutte le proprietà di tutti i documenti e forniscono query coerenti (ovvero l'indice viene aggiornato in modo sincrono con la scrittura del documento). In che modo DocumentDB supporta aggiornamenti consistenti per la struttura dell'indice su larga scala? DocumentDB utilizza tecniche di manutenzione dell’indice con scrittura ottimizzata, blocco libero e log strutturato. Questo significa che DocumentDB supporta un volume consistente di scritture veloci e al tempo stesso l'esecuzione di query coerenti.

L'indicizzazione di DocumentDB è progettata per l'efficienza di archiviazione e per gestire multi-tenancy. Per conseguire l'efficienza dei costi, le risorse di archiviazione su disco dell'indice sono vincolate e prevedibili. Gli aggiornamenti dell'indice vengono eseguiti entro i limiti delle risorse di sistema allocate per la raccolta di DocumentDB.

##<a name="NextSteps"></a> Passaggi successivi
- Scaricare ["Indicizzazione senza schema con Azure DocumentDB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) che verrà presentato alla 41a Conferenza Internazionale VLDB, 31 agosto - 4 settembre 2015.
- [Eseguire query con DocumentDB SQL](documentdb-sql-query.md)
- Informazioni su come personalizzare l'indice di DocumentDB [qui](documentdb-indexing-policies.md)
 

<!---HONumber=Oct15_HO3-->