<properties 
	pageTitle="Come implementare l'esplorazione in base a facet in Ricerca di Azure | Microsoft Azure | categorie di esplorazione di ricerca" 
	description="Aggiungere Esplorazione in base a facet alle applicazioni che si integrano con la Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="08/08/2016" 
	ms.author="heidist"/>

#Come implementare l'esplorazione in base a facet in Ricerca di Azure

L'esplorazione in base a facet è un meccanismo di filtro che consente un'esplorazione drill-down mirata nelle applicazioni di ricerca. Sebbene il termine esplorazione in base a facet sia poco noto, si tratta quasi certamente di una modalità già usata in precedenza. Come illustrato nell'esempio seguente, l'esplorazione in base a facet non è rappresentata dalle categorie usate per filtrare i risultati.

## Aspetto

 ![][1]
  
I facet consentono di trovare ciò che si sta cercando con una percentuale di successo accertata. Per gli sviluppatori i facet consentono di esporre i criteri di ricerca più utili per lo spostamento del corpo di ricerca. Nelle applicazioni di vendita online, l'esplorazione in base a facet si basa spesso sulle marche, sui reparti (scarpe per bambini), sulla taglia, sul prezzo, sulla popolarità e sulle classificazioni.

L'implementazione dell'esplorazione in base a facet differisce fra le tecnologie di ricerca e può essere molto complessa. Nella Ricerca di Azure, l'esplorazione in base a facet viene creata in fase di query, usando campi con gli attributi specificati in precedenza nello schema. Le query compilate dall'applicazione devono consentire l'invio dei *parametri di query facet* per ricevere i valori di filtro facet disponibili per il set di risultati dei documenti. Per ridurre effettivamente il set di risultati dei risultati, l'applicazione deve applicare un'espressione `$filter`.

In termini di sviluppo di applicazioni, la scrittura di codice che crea query costituisce la maggior parte del lavoro. Molti dei comportamenti dell'applicazione desiderata dall'esplorazione in base a facet viene fornita dal servizio, incluso il supporto incorporato per l'impostazione degli intervalli e il recupero dei conteggi relativi ai risultati del facet. Il servizio include anche valori predefiniti appropriati che consentono di evitare le strutture di spostamento ingombranti.

In questo articolo sono contenute le sezioni seguenti:

- [Come compilare la soluzione](#howtobuildit)
- [Creare il livello di presentazione](#presentationlayer)
- [Compilare l'indice](#buildindex)
- [Controllo della qualità dei dati](#checkdata)
- [Creare la query](#buildquery)
- [Suggerimenti su come controllare l'esplorazione in base a facet](#tips)
- [Esplorazione in base a facet basata sui valori di intervallo](#rangefacets)
- [Esplorazione in base a facet basata su GeoPoint](#geofacets)
- [Provare il servizio](#tryitout)

##Perché usarlo
Le applicazioni di ricerca più efficaci dispongono di più modelli di interazione oltre a una casella di ricerca. L'esplorazione in base a facet è un punto di partenza alternativo per la ricerca, che offre una valida alternativa all'immissione manuale di espressioni di ricerca complesse.

##Conoscere i concetti fondamentali

Per gli utenti che si sono appena approcciati allo sviluppo di funzionalità di ricerca, il modo migliore per concepire l'esplorazione in base a facet è il fatto che mostra le possibilità di ricerca mirata. Si tratta di un tipo di esperienza di ricerca drill-down, basata su filtri predefiniti usati per limitare rapidamente i risultati di ricerca tramite semplici azioni in un solo clic.

**Modello di interazione**

L'esperienza di ricerca dell'esplorazione in base a facet è iterativa. Pertanto, è opportuno iniziare con il concepirlo come una sequenza di query create in risposta ad azioni dell'utente.

Il punto di partenza è una pagina dell'applicazione che consente un'esplorazione in base a facet, in genere sul perimetro. L'esplorazione in base a facet è spesso una struttura ad albero, con caselle di controllo per ogni valore o testo selezionabile.

1.	Una query inviata a Ricerca di Azure specifica la struttura dell'esplorazione in base a facet tramite uno o più parametri di query del facet. Ad esempio, la query potrebbe includere `facet=Rating`, ad esempio con l’opzione `:values` o `:sort` per specificare ulteriormente la presentazione.
2.	Il livello di presentazione consente di eseguire il rendering di una pagina di ricerca che permette l'esplorazione in base ai facet specificati nella richiesta.
3.	A fronte di una struttura di esplorazione in base a facet che include la classificazione, l'utente fa clic su "4" per indicare che devono essere visualizzati solo i prodotti con una valutazione pari a 4 stelle o superiore.
4.	In risposta, l'applicazione invia una query che include `$filter=Rating ge 4`
5.	Il livello di presentazione aggiorna la pagina con un set di risultati ridotto, contenente solo gli elementi che soddisfano i nuovi criteri (in questo caso, i prodotti valutati con 4 stelle e superiore).

Il facet è un parametro di query, da non confondere con l'input della query. Non viene mai usato come criterio di selezione in una query. Al contrario, occorre concepire i parametri di query di un facet come input per la struttura di navigazione che viene restituita nella risposta. Per ogni parametro di query del facet fornito, Ricerca di Azure restituirà quanti documenti sono presenti nei risultati parziali per ogni valore del facet.

Si noti il `$filter` nel passaggio 4. Si tratta di un aspetto importante dell'esplorazione in base a facet. Sebbene facet e filtri siano indipendenti nell'API, saranno necessari entrambi per offrire l'esperienza desiderata.

**Modelli di progettazione**

Nel codice dell'applicazione, il modello consiste nell'usare parametri di query di facet per restituire la struttura del sito di collaborazione con i risultati di facet, oltre a un'espressione $filter che gestisce l'evento di selezione. L'espressione `$filter` deve essere considerata come il codice alla base della riduzione effettiva dei risultati della ricerca restituiti al livello di presentazione. A fronte di un facet di colori, la selezione del colore rosso viene implementata tramite un'espressione `$filter` che consente di selezionare solo gli elementi di colore rosso.

**Nozioni fondamentali sulle query di Ricerca di Azure**

In Ricerca di Azure una richiesta viene specificata tramite uno o più parametri di query (vedere [Eseguire ricerche nei documenti](http://msdn.microsoft.com/library/azure/dn798927.aspx) per una descrizione di ciascuno di essi). Nessuno dei parametri di query è obbligatorio, ma è necessario disporre di almeno di uno affinché una query sia valida.

La precisione, in genere interpretata come la possibilità di filtrare i riscontri irrilevanti, avviene attraverso una o entrambe queste espressioni:

- **search =**<br/> Il valore di questo parametro rappresenta l'espressione di ricerca. Potrebbe essere una singola parte di testo o un'espressione di ricerca complessa che include più termini e operatori. Nel server un'espressione di ricerca viene usata per la ricerca full-text, per l'esecuzione di query su campi disponibili per la ricerca nell'indice di termini corrispondenti, per la restituzione dei risultati in ordine di classificazione. Se si imposta `search` su null, l'esecuzione della query va oltre l'intero indice (ovvero, `search=*`). In questo caso, gli altri elementi della query, ad esempio un `$filter` o profilo di assegnazione del punteggio, saranno restituiti i fattori principali che influiscono sul tipo di documenti restituiti `($filter` e il relativo ordine (`scoringProfile` o `$orderb`y).

- **$filter =**<br/> Un filtro è un meccanismo potente per limitare le dimensioni dei risultati della ricerca in base ai valori degli attributi del documento specifico. Viene prima valutato un `$filter`, seguito dalla logica di facet che genera i valori disponibili e i conteggi corrispondenti per ciascun valore.

Le espressioni di ricerca complesse comporteranno una riduzione delle prestazioni della query. Ove possibile, usare le espressioni di filtro ben strutturate per aumentare la precisione e migliorare le prestazioni delle query.

Per comprendere meglio in che modo un filtro aggiunge maggiore precisione, confrontare un'espressione di ricerca complessa che includa un'espressione di filtro:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Anche se entrambe le query sono valide, il secondo è superiore se si sta cercando non motel con parcheggio in Seattle. La prima query si basa su tali parole specifiche non indicate nei campi stringa come nome, descrizione e qualsiasi altro campo contenente dati disponibili per la ricerca. La seconda query consente di cercare le corrispondenze precise sui dati strutturati e probabilmente più accurate.

Nelle applicazioni che includono l'esplorazione in base a facet, sarà necessario assicurarsi che ogni azione dell'utente su una struttura di esplorazione in base a facet sia accompagnata da una restrizione dei risultati della ricerca, ottenuta tramite un'espressione di filtro.

<a name="howtobuildit"></a>
##Come compilare la soluzione

L'esplorazione in base a facet nella ricerca di Azure viene implementata nel codice dell'applicazione che crea la richiesta, ma si basa su elementi predefiniti dello schema.

Predefinito nell'indice di ricerca è l'attributo dell'indice `Facetable [true|false]`, impostato in campi selezionati per abilitare o disabilitare l'uso in una struttura di esplorazione in base a facet. Senza `"Facetable" = true` un campo non può essere usato nell'esplorazione in base a facet.

In fase di query, il codice dell'applicazione consente di creare una richiesta che include `facet=[string]`, un parametro di richiesta che fornisce i facet per il campo. Una query può avere più facet, ad esempio `&facet=color&facet=category&facet=rating`, ciascuno separato da un carattere e commerciale (&).

Il codice dell'applicazione deve inoltre consentire la creazione di un'espressione `$filter` per gestire gli eventi di selezione nell'esplorazione in base a facet. `$filter` riduce i risultati della ricerca, usando il valore del facet come criteri di filtro.

Ricerca di Azure restituisce i risultati di ricerca, in base ai termini immessi dall'utente insieme agli aggiornamenti della struttura di esplorazione in base a facet. In Ricerca di Azure l'esplorazione in base a facet è una costruzione a livello singolo, con valori di facet, che calcola il numero di risultati trovati per ciascuno di essi.

Il livello di presentazione del codice offre l'esperienza dell'utente. Dovrebbero essere indicate le parti costitutive dell'esplorazione in base a facet, ad esempio l'etichetta, i valori, le caselle di controllo e il conteggio. L'API REST di Ricerca di Azure è indipendente dalla piattaforma, quindi è possibile usare qualsiasi lingua e la piattaforma desiderata. L'aspetto importante consiste nell'includere gli elementi dell'interfaccia utente che supportano l'aggiornamento incrementale, con lo stato dell'interfaccia utente aggiornato man mano che vengono selezionate facet aggiuntive.

Nelle sezioni seguenti verrà fornita un'analisi più approfondita su come creare ogni parte a partire dal livello di presentazione.

<a name="presentationlayer"></a>
##Creare il livello di presentazione

Partire dal livello di presentazione consente di scoprire i requisiti che è possibile non vengano individuati in caso contrario e di comprendere le funzionalità essenziali per l'esperienza di ricerca.

In termini di esplorazione in base a facet, la pagina Web o un'applicazione consente di visualizzare la struttura del sito di collaborazione, rileva l'input dell'utente nella pagina e inserisce gli elementi modificati.

Per le applicazioni Web, viene comunemente usata la tecnologia AJAX nel livello di presentazione, poiché consente di aggiornare le modifiche incrementali. È inoltre possibile usare ASP.NET MVC o qualsiasi altra piattaforma di visualizzazione che può connettersi a un servizio di Ricerca di Azure tramite HTTP. L'applicazione di esempio a cui fa riferimento in questo articolo, ovvero **AdventureWorks Catalog**, è un'applicazione MVC ASP.NET.

Nell'esempio seguente, tratto dal file **index. cshtml** dell'applicazione di viene creata una struttura HTML dinamica per la visualizzazione dell’esplorazione in base a facet nella pagina dei risultati di ricerca. Nell'esempio l'esplorazione in base a facet è incorporata nella pagina dei risultati della ricerca e viene visualizzata quando l'utente immette un termine di ricerca.

Si noti che ogni facet contiene un'etichetta (colori, categorie, prezzi), un'associazione a un campo di collaborazione (colore, categoryName, listPrice) e un parametro `.count`, usati per restituire il numero di elementi trovati per tale risultato facet.

  ![][2]
 

> [AZURE.TIP] Quando si progetta la pagina di risultati di ricerca, ricordarsi di aggiungere un meccanismo per la cancellazione dei facet. Se si usano le caselle di controllo, gli utenti possono facilmente intuire come cancellare i filtri. Per altri layout potrebbe essere necessario un modello di navigazione o un altro approccio creativo. Ad esempio, nell'applicazione di esempio AdventureWorks Catalog, è possibile fare clic sul titolo AdventureWorks Catalog per reimpostare la pagina di ricerca.

<a name="buildindex"></a>
##Compilare l'indice

L'esplorazione in base a facet viene abilitata per ogni campo singolarmente, tramite l'attributo di indice: `"Facetable": true`.  
Tutti i tipi di campo che possono essere usati in esplorazione in base a facet sono `Facetable` per impostazione predefinita. Tali tipi di campo includono `Edm.String`, `Edm.DateTimeOffset` e tutti i tipi di campo numerico (ossia, tutti i tipi di campo consentono un'esplorazione in base a facet tranne `Edm.GeographyPoint`, che non può essere usato nell'esplorazione in base a facet).

Durante la creazione di un indice, è consigliabile disattivare in modo esplicito l'esplorazione in base a facet per i campi che non devono mai essere usati come un facet. In particolare, i campi stringa per i valori singleton, ad esempio un nome di prodotto o ID, devono essere impostati su `"Facetable": false` per impedirne l'uso accidentale (e inefficace) nell'esplorazione in base a facet.

Di seguito è riportato lo schema per l'applicazione di esempio AdventureWorks Catalog (senza alcuni attributi per ridurre le dimensioni complessive):

 ![][3]
 
Si noti che `Facetable` è disattivato per i campi stringa che non devono essere usati come facet, ad esempio un ID o un nome. La disattivazione dell'esplorazione in base a facet laddove non necessaria consente di mantenere ridotte le dimensioni dell'indice e in genere consente di migliorare le prestazioni.

> [AZURE.TIP] Come procedura consigliata, includere il set completo di attributi dell'indice per ogni campo. Benché `Facetable` sia attivato per impostazione predefinita per quasi tutti i campi, l'impostazione intenzionale di ogni attributo consentono di valutare le implicazioni di ogni decisione di schema.

<a name="checkdata"></a>
##Controllo della qualità dei dati 

Durante lo sviluppo di tutte le applicazioni incentrate sui dati, la preparazione dei dati è spesso una delle parti più importanti del processo. Le applicazioni di ricerca non fanno eccezione. La qualità dei dati ha un'incidenza diretta sulla possibilità che la struttura di esplorazione in base a facet si prospetti come previsto, nonché sull'efficacia del processo di creazione dei filtri che consentono di ridurre il set di risultati.

In Ricerca di Azure, il corpo di ricerca è formato da documenti che popolano un indice. I documenti forniscono i valori usati per calcolare il facet. Se si desidera effettuare un'esplorazione in base a facet per marca o prezzo, ogni documento deve contenere i valori relativi a *BrandName* e *ProductPrice* che sono validi, coerenti e produttivi come opzione di filtro.

Di seguito sono elencati alcuni promemoria di ciò che è necessario eliminare:

- Per ogni campo da usare per l'esplorazione in base a facet, è necessario stabilire se contiene valori adatti all'uso come filtri nella ricerca mirata. I valori devono essere brevi, descrittivi sufficientemente distintivi per offrire una scelta definita tra opzioni concorrenti.
- Errori di ortografia o valori quasi corrispondenti. Se si effettua l'esplorazione in base a facet per Colore e i valori dei campi includono Arancione e Arncione (un errore di ortografia), un'esplorazione in base a facet in base al campo colore comporterebbe la selezione di entrambi.
- Il testo con maiuscole e minuscole e può inoltre provocare danni all'esplorazione in base a facet con arancione e Arancione visualizzati come due valori diversi.
- Le versioni singolari e plurali dello stesso valore possono comportare un facet separato.

Come si può immaginare, la scrupolosità di preparazione dei dati è un aspetto essenziale dell'esplorazione in base a facet.

<a name="buildquery"></a>
##Creare la query

Il codice creato per la creazione di query deve specificare tutte le parti di una query valida, tra cui espressioni di ricerca, i facet, i filtri, l'assegnazione dei punteggi dei profili, ovvero qualsiasi elemento che consente di formulare una richiesta. In questa sezione verrà illustrata la collocazione dei facet in una query e l'uso dei filtri con i facet per fornire un set di risultati ridotto.

Un esempio è spesso un buon punto di inizio. Nell'esempio seguente, tratto dal file **CatalogSearch.cs**, viene creata una richiesta che permette l'esplorazione in base a facet basata su Colore, Categoria e Prezzo.

Si noti che i facet sono parte integrante di questa applicazione di esempio. La funzionalità di ricerca nel AdventureWorks Catalog è progettata sulla base dei filtri e dell'esplorazione in base a facet. Ciò è evidente nel posizionamento principale dell'esplorazione in base a facet nella pagina. L'applicazione di esempio include i parametri URI per facet (colore, categoria, prezzi) come proprietà nel metodo di ricerca (in base a come è stato creato nell'applicazione di esempio).

  ![][4]
 
Un parametro di query del facet è impostato su un campo e a seconda del tipo di dati usati, può essere ulteriormente dotato di parametri in un elenco delimitato da virgole che includa `count:<integer>`, `sort:<>`, `intervals:<integer>` e `values:<list>`. Durante l'impostazione di intervalli è supportato un elenco di valori per i dati numerici. Per informazioni dettagliate, vedere [Eseguire ricerche nei documenti (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Insieme ai facet, la richiesta formulata dall'applicazione deve anche permettere la creazione di filtri per limitare il set di documenti candidato in base alla selezione di un valore di facet. Per un archivio di biciclette, l'esplorazione in base a facet fornisce indicazioni per domande come "Quali tipi di biciclette, produttori e i colori sono disponibili", filtrando le risposte fornisce risposte a domande "Quali biciclette sono mountain bike, in questo intervallo di prezzi".

Quando un utente fa clic su "Red" per indicare che devono essere visualizzati solo i prodotti rossi, la query successiva che l'applicazione invia includerebbe `$filter=Color eq ‘Red’`.

## Procedure consigliate per l'esplorazione in base a facet

Nell'elenco seguente vengono riepilogate alcune procedure consigliate.

- **Precisione**<br/> Usare i filtri. Se si usano solo le espressioni di ricerca, lo stemming può comportare la restituzione di un documento che non dispone del valore di facet esatto in uno dei relativi campi.

- **Campi di destinazione**<br/> In un drill-down di un'esplorazione in base a facet, è possibile includere solo i documenti che contengono il valore del facet in un campo specifico (collaborazione), non in qualsiasi punto in tutti i campi disponibili per la ricerca. L'aggiunta di un filtro mette in evidenza il campo di destinazione indirizzando il servizio alla ricerca solo nel campo di esplorazione in base a facet per un valore corrispondente.

- **Efficienza dell'indice**<br/> Se l'applicazione usa esclusivamente l'esplorazione in base a facet (vale a dire nessuna casella di ricerca), è possibile contrassegnare il campo come `searchable=false`, `facetable=true` per produrre un indice più compatto. Inoltre, l'indicizzazione si verifica solo su valori di facet interi con nessuna interruzione parola o indicizzazione delle parti di un valore per più parole.

- **Prestazioni**<br/> I filtri riducono il set di documenti candidati per la ricerca ed escluderli dalla classificazione. Se si dispone di un ampio set di documenti, l'uso di un drill-down di facet molto selettivo spesso consentirà un miglioramento significativo delle prestazioni.


<a name="tips"></a>
##Suggerimenti su come controllare l'esplorazione in base a facet

Di seguito è un foglio di suggerimenti con informazioni aggiuntive sui problemi specifici.

**Aggiungere etichette per ogni campo nella navigazione facet**

Le etichette vengono in genere definite nel codice HTML o nel modulo (**index.cshtml** nell'applicazione di esempio). Non esiste alcuna API in Ricerca di Azure per le etichette di navigazione facet o qualsiasi altro tipo di metadati.

**Definire quali campi possono essere usati come facet**

Tenere presente che lo schema dell'indice determina quali campi sono disponibili da usare come facet. Presupponendo che un campo consenta un'esplorazione in base a facet, la query specifica per quali campi eseguirla. Il campo per cui si eseguire l'esplorazione in base a facet fornisce i valori visualizzati sotto l'etichetta.

I valori visualizzati in ogni etichetta vengono recuperati dall'indice. Ad esempio, se il campo del facet è *Colore*, i valori disponibili per il filtro aggiuntivi saranno i valori per il campo (rosso, bianco e così via).

Per valori numerici e data/ora solo, è possibile impostare in modo esplicito i valori nel campo facet (ad esempio, `facet=Rating,values:1|2|3|4|5`). È consentito un elenco di valori per questi tipi di campo semplificare la separazione dei risultati di facet in intervalli contigui (entrambi gli intervalli in base a valori numerici o periodi di tempo).

**Riduzione dei risultati facet**

I risultati facet sono documenti trovati nei risultati della ricerca che corrispondono a un termine di facet. Nell'esempio seguente, nei risultati della ricerca per *il cloud computing*, i 254 elementi dispongono inoltre di una *specifica interna* come tipo di contenuto. Gli elementi non si escludono necessariamente a vicenda. Se un elemento soddisfa i criteri di entrambi i filtri, viene conteggiato in ognuno di essi. Ciò è possibile quando l'esplorazione in base a facet su campi `Collection(Edm.String)` che vengono spesso utilizzati per implementare l'aggiunta di tag nel documento.

		Search term: "cloud computing"
		Content type
		   Internal specification (254)
		   Video (10) 

In generale, se si ritiene che i risultati facet siano costantemente troppo elevati, è consigliabile che aggiungere ulteriori filtri, come illustrato nelle sezioni precedenti, offrire agli utenti di applicazioni più opzioni per restringere la ricerca.

**Limitare gli elementi nel riquadro di esplorazione in base a facet**

Per ogni campo di esplorazione in base a facet nella struttura di spostamento, esiste un limite predefinito di 10 valori. Questa impostazione predefinita risulta utile per le strutture di navigazione perché mantiene l'elenco di valori a un livello gestibile. È possibile sostituire il valore predefinito assegnando un valore per il conteggio.

- `&facet=city,count:5` specifica che solo le prime 5 città trovate nei risultati migliori vengono restituite come risultato facet. Dato un termine di ricerca di "aeroporto" e 32 corrispondenze, se la query specifica `&facet=city,count:5`, solo le prime cinque città univoche con il maggior numero di documenti nei risultati della ricerca vengono incluse nei risultati del facet.

Si noti la distinzione tra i risultati di facet e i risultati della ricerca. I risultati della ricerca sono tutti i documenti che corrispondono alla query. I risultati facet sono le corrispondenze per ogni valore del facet. Nell'esempio verranno restituiti i nomi di città non presenti nell'elenco di classificazione facet (5 nel nostro esempio). I risultati che vengono filtrati tramite l'esplorazione in base a facet diventano visibili all'utente quando egli cancella i facet o sceglie altri facet oltre a città.

> [AZURE.NOTE] La discussione relativa a `count` quando ne esiste più di un tipo potrebbe portare a confusione. La tabella seguente offre un breve riepilogo di come viene usato il termine nell'API di Ricerca di Azure, nel codice di esempio e nella documentazione.

- `@colorFacet.count`<br/> Nel codice di presentazione, si noterà un parametro di conteggio del facet usato per visualizzare il numero di risultati facet. Nei risultati facet, Conteggio indica il numero di documenti che corrispondono a un intervallo o termine facet.

- `&facet=City,count:12`<br/> In una query di facet, è possibile impostare il conteggio su un valore. Il valore predefinito è 10, ma è possibile impostarlo a un livello superiore o inferiore. L'impostazione `count:12` consente di recuperare le 12 corrispondenze principali nei risultati di facet per numero di documenti.

- "`@odata.count`"<br/> Nella risposta alla query, questo valore indica il numero di elementi corrispondenti nei risultati della ricerca. In media, è maggiore della somma di tutti i facet risultati combinati, a causa della presenza di elementi che corrispondono al termine di ricerca, ma senza corrispondenze di valore del facet.


**Livelli nell'esplorazione in base a facet**

Come accennato, non esiste alcun supporto diretto per la nidificazione facet in una gerarchia. L'esplorazione in base a facet per impostazione predefinita supporta solo un livello di filtri. Tuttavia, esistono soluzioni alternative. È possibile codificare una struttura gerarchica facet in un `Collection(Edm.String)` con un punto di ingresso per singola gerarchia. L'implementazione di questa soluzione alternativa esula dall'ambito di questo articolo, ma è possibile ottenere maggiori informazioni sulle raccolte in [OData per singolo esempio](http://msdn.microsoft.com/library/ff478141.aspx).

**Convalidare i campi**

Se si compila l'elenco di facet dinamicamente in base a input dell'utente non attendibile, è necessario verificare che i nomi dei campi di collaborazione siano validi o escluderne i nomi durante la creazione di URL usando `Uri.EscapeDataString()` in .NET o l'equivalente nella piattaforma preferita.

**Conteggi nei risultati di facet**

Quando si aggiunge un filtro a una query di collaborazione, è possibile mantenere l'istruzione facet (ad esempio, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, facet=Rating non è necessaria, ma mantenerla permette di restituire i conteggi dei valori di facet per le classificazioni a 4 stelle e superiori. Ad esempio, se un utente fa clic su "4" e la query include un filtro per maggiore o uguale a "4", i conteggi vengono restituiti per ciascuna valutazione uguale a 4 e superiore.

**Implicazioni di partizionamento orizzontale al conteggio di facet**

In determinate circostanze, si noterà che i conteggi facet non corrispondono ai set di risultati (vedere [navigazione collaborazione nella Ricerca di Azure (post di forum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

I conteggi di facet possono essere inesatti grazie all'architettura di partizionamento orizzontale. Ogni indice di ricerca include più partizioni e ciascuno di essi segnala i principali N facet per numero di documenti, che viene quindi combinato in un singolo risultato. Se alcune partizioni dispongono di diversi valori corrispondenti a differenza di altre, è probabile che alcuni valori di facet siano mancanti o non calcolati nei risultati.

Sebbene questo comportamento potrebbe cambiare in qualsiasi momento, se si verifica subito, è possibile risolverlo "gonfiando" artificialmente il conteggio:<numero> per un numero molto elevato di documenti e applicare il report completo di ogni partizione. Se il valore del conteggio è maggiore o uguale al numero di valori univoci nel campo, vengono garantiti risultati accurati. Tuttavia, quando i conteggi di documenti sono veramente elevati, si verifica una riduzione delle prestazioni. Si consiglia, pertanto, di usare questa opzione con cautela.

<a name="rangefacets"></a>
##Esplorazione in base a facet basata sui valori di intervallo

L'esplorazione in base a facet su intervalli è un requisito comune delle applicazioni di ricerca. Gli intervalli sono supportati per i dati numerici e i valori DateTime. Per ulteriori informazioni su ogni approccio in [Eseguire ricerche nei documenti (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Ricerca di Azure semplifica la creazione degli intervalli fornendo due approcci per l'elaborazione di un intervallo. Per entrambi gli approcci Ricerca di Azure crea gli intervalli appropriati in base agli input resi disponibili. Ad esempio, se si specificano valori di intervallo di 10|20|30, verranno creati automaticamente gli intervalli 0 -10, 10-20, 20-30. L'applicazione di esempio consente di rimuovere qualsiasi intervallo vuoto.

**Approccio 1: usare il parametro intervallo**<br/> Per impostare i facet di prezzo in incrementi di 10 dollari, è necessario specificare: `&facet=price,interval:10`


**Approccio 2: usare un elenco di valori**<br/> Per dati numerici, è possibile usare un elenco di valori. Prendere in considerazione l'intervallo di facet per listPrice, sottoposto a rendering come segue:

  ![][5]

L'intervallo è specificato nel file **CatalogSearch.cs** in cui viene usato un elenco di valori:

    facet=listPrice,values:10|25|100|500|1000|2500

Ogni intervallo viene compilato usando 0 come punto di partenza e un valore dall'elenco come endpoint e quindi privato dell'intervallo precedente per creare intervalli discreti. Ricerca di Azure eseguirà questa operazione nell'ambito dell'esplorazione in base facet. Non è necessario scrivere codice per la struttura di ogni intervallo.

### Creare un filtro per gli intervalli di facet ###

Per filtrare i documenti in base a un intervallo selezionato dall'utente, è possibile usare gli operatori di filtro `"ge"` e `"lt"` in un'espressione di due parti che definisce gli endpoint dell'intervallo. Ad esempio, se l'utente sceglie l'intervallo 10-25, il filtro sarà `$filter=listPrice ge 10 and listPrice lt 25`.

Nell'applicazione di esempio nell'espressione di filtro vengono utilizzati i parametri **priceFrom** e **priceTo** per impostare gli endpoint. Il metodo **BuildFilter** in **CatalogSearch.cs** contiene l'espressione di filtro che fornisce i documenti all'interno di un intervallo.

  ![][6]

<a name="geofacets"></a>
##Esplorazione in base a facet basata su GeoPoint

È comune vedere filtri che consentono di scegliere un negozio, un ristorante o una destinazione in base alla prossimità alla posizione corrente. Questo tipo di filtro potrebbe somigliare all'esplorazione in base a facet, ma è semplicemente un filtro. Viene menzionato per coloro che cercano in particolare consigli di implementazione per tale problema particolare di progettazione.

Sono disponibili due funzioni geospaziali in Ricerca di Azure, **geo.distance** e **geo.intersects**.

- La funzione **geo.distance** restituisce la distanza in chilometri tra due punti, uno è un campo e una è una costante passata come parte del filtro.

- La funzione **geo.intersects** restituisce true se un punto specificato si trova all'interno di un poligono specificato, dove il punto è un campo e il poligono è specificato come un elenco costante di coordinate passato come parte del filtro.

È possibile trovare alcuni esempi di filtri in [Sintassi dell'espressione di OData (Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##Provare il servizio

La demo Adventure Works di Ricerca di Azure in Codeplex contiene gli esempi descritti nel presente articolo. Quando si lavora con i risultati, controllare l'URL per le modifiche nella costruzione delle query. Questa applicazione è casualmente incaricata all'accodamento i facet all'URI a ogni selezione.

1.	Configurare l'applicazione di esempio per usare l'URL e la chiave api del servizio.

	Si noti lo schema definito nel file Program.cs del progetto CatalogIndexer. Specifica campi di esplorazione in base a facet per colore, listPrice, dimensioni, peso, categoryName e modelName. Solo alcuni di questi (colore, listPrice, categoryName) vengono effettivamente implementate nell'esplorazione in base a facet.

3.	Eseguire l'applicazione.

	Inizialmente, solo la casella di ricerca è visibile. È possibile fare clic sul pulsante di ricerca per ottenere tutti i risultati o digitare un termine di ricerca.

	![][7]
 
4.	Immettere un termine di ricerca, ad esempio bicicletta, quindi fare clic sul pulsante di **ricerca**. La query viene eseguita rapidamente.
 
	Una struttura di esplorazione in base a facet viene inoltre restituita con i risultati della ricerca. Nell'URL i facet per i colori, le categorie e i prezzi sono null. Nella pagina dei risultati di ricerca la struttura di esplorazione in base a facet include conteggi per ogni risultato facet.

	 ![][8]
 
5.	Fare clic su un colore, una categoria e una fascia di prezzo. I facet sono null in una ricerca iniziale, ma come assumono i valori, vengono eliminati i risultati della ricerca di elementi che non corrispondono. Si noti che l'URI visualizza le modifiche apportate alla query.

	![][9]
 
6.	Per cancellare la query di collaborazione in modo che sia possibile provare i comportamenti di query diversi, fare clic su **AdventureWorks Catalog** nella parte superiore della pagina.

	![][10]
 
<a name="nextstep"></a>
##Passaggio successivo

Per verificare le proprie conoscenze, è possibile aggiungere un campo di facet per *modelName*. L'indice è già impostato per il facet, pertanto non è richiesta alcuna modifica all'indice. Sarà, tuttavia, necessario modificare il codice HTML in modo che includa un nuovo facet per i modelli e aggiungere il campo di facet per il costruttore di query.

Per altre informazioni sui principi di progettazione per l'esplorazione in base a facet, è consigliabile usare i collegamenti seguenti:

- [Progettazione per la ricerca con esplorazione in base a facet](http://www.uie.com/articles/faceted_search/)
- [Modelli di progettazione: esplorazione in base a facet](http://alistapart.com/article/design-patterns-faceted-navigation)

È anche possibile guardare gli [approfondimenti su Ricerca di Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Al minuto 45:25 è presente una dimostrazione su come implementare i facet.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 

<!---HONumber=AcomDC_0907_2016-->
