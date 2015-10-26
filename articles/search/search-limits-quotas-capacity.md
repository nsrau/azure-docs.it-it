<properties
	pageTitle="Limiti dei servizi in Ricerca di Azure"
	description="Limiti della Ricerca di Azure utilizzati nella pianificazione della capacità e limiti massimi di richieste e risposte."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.date="08/18/2015"
	ms.author="heidist"/>

#Limiti dei servizi in Ricerca di Azure

I limiti massimi per archiviazione, carichi di lavoro e quantità di indici, documenti e altri oggetti dipendono da come si effettua la sottoscrizione a Ricerca di Azure. Il servizio gratuito è destinato al test del modello di prova e alla valutazione con limiti inferiori per tutti gli oggetti e i carichi di lavoro in modo che le risorse possano essere condivise più equamente.

La versione Standard viene eseguita su computer usati solo dal servizio. L'uso esclusivo di un servizio dedicato offre la possibilità di scalare verso l'alto o verso il basso, con ulteriori capacità di elaborazione e archiviazione a ogni livello, inclusa la configurazione minima.

##Limiti massimi per un servizio di ricerca gratuito (condiviso)

I sottoscrittori di Azure possono usare il servizio di ricerca condiviso (multi-tenant) per lo sviluppo o per applicazioni di ricerca molto piccole. Il servizio condiviso viene fornito con la sottoscrizione di Azure. Questa è un'opzione gratuita che consente di sperimentare il servizio prima dell'iscrizione. Fornisce:

Object|Limite
------|-----
Numero massimo di indici|3
Numero massimo di campi per ogni indice|1000
Conteggio massimo di documenti|10\.000
Dimensioni massime di archiviazione|50 MB
Partizioni massime|N/D
Repliche massime|N/D
Unità massima di ricerca|N/D
Numero massimo di indicizzatori|3
Numero massimo di origini dati indicizzatore|3
Numero massimo di documenti indicizzati per ogni chiamata indicizzatore|10\.000

Si noti che non sono presenti quote o limiti massimi associati alle query. Il valore di query al secondo (QPS) è variabile a seconda della larghezza di banda disponibile e della concorrenza per le risorse di sistema. Le risorse di calcolo e di archiviazione di Azure che supportano il servizio condiviso vengono condivise da più sottoscrittori, pertanto il valore di QPS per la soluzione varia a seconda di come i numerosi altri carichi di lavoro sono in esecuzione nello stesso momento.

##Limiti massimi per un servizio di ricerca (dedicato) standard

Nel livello di prezzo standard, un servizio di ricerca dedicato archivia solo i dati ed esegue solo i carichi di lavoro dell'utente. Diversamente dal servizio condiviso, l'allocazione delle risorse per un servizio di ricerca dedicato è regolabile, a qualsiasi livello di scalabilità necessario. È possibile impostare in modo indipendente i livelli delle risorse per le partizioni (per la scalabilità verticale archiviazione) e le repliche (per garantire un'elevata disponibilità e la scalabilità verticale di QPS e prestazioni di indicizzazione). Vedere [Gestire una soluzione di ricerca](search-manage.md) per informazioni dettagliate sulle diverse configurazioni di risorse.

La tabella seguente illustra un elenco di limiti superiori, ma è necessario esaminare il grafico di matrice ulteriormente per comprendere la capacità in termini di [combinazioni di partizioni e repliche](#chart) consentite.

Object|Limite
------|----
Numero massimo di indici|50 per servizio di ricerca
Numero massimo di campi per ogni indice|1000
Conteggio massimo di documenti|15 milioni per partizione
Dimensioni massime di archiviazione|25 GB per partizione
Partizioni massime|12 per servizio di ricerca
Repliche massime|6 per servizio di ricerca
Unità massima di ricerca|36 per servizio di ricerca
Numero massimo di indicizzatori|50 per servizio di ricerca
Numero massimo di origini dati indicizzatore|50 per servizio di ricerca
Numero massimo di documenti indicizzati per ogni chiamata indicizzatore|Senza limiti

La capacità di Ricerca di Azure può essere acquistata in incrementi denominati unità di ricerca. Il livello di prezzo standard consente fino a 36 unità di ricerca per servizio di ricerca. Questo limite sostituisce i singoli limiti di partizioni e repliche. Ad esempio, non è possibile scalare il servizio verso l'alto fino a 12 partizioni e 6 repliche poiché tale operazione richiederebbe 72 unità di ricerca (12 x 6), superando il limite di 36 unità di ricerca per servizio.

##Informazioni sulle partizioni e sulle repliche

Le **partizioni** forniscono archiviazione e I/O. Un singolo servizio di ricerca può avere un massimo di 12 partizioni. Ogni partizione viene fornita con un limite di 15 milioni di documenti o 25 GB di spazio di archiviazione, a seconda di quale valore si verifica per primo. Se si aggiungono le partizioni, il servizio di ricerca può caricare più documenti. Un servizio con una sola partizione che archivia inizialmente fino a 25 GB di dati, ad esempio, può archiviare a 50 GB quando si aggiunge una seconda partizione al servizio.

Le **repliche** sono copie del motore di ricerca. Ogni singolo servizio di ricerca può avere un massimo di 6 partizioni. Sono necessarie almeno 2 repliche per la disponibilità di lettura (query) e almeno 3 repliche per la disponibilità di lettura e scrittura (query, indicizzazione).

In ogni replica viene eseguita una copia di ogni indice. Quando si aggiungono le repliche, copie aggiuntive dell'indice vengono portate online per supportare i carichi di lavoro di query maggiori e per bilanciare il carico delle richieste su più repliche. Se si dispone di più indici, ad esempio 6 indici e 3 repliche, ogni replica avrà una copia di tutti i 6 indici.

Si noti che non vengono fornite le stime per query al secondo (QPS), in quanto l'esecuzione di query può variare notevolmente a seconda della complessità della query e dei carichi di lavoro concorrenti. In media, una replica può soddisfare circa 15 QPS, ma la velocità effettiva sarà leggermente superiore o inferiore a seconda della complessità della query (le query con facet sono più complesse) e della latenza di rete. Inoltre, è importante riconoscere che mentre l'aggiunta delle repliche aggiunge in modo definito scalabilità e prestazioni, il risultato finale non è strettamente lineare: l'aggiunta di 3 repliche non garantisce di triplicare la velocità effettiva. La latenza delle query è un indicatore che potrebbe essere necessario per le repliche aggiuntive.

<a id="chart"></a>
##Combinazioni supportate di partizioni e repliche

Come accennato in precedenza, il limite effettivo di partizioni e repliche è basato sulla combinazione delle risorse selezionate, entro il limite di 36 unità di ricerca per servizio. Le risorse vengono allocate in termini di unità di ricerca. Un servizio di ricerca dedicato inizia con una replica e una partizione come unità di ricerca.

La capacità aggiuntiva viene calcolata come partizioni moltiplicate per le repliche, producendo il numero totale di unità di ricerca necessarie per supportare una determinata configurazione.

Nella tabella seguente è riportato un grafico in cui sono elencate le repliche sull'asse verticale e le partizioni sull'asse orizzontale. L'intersezione mostra il numero di unità di ricerca necessarie per supportare ogni combinazione, entro il limite di 36 unità di ricerca per servizio. Ad esempio, per 6 repliche e 2 partizioni, questa configurazione richiederebbe 12 unità di ricerca. Per utilizzare 4 repliche e 2 partizioni, sono necessarie 8 unità di ricerca. Come regola generale, la maggior parte delle applicazioni di ricerca tendono a richiedere più repliche che partizioni.

<table cellspacing="0" border="1">
<tr><td><b>6 repliche</b></td><td>6 unità di ricerca</td><td>12 unità di ricerca</td><td>18 unità di ricerca</td><td>24 unità di ricerca</td><td>36 unità di ricerca</td><td>N/D</td></tr>
<tr><td><b>5 repliche</b></td><td>5 unità di ricerca</td><td>10 unità di ricerca</td><td>15 unità di ricerca</td><td>20 unità di ricerca</td><td>30 unità di ricerca</td><td>N/D</td></tr>
<tr><td><b>4 repliche</b></td><td>4 unità di ricerca</td><td>8 unità di ricerca</td><td>12 unità di ricerca</td><td>16 unità di ricerca</td><td>24 unità di ricerca</td><td>N/D </td></tr>
<tr><td><b>3 repliche</b></td><td>3 unità di ricerca</td><td>6 unità di ricerca</td><td>9 unità di ricerca</td><td>12 unità di ricerca</td><td>18 unità di ricerca</td><td>36 unità di ricerca</td></tr>
<tr><td><b>2 repliche</b></td><td>2 unità di ricerca</td><td>4 unità di ricerca</td><td>6 unità di ricerca</td><td>8 unità di ricerca</td><td>12 unità di ricerca</td><td>24 unità di ricerca</td></tr>
<tr><td><b>1 replica</b>.</td><td>1 unità di ricerca</td><td>2 unità di ricerca</td><td>3 unità di ricerca</td><td>4 unità di ricerca</td><td>6 unità di ricerca</td><td>12 unità di ricerca</td></tr>
<tr><td>N/D</td><td><b>1 partizione</b></td><td><b>2 partizioni</b></td><td><b>3 partizioni</b></td><td><b>4 partizioni</b></td><td><b>6 partizioni</b></td><td><b>12 partizioni</b></td></tr> 
</table>

Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per altre informazioni, vedere [Informazioni sui prezzi](http://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE]Il numero di partizioni scelto si deve dividere equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). In questo modo Ricerca di Azure divide preventivamente ogni indice in 12 partizioni in modo che possa essere distribuito tra le partizioni. Ad esempio, se il servizio dispone di tre partizioni e si crea un nuovo indice, ogni partizione conterrà 4 partizioni dell'indice. Come viene suddiviso in partizioni un indice in Ricerca di Azure è un dettaglio di implementazione, soggetto a modifiche nella prossima versione. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.

##Scegliere una combinazione di partizioni e repliche per la disponibilità elevata

Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. Per molte distribuzioni, una partizione fornisce archiviazione e I/O sufficienti (15 milioni di documenti per partizione).

I carichi di lavoro delle query, tuttavia, si basano sulle repliche. Se è necessaria maggiore velocità effettiva o la disponibilità elevata, è possibile richiedere repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

- 2 repliche per la disponibilità elevata dei carichi di lavoro di sola lettura (query)
- 3 o più repliche per la disponibilità elevata dei carichi di lavoro di lettura/scrittura (query e indicizzazione)

Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. In alternativa, è possibile aggiungere la ridondanza a livello di servizio. Per una discussione più approfondita delle soluzioni alternative, vedere [questo post di forum](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE]Tenere presente che la scalabilità e i contratti di servizio sono funzionalità del servizio standard. Il servizio gratuito è disponibile a un livello di risorse fisso, con le repliche e le partizioni condivise da più sottoscrittori. Se si è partiti dal servizio gratuito e ora si desidera aggiornare, è necessario creare un nuovo servizio Ricerca di Azure a livello di standard e quindi ricaricare gli indici e i dati nel nuovo servizio. Vedere [Creare un servizio Ricerca di Azure nel portale](search-create-portal.md) per istruzioni sul provisioning del servizio.

##Limiti della chiave API

Le chiavi API vengono utilizzate per l'autenticazione del servizio. Sono disponibili due tipi. Le chiavi di amministrazione sono specificate nell'intestazione della richiesta. Le chiavi di query sono specificate nell'URL. Per ulteriori informazioni sulla gestione delle chiavi, vedere [Gestire il servizio di ricerca in Microsoft Azure](search-manage.md).

- 2 chiavi di amministrazione al massimo per ogni servizio
- 50 chiavi di query al massimo per ogni servizio

##Limiti di richiesta

- 16 MB al massimo per ogni richiesta
- 8 KB al massimo per la lunghezza dell'URL 
- 1000 documenti al massimo per ogni batch di carichi, unioni o eliminazioni di indice
- 32 campi al massimo nella clausola $orderby
- 32766 byte (32 KB meno 2 byte) di testo codificato UTF-8 per la dimensione massima del termine di ricerca

##Limiti di risposta

- 1000 documenti al massimo restituiti per pagina di risultati della ricerca
- 100 suggerimenti al massimo restituiti per richiesta di API di suggerimento

<!---HONumber=Oct15_HO3-->