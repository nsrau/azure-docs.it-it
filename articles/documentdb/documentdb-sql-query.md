<properties 
	pageTitle="Query q sintassi SQL per DocumentDB | Microsoft Azure" 
	description="Informazioni sulla sintassi SQL, sui concetti relativi ai database e sulle query SQL per DocumentDB, un database NoSQL. SQL può essere usato come linguaggio di query JSON in DocumentDB." 
	keywords="sintassi sql, query sql, linguaggio di query json, concetti relativi ai database e query sql"
	services="documentdb" 
	documentationCenter="" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2016" 
	ms.author="arramac"/>

# Query e sintassi SQL in DocumentDB
Microsoft Azure DocumentDB supporta l'esecuzione di query di documenti mediante SQL (Structured Query Language) come linguaggio di query JSON. DocumentDB è effettivamente privo di schema. Grazie all'impegno nei confronti del modello di dati JSON direttamente nel motore del database, fornisce l'indicizzazione automatica dei documenti JSON senza richiedere schemi espliciti o la creazione di indici secondari.

Nella progettazione del linguaggio di query per DocumentDB sono stati tenuti in considerazione due obiettivi:

-	Invece di inventare un nuovo linguaggio di query JSON, è stato introdotto il supporto del linguaggio SQL. SQL è uno dei linguaggi di query più familiari e popolari. Il linguaggio di query SQL di DocumentDB fornisce un modello di programmazione formale per le query complesse sui documenti JSON.
-	Poiché un database di documenti JSON può eseguire JavaScript direttamente nel motore di database, l'obiettivo era di usare il modello di programmazione di JavaScript come base per il linguaggio di query. Il linguaggio di query SQL di DocumentDB è radicato nel sistema di tipi, nella valutazione delle espressioni e nella chiamata di funzioni di JavaScript. Questo rappresenta a sua volta un modello di programmazione naturale per le proiezioni relazionali, la navigazione gerarchica attraverso i documenti JSON, i self join, query spaziali e la chiamata di funzioni definite dall'utente (UDF) scritte interamente in JavaScript, tra le altre funzionalità.

Queste capacità costituiscono la chiave per la riduzione dell'attrito tra l'applicazione e il database e sono di importanza critica per la produttività degli sviluppatori.

Si consiglia di iniziare guardando il video seguente, in cui Aravind Ramachandran mostra le funzionalità di query di DocumentDB, e visitando la pagina [Query Playground](http://www.documentdb.com/sql/demo), in cui è possibile provare DocumentDB ed eseguire query SQL rispetto a un set di dati predefinito.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Tornare quindi a questo articolo, dove inizierà un’esercitazione sulle query SQL in cui verranno illustrati alcuni semplici documenti JSON e comandi SQL.

## Guida introduttiva ai comandi SQL in DocumentDB
Per osservare il funzionamento del linguaggio SQL di DocumentDB, si inizierà con alcuni semplici documenti JSON e si procederà eseguendo alcune semplici query su tali documenti. Considerare questi due documenti JSON come se riguardassero due famiglie. Da notare che, con DocumentDB, non è necessario creare alcuno schema o indici secondari in maniera esplicita. È sufficiente inserire i documenti JSON in una raccolta di DocumentDB e successivamente eseguire una query. In questo caso è illustrato un semplice documento JSON relativo alla famiglia Andersen: i genitori, i figli (e i loro animali domestici), l'indirizzo e le informazioni di registrazione. Il documento contiene stringhe, numeri, valori booleani, matrici e proprietà annidate.

**Documento**

	{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}


Ecco un secondo documento con una sottile differenza: vengono usati `givenName` e `familyName` invece di `firstName` e `lastName`.

**Documento**

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	            "familyName": "Miller", 
	             "givenName": "Lisa", 
	             "gender": "female", 
	             "grade": 8 }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "creationDate": 1431620462,
	    "isRegistered": false
	}



A questo punto è possibile provare a eseguire alcune query a fronte di questi dati per comprendere alcuni aspetti chiave del linguaggio SQL di DocumentDB. Ad esempio, la query seguente restituisce i documenti in cui il campo ID corrisponde a `AndersenFamily`. Poiché si tratta di un'istruzione `SELECT *`, l'output della query è il documento JSON completo:

**Query**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]


Si prenda ora in considerazione il caso in cui fosse necessario modificare la formattazione dell'output JSON in una forma differente. Questa query proietta un nuovo oggetto JSON con due campi selezionati, Name e City, quando la città in cui si trova l'indirizzo ha lo stesso nome dello stato. In questo caso, "NY, NY" corrispondono.

**Query**

	SELECT {"Name":f.id, "City":f.address.city} AS Family 
	FROM Families f 
	WHERE f.address.city = f.address.state

**Risultati**

	[{
	    "Family": {
	        "Name": "WakefieldFamily", 
	        "City": "NY"
	    }
	}]


La query successiva restituisce i nomi di elementi figlio specificati nella famiglia con ID corrispondente a `WakefieldFamily` ordinato in base alla città di residenza.

**Query**

	SELECT c.givenName 
	FROM Families f 
	JOIN c IN f.children 
	WHERE f.id = 'WakefieldFamily'
	ORDER BY f.address.city ASC

**Risultati**

	[
	  { "givenName": "Jesse" }, 
	  { "givenName": "Lisa"}
	]


È opportuno prestare attenzione ad alcuni aspetti salienti del linguaggio di query di DocumentDB attraverso gli esempi finora esaminati:
 
-	Poiché il linguaggio SQL di DocumentDB elabora i valori JSON, deve gestire entità con struttura ad albero invece di righe e colonne. Di conseguenza, il linguaggio consente di fare riferimento ai nodi dell'albero a qualsiasi profondità arbitraria, ad esempio `Node1.Node2.Node3…..Nodem`, in modo analogo al linguaggio SQL relazionale con il riferimento in due parti di `<table>.<column>`.
-	Il linguaggio strutturato di interrogazione funziona con dati senza schema. perciò il sistema di tipi deve essere associato in modo dinamico. La stessa espressione potrebbe produrre tipi differenti su documenti differenti. Il risultato di una query è un valore JSON valido, ma non è garantito che appartenga a uno schema fisso.
-	DocumentDB supporta solo i documenti JSON completi. Ciò significa che il sistema di tipi e le espressioni sono limitati all'interazione esclusiva con i tipi JSON. Per altre informazioni, vedere le [specifiche JSON](http://www.json.org/).
-	Una raccolta di DocumentDB è un contenitore senza schema dei documenti JSON. Le relazioni nelle entità di dati all'interno e tra i documenti in una raccolta vengono implicitamente acquisiti dal contenitore e non dalle relazioni chiave primaria e chiave esterna. È un aspetto importante da sottolineare alla luce dei join tra documenti, descritti più avanti in questo articolo.

## Indicizzazione di DocumentDB

Prima di addentrarsi nella sintassi SQL di DocumentDB vale la pena esplorare la progettazione dell'indicizzazione di DocumentDB.

Lo scopo degli indici di database è gestire le query in varie forme con un consumo di risorse ridotto al minimo (ad esempio CPU, input/output), offrendo al contempo una buona velocità effettiva e basse latenze. Spesso, la scelta dell'indice corretto per l'interrogazione di un database richiede una lunga pianificazione e sperimentazione. Questo approccio costituisce una sfida per i database senza schema, in cui i dati non si conformano a un rigido schema ed evolvono rapidamente.

Di conseguenza, durante la progettazione del sottosistema di indicizzazione di DocumentDB sono stati fissati gli obiettivi seguenti:

-	Indicizzare i documenti senza schema: il sottosistema di indicizzazione non richiede alcuna informazione sullo schema o supposizioni sullo schema dei documenti.

-	Supporto di query relazionali e gerarchiche efficienti e complesse: l'indice supporta il linguaggio di query DocumentDB in modo efficiente, incluso il supporto per le proiezioni relazionali e gerarchiche.

-	Supporto per le query coerente sia un volume di scritture sostenuta: per la scrittura ad alta velocità effettiva dei carichi di lavoro con query coerenti, l'indice viene aggiornato in modo incrementale, in modo efficiente e in linea con un volume sostenuto delle operazioni di scrittura. L'aggiornamento coerente dell'indice è fondamentale per la gestione delle query secondo il livello di coerenza con cui l'utente ha configurato il servizio documenti.

-	Supporto per multi-tenancy: dato il modello basato sulla prenotazione per la governance delle risorse tra tenant, gli aggiornamenti dell'indice vengono eseguiti mantenendosi nel budget delle risorse di sistema (CPU, memoria e operazioni di input/output al secondo) allocate per ogni replica.

-	Per conseguire l'efficienza dei costi, le risorse di archiviazione su disco dell'indice sono vincolate e prevedibili. Ciò è fondamentale perché DocumentDB consente allo sviluppatore di accettare compromessi basati sul costo tra spese relative all'indice e prestazioni delle query.

Per un esempio che illustra come configurare i criteri di indicizzazione per una raccolta, vedere gli esempi relativi a [DocumentDB su MSDN](https://github.com/Azure/azure-documentdb-net). Verrà ora analizzata più dettagliatamente la sintassi SQL di DocumentDB.


## Nozioni di base di una query SQL di DocumentDB
Ogni query consiste in una clausola SELECT e clausole FROM e WHERE facoltative in base agli standard ANSI-SQL. In genere, l'origine nella clausola FROM per ogni query viene enumerata, quindi il filtro nella clausola WHERE viene applicato all'origine per recuperare un sottoinsieme di documenti JSON. Infine, viene usata la clausola SELECT per proiettare i valori JSON richiesti nell'elenco selezionato.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## Clausola FROM
La clausola `FROM <from_specification>` è facoltativa, a meno che l'origine non sia filtrata o proiettata più avanti nella query. Lo scopo di questa query è specificare l'origine dati in base alla quale deve operare la query. Comunemente, l'origine è rappresentata dall'intera raccolta, ma è possibile specificare piuttosto un sottoinsieme della raccolta.

Una query come `SELECT * FROM Families` indica che l'intera raccolta Families è il database di origine in base al quale eseguire l'enumerazione. Invece di usare il nome della raccolta, è possibile usare uno speciale identificatore ROOT per rappresentare la raccolta. L'elenco seguente include le regole applicate per ogni query:

- È possibile effettuare l'aliasing della raccolta, come in `SELECT f.id FROM Families AS f` o semplicemente in `SELECT f.id FROM Families f`. In questo caso, `f` è l'equivalente di `Families`. `AS` è una parola chiave facoltativa per eseguire l'aliasing dell'identificatore.

-	Tenere presente che, una volta effettuato l'aliasing, non sarà più possibile associare l'origine iniziale. Ad esempio, la sintassi di `SELECT Families.id FROM Families f` non è valida perché non è più possibile risolvere l'identificatore "Families".

-	Tutte le proprietà a cui è necessario fare riferimento devono essere complete. In assenza di una rigorosa aderenza allo schema, ciò viene applicato per evitare eventuali associazioni ambigue. Di conseguenza, la sintassi di `SELECT id FROM Families f` non è valida perché la proprietà `id` non è associata.
	
### Documenti secondari
È anche possibile ridurre il database di origine a un sottoinsieme di dimensioni inferiori. Ad esempio, per enumerare un solo sottoalbero in ogni documento, la sottoradice può quindi diventare l'origine, come nell'esempio seguente.

**Query**

	SELECT * 
	FROM Families.children

**Risultati**

	[
	  [
	    {
	        "firstName": "Henriette Thaulow",
	        "gender": "female",
	        "grade": 5,
	        "pets": [
	          {
	              "givenName": "Fluffy"
	          }
	        ]
	    }
	  ],
	  [
	    {
	        "familyName": "Merriam",
	        "givenName": "Jesse",
	        "gender": "female",
	        "grade": 1
	    },
	    {
	        "familyName": "Miller",
	        "givenName": "Lisa",
	        "gender": "female",
	        "grade": 8
	    }
	  ]
	]

Se nell'esempio precedente veniva usata una matrice come origine, è possibile usare anche un oggetto come origine, come illustrato nell'esempio seguente. Qualsiasi valore JSON valido (diverso da Undefined) che è disponibile nell'origine verrà considerato per essere incluso nel risultato della query. Se alcune famiglie non hanno un valore `address.state` verranno escluse dal risultato della query.

**Query**

	SELECT * 
	FROM Families.address.state

**Risultati**

	[
	  "WA", 
	  "NY"
	]


## Clausola WHERE
La clausola WHERE (**`WHERE <filter_condition>`**) è facoltativa. e specifica la condizione (o le condizioni) che i documenti JSON forniti dall'origine devono soddisfare per essere inclusi come parte del risultato. Per essere considerato per il risultato, qualsiasi documento JSON deve valutare le condizioni specificate come "true". La clausola WHERE viene usata dal livello di indice allo scopo di determinare il sottoinsieme più piccolo in assoluto di documenti di origine che possono fare parte del risultato.

La query seguente richiede documenti che contengono una proprietà nome il cui valore è `AndersenFamily`. Qualsiasi altro documento che non contiene una proprietà nome o il cui valore non corrisponde a `AndersenFamily` verrà escluso.

**Query**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


Nell'esempio precedente è stata illustrata una semplice query di uguaglianza. Il linguaggio SQL di DocumentDB supporta anche una varietà di espressioni scalari. Quelle di uso più comune sono le espressioni binarie e unarie. Anche i riferimenti di proprietà dell'oggetto JSON sono espressioni valide.

Gli operatori binari seguenti sono attualmente supportati e possono essere usati nelle query come illustrato negli esempi riportati di seguito:
<table>
<tr>
<td>Aritmetico</td>	
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bit per bit</td>	
<td>|, &amp;, ^, &lt;&lt;, >>, >>> (spostamento a destra riempimento zero) </td>
</tr>
<tr>
<td>Logico</td>
<td>AND, OR, NOT</td>
</tr>
<tr>
<td>Confronto</td>	
<td>=, !=, &lt;, >, &lt;=, >=, &lt;></td>
</tr>
<tr>
<td>String</td>	
<td>|| (concatenazione)</td>
</tr>
</table>  

Saranno ora prese in esame alcune query che usano gli operatori binari.

	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade % 2 = 1     -- matching grades == 5, 1
	
	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade ^ 4 = 1    -- matching grades == 5
	
	SELECT *
	FROM Families.children[0] c
	WHERE c.grade >= 5     -- matching grades == 5


Sono supportati anche gli operatori unari +,-, ~ e NOT, che possono essere usati all'interno delle query come illustrato di seguito:

	SELECT *
	FROM Families.children[0] c
	WHERE NOT(c.grade = 5)  -- matching grades == 1
	
	SELECT *
	FROM Families.children[0] c
	WHERE (-c.grade = -5)  -- matching grades == 5



Oltre agli operatori binari e unari, sono consentiti anche i riferimenti di proprietà. Ad esempio, `SELECT * FROM Families f WHERE f.isRegistered` restituirebbe i documenti JSON contenenti la proprietà `isRegistered` dove il valore della proprietà è uguale al valore JSON `true`. Qualsiasi altro valore (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>` e così via) comporta l'esclusione del documento di origine dai risultati.

### Operatori di confronto e uguaglianza
La tabella seguente illustra il risultato dei confronti di uguaglianza nel linguaggio SQL di DocumentDB tra due tipi JSON qualsiasi.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolean</strong>
         </td>
         <td valign="top">
            <strong>Number</strong>
         </td>
         <td valign="top">
            <strong>String</strong>
         </td>
         <td valign="top">
            <strong>Object</strong>
         </td>
         <td valign="top">
            <strong>Array</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolean<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Number<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>String<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Object<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Array<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Per gli altri operatori di confronto, ad esempio >, >=, !=, < e <=, si applicano le regole seguenti:

-	Confronto tra risultati dei tipi in Undefined.
-	Confronto tra i risultati di due oggetti o due matrici in Undefined.

Se il risultato dell'espressione scalare nel filtro è Undefined, il documento corrispondente non verrebbe incluso nel risultato, perché Undefined non è logicamente uguale a "true".

### Parola chiave BETWEEN
È possibile usare anche la parola chiave BETWEEN per esprimere query su intervalli di valori come in SQL ANSI. La parola BETWEEN può essere utilizzata con stringhe o numeri.

Ad esempio, questa query restituisce tutti i documenti della famiglia in cui la classe frequentata dal primo figlio sia compresa tra 1 e 5 (inclusi).

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

A differenza di SQL ANSI, è possibile usare la clausola BETWEEN anche nella clausola FROM, come nell'esempio seguente.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Per accelerare l'esecuzione della query, creare un criterio di indicizzazione che usa un tipo di indice di intervallo su qualsiasi proprietà/percorso numerico filtrato nella clausola BETWEEN.

La differenza principale tra l'uso di BETWEEN in DocumentDB e SQL ANSI consiste nel fatto che è possibile esprimere le query di intervallo su proprietà di tipo misto, ad esempio "grade" può essere un numero (5) in alcuni documenti e stringhe in altri ("grade4"). In questi casi, come in JavaScript, un confronto tra due tipi diversi restituisce un risultato "undefined" e il documento viene ignorato.

### Operatori logici (AND, OR e NOT)
Gli operatori logici funzionano con valori booleani. Le tabelle di veridicità logica per questi operatori sono illustrate di seguito.

OPPURE|True|False|Undefined
---|---|---|---
True|True|True|True
False|True|False|Undefined
Undefined|True|Undefined|Undefined

AND|True|False|Undefined
---|---|---|---
True|True|False|Undefined
False|False|False|False
Undefined|Undefined|False|Undefined

NOT| |
---|---
True|False
False|True
Undefined|Undefined

### Parola chiave IN
La parola chiave IN consente di controllare se un valore specificato corrisponde a qualsiasi valore in un elenco. Ad esempio, questa query restituisce tutti i documenti famiglia dove l'id è uno dei "WakefieldFamily" o "AndersenFamily".
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

In questo esempio restituisce tutti i documenti in cui lo stato è uno dei valori specificati.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### Operatori Ternary (?) e Coalesce (??)
Gli operatori Ternary e Coalesce possono essere usati per compilare espressioni condizionali, analogamente ai linguaggi di programmazione più diffusi come C# e JavaScript.

L'operatore Ternary (?) può essere molto comodo quando si costruiscono rapidamente nuove proprietà JSON. Ad esempio, ora è possibile scrivere query per classificare i livelli di istruzione in forma leggibile, ad esempio principiante/intermedio/avanzati, come mostrato di seguito.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

È anche possibile annidare le chiamate all'operatore come nella query seguente.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Come con altri operatori di query, se le proprietà cui viene fatto riferimento nell'espressione condizionale non sono presenti in alcun documento o se i tipi confrontati sono diversi, tali documenti verranno esclusi dai risultati della query.

L'operatore Coalesce (??) può essere usato per verificare se una proprietà è presente (definita) in un documento. Questo risulta utile per le query su dati semistrutturati o di tipo misto Ad esempio, questa query restituisce il valore "lastName" se è presente oppure il valore "surname" se non è presente.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### Funzione di accesso della proprietà di delimitazione
È anche possibile accedere alle proprietà mediante l'operatore della proprietà di delimitazione `[]`. Ad esempio, `SELECT c.grade` e `SELECT c["grade"]` sono equivalenti. Questa sintassi risulta utile quando occorre usare i caratteri di escape per una proprietà che contiene spazi, caratteri speciali o condivide lo stesso nome di una parola chiave SQL o una parola riservata.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## Clausola SELECT
La clausola SELECT (**`SELECT <select_list>`**) è obbligatoria e specifica quali valori saranno recuperati dalla query, proprio come in ANSI-SQL. Il sottoinsieme filtrato dai documenti di origine viene passato alla fase di proiezione, in cui vengono recuperati i valori JSON specificati viene costruito un nuovo oggetto JSON, per ogni input passato ad esso.

L'esempio seguente illustra una tipica query SELECT.

**Query**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


### Proprietà annidate
Nell'esempio seguente vengono proiettate due proprietà annidate `f.address.state` e `f.address.city`.

**Query**

	SELECT f.address.state, f.address.city
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	  "state": "WA", 
	  "city": "seattle"
	}]


La proiezione supporta anche le espressioni JSON, come illustrato nell'esempio seguente.

**Query**

	SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle", 
	    "name": "AndersenFamily"
	  }
	}]


Verrà ora esaminato il ruolo di `$1`. La clausola `SELECT` deve creare un oggetto JSON e, poiché non è stata fornita alcuna chiave, verranno usati i nomi di variabile di argomento implicita che iniziano per `$1`. Ad esempio, questa query restituisce due variabili di argomento implicite, etichettate `$1` e `$2`.

**Query**

	SELECT { "state": f.address.state, "city": f.address.city }, 
	       { "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "$2": {
	    "name": "AndersenFamily"
	  }
	}]


### Aliasing
L'esempio precedente verrà ora esteso con l'aliasing esplicito dei valori. AS è la parola chiave usata per l'aliasing. Si noti che è facoltativo, come è possibile vedere durante la proiezione del secondo valore come `NameInfo`.

Nel caso in cui una query avesse due proprietà con lo stesso nome, è necessario usare l'aliasing per rinominare una o entrambe le proprietà, in modo da evitare ambiguità nel risultato proiettato.

**Query**

	SELECT 
	       { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
	       { "name": f.id } NameInfo
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	  "AddressInfo": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "NameInfo": {
	    "name": "AndersenFamily"
	  }
	}]


### Espressioni scalari
Oltre ai riferimenti di proprietà, la clausola SELECT supporta anche le espressioni scalari, ad esempio le costanti, le espressioni aritmetiche, le espressioni logiche e così via. Ad esempio, ecco una semplice query "Hello World".

**Query**

	SELECT "Hello World"

**Risultati**

	[{
	  "$1": "Hello World"
	}]


Di seguito è presentato un esempio più complesso che usa un'espressione scalare.

**Query**

	SELECT ((2 + 11 % 7)-2)/3	

**Risultati**

	[{
	  "$1": 1.33333
	}]


Nell'esempio seguente, il risultato dell'espressione scalare è un valore booleano.

**Query**

	SELECT f.address.city = f.address.state AS AreFromSameCityState
	FROM Families f	

**Risultati**

	[
	  {
	    "AreFromSameCityState": false
	  }, 
	  {
	    "AreFromSameCityState": true
	  }
	]


### Creazione di oggetti e matrici
Un'altra funzione fondamentale del linguaggio SQL di DocumentDB è la creazione di matrici/oggetti. Nell'esempio precedente si è osservato che è stato creato un nuovo oggetto JSON. In modo analogo, è possibile creare matrici, come illustrato negli esempi seguenti.

**Query**

	SELECT [f.address.city, f.address.state] AS CityState 
	FROM Families f	

**Risultati**

	[
	  {
	    "CityState": [
	      "seattle", 
	      "WA"
	    ]
	  }, 
	  {
	    "CityState": [
	      "NY", 
	      "NY"
	    ]
	  }
	]

### Parola chiave VALUE
La parola chiave **VALUE** consente di restituire un valore JSON. Ad esempio, la query mostrata di seguito restituisce l'espressione scalare `"Hello World"` invece di `{$1: "Hello World"}`.

**Query**

	SELECT VALUE "Hello World"

**Risultati**

	[
	  "Hello World"
	]


La query seguente restituisce il valore JSON senza l'etichetta `"address"` nei risultati.

**Query**

	SELECT VALUE f.address
	FROM Families f	

**Risultati**

	[
	  {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }, 
	  {
	    "state": "NY", 
	    "county": "Manhattan", 
	    "city": "NY"
	  }
	]

L'esempio seguente estende questo risultato mostrando come restituire valori primitivi JSON (il livello foglia dell'albero JSON).

**Query**

	SELECT VALUE f.address.state
	FROM Families f	

**Risultati**

	[
	  "WA",
	  "NY"
	]


###Operatore *
L'operatore speciale (***) è supportato per proiettare il documento così com'è. Quando usato, deve essere l'unico campo proiettato. Benché una query come `SELECT * FROM Families f` sia valida, `SELECT VALUE * FROM Families f ` e `SELECT *, f.id FROM Families f ` non lo sono.

**Query**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Risultati**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]

###Operatore TOP
La parola chiave TOP può essere usata per limitare il numero di valori restituiti da una query. Se si usa TOP in combinazione con la clausola ORDER BY, il set di risultati è limitato al primo numero N di valori ordinati. In caso contrario, restituisce il primo numero N di risultati in un ordine non definito. Come procedura consigliata, in un'istruzione SELECT, usare sempre una clausola ORDER BY con la clausola TOP. Questo è l'unico modo per indicare in modo prevedibile le righe interessate dalla clausola TOP.


**Query**

	SELECT TOP 1 * 
	FROM Families f 

**Risultati**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]

È possibile usare TOP con un valore costante (come illustrato in precedenza) o con un valore della variabile usando le query con parametri. Per altre informazioni dettagliate, vedere le query con parametri seguenti.

## Clausola ORDER BY
Come in SQL ANSI, è possibile includere una clausola Order By facoltativa durante l'esecuzione di query. La clausola può includere un argomento ASC/DESC facoltativo per specificare l'ordine in cui i risultati devono essere recuperati. Per un'analisi più approfondita di Order By, fare riferimento alla [procedura dettagliata su Order By in DocumentDB](documentdb-orderby.md).

Ad esempio, la query seguente recupera le famiglie in ordine di nome della città di residenza.

**Query**

	SELECT f.id, f.address.city
	FROM Families f 
	ORDER BY f.address.city
	
**Risultati**
	
	[
	  {
	    "id": "WakefieldFamily",
	    "city": "NY"
	  },
	  {
	    "id": "AndersenFamily",
	    "city": "Seattle"	
	  }
	]

La query seguente recupera le famiglie in ordine di data di creazione, archiviata come numero che rappresenta il periodo, ovvero il tempo trascorso dall'1 gennaio 1970 in secondi.

**Query**

	SELECT f.id, f.creationDate
	FROM Families f 
	ORDER BY f.creationDate DESC
	
**Risultati**
	
	[
	  {
	    "id": "WakefieldFamily",
	    "creationDate": 1431620462
	  },
	  {
	    "id": "AndersenFamily",
	    "creationDate": 1431620472	
	  }
	]
	
## Concetti avanzati relativi ai database e alle query SQL
### Iterazione
Nel linguaggio SQL di DocumentDB è stato aggiunto un nuovo costrutto tramite la parola chiave **IN** per fornire supporto all'iterazione nelle matrici JSON. L'origine FROM fornisce supporto per l'iterazione. Esaminare l'esempio seguente:

**Query**

	SELECT * 
	FROM Families.children

**Risultati**

	[
	  [
	    {
	      "firstName": "Henriette Thaulow", 
	      "gender": "female", 
	      "grade": 5, 
	      "pets": [{ "givenName": "Fluffy"}]
	    }
	  ], 
	  [
	    {
	        "familyName": "Merriam", 
	        "givenName": "Jesse", 
	        "gender": "female", 
	        "grade": 1
	    }, 
	    {
	        "familyName": "Miller", 
	        "givenName": "Lisa", 
	        "gender": "female", 
	        "grade": 8
	    }
	  ]
	]

Esaminare ora un'altra query che esegue l'iterazione sui figli nella raccolta. Notare la differenza nella matrice di output. Questo esempio suddivide `children` e converte i risultati in un'unica matrice.

**Query**

	SELECT * 
	FROM c IN Families.children

**Risultati**

	[
	  {
	      "firstName": "Henriette Thaulow",
	      "gender": "female",
	      "grade": 5,
	      "pets": [{ "givenName": "Fluffy" }]
	  },
	  {
	      "familyName": "Merriam",
	      "givenName": "Jesse",
	      "gender": "female",
	      "grade": 1
	  },
	  {
	      "familyName": "Miller",
	      "givenName": "Lisa",
	      "gender": "female",
	      "grade": 8
	  }
	]

Può essere usato per filtrare ulteriormente ciascuna voce individuale della matrice, come illustrato nell'esempio seguente.

**Query**

	SELECT c.givenName
	FROM c IN Families.children
	WHERE c.grade = 8

**Risultati**

	[{
	  "givenName": "Lisa"
	}]

### Join
In un database relazionale, la necessità creare un join tra tabelle è molto importante. È il corollario logico della progettazione di schemi normalizzati. Al contrario, DocumentDB gestisce un modello dati denormalizzato di documenti senza schema. È l'equivalente logico di un "self-join".

La sintassi supportata dal linguaggio è <from\_source1> JOIN <from\_source2> JOIN ... JOIN <from\_sourceN>. In generale, restituisce un set di tuple **N** (tupla con valori **N**). Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias della raccolta sui rispettivi set. In altri termini, si tratta del prodotto incrociato completo dei set che partecipano al join.

Gli esempi seguenti illustrano il funzionamento della clausola JOIN. Nell'esempio seguente, il risultato è vuoto perché il prodotto incrociato di ciascun documento dall'origine e un set vuoto è a sua volta vuoto.

**Query**

	SELECT f.id
	FROM Families f
	JOIN f.NonExistent

**Risultati**

	[{
	}]


Nell'esempio seguente il join avviene tra la radice del documento e la sottoradice di `children`. È un prodotto incrociato tra due oggetti JSON. Il fatto che i figli siano una matrice non è effettivo nel JOIN in quanto in questo esempio si ha a che fare con una singola radice che è anche la matrice dei figli. Di conseguenza, il risultato contiene solo due risultati, perché il prodotto incrociato di ogni documento con la matrice produce esattamente un solo documento.

**Query**

	SELECT f.id
	FROM Families f
	JOIN f.children
 
**Risultati**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]


L'esempio seguente illustra un join più convenzionale:

**Query**

	SELECT f.id
	FROM Families f
	JOIN c IN f.children 

**Risultati**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]



Per prima cosa occorre notare che il valore `from_source` della clausola **JOIN** è un iteratore. Pertanto il flusso in questo caso è il seguente:

-	Espandere ciascun elemento figlio **c** nella matrice.
-	Applicare un prodotto incrociato con la radice del documento **f** con ogni elemento figlio **c** che è stato convertito nel primo passaggio.
-	Infine, proiettare solo la proprietà nome **f** dell'oggetto radice.

Il primo documento (`AndersenFamily`) contiene solo un elemento figlio, quindi il set di risultati contiene un singolo oggetto corrispondente a questo documento. Il secondo documento (`WakefieldFamily`) contiene due elementi figlio. Quindi, il prodotto incrociato genera un oggetto separato per ogni figli, dando come risultato due oggetti, uno per ogni figlio corrispondente a questo documento. Da notare che i campi radice in entrambi i documenti saranno uguali, proprio come ci si aspetterebbe in un prodotto incrociato.

La vera utilità del JOIN consiste nel formare tuple dal prodotto incrociato in una forma che altrimenti sarebbe difficile proiettare. Inoltre, come illustrato nell'esempio seguente, è possibile filtrare la combinazione di una tupla che consenta all'utente di scegliere una condizione soddisfatta dall'insieme delle tuple.

**Query**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
 
**Risultati**

	[
	  {
	    "familyName": "AndersenFamily", 
	    "childFirstName": "Henriette Thaulow", 
	    "petName": "Fluffy"
	  }, 
	  {
	    "familyName": "WakefieldFamily", 
	    "childGivenName": "Jesse", 
	    "petName": "Goofy"
	  }, 
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]



Questo esempio è un'estensione naturale del precedente e illustra l'esecuzione di un doppio join. Il prodotto incrociato può quindi essere visualizzato come lo pseudo-codice seguente.

	for-each(Family f in Families)
	{	
		for-each(Child c in f.children)
		{
			for-each(Pet p in c.pets)
			{
				return (Tuple(f.id AS familyName, 
	              c.givenName AS childGivenName, 
	              c.firstName AS childFirstName,
	              p.givenName AS petName));
			}
		}
	}

`AndersenFamily` ha un figlio che ha un animale domestico. Il prodotto incrociato genera dunque una riga (1*1*1) da questa famiglia. Tuttavia, la famiglia WakefieldFamily ha due figli, ma un solo figlio, "Jesse", ha animali domestici. Jesse ha 2 animali domestici, il prodotto incrociato genera dunque 1*1*2 = 2 righe da questa famiglia.

Nell'esempio successivo è presente un filtro aggiuntivo su `pet`. In tal modo vengono escluse tutte le tuple laddove il nome dell'animale non è "Shadow". Notare che è possibile creare tuple da matrici, filtrare in base a uno qualsiasi degli elementi della tupla e proiettare qualsiasi combinazione degli elementi.

**Query**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
	WHERE p.givenName = "Shadow"

**Risultati**

	[
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]


## Integrazione JavaScript
DocumentDB offre un modello di programmazione per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nelle raccolte in termini di stored procedure e trigger. Ciò consente quanto segue:

-	Possibilità di eseguire query e operazioni CRUD transazionali con prestazioni elevate a fronte dei documenti in una raccolta grazie alla stretta integrazione del runtime JavaScript direttamente nel motore di database.
-	Modellazione naturale del flusso di controllo, definizione dell'ambito delle variabili e assegnazione e integrazione di primitivi di gestione delle eccezioni con transazioni di database. Per altri dettagli sul supporto di DocumentDB per l'integrazione di JavaScript, vedere la documentazione relativa alla programmabilità lato server di JavaScript.

###Funzioni definite dall'utente (UDF)
Oltre ai tipi già specificati in questo articolo, il linguaggio SQL di DocumentDB offre il supporto per le funzioni definite dall'utente (UDF). In particolare, le UDF scalari sono supportate laddove gli sviluppatori possono passare zero o molti argomenti e restituire un unico argomento. Verrà quindi eseguito un controllo per verificare che ciascuno di questi argomenti sia un valore JSON legale.

La sintassi SQL di DocumentDB viene estesa per supportare la logica delle applicazioni personalizzata usando le funzioni definite dall'utente. Le UDF possono essere registrate con DocumentDB ed è quindi possibile fare loro riferimento come parte di una query SQL. In effetti, le UDF sono progettate espressamente per essere richiamate dalle query. Come corollario a questa scelta, le UDF non hanno accesso all'oggetto di contesto a cui possono invece accedere altri tipi di Javascript (stored procedure e trigger). Poiché le query vengono eseguite in sola lettura, è possibile eseguirle sulle repliche primarie o secondarie. Di conseguenza, a differenza di altri tipi di JavaScript, le UDF vengono progettate per l'esecuzione sulle repliche secondarie.

Di seguito è riportato un esempio di come è possibile registrare una UDF nel database di DocumentDB, in maniera specifica in una raccolta di documenti.

   
	   UserDefinedFunction regexMatchUdf = new UserDefinedFunction
	   {
	       Id = "REGEX_MATCH",
	       Body = @"function (input, pattern) { 
	                   return input.match(pattern) !== null;
	               };",
	   };
	   
	   UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
	       UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
	       regexMatchUdf).Result;  
                                                                             
Nell'esempio precedente è stata creata una UDF, denominata `REGEX_MATCH`. Accetta due valori stringa JSON `input` e `pattern` e controlla se il primo corrisponde al modello specificato nel secondo mediante la funzione string.match() di JavaScript.


È ora possibile usare questa UDF in una query in una proiezione. Le UDF devono essere qualificate con il prefisso con distinzione tra maiuscole e minuscole "udf." quando chiamate dall'interno delle query.

>[AZURE.NOTE] Prima del 17/03/2015, DocumentDB supportava le chiamate UDF senza il prefisso "udf.", come SELECT REGEX\_MATCH(). Questo modello di chiamata è stato deprecato.

**Query**

	SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
	FROM Families

**Risultati**

	[
	  {
	    "$1": true
	  }, 
	  {
	    "$1": false
	  }
	]

È anche possibile usare l'UDF all'interno di un filtro, come mostrato nell'esempio seguente, anch'esso qualificato con il prefisso "udf.":

**Query**

	SELECT Families.id, Families.address.city
	FROM Families
	WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Risultati**

	[{
	    "id": "AndersenFamily",
	    "city": "Seattle"
	}]


In sostanza, le UDF sono espressioni scalari valide che è possibile usare sia nelle proiezioni sia nei filtri.

Per ampliare la potenza delle UDF, verrà ora analizzato un altro esempio che prevede la logica condizionale:

	   UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
	   {
	       Id = "SEALEVEL",
	       Body = @"function(city) {
	       		switch (city) {
	       		    case 'seattle':
	       		        return 520;
	       		    case 'NY':
	       		        return 410;
	       		    case 'Chicago':
	       		        return 673;
	       		    default:
	       		        return -1;
	                }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
	
	
Di seguito è riportato un esempio per l'esercitazione con le UDF.

**Query**

	SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
	FROM Families f	

**Risultati**

	 [
	  {
	    "city": "seattle", 
	    "seaLevel": 520
	  }, 
	  {
	    "city": "NY", 
	    "seaLevel": 410
	  }
	]


Come mostra l'esempio precedente, le UDF integrano la potenza del linguaggio JavaScript con quella del linguaggio SQL di DocumentDB per fornire un'interfaccia programmabile avanzata con la quale eseguire una logica condizionale e procedurale complessa con l'ausilio delle capacità di runtime JavaScript integrate.

Il linguaggio SQL di DocumentDB fornisce gli argomenti alle UDF per ogni documento nel database di origine nella fase corrente (clausola WHERE o clausola SELECT) di elaborazione della UDF. Il risultato verrà incorporato in maniera uniforme nella pipeline di esecuzione generale. Se le proprietà a cui fanno riferimento i parametri della UDF non sono disponibili nel valore JSON, il parametro verrà considerato come Undefined, quindi la chiamata alla UDF verrà interamente ignorata. Analogamente, se il risultato della UDF è Undefined, non verrà incluso nel risultato.

Riepilogando, le UDF sono un ottimo strumento per eseguire una logica di business complessa come parte della query.

### Valutazione degli operatori
Essendo un database JSON, DocumentDB fa un confronto con gli operatori JavaScript e la relativa semantica di valutazione. Benché DocumentDB provi a mantenere la semantica di JavaScript in termini di supporto JSON, la valutazione dell'operazione devia in alcune istanze.

A differenza del linguaggio SQL tradizionale, nel linguaggio di query SQL di DocumentDB spesso i tipi di valori non sono noti fino al loro effettivo recupero dal database. Per poter eseguire le query in maniera efficiente, gran parte degli operatori ha rigorosi requisiti di tipi.

Il linguaggio SQL di DocumentDB non esegue conversioni implicite, a differenza di JavaScript. Ad esempio, una query come `SELECT * FROM Person p WHERE p.Age = 21` corrisponde a documenti che contengono una proprietà Age il cui valore è 21. Qualsiasi altro documento la cui proprietà Age corrisponde alla stringa "21", o ad altre possibili variazioni come "021", "21.0", "0021", "00021" e così via, non verrà trovato. Questo comportamento è diverso da quanto avviene in JavaScript, che consente di eseguire implicitamente il cast dei valori di stringa al numero (in base all'operatore, ad esempio: ==). Questa scelta è fondamentale per la ricerca di indici corrispondenti nel linguaggio SQL di DocumentDB.

## Query SQL con parametri
DocumentDB supporta le query con parametri espressi con la consueta notazione @. SQL con parametri fornisce solide capacità di gestione ed escape dell'input utente, evitando l'esposizione accidentale di dati mediante attacchi SQL injection.

Ad esempio, è possibile scrivere una query che accetta come parametri il cognome e lo stato di residenza e quindi eseguirla per diversi valori di cognome e stato di residenza in base all'input dell'utente.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Questa richiesta può quindi essere inviata a DocumentDB come query con parametri JSON, come illustrato di seguito.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

L'argomento può essere impostato su TOP mediante query con parametri, come illustrato di seguito.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

I valori dei parametri possono essere qualsiasi valore JSON valido (stringhe, numeri, valori booleani, valori null, persino matrici o valori JSON annidati). Inoltre, dato che DocumentDB è senza schema, i parametri non vengono convalidati rispetto a qualsiasi tipo.

##Funzioni predefinite
DocumentDB supporta anche una serie di funzioni predefinite per le operazioni comuni, che possono essere usate all'interno di query come le funzioni definite dall'utente (UDF).

<table>
<tr>
<td>Funzioni matematiche</td>	
<td>ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, gradi, PI, radianti, SIN e TAN</td>
</tr>
<tr>
<td>Funzioni di controllo del tipo</td>	
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE</td>
</tr>
<tr>
<td>Funzioni stringa</td>	
<td>CONCAT, CONTAINS, ENDSWITH, INDEX_OF, sinistra, lunghezza, inferiore, LTRIM, REPLACE, replica, inversa, destra, RTRIM, STARTSWITH, SUBSTRING e superiore</td>
</tr>
<tr>
<td>Funzioni di matrice</td>	
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE</td>
</tr>
<tr>
<td>Funzioni spaziali</td>	
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID e ST_ISVALIDDETAILED</td>
</tr>
</table>  

Se attualmente si usa una funzione definita dall'utente (UDF) per cui è ora disponibile una funzione predefinita, è consigliabile usare la corrispondente funzione predefinita perché la sua esecuzione sarà più rapida ed efficiente.

### Funzioni matematiche
Le funzioni matematiche eseguono un calcolo basato in genere su valori di input passati come argomenti e restituiscono un valore numerico. Di seguito è riportata una tabella delle funzioni matematiche predefinite supportate.

<table>
<tr>
<td><strong>Utilizzo</strong></td>
<td><strong>Descrizione</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td>	
<td>Restituisce il valore assoluto (positivo) dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td>	
<td>Restituisce il più piccolo valore integer maggiore di o uguale all'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td>	
<td>Restituisce il valore integer più alto, minore di o uguale all'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td>	
<td>Restituisce l'esponente dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [,base])</a></td>	
<td>Restituisce il logaritmo naturale dell'espressione numerica specificata oppure il logaritmo usando la base specificata</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td>	
<td>Restituisce il valore logaritmico in base 10 dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td>	
<td>Restituisce un valore numerico, arrotondato al valore integer più vicino.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td>	
<td>Restituisce un valore numerico, troncato al valore integer più vicino.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>	
<td>Restituisce la radica quadrata dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">SQUARE (num_expr)</a></td>	
<td>Restituisce il quadrato dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>	
<td>Restituisce la potenza dell'espressione numerica specificata al valore specificato.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGN (num_expr)</a></td>	
<td>Restituisce il valore del segno (-1, 0, 1) dell'espressione numerica specificata.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>	
<td>Restituisce l'angolo, espresso in radianti, il cui coseno corrisponde all'espressione numerica specificata. Denominato anche arcocoseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>	
<td>Restituisce l'angolo, espresso in radianti, il cui seno è l'espressione numerica specificata. Detta anche arcoseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>	
<td>Restituisce l'angolo, espresso in radianti, la cui tangente è l'espressione numerica specificata. Detta anche arcotangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>	
<td>Restituisce l'angolo, espresso in radianti, tra l'asse x positivo e il raggio dall'origine al punto (y, x), dove x e y sono i valori delle due espressioni float specificate.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td>	
<td>Restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td>	
<td>Restituisce la cotangente trigonometrica dell'angolo specificato, espresso in radianti, nell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">GRADI (num_expr)</a></td>	
<td>Restituisce l'angolo corrispondente in gradi di un angolo specificato in radianti.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI ()</a></td>	
<td>Restituisce il valore costante di pi greco.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANTI (num_expr)</a></td>	
<td>Restituisce radianti quando viene immessa un'espressione numerica, espresso in gradi.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td>	
<td>Restituisce il seno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td>	
<td>Restituisce la tangente dell'espressione di input nell'espressione specificata.</td>
</tr>

</table> 

Ad esempio, è ora possibile eseguire query come le seguenti:

**Query**

    SELECT VALUE ABS(-4)

**Risultati**

    [4]

La differenza principale tra le funzioni di DocumentDB confrontate con ANSI SQL è che sono progettate per interagire correttamente con dati senza schema e con schema misto. Se ad esempio si ha un documento in cui manca la proprietà Size oppure caratterizzato da un valore non numerico, come "unknown", il documento viene ignorato invece di restituire un errore.

### Funzioni di controllo del tipo
Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione nell'ambito delle query SQL. Le funzioni di controllo del tipo possono essere usate per determinare il tipo di proprietà all'interno dei documenti al volo, quando è variabile o sconosciuto. Di seguito è riportata una tabella di funzioni predefinite di controllo del tipo supportate.

<table>
<tr>
  <td><strong>Utilizzo</strong></td>
  <td><strong>Descrizione</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Restituisce un valore booleano che indica se il tipo del valore è una matrice.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Restituisce un valore booleano che indica se il tipo del valore è booleano.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Restituisce un valore booleano che indica se il tipo del valore è Null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Restituisce un valore booleano che indica se il tipo del valore è un numero.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Restituisce un valore booleano che indica se il tipo del valore è un oggetto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Restituisce un valore booleano che indica se il tipo del valore è una stringa.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (espressione)</a></td>
  <td>Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (espressione)</a></td>
  <td>Restituisce un valore booleano che indica se il tipo del valore è stringa, numero, valore booleano o Null.</td>
</tr>

</table>

Usando queste funzioni, è ora possibile eseguire query come le seguenti:

**Query**

    SELECT VALUE IS_NUMBER(-4)

**Risultati**

    [true]

### Funzioni stringa
Le funzioni scalari seguenti eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano. Ecco una tabella di funzioni per stringhe:

Uso|Descrizione
---|---
[LUNGHEZZA (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Restituisce il numero di caratteri dell'espressione stringa specificata
[CONCAT (str\_expr, str\_expr [, str\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa.
[SUBSTRING (str\_expr, num\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Restituisce parte di un'espressione stringa.
[STARTSWITH (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo.
[ENDSWITH (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo.
[CONTAINS (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo.
[INDEX\_OF (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Restituisce la posizione iniziale della prima occorrenza della seconda stringa di espressione all'interno della prima espressione stringa specificata oppure -1 se la stringa non viene trovata.
[LEFT (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Restituisce la parte sinistra di una stringa con il numero specificato di caratteri.
[RIGHT (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Restituisce la parte destra di una stringa con il numero specificato di caratteri.
[LTRIM (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Restituisce un'espressione stringa dopo aver rimosso gli spazi vuoti iniziali.
[RTRIM (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Restituisce un'espressione stringa dopo la rimozione di tutti gli spazi vuoti finali.
[INFERIORE (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli.
[SUPERIORE (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli.
[SOSTITUISCI (str\_expr, str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Sostituisce tutte le occorrenze di un valore stringa specificato con un altro valore stringa.
[REPLICATE (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Ripete un valore stringa in un numero di volte specificato.
[REVERSE (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Restituisce l'inverso di un valore stringa.

Usando queste funzioni, è ora possibile eseguire query come le seguenti. Ad esempio, è possibile restituire il nome della famiglia in lettere maiuscole come segue:

**Query**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Risultati**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

In alternativa, è possibile concatenare stringhe come in questo esempio:

**Query**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Risultati**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Le funzioni stringa possono essere usate anche nella clausola WHERE per filtrare i risultati, come nell'esempio seguente:

**Query**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Risultati**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### Funzioni di matrice
Le funzioni scalari seguenti eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice. La tabella seguente include funzioni di matrice predefinite:

Uso|Descrizione
---|---
[ARRAY\_LENGTH (arr\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Restituisce il numero di elementi dell'espressione di matrice specificato.
[ARRAY\_CONCAT (arr\_expr, arr\_expr [, arr\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Restituisce una matrice che rappresenta il risultato della concatenazione di due o più valori della matrice.
[ARRAY\_CONTAINS (arr\_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Restituisce un valore booleano che indica se la matrice contiene il valore specificato.
[ARRAY\_SLICE (arr\_expr, num\_expr [, num\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Restituisce parte di un'espressione di matrice.

Le funzioni di matrice possono essere usate per manipolare le matrici in JSON. Ad esempio, la query seguente restituisce tutti i documenti in cui uno dei genitori è "Robin Wakefield".

**Query**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Risultati**

    [{
      "id": "WakefieldFamily"
    }]

L'esempio seguente usa ARRAY\_LENGTH per ottenere il numero di figli per ogni famiglia.

**Query**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Risultati**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### Funzioni spaziali

DocumentDB supporta le seguenti funzioni predefinite di Open Geospatial Consortium (OGC) per l'esecuzione di query geospaziali. Per ulteriori informazioni sul supporto geospaziale in DocumentDB, vedere [Uso dei dati geospaziali in Azure DocumentDB](documentdb-geospatial.md).

<table>
<tr>
  <td><strong>Utilizzo</strong></td>
  <td><strong>Descrizione</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Restituisce la distanza tra le due espressioni punto GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Restituisce un'espressione booleana che indica se il punto GeoJSON specificato nel primo argomento è all'interno del poligono GeoJSON nel secondo argomento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Restituisce un valore booleano che indica se l'espressione punto o poligono GeoJSON specificata è valida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Restituisce un valore JSON che contiene un valore booleano valore se l'espressione punto o poligono GeoJSON specificata è valida e, se non valida, anche il motivo come valore stringa.</td>
</tr>
</table>

Le funzioni spaziali possono essere utilizzate per eseguire query di prossimità rispetto ai dati spaziali. Ad esempio, di seguito è riportata una query che restituisce tutti i documenti della famiglia entro 30 km della posizione specificata utilizzando la funzione predefinita ST\_DISTANCE.

**Query**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Risultati**

    [{
      "id": "WakefieldFamily"
    }]

Se si include l'indicizzazione spaziale nel criterio di indicizzazione, le "query distance" verranno servite in modo efficiente tramite l'indice. Per altre informazioni sull'indicizzazione spaziale, vedere la sezione seguente. Se non si dispone di un indice spaziale per i percorsi specificati, è comunque possibile eseguire query spaziali specificando `x-ms-documentdb-query-enable-scan` intestazione della richiesta con il valore impostato su "true". In .NET, questa operazione può essere eseguita passando l’argomento facoltativo **FeedOptions** alle query con [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) impostato su true.

ST\_WITHIN può essere utilizzato per controllare se un punto si trova all'interno di un poligono. I poligoni vengono comunemente utilizzati per rappresentare limiti come codici postali, confini di stato o formazioni naturali. Ancora una volta, se si include l'indicizzazione spaziale nel criterio di indicizzazione, le query "within" verranno servite in modo efficiente tramite l'indice.

Gli argomenti Polygon in ST\_WITHIN possono contenere solo un anello singolo, ad esempio i poligoni non devono contenere aree vuote. Controllare i [limiti di DocumentDB](documentdb-limits.md) per il numero massimo di punti consentiti in un poligono per una query ST\_WITHIN.

**Query**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
    	'type':'Polygon', 
    	'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Risultati**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Come per il funzionamento di tipi non corrispondenti nella query di DocumentDB, se il valore del percorso specificato nell'argomento è non corretto o non valido, verrà valutato **undefined** e il documento valutato verrà ignorato dai risultati della query. Se la query non restituisce alcun risultato, eseguire ST\_ISVALIDDETAILED per eseguire il debug del tipo spatail non valido.

ST\_ISVALID e ST\_ISVALIDDETAILED possono essere utilizzati per verificare la validità di un oggetto spaziale. Ad esempio, la seguente query controlla la validità di un punto con un valore di latitudine fuori scala (-132,8). ST\_ISVALID restituisce solo un valore booleano e ST\_ISVALIDDETAILED restituisce il valore booleano e una stringa contenente il motivo per cui è considerato non valido.

**Query**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Risultati**

    [{
      "$1": false
    }]

Queste funzioni possono essere utilizzate anche per convalidare i poligoni. Ad esempio, qui utilizziamo ST\_ISVALIDDETAILED per convalidare un poligono che non è chiuso.

**Query**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
    	[ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
    	]]})

**Risultati**

    [{
       "$1": { 
      	  "valid": false, 
      	  "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
      	}
    }]
    
Viene eseguito il wrapping di funzioni spaziali e della sintassi SQL per DocumentDB. Verrà ora esaminato il funzionamento delle query LINQ e verrà illustrato il modo in cui interagiscono con la sintassi esaminata fino ad ora.

## Da LINQ a SQL di DocumentDB
LINQ è un modello di programmazione .NET che esprime il calcolo come query su flussi di oggetti. DocumentDB fornisce una libreria lato client che si interfaccia con LINQ agevolando una conversione tra oggetti JSON e .NET e un mapping da un sottoinsieme di query LINQ alle query di DocumentDB.

Nell'immagine seguente è illustrata l'architettura di supporto delle query LINQ usando DocumentDB. Con il client di DocumentDB, gli sviluppatori possono creare un oggetto **IQueryable** che comunica una query al provider di query di DocumentDB, il quale a sua volta traduce la query LINQ in una query di DocumentDB. Questa viene quindi passata al server di DocumentDB per recuperare un set di risultati in formato JSON. I risultati restituiti vengono deserializzati in un flusso di oggetti .NET sul lato client.

![Architettura di supporto delle query LINQ usando DocumentDB - Sintassi SQL, linguaggio di query JSON, concetti relativi ai database e query SQL][1]
 


### Mapping .NET e JSON
Il mapping tra oggetti .NET e documenti JSON avviene naturalmente: ogni campo del membro dati viene mappato a un oggetto JSON, in cui il nome del campo viene mappato alla parte "chiave" dell'oggetto e la parte "valore" viene mappata in modo ricorsivo alla parte del valore dell'oggetto. Si consideri l'esempio seguente. L'oggetto Family creato viene mappato al documento JSON, come illustrato di seguito. Viceversa, il documento JSON viene mappato nuovamente a un oggetto .NET.

**Classe C#**

	public class Family
	{
	    [JsonProperty(PropertyName="id")]
	    public string Id;
	    public Parent[] parents;
	    public Child[] children;
	    public bool isRegistered;
	};
	
	public struct Parent
	{
	    public string familyName;
	    public string givenName;
	};
	
	public class Child
	{
	    public string familyName;
	    public string givenName;
	    public string gender;
	    public int grade;
	    public List<Pet> pets;
	};
	
	public class Pet
	{
	    public string givenName;
	};
	
	public class Address
	{
	    public string state;
	    public string county;
	    public string city;
	};
	
	// Create a Family object.
	Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
	Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
	Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
	Pet pet = new Pet { givenName = "Fluffy" };
	Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
	Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", 
	            "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	          "familyName": "Miller", 
	          "givenName": "Lisa", 
	          "gender": "female", 
	          "grade": 8 
	        }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	};



### Traduzione LINQ in SQL
Il provider di query di DocumentDB esegue con il massimo impegno un mapping da una query LINQ in una query SQL di DocumentDB. Nella descrizione seguente si presuppone che il lettore abbia una certa familiarità con LINQ.

In primo luogo, per il sistema di tipi sono supportati tutti i tipi primitivi JSON: tipi numerici, booleani, stringa e null. Sono supportati solo questi tipi JSON. Sono supportate le seguenti espressioni scalari.

-	Valori costanti: includono i valori costanti dei tipo di dati primitivi al momento della valutazione della query.

-	Espressioni indice della matrice/di proprietà: queste espressioni si riferiscono alla proprietà di un oggetto o di un elemento matrice.

		family.Id;
		family.children[0].familyName;
		family.children[0].grade;
		family.children[n].grade; //n is an int variable

-	Espressioni aritmetiche: includono espressioni aritmetiche comuni su valori numerici e booleani. Per un elenco completo, fare riferimento alle specifiche di SQL.

		2 * family.children[0].grade;
		x + y;

-	Espressione di confronto stringhe: includono il confronto di un valore di stringa con un valore di stringa costante.
 
		mother.familyName == "Smith";
		child.givenName == s; //s is a string variable

-	Espressione di creazione oggetto/matrice: restituisce un oggetto di tipo valore composito o tipo anonimo o ancora un matrice di tali oggetti. Questi valori non possono essere annidati.

		new Parent { familyName = "Smith", givenName = "Joe" };
		new { first = 1, second = 2 }; //an anonymous type with 2 fields              
		new int[] { 3, child.grade, 5 };

### Elenco di operatori LINQ supportati
Di seguito è riportato un elenco di operatori LINQ supportati nel provider LINQ incluso in DocumentDB .NET SDK.

-	**Select**: le proiezioni convertono in SQL SELECT inclusa la costruzione dell'oggetto.
-	**Where**: i filtri convertono in SQL WHERE e supportano la conversione tra && , || e ! in operatori SQL.
-	**SelectMany**: consente la rimozione di matrici nella clausola SQL JOIN. Può essere usato per concatenare/annidare le espressioni per filtrare in base agli elementi della matrice.
-	**OrderBy e OrderByDescending**: converte in ORDER BY crescente o decrescente.
-	**CompareTo**: converte in confronti di intervallo. In genere usato per le stringhe in quanto non sono confrontabili in .NET
-	**Take**: converte in SQL TOP per limitare i risultati da una query.
-	**Math Functions**: supporta la conversione da Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan e Truncate di .NET nelle funzioni predefinite di SQL equivalenti.
-	**String Functions**: supporta la conversione da Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString e ToUpper di .NET nelle funzioni predefinite di SQL equivalenti.
-	**Array Functions**: supporta la conversione da Concat, Contains e Count di .NET nelle funzioni predefinite di SQL equivalenti.
-	**Geospatial Extension Functions**: supporta la conversione dai metodi sthub Distance, Within, IsValid e IsValidDetailed nelle funzioni predefinite di SQL equivalenti.
-	**User Defined Function Extension Function**: supporta la conversione dal metodo stub UserDefinedFunctionProvider.Invoke alla funzione corrispondente definita dall'utente.
-	**Miscellaneous**: supporta la conversione degli operatori condizionali e di unione. Consente la conversione di Contains in String CONTAINS, ARRAY\_CONTAINS o SQL IN in base al contesto.

### Operatori di query SQL
Di seguito sono riportati alcuni esempi che illustrano in che modo gli operatori di query LINQ standard vengono tradotti nelle query di DocumentDB.

#### Operatore Select
La sintassi è `input.Select(x => f(x))`, dove `f` è un'espressione scalare.

**Espressione lambda LINQ**

	input.Select(family => family.parents[0].familyName);

**SQL**

	SELECT VALUE f.parents[0].familyName
	FROM Families f



**Espressione lambda LINQ**

	input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL**

	SELECT VALUE f.children[0].grade + c
	FROM Families f 



**Espressione lambda LINQ**

	input.Select(family => new
	{
	    name = family.children[0].familyName,
	    grade = family.children[0].grade + 3
	});


**SQL**

	SELECT VALUE {"name":f.children[0].familyName, 
	              "grade": f.children[0].grade + 3 }
	FROM Families f



#### Operatore SelectMany
La sintassi è `input.SelectMany(x => f(x))`, dove `f` è un'espressione scalare che restituisce un tipo di raccolta.

**Espressione lambda LINQ**

	input.SelectMany(family => family.children);

**SQL**

	SELECT VALUE child
	FROM child IN Families.children



#### Operatore Where
La sintassi è `input.Where(x => f(x))`, dove `f` è un'espressione scalare che restituisce un valore booleano.

**Espressione lambda LINQ**

	input.Where(family=> family.parents[0].familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith" 



**Espressione lambda LINQ**

	input.Where(
	    family => family.parents[0].familyName == "Smith" && 
	    family.children[0].grade < 3);

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"
	AND f.children[0].grade < 3


### Query SQL composte
Gli operatori sopra riportati possono essere composti in modo da formare query più potenti. Poiché DocumentDB supporta raccolte nidificate, la composizione può essere concatenata o annidata.

#### Concatenazione 

La sintassi è `input(.|.SelectMany())(.Select()|.Where())*`. Una query concatenata può iniziare con una query `SelectMany` facoltativa, seguita da più operatori `Select` o `Where`.


**Espressione lambda LINQ**

	input.Select(family=>family.parents[0])
	    .Where(familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"



**Espressione lambda LINQ**

	input.Where(family => family.children[0].grade > 3)
	    .Select(family => family.parents[0].familyName);

**SQL**

	SELECT VALUE f.parents[0].familyName
	FROM Families f
	WHERE f.children[0].grade > 3



**Espressione lambda LINQ**

	input.Select(family => new { grade=family.children[0].grade}).
	    Where(anon=> anon.grade < 3);
            
**SQL**

	SELECT *
	FROM Families f
	WHERE ({grade: f.children[0].grade}.grade > 3)



**Espressione lambda LINQ**

	input.SelectMany(family => family.parents)
	    .Where(parent => parents.familyName == "Smith");

**SQL**

	SELECT *
	FROM p IN Families.parents
	WHERE p.familyName = "Smith"



#### Annidamento

La sintassi è `input.SelectMany(x=>x.Q())` dove Q è un operatore `Select`, `SelectMany` o `Where`.

In una query annidata, la query più interna viene applicata a ogni elemento della raccolta esterna. Una funzionalità importante è che la query interna può riferirsi ai campi degli elementi nella raccolta esterna come a self-join.

**Espressione lambda LINQ**

	input.SelectMany(family=> 
	    family.parents.Select(p => p.familyName));

**SQL**

	SELECT VALUE p.familyName
	FROM Families f
	JOIN p IN f.parents


**Espressione lambda LINQ**

	input.SelectMany(family => 
	    family.children.Where(child => child.familyName == "Jeff"));
            
**SQL**

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = "Jeff"



**Espressione lambda LINQ**
            
	input.SelectMany(family => family.children.Where(
	    child => child.familyName == family.parents[0].familyName));

**SQL**

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = f.parents[0].familyName


## Esecuzione di query SQL
DocumentDB espone risorse tramite un'API REST che può essere chiamata da qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS. In DocumentDB sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni, come NET, Node.js, JavaScript e Python. L'API REST e le varie librerie supportano tutte l'esecuzione di query tramite SQL. .NET SDK supporta l'esecuzione di query LINQ oltre a SQL.

Negli esempi seguenti viene illustrato come creare una query e inviarla a fronte di un account di database di DocumentDB.

### API REST
DocumentDB offre un modello di programmazione aperto RESTful su HTTP. È possibile effettuare il provisioning degli account di database usando una sottoscrizione di Azure. Il modello di risorse di DocumentDB è costituito da un set di risorse disponibili in un account di database e indirizzabili singolarmente tramite un URI logico e stabile. Un insieme di risorse viene definito feed nel presente documento. Un account di database è costituito da un set di database, ognuno dei quali include più raccolte, che possono contenere documenti, UDF e altri tipi di risorse.

Il modello di interazione di base con queste risorse usa i verbi HTTP GET, PUT, POST e DELETE con la relativa interpretazione standard. Il verbo POST viene usato per creare una nuova risorsa, per eseguire una stored procedure o per inviare una query di DocumentDB. Le query sono sempre operazioni di sola lettura senza nessun effetto collaterale.

Gli esempi seguenti illustrano il verbo POST per una query di DocumentDB a fronte di una raccolta contenente i due documenti di esempio esaminati finora. La query ha un semplice filtro sulla proprietà nome JSON. Si noti l'uso delle intestazioni `x-ms-documentdb-isquery` e Content-Type `application/query+json` per indicare che l'operazione è una query.


**Richiesta**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
	

**Risultati**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
	x-ms-item-count: 1
	x-ms-request-charge: 0.32
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "id":"AndersenFamily",
	         "lastName":"Andersen",
	         "parents":[  
	            {  
	               "firstName":"Thomas"
	            },
	            {  
	               "firstName":"Mary Kay"
	            }
	         ],
	         "children":[  
	            {  
	               "firstName":"Henriette Thaulow",
	               "gender":"female",
	               "grade":5,
	               "pets":[  
	                  {  
	                     "givenName":"Fluffy"
	                  }
	               ]
	            }
	         ],
	         "address":{  
	            "state":"WA",
	            "county":"King",
	            "city":"seattle"
	         },
	         "_rid":"u1NXANcKogEcAAAAAAAAAA==",
	         "_ts":1407691744,
	         "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
	         "_etag":"00002b00-0000-0000-0000-53e7abe00000",
	         "_attachments":"_attachments\/"
	      }
	   ],
	   "count":1
	}


Il secondo esempio mostra una query più complessa che restituisce più risultati dal join.

**Richiesta**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json
	
    {      
        "query": "SELECT 
				     f.id AS familyName, 
				     c.givenName AS childGivenName, 
				     c.firstName AS childFirstName, 
				     p.givenName AS petName 
				  FROM Families f 
				  JOIN c IN f.children 
				  JOIN p in c.pets",     
        "parameters": [] 
    }


**Risultati**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
	x-ms-item-count: 1
	x-ms-request-charge: 7.84
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "familyName":"AndersenFamily",
	         "childFirstName":"Henriette Thaulow",
	         "petName":"Fluffy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Goofy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Shadow"
	      }
	   ],
	   "count":3
	}


Se il numero di risultati di una query supera le dimensioni di una singola pagina, l'API REST restituisce un token di continuazione attraverso l'intestazione di risposta `x-ms-continuation-token`. I client possono impaginare i risultati includendo l'intestazione nei risultati successivi. È possibile controllare il numero di risultati per pagina anche attraverso l'intestazione di numero `x-ms-max-item-count`.

Per gestire i criteri di coerenza dei dati per le query, usare l'intestazione `x-ms-consistency-level` come tutte le richieste dell'API REST. Ai fini della coerenza della sessione, è necessario anche ripetere l'ultima intestazione cookie `x-ms-session-token` nella richiesta di query. Notare che i criteri di indicizzazione della raccolta sulla quale è stata eseguita la query possono influenzare anche la coerenza dei risultati della query. Con le impostazioni predefinite dei criteri di indicizzazione, per le raccolte l'indice è sempre aggiornato con il contenuto del documento e i risultati della query corrisponderanno alla coerenza scelta per i dati. Se i criteri di indicizzazione vengono ridotti alla modalità differita, le query possono restituire risultati obsoleti. Per altre informazioni, vedere [Livelli di coerenza di DocumentDB][consistency-levels].

Se i criteri di indicizzazione configurati sulla raccolta non possono supportare la query specificata, il server di DocumentDB restituisce il codice di errore 400 (Richiesta non valida). Questo codice viene restituito per le query di intervallo per ricerche hash (uguaglianza) e per i percorsi esplicitamente esclusi dall'indicizzazione. È possibile specificare l'intestazione `x-ms-documentdb-query-enable-scan` per consentire alla query di eseguire una scansione quando non è disponibile un indice.

### C# (.NET) SDK
.NET SDK supporta l'esecuzione di query LINQ ed SQL. Nell'esempio seguente viene illustrato come eseguire la semplice query di filtro introdotta in precedenza in questo documento.


	foreach (var family in client.CreateDocumentQuery(collectionLink, 
	    "SELECT * FROM Families f WHERE f.id = "AndersenFamily""))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

	foreach (var family in (
	    from f in client.CreateDocumentQuery(collectionLink)
	    where f.Id == "AndersenFamily"
	    select f))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in client.CreateDocumentQuery(collectionLink)
	    .Where(f => f.Id == "AndersenFamily")
	    .Select(f => f))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


In questo esempio vengono confrontate due proprietà per l'uguaglianza all'interno di ciascun documento, usando le proiezioni anonime.


	foreach (var family in client.CreateDocumentQuery(collectionLink,
	    @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
	    FROM Families f 
	    WHERE f.address.city = f.address.state"))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
	foreach (var family in (
	    from f in client.CreateDocumentQuery<Family>(collectionLink)
	    where f.address.city == f.address.state
	    select new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .Where(f => f.address.city == f.address.state)
	    .Select(f => new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


Nell'esempio successivo vengono illustrati i join, espressi tramite la clausola SelectMany di LINQ.


	foreach (var pet in client.CreateDocumentQuery(collectionLink,
	      @"SELECT p
	        FROM Families f 
	             JOIN c IN f.children 
	             JOIN p in c.pets 
	        WHERE p.givenName = ""Shadow"""))
	{
	    Console.WriteLine("\tRead {0} from SQL", pet);
	}
	
	// Equivalent in Lambda expressions
	foreach (var pet in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .SelectMany(f => f.children)
	    .SelectMany(c => c.pets)
	    .Where(p => p.givenName == "Shadow"))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", pet);
	}



Il client .NET esegue automaticamente l'iterazione attraverso tutte le pagine dei risultati della query nei blocchi foreach, come sopra illustrato. Le opzioni di query presentate nella sezione dell'API REST sono disponibili anche in .NET SDK usando le classi `FeedOptions` e `FeedResponse` nel metodo CreateDocumentQuery. È possibile controllare il numero di pagine usando l'impostazione `MaxItemCount`.

È anche possibile controllare esplicitamente il paging creando un oggetto `IDocumentQueryable` che usi l'oggetto `IQueryable`, quindi leggendo i valori ` ResponseContinuationToken` e passandoli nuovamente come `RequestContinuationToken` in `FeedOptions`. È possibile impostare `EnableScanInQuery` per abilitare le analisi quando la query non può essere supportata dai criteri di indicizzazione configurati. Per le raccolte partizionate, è possibile usare `PartitionKey` per eseguire la query in una partizione singola, anche se DocumentDB può eseguire l'estrazione automatica dal testo della query, e `EnableCrossPartitionQuery` per eseguire query che potrebbe essere necessario ripetere in più partizioni.

Per altri esempi contenenti query, vedere gli [esempi di .NET in DocumentDB](https://github.com/Azure/azure-documentdb-net).

### API lato server JavaScript 
DocumentDB offre un modello di programmazione per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nelle raccolte usando stored procedure e trigger. La logica JavaScript registrata a livello di raccolta può quindi rilasciare operazioni sui documenti della raccolta specifica. Viene quindi eseguito il wrapping di queste operazioni nelle transazioni ACID Ambient.

L'esempio seguente illustra come usare queryDocuments nell'API del server JavaScript per eseguire query dall'interno di stored procedure e trigger.


	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();
	    var collectionLink = collectionManager.getSelfLink()
	
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        { name: name, author: author },
	        function (err, documentCreated) {
	            if (err) throw new Error(err.message);
	
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function (err, matchingDocuments) {
	                    if (err) throw new Error(err.message);
	context.getResponse().setBody(matchingDocuments.length);
	
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);
	                    }
	                })
	        });
	}


##Riferimenti
1.	[Introduzione ad Azure DocumentDB][introduction]
2.	[Specifica di SQL DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.	[Esempi di .NET in DocumentDB](https://github.com/Azure/azure-documentdb-net)
4.	[Livelli di coerenza in DocumentDB][consistency-levels]
5.	ANSI SQL 2011 [http://www.iso.org/iso/iso\_catalogue/catalogue\_tc/catalogue\_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.	JSON [http://json.org/](http://json.org/)
7.	Specifiche Javascript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
8.	LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx)
9.	Tecniche di valutazione delle query per database di grandi dimensioni [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10.	Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11.	Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12.	Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: un linguaggio non così estraneo per l'elaborazione dati, SIGMOD 2008.
13.     G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 

<!---HONumber=AcomDC_0713_2016-->