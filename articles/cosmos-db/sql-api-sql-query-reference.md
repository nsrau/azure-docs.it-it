---
title: 'Azure Cosmos DB: Riferimento alla query di sintassi SQL | Documenti Microsoft'
description: Documentazione di riferimento per il linguaggio di query di database SQL di Azure Cosmos.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: mimig
ms.openlocfilehash: 64c5e1284cd8a0413ebc73b1659822078f62d2ef
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Riferimento alla sintassi di Cosmos DB SQL Azure

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB supporta l'esecuzione di query di documenti con una familiarità SQL (Structured Query Language) come grammatica su documenti JSON gerarchici senza richiedere uno schema esplicito o la creazione di indici secondari. In questo argomento fornisce la documentazione di riferimento per il linguaggio di query SQL, è compatibile con gli account di API di SQL.

Per una procedura dettagliata del linguaggio di query SQL, vedere [query SQL di Azure Cosmos DB](sql-api-sql-query.md).  
  
Consigliamo di visitare anche il [Query Playground](http://www.documentdb.com/sql/demo) in cui è possibile provare Azure Cosmos DB ed eseguire query SQL usando il set di dati disponibile.  
  
## <a name="select-query"></a>Query SELECT  
Recupera i documenti JSON dal database. Supporta la valutazione delle espressioni, le proiezioni, il filtraggio e i join.  Le convenzioni usate per descrivere le istruzioni SELECT sono riportate nella sezione relativa alle convenzioni della sintassi.  
  
**Sintassi**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Osservazioni:**  
  
 Per i dettagli su ogni clausola, vedere le sezioni seguenti:  
  
-   [Clausola SELECT](#bk_select_query)  
  
-   [Clausola FROM](#bk_from_clause)  
  
-   [Clausola WHERE](#bk_where_clause)  
  
-   [Clausola ORDER BY](#bk_orderby_clause)  
  
Le clausole nell'istruzione SELECT devono essere ordinate come indicato sopra. Una clausola facoltativa può essere omessa. Ma se si usano clausole facoltative, è necessario specificarle nell'ordine corretto.  
  
**Ordine di elaborazione logico dell'istruzione SELECT**  
  
L'ordine in cui vengono elaborate clausole è:  

1.  [Clausola FROM](#bk_from_clause)  
2.  [Clausola WHERE](#bk_where_clause)  
3.  [Clausola ORDER BY](#bk_orderby_clause)  
4.  [Clausola SELECT](#bk_select_query)  

Si noti che l'ordine è diverso da quello in cui appaiono nella sintassi. L'ordinamento è tale che tutti i nuovi simboli introdotti da una clausola elaborata sono visibili e possono essere usati nelle clausole elaborate successivamente. Ad esempio, gli alias dichiarati in una clausola FROM sono accessibili nelle clausole WHERE e SELECT.  

**Spazi vuoti e commenti**  

Tutti i caratteri di spazio vuoto che non fanno parte di una stringa o un identificatore delimitato non fanno parte della grammatica del linguaggio e vengono ignorati durante l'analisi.  

Il linguaggio di query supporta i commenti in stile T-SQL come  

-   istruzione SQL `-- comment text [newline]`  

Anche se i commenti e gli spazi vuoti non hanno alcun significato nella grammatica, devono essere usati per separare i token. Ad esempio: `-1e5` è un token numero singolo, mentre `: – 1 e5` è un token meno seguito dal numero 1 e dall'identificatore e5.  

##  <a name="bk_select_query"></a> Clausola SELECT  
Le clausole nell'istruzione SELECT devono essere ordinate come indicato sopra. Una clausola facoltativa può essere omessa. Ma se si usano clausole facoltative, è necessario specificarle nell'ordine corretto.  

**Sintassi**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argomenti**  
  
 `<select_specification>`  
  
 Proprietà o valore da selezionare per il set di risultati.  
  
 `'*'`  
  
Specifica che il valore deve essere recuperato senza apportare alcuna modifica. In particolare, se il valore elaborato è un oggetto, tutte le proprietà verranno recuperate.  
  
 `<object_property_list>`  
  
Specifica l'elenco di proprietà da recuperare. Ogni valore restituito sarà un oggetto con le proprietà specificate.  
  
`VALUE`  
  
Specifica che deve essere recuperato il valore JSON anziché l'oggetto JSON completo. A differenza di `<property_list>` non esegue il wrapping del valore previsto in un oggetto.  
  
`<scalar_expression>`  
  
Espressione che rappresenta il valore da calcolare. Vedere la sezione [Espressioni scalari](#bk_scalar_expressions) per informazioni dettagliate.  
  
**Osservazioni:**  
  
La sintassi di `SELECT *` è valida solo se la clausola FROM ha dichiarato esattamente un alias. `SELECT *` offre una proiezione dell'identità, che può essere utile se non è necessaria alcuna proiezione. SELECT * è valida solo se viene specificata la clausola FROM e se viene introdotta solo una singola origine di input.  
  
Si noti che `SELECT <select_list>` e `SELECT *` sono "zucchero sintattico" e può essere espressi in alternativa usando semplici istruzioni SELECT, come illustrato di seguito.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     Equivale a:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     Equivale a:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Vedere anche**  
  
[Espressioni scalari](#bk_scalar_expressions)  
[Clausola SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Clausola FROM  
Specifica l'origine o le origini unite in join. La clausola FROM è facoltativa. Se non viene specificata, le altre clausole verranno comunque eseguite come se la clausola FROM specificasse un singolo documento.  
  
**Sintassi**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Argomenti**  
  
`<from_source>`  
  
Specifica un'origine dati, con o senza un alias. Se non viene specificato, l'alias verrà dedotto da `<collection_expression>` usando le seguenti regole:  
  
-   Se l'espressione è un nome di raccolta, come alias verrà usato il nome della raccolta.  
  
-   Se l'espressione è `<collection_expression>` seguito da un nome di proprietà, come alias verrà usato il nome della proprietà. Se l'espressione è un nome di raccolta, come alias verrà usato il nome della raccolta.  
  
AS `input_alias`  
  
Specifica che l'oggetto `input_alias` è un set di valori restituiti dall'espressione di raccolta sottostante.  
 
`input_alias` IN  
  
Specifica che `input_alias` deve rappresentare il set di valori ottenuto eseguendo l'iterazione su tutti gli elementi di ogni matrice restituita dall'espressione di raccolta sottostante. Tutti i valori restituiti dall'espressione di raccolta sottostante che non sono matrici vengono ignorati.  
  
`<collection_expression>`  
  
Specifica l'espressione di raccolta da usare per recuperare i documenti.  
  
`ROOT`  
  
Specifica che il documento deve essere recuperato dalla raccolta predefinita attualmente connessa.  
  
`collection_name`  
  
Specifica che il documento deve essere recuperato dalla raccolta specificata. Il nome della raccolta deve corrispondere al nome della raccolta a cui si è attualmente connessi.  
  
`input_alias`  
  
Specifica che il documento deve essere recuperato dall'altra origine definita dall'alias indicato.  
  
`<collection_expression> '.' property_`  
  
Specifica che il documento deve essere recuperato eseguendo l'accesso alla proprietà `property_name` o all'elemento di matrice array_index per tutti i documenti recuperati dall'espressione di raccolta specificata.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Specifica che il documento deve essere recuperato eseguendo l'accesso alla proprietà `property_name` o all'elemento di matrice array_index per tutti i documenti recuperati dall'espressione di raccolta specificata.  
  
**Osservazioni:**  
  
Tutti gli alias specificati o dedotti in `<from_source>(` devono essere univoci. La sintassi `<collection_expression>.`nome proprietà equivale a `<collection_expression>' ['"property_name"']'`. Tuttavia, la seconda sintassi può essere usata se un nome di proprietà contiene un carattere non identificatore.  
  
**Proprietà mancanti, elementi di matrice mancanti, gestione dei valori non definita**  
  
Se un'espressione di raccolta accede alle proprietà o agli elementi di matrice e il valore non esiste, tale valore verrà ignorato e non elaborato ulteriormente.  
  
**Definizione dell'ambito per il contesto dell'espressione di raccolta**  
  
Un'espressione di raccolta può avere come ambito una raccolta o un documento:  
  
-   Un'espressione ha un ambito raccolta se l'origine dell'espressione di raccolta sottostante è ROOT o `collection_name`. Un'espressione di questo tipo rappresenta un set di documenti recuperati direttamente dalla raccolta e non dipende dall'elaborazione di altre espressioni di raccolta.  
  
-   Un'espressione ha un ambito documento se l'origine dell'espressione di raccolta sottostante è `input_alias` introdotta in precedenza nella query. Tale espressione rappresenta un set di documenti ottenuti dalla valutazione dell'espressione di raccolta nell'ambito di ogni documento appartenente al set associato alla raccolta con alias.  Il set risultante sarà un'unione di set ottenuti dalla valutazione dell'espressione di raccolta per ogni documento del set sottostante.  
  
**Join**  
  
Nella versione corrente, Azure Cosmos DB supporta gli inner join. Altre funzionalità di join saranno presto disponibili.

Gli inner join generano un prodotto incrociato completo dei set che partecipano al join. Il risultato di un join a N vie è un set di tuple a N elementi, in cui ogni valore presente nella tupla è associato al set con alias che partecipa al join e l'accesso al set è possibile facendo riferimento a tale alias in altre clausole.  
  
La valutazione del join dipende dall'ambito del contesto dei set partecipanti:  
  
-  Un join tra il set di raccolta A e il set di raccolta con ambito B genera un prodotto incrociato di tutti gli elementi presenti nei set A e B.
  
-   Un join tra il set A e il set con ambito documento B genera un'unione di tutti i set ottenuti dalla valutazione di un set con ambito documento B per ogni documento del set A.  
  
 Nella versione corrente l'elaborazione delle query supporta al massimo un'espressione con ambito raccolta.  
  
**Esempi di join:**  
  
Verrà ora esaminata la seguente clausola FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Consentire a ogni origine di definire `input_alias1, input_alias2, …, input_aliasN`. Questa clausola FROM restituisce un set di N tuple (tuple con N valori). Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias della raccolta sui rispettivi set.  
  
*Esempio di JOIN 1, con 2 origini:*  
  
- Consentire a `<from_source1>` di avere un ambito raccolta e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source2>` di avere un ambito documento che fa riferimento a input_alias1 e di rappresentare i set:  
  
    {1, 2} per `input_alias1 = A,`  
  
    {3} per `input_alias1 = B,`  
  
    {4, 5} per `input_alias1 = C,`  
  
- La clausola FROM `<from_source1> JOIN <from_source2>` produrrà le tuple seguenti:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*Esempio di JOIN 2, con 3 origini:*  
  
- Consentire a `<from_source1>` di avere un ambito raccolta e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source2>` di avere un ambito documento che fa riferimento `input_alias1` e di rappresentare i set:  
  
    {1, 2} per `input_alias1 = A,`  
  
    {3} per `input_alias1 = B,`  
  
    {4, 5} per `input_alias1 = C,`  
  
- Consentire a `<from_source3>` di avere un ambito documento che fa riferimento `input_alias2` e di rappresentare i set:  
  
    {100, 200} per `input_alias2 = 1,`  
  
    {300} per `input_alias2 = 3,`  
  
- La clausola FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` produrrà le tuple seguenti:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> Mancano le tuple per altri valori di `input_alias1`, `input_alias2`, per cui `<from_source3>` non ha restituito alcun valore.  
  
*Esempio di JOIN 3, con 3 origini:*  
  
- Consentire a <from_source1> di avere un ambito raccolta e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source1>` di avere un ambito raccolta e di rappresentare il set {A, B, C}.  
  
- Consentire a <from_source2> di avere un ambito documento che fa riferimento a input_alias1 e di rappresentare i set:  
  
    {1, 2} per `input_alias1 = A,`  
  
    {3} per `input_alias1 = B,`  
  
    {4, 5} per `input_alias1 = C,`  
  
- Consentire a `<from_source3>` di avere ambito `input_alias1` e rappresentare i set:  
  
    {100, 200} per `input_alias2 = A,`  
  
    {300} per `input_alias2 = C,`  
  
- La clausola FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` produrrà le tuple seguenti:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
> [!NOTE]
> Viene generato un prodotto incrociato tra `<from_source2>` e `<from_source3>` perché entrambi hanno come ambito lo stesso elemento `<from_source1>`.  Sono state create 4 (2x2) tuple con valore A, 0 tuple con valore B (1 x 0) e 2 (2x1) tuple con valore C.  
  
**Vedere anche**  
  
 [Clausola SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Clausola WHERE  
 Specifica la condizione di ricerca per i documenti restituiti dalla query.  
  
 **Sintassi**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argomenti**  
  
-   `<filter_condition>`  
  
     Specifica la condizione che deve essere soddisfatta per i documenti da restituire.  
  
-   `<scalar_expression>`  
  
     Espressione che rappresenta il valore da calcolare. Vedere la sezione [Espressioni scalari](#bk_scalar_expressions) per informazioni dettagliate.  
  
 **Osservazioni:**  
  
 Affinché il documento venga restituito, un'espressione specificata come condizione di filtro deve restituire true. Solo un valore booleano true soddisferà la condizione, i valori non definiti, null, false, numero, matrice o oggetto non soddisfano la condizione.  
  
##  <a name="bk_orderby_clause"></a> Clausola ORDER BY  
 Specifica l'ordinamento dei risultati restituiti dalla query.  
  
 **Sintassi**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argomenti**  
  
-   `<sort_specification>`  
  
     Specifica una proprietà o espressione in cui ordinare il set di risultati della query. Una colonna di ordinamento può essere specificata come alias di nome o di colonna.  
  
     È possibile specificare più colonne di ordinamento. I nomi delle colonne devono essere univoci. La sequenza delle colonne di ordinamento nella clausola ORDER BY definisce l'organizzazione del set di risultati ordinato. Ovvero, il set di risultati viene ordinato in base alla prima proprietà e quindi l'elenco così ordinato viene ordinato in base alla seconda proprietà e così via.  
  
     I nomi delle colonne a cui si fa riferimento nella clausola ORDER BY devono corrispondere a una colonna dell'elenco di selezione o a una colonna definita in una tabella specificata nella clausola FROM senza ambiguità.  
  
-   `<sort_expression>`  
  
     Specifica una singola proprietà o espressione in cui ordinare il set di risultati della query.  
  
-   `<scalar_expression>`  
  
     Vedere la sezione [Espressioni scalari](#bk_scalar_expressions) per informazioni dettagliate.  
  
-   `ASC | DESC`  
  
     Specifica che i valori presenti nella colonna specificata devono essere ordinati in ordine crescente o decrescente. ASC (crescente) esegue l'ordinamento dal valore più basso a quello più alto. DESC (decrescente) esegue l'ordinamento dal valore più alto a quello più basso. L'ordinamento predefinito è ASC (crescente). I valori Null vengono considerati come i valori più bassi possibile.  
  
 **Osservazioni:**  
  
 Benché la sintassi di query supporti più proprietà di ordinamento, il runtime di query di Azure Cosmos DB supporta l'ordinamento solo in base a una singola proprietà e solo in base ai nomi di proprietà, ovvero non in base a proprietà calcolate. L'ordinamento richiede anche che i criteri di indicizzazione includano un indice di intervallo per la proprietà e il tipo specificato, con la massima precisione. Per informazioni dettagliate, fare riferimento alla documentazione sui criteri di indicizzazione.  
  
##  <a name="bk_scalar_expressions"></a> Espressioni scalari  
 Un'espressione scalare è una combinazione di simboli e operatori che si può valutare per ottenere un singolo valore. Le espressioni semplici possono essere costanti, riferimenti a proprietà, riferimenti a elementi di matrice, riferimenti ad alias o chiamate di funzioni. Le espressioni semplici possono essere combinate in espressioni complesse usando gli operatori.  
  
 Per informazioni dettagliate sui valori che possono essere contenuti nelle espressioni scalari, vedere la sezione relativa alle [costanti](#bk_constants).  
  
 **Sintassi**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argomenti**  
  
-   `<constant>`  
  
     Rappresenta un valore costante. Per informazioni dettagliate, vedere la sezione [Costanti](#bk_constants).  
  
-   `input_alias`  
  
     Rappresenta un valore definito dall'elemento `input_alias` introdotto nella clausola `FROM`.  
    Questo valore è sicuramente diverso da un valore **non definito**: i valori **non definiti** presenti nell'input verranno ignorati.  
  
-   `<scalar_expression>.property_name`  
  
     Rappresenta un valore della proprietà di un oggetto. Se la proprietà non esiste o si fa riferimento alla proprietà per un valore che non è un oggetto, l'espressione restituisce un valore **non definito**.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Rappresenta un valore della proprietà con nome `property_name` o un elemento di matrice con indice `array_index` di un oggetto o una matrice. Se l'indice della proprietà o matrice non esiste o si fa riferimento all'indice della proprietà o matrice per un valore che non è un oggetto o una matrice, l'espressione restituisce un valore non definito.  
  
-   `unary_operator <scalar_expression>`  
  
     Rappresenta un operatore applicato a un singolo valore. Per informazioni dettagliate, vedere la sezione [Operatori](#bk_operators).  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Rappresenta un operatore applicato a due valori. Per informazioni dettagliate, vedere la sezione [Operatori](#bk_operators).  
  
-   `<scalar_function_expression>`  
  
     Rappresenta un valore definito da un risultato di una chiamata di funzione.  
  
-   `udf_scalar_function`  
  
     Nome della funzione scalare definita dall'utente.  
  
-   `builtin_scalar_function`  
  
     Nome della funzione scalare predefinita.  
  
-   `<create_object_expression>`  
  
     Rappresenta un valore ottenuto creando un nuovo oggetto con proprietà specificate e i relativi valori.  
  
-   `<create_array_expression>`  
  
     Rappresenta un valore ottenuto creando una nuova matrice con valori specificati come elementi  
  
-   `parameter_name`  
  
     Rappresenta un valore del nome di parametro specificato. I nomi di parametro devono avere un singolo carattere @ come primo carattere.  
  
 **Osservazioni:**  
  
 Quando si chiama una funzione scalare predefinita o definita dall'utente è necessario definire tutti gli argomenti. Se uno degli argomenti non è definito, la funzione non verrà chiamata e il risultato sarà indefinito.  
  
 Quando si crea un oggetto, qualsiasi proprietà a cui viene assegnato un valore non definito verrà ignorata e non inclusa nell'oggetto creato.  
  
 Quando si crea una matrice, qualsiasi valore di elemento a cui viene assegnato un valore **non definito** verrà ignorato e non incluso nell'oggetto creato. Ciò causa la sostituzione dell'elemento da parte dell'elemento definito successivo in modo tale che la matrice creata non abbia indici ignorati.  
  
##  <a name="bk_operators"></a> Operatori  
 Questa sezione descrive gli operatori supportati. Ogni operatore può essere assegnato a esattamente una categoria.  
  
 Vedere la tabella delle **categorie di operatori** riportata di seguito per informazioni dettagliate sulla gestione dei valori **non definiti**, sui requisiti di tipi per valori di input e sulla gestione dei valori con tipi non corrispondenti.  
  
 **Categorie di operatori:**  
  
|**Categoria**|**Dettagli**|  
|-|-|  
|**aritmetico**|L'operatore prevede che gli input siano numerici. Anche l'output è un numero. Se uno degli input è **non definito** o di tipo diverso da un numero, il risultato è **non definito**.|  
|**bit per bit**|L'operatore prevede che gli input siano numeri interi con segno a 32 bit. Anche l'output è un numero intero con segno a 32 bit.<br /><br /> Gli eventuali valori non interi vengono arrotondati. I valori positivi verranno arrotondati per difetto, i valori negativi per eccesso.<br /><br /> Qualsiasi valore esterno all'intervallo di interi a 32 bit verrà convertito prendendo gli ultimi 32 bit della relativa notazione di complemento a due.<br /><br /> Se uno degli input è **non definito** o di tipo diverso da un numero, il risultato è **non definito**.<br /><br /> **Nota:** il comportamento descritto sopra è compatibile con il comportamento di un operatore bit per bit di JavaScript.|  
|**logico**|L'operatore prevede che gli input siano valori booleani. Anche l'output è un valore booleano.<br />Se uno degli input è **non definito** o di tipo diverso da un valore booleano, il risultato sarà **non definito**.|  
|**confronto**|L'operatore prevede che gli input abbiano lo stesso tipo e non siano indefiniti. L'output è un valore booleano.<br /><br /> Se uno degli input è **non definito** o gli input hanno tipi diversi, il risultato è **non definito**.<br /><br /> Vedere la tabella dell'**ordinamento dei valori per il confronto** per informazioni dettagliate sull'ordinamento dei valori.|  
|**string**|L'operatore prevede che gli input siano stringhe. Anche l'output è una stringa.<br />Se uno degli input è **non definito** o di tipo diverso da una stringa, il risultato è **non definito**.|  
  
 **Operatori unari:**  
  
|**Nome**|**Operatore**|**Dettagli**|  
|-|-|-|  
|**aritmetico**|+<br /><br /> -|Restituisce il valore numerico.<br /><br /> Negazione bit per bit. Restituisce il valore numerico negato.|  
|**bit per bit**|~|Complemento a uno. Restituisce un complemento di un valore numerico.|  
|**Logico**|**NOT**|Negazione. Restituisce un valore booleano negato.|  
  
 **Operatori binari:**  
  
|**Nome**|**Operatore**|**Dettagli**|  
|-|-|-|  
|**aritmetico**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Addizione.<br /><br /> Sottrazione.<br /><br /> Moltiplicazione.<br /><br /> Divisione.<br /><br /> Modulazione.|  
|**bit per bit**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|OR bit per bit.<br /><br /> AND bit per bit.<br /><br /> XOR bit per bit.<br /><br /> Spostamento a sinistra.<br /><br /> Spostamento a destra.<br /><br /> Spostamento a destra riempimento zero.|  
|**logico**|**AND**<br /><br /> **OR**|Congiunzione logica. Restituisce **true** se entrambi gli argomenti sono **true**, altrimenti restituisce **false**.<br /><br /> Congiunzione logica. Restituisce **true** se entrambi gli argomenti sono **true**, altrimenti restituisce **false**.|  
|**confronto**|**=**<br /><br /> **!=, &lt;&gt;**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Uguale a. Restituisce **true** se gli argomenti sono uguali, altrimenti restituisce **false**.<br /><br /> Diverso da. Restituisce **true** se gli argomenti non sono uguali, altrimenti restituisce **false**.<br /><br /> Maggiore di. Restituisce **true** se il primo argomento è maggiore del secondo, altrimenti restituisce **false**.<br /><br /> Maggiore o uguale a. Restituisce **true** se il primo argomento è maggiore o uguale al secondo, altrimenti restituisce **false**.<br /><br /> Minore di. Restituisce **true** se il primo argomento è minore del secondo, altrimenti restituisce **false**.<br /><br /> Minore o uguale a. Restituisce **true** se il primo argomento è minore o uguale al secondo, altrimenti restituisce **false**.<br /><br /> Unione. Restituisce il secondo argomento se il primo argomento è un valore **non definito**.|  
|**Stringa**|**&amp;#124;&amp;#124;**|Concatenazione. Restituisce una concatenazione di entrambi gli argomenti.|  
  
 **Operatori ternari:**  
  
|Operatore ternario|?|Restituisce il secondo argomento se il primo argomento restituisce **true**, altrimenti restituisce il terzo argomento.|  
|-|-|-|  
  
 **Ordinamento dei valori per il confronto**  
  
|**Tipo**|**Ordine dei valori**|  
|-|-|  
|**Undefined**|Non confrontabile.|  
|**Null**|Singolo valore: **null**|  
|**Number**|Numero reale naturale.<br /><br /> Il valore infinito negativo è minore di qualsiasi altro valore numerico.<br /><br /> Il valore infinito positivo è maggiore di qualsiasi altro valore numerico. il valore **NaN** non è confrontabile. Il confronto con **NaN** restituisce un valore **non definito**.|  
|**String**|Ordine lessicografico.|  
|**Array**|Nessun ordinamento, ma equo.|  
|**Object**|Nessun ordinamento, ma equo.|  
  
 **Osservazioni:**  
  
 In Azure Cosmos DB i tipi di valori sono spesso sconosciuti finché non vengono materialmente recuperati dal database. Per supportare un'esecuzione efficiente delle query, gran parte degli operatori presentano rigorosi requisiti di tipi. Inoltre, gli operatori di per sé non eseguono conversioni implicite.  
  
 Ciò significa che una query come: SELECT * FROM ROOT r WHERE r.Age = 21 restituisce solo documenti con la proprietà Age uguale al numero 21. I documenti con la proprietà Age uguale alla stringa "21" o alla stringa "0021" non corrispondono, poiché l'espressione "21" = 21 restituisce un valore non definito. Questo consente un uso migliore degli indici, poiché la ricerca di un valore specifico, ovvero il numero 21, è più veloce rispetto alla ricerca di un numero indefinito di possibili corrispondenze (il numero 21 o le stringhe "21", "021", "21.0"...). Ciò si differenzia dal modo in cui JavaScript valuta gli operatori per valori di tipi diversi.  
  
 **Confronto e uguaglianza di oggetti e matrici**  
  
 Il confronto dei valori di oggetto o matrice con gli operatori di intervallo (>, >=, <, <=) produce un risultato indefinito poiché non è definito un ordine per i valori di oggetto o matrice. Tuttavia l'uso degli operatori di uguaglianza/disuguaglianza (=,! =, <>) è supportato e i valori vengono confrontati strutturalmente.  
  
 Le matrici sono uguali se entrambe le matrici hanno stesso numero di elementi e sono uguali anche gli elementi nelle posizioni corrispondenti. Se il confronto di qualsiasi coppia di elementi produce un risultato indefinito, il risultato del confronto di matrici è indefinito.  
  
 Gli oggetti sono uguali se entrambi gli oggetti hanno le stesse proprietà definite e se anche i valori delle proprietà corrispondenti sono uguali. Se il confronto di qualsiasi coppia di valori di proprietà produce un risultato indefinito, il risultato del confronto di oggetti è indefinito.  
  
##  <a name="bk_constants"></a> Costanti  
 Una costante, nota anche come valore letterale o scalare, è un simbolo che rappresenta un valore di dati specifico. Il formato di una costante dipende dal tipo di dati del valore che rappresenta.  
  
 **Tipi di dati scalari supportati:**  
  
|**Tipo**|**Ordine dei valori**|  
|-|-|  
|**Undefined**|Singolo valore: **non definito**|  
|**Null**|Singolo valore: **null**|  
|**Boolean**|Valori: **false**, **true**.|  
|**Number**|Un numero a virgola mobile e precisione doppia, standard IEEE 754.|  
|**String**|Una sequenza di zero o più caratteri Unicode. Le stringhe devono essere racchiuse tra virgolette singole o doppie.|  
|**Array**|Una sequenza di zero o più elementi. Ogni elemento può essere un valore di qualsiasi tipo di dati scalare, tranne Undefined.|  
|**Object**|Un set non ordinato di zero o più coppie nome/valore. Il nome è una stringa Unicode, il valore può essere di qualsiasi tipo di dati scalare, tranne **Undefined**.|  
  
 **Sintassi**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argomenti**  
  
1.  `<undefined_constant>; undefined`  
  
     Rappresenta un valore non definito di tipo Undefined.  
  
2.  `<null_constant>; null`  
  
     Rappresenta un valore **null** di tipo **Null**.  
  
3.  `<boolean_constant>`  
  
     Rappresenta una costante di tipo Boolean.  
  
4.  `false`  
  
     Rappresenta un valore **false** di tipo booleano.  
  
5.  `true`  
  
     Rappresenta un valore **true** di tipo booleano.  
  
6.  `<number_constant>`  
  
     Rappresenta una costante.  
  
7.  `decimal_literal`  
  
     I valori letterali decimali sono numeri rappresentati usando la notazione decimale o la notazione scientifica.  
  
8.  `hexadecimal_literal`  
  
     I valori letterali esadecimali sono numeri rappresentati usando il prefisso "0x" seguito da una o più cifre esadecimali.  
  
9. `<string_constant>`  
  
     Rappresenta una costante di tipo String.  
  
10. `string _literal`  
  
     I valori letterali stringa sono stringhe Unicode rappresentate da una sequenza di zero o più caratteri Unicode o sequenze di escape. I valori letterali stringa sono racchiusi tra virgolette singole (apostrofo: ') o virgolette doppie (virgolette inglesi: ").  
  
 Sono consentite le sequenze di escape seguenti:  
  
|**Sequenza di escape**|**Descrizione**|**Carattere Unicode**|  
|-|-|-|  
|\\'|apostrofo (')|U + 0027|  
|\\"|virgolette doppie (")|U + 0022|  
|\\\|barra rovesciata (\\)|U + 005C|  
|\\/|barra (/)|U + 002F|  
|\b|BACKSPACE|U + 0008|  
|\f|avanzamento carta|U + 000C|  
|\n|avanzamento riga|U + 000A|  
|\r|ritorno a capo|U + 000D|  
|\t|TAB|U + 0009|  
|\uXXXX|Carattere Unicode definito da 4 cifre esadecimali.|U + XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Linee guida sulle prestazioni delle query  
 Per poter eseguire una query in modo efficiente per una raccolta di grandi dimensioni, è necessario usare filtri che possano essere gestiti attraverso uno o più indici.  
  
 Per la ricerca negli indici vengono considerati questi filtri:  
  
-   Usare l'operatore di uguaglianza (=) con un'espressione percorso documenti e una costante.  
  
-   Usare gli operatori di intervallo (<, \<=, >, >=) con un'espressione percorso documenti e costanti numeriche.  
  
-   Con "espressione percorso documenti" si indica qualsiasi espressione che identifica un percorso costante nei documenti provenienti dalla raccolta di database a cui si fa riferimento.  
  
 **Espressione percorso documenti**  
  
 Le espressioni percorso documenti sono espressioni usate in un percorso di funzioni di valutazione dell'indicizzatore proprietà o matrice per un documento proveniente da documenti di una raccolta di database. Questo percorso può essere usato per identificare la posizione dei valori a cui si fa riferimento in un filtro direttamente all'interno dei documenti della raccolta di database.  
  
 Per essere considerata un'espressione percorso documenti, è necessario che un'espressione:  
  
1.  Faccia riferimento direttamente alla radice della raccolta.  
  
2.  Faccia riferimento all'indicizzatore di proprietà o matrici costanti di un'espressione percorso documenti  
  
3.  Faccia riferimento a un alias che rappresenta un'espressione percorso documenti.  
  
     **Convenzioni della sintassi**  
  
     Nella tabella seguente vengono descritte le convenzioni utilizzate per descrivere la sintassi nel riferimento SQL seguente.  
  
    |**Convenzione**|**Usata per**|  
    |-|-|    
    |LETTERE MAIUSCOLE|Parole chiave che non fanno distinzione tra maiuscole e minuscole.|  
    |lettere minuscole|Parole chiave che fanno distinzione tra maiuscole e minuscole.|  
    |\<non terminale>|Non terminale, definito separatamente.|  
    |\<non terminale> ::=|Definizione di sintassi dell'elemento non terminale.|  
    |altro_terminale|Terminale (token), descritto dettagliatamente con parole.|  
    |identificatore|Identificatore. Consente solo i seguenti caratteri: a-z A-Z 0-9 _Il primo carattere non può essere una cifra.|  
    |"stringa"|Stringa tra virgolette. Consente qualsiasi stringa valida. Vedere la descrizione di string_literal.|  
    |'simbolo'|Simbolo letterale che fa parte della sintassi.|  
    |&#124; (barra verticale)|Alternative per gli elementi di sintassi. È possibile usare solo uno degli elementi specificati.|  
    |[ ] /(parentesi quadre)|Le parentesi quadre racchiudono uno o più elementi facoltativi.|  
    |[ ,...n ]|Indica che l'elemento precedente può essere ripetuto n volte. Le occorrenze sono separate da virgole.|  
    |[ ...n ]|Indica che l'elemento precedente può essere ripetuto n volte. Le occorrenze sono separate da spazi.|  
  
##  <a name="bk_built_in_functions"></a> Funzioni predefinite  
 In Azure Cosmos DB sono disponibili molte funzioni SQL predefinite. Le categorie di funzioni predefinite sono elencate di seguito.  
  
|Funzione|DESCRIZIONE|  
|--------------|-----------------|  
|[Funzioni matematiche](#bk_mathematical_functions)|Le funzioni matematiche eseguono un calcolo basato in genere su valori di input passati come argomenti e restituiscono un valore numerico.|  
|[Funzioni di controllo del tipo](#bk_type_checking_functions)|Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione nell'ambito delle query SQL.|  
|[Funzioni stringa](#bk_string_functions)|Le funzioni stringa eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano.|  
|[Funzioni di matrice](#bk_array_functions)|Le funzioni di matrice eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice.|  
|[Funzioni spaziali](#bk_spatial_functions)|Le funzioni spaziali eseguono un'operazione su un valore di input di oggetto spaziale e restituiscono un valore numerico o booleano.|  
  
###  <a name="bk_mathematical_functions"></a> Funzioni matematiche  
 Le seguenti funzioni eseguono un calcolo basato in genere su valori di input passati come argomenti e restituiscono un valore numerico.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Restituisce il valore assoluto (positivo) dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 Nell'esempio seguente vengono illustrati i risultati dell'uso della funzione ABS su tre numeri diversi.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Restituisce l'angolo, espresso in radianti, il cui coseno corrisponde all'espressione numerica specificata. Denominato anche arcocoseno.  
  
 **Sintassi**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce l'arcocoseno di -1.  
  
```  
SELECT ACOS(-1)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Restituisce l'angolo, espresso in radianti, il cui seno è l'espressione numerica specificata. Detta anche arcoseno.  
  
 **Sintassi**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce l'arcoseno di -1.  
  
```  
SELECT ASIN(-1)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Restituisce l'angolo, espresso in radianti, la cui tangente è l'espressione numerica specificata. Detta anche arcotangente.  
  
 **Sintassi**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce l'arcotangente del valore specificato.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Restituisce il valore principale dell'arcotangente di y/x, espresso in radianti.  
  
 **Sintassi**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente calcola l'arcotangente per i componenti x e y specificati.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Restituisce il più piccolo valore integer maggiore di o uguale all'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente usa valori numerici positivi, negativi e zero con la funzione CEILING.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
 **Sintassi**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente calcola il coseno dell'angolo specificato.  
  
```  
SELECT COS(14.78)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Restituisce la cotangente trigonometrica dell'angolo specificato, espresso in radianti, nell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente calcola la cotangente dell'angolo specificato.  
  
```  
SELECT COT(124.1332)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Restituisce l'angolo corrispondente in gradi di un angolo specificato in radianti.  
  
 **Sintassi**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce il numero di gradi di un angolo di PI/2 radianti.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Restituisce il valore integer più alto, minore di o uguale all'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente usa valori numerici positivi, negativi e zero con la funzione FLOOR.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Restituisce il valore esponenziale dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **Osservazioni:**  
  
 La costante **e** (2,718281 …) è la base dei logaritmi naturali.  
  
 L'esponente di un numero è la costante **e** elevata alla potenza del numero. Ad esempio, EXP(1.0) = e^1.0 = 2,71828182845905 e EXP(10) = e^10 = 22026,4657948067.  
  
 Il valore esponenziale del logaritmo naturale di un numero è il numero stesso: EXP (LOG (n)) = n. E il logaritmo naturale dell'esponente di un numero è il numero stesso: LOG (EXP (n)) = n.  
  
 **esempi**  
  
 Nell'esempio seguente viene dichiarata una variabile e restituito il valore esponenziale della variabile specificata (10).  
  
```  
SELECT EXP(10)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 L'esempio seguente restituisce il valore esponenziale del logaritmo naturale di 20 e il logaritmo naturale dell'esponente di 20. Poiché queste funzioni sono inverse l'una dell'altra, il valore restituito con arrotondamento per il calcolo a virgola mobile in entrambi i casi è 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Restituisce il logaritmo naturale dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
-   `base`  
  
     Argomento numerico facoltativo che imposta la base per il logaritmo.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **Osservazioni**  
  
 Per impostazione predefinita, LOG() restituisce il logaritmo naturale. È possibile modificare la base del logaritmo e impostare un altro valore usando il parametro di base facoltativo.  
  
 Il logaritmo naturale è il logaritmo in base **e**, dove **e** è una costante irrazionale approssimativamente uguale a 2,718281828.  
  
 Il logaritmo naturale dell'esponente di un numero è il numero stesso: LOG ( EXP ( n ) ) = n. E il valore esponenziale del logaritmo naturale di un numero è il numero stesso: EXP( LOG( n ) ) = n.  
  
 **esempi**  
  
 Nell'esempio seguente viene dichiarata una variabile e restituito il logaritmo della variabile specificata (10).  
  
```  
SELECT LOG(10)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 L'esempio seguente calcola il logaritmo per l'esponente di un numero.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Restituisce il logaritmo in base 10 dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **Osservazioni:**  
  
 Le funzioni LOG10 e POWER sono inversamente correlate tra loro. Ad esempio, 10 ^ LOG10(n) = n.  
  
 **esempi**  
  
 Nell'esempio seguente viene dichiarata una variabile e restituito il valore LOG10 della variabile specificata (100).  
  
```  
SELECT LOG10(100)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Restituisce il valore costante di pi greco.  
  
 **Sintassi**  
  
```  
PI ()  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce il valore di pi greco.  
  
```  
SELECT PI()  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Restituisce il valore dell'espressione specificata alla potenza specificata.  
  
 **Sintassi**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
-   `y`  
  
     È la potenza a cui elevare `numeric_expression`.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 Nell'esempio seguente viene elevato un numero alla potenza di 3 (cubo del numero).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Restituisce radianti quando viene immessa un'espressione numerica, espresso in gradi.  
  
 **Sintassi**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente accetta alcuni angoli come input e restituisce i valori in radianti corrispondenti.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Questo è il set di risultati.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Restituisce un valore numerico, arrotondato al valore integer più vicino.  
  
 **Sintassi**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente arrotonda numeri positivi e negativi al numero intero più prossimo.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 Restituisce il segno positivo (+1), zero (0) o negativo (-1) dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce i valori SIGN dei numeri da -2 a 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Restituisce il seno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
 **Sintassi**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente calcola il seno dell'angolo specificato.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Restituisce la radice quadrata del valore numerico specificato.  
  
 **Sintassi**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce le radici quadrate dei numeri da 1 a 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Restituisce il quadrato del valore numerico specificato.  
  
 **Sintassi**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce i quadrati dei numeri da 1 a 3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Restituisce la tangente dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
 **Sintassi**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente calcola la tangente di PI()/2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Restituisce un valore numerico, troncato al valore integer più vicino.  
  
 **Sintassi**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argomenti**  
  
-   `numeric_expression`  
  
     È un'espressione numerica.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente tronca numeri positivi e negativi al numero intero più prossimo.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Funzioni di controllo del tipo  
 Le funzioni seguenti supportano il controllo del tipo per i valori di input e ogni funzione restituisce un valore booleano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è una matrice.  
  
 **Sintassi**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un valore booleano.  
  
 **Sintassi**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore.  
  
 **Sintassi**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente verifica la presenza di una proprietà all'interno del documento JSON specificato. La prima parte restituisce true perché "a" è presente, ma la seconda restituisce false perché "b" è assente.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Questo è il set di risultati.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è nulla.  
  
 **Sintassi**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un numero.  
  
 **Sintassi**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un oggetto JSON.  
  
 **Sintassi**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un primitivo (stringa, valore booleano, numerico o null).  
  
 **Sintassi**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è una stringa.  
  
 **Sintassi**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argomenti**  
  
-   `expression`  
  
     È qualsiasi espressione valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Funzioni stringa  
 Le funzioni scalari seguenti eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[UPPER](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa.  
  
 **Sintassi**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente restituisce la stringa concatenata dei valori specificati.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo.  
  
 **Sintassi**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 Nell'esempio seguente viene verificato se "abc" contiene "ab" e contiene "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo.  
  
 **Sintassi**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente restituisce "abc" che termina con "b" e "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Restituisce la posizione iniziale della prima occorrenza della seconda stringa di espressione all'interno della prima espressione stringa specificata oppure -1 se la stringa non viene trovata.  
  
 **Sintassi**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente restituisce l'indice di diverse sottostringhe all'interno di "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Restituisce la parte sinistra di una stringa con il numero specificato di caratteri.  
  
 **Sintassi**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
-   `num_expr`  
  
     È qualsiasi espressione numerica valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente restituisce la parte sinistra di "abc" per diversi valori di lunghezza.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 Restituisce il numero di caratteri dell'espressione stringa specificata.  
  
 **Sintassi**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente restituisce la lunghezza di una stringa.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli.  
  
 **Sintassi**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente illustra come usare LOWER in una query.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Restituisce un'espressione stringa dopo aver rimosso gli spazi vuoti iniziali.  
  
 **Sintassi**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente illustra come usare LTRIM all'interno di una query.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 Sostituisce tutte le occorrenze di un valore stringa specificato con un altro valore stringa.  
  
 **Sintassi**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente illustra come usare REPLACE in una query.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Ripete un valore stringa in un numero di volte specificato.  
  
 **Sintassi**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
-   `num_expr`  
  
     È qualsiasi espressione numerica valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente illustra come usare REPLICATE in una query.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Restituisce l'inverso di un valore stringa.  
  
 **Sintassi**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente illustra come usare REVERSE in una query.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Restituisce la parte destra di una stringa con il numero specificato di caratteri.  
  
 **Sintassi**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
-   `num_expr`  
  
     È qualsiasi espressione numerica valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente restituisce la parte destra di "abc" per diversi valori di lunghezza.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Restituisce un'espressione di stringa dopo aver rimosso gli spazi vuoti finali.  
  
 **Sintassi**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente illustra come usare RTRIM all'interno di una query.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo.  
  
 **Sintassi**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 L'esempio seguente verifica se la stringa "abc" inizia con "b" e "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> SUBSTRING  
 Restituisce parte di un'espressione stringa a partire dalla posizione in base al carattere zero specificata e continua fino alla lunghezza specificata o alla fine della stringa.  
  
 **Sintassi**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
-   `num_expr`  
  
     È qualsiasi espressione numerica valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente restituisce la sottostringa di "abc" a partire da 1 e per una lunghezza di 1 carattere.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> UPPER  
 Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli.  
  
 **Sintassi**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argomenti**  
  
-   `str_expr`  
  
     È qualsiasi espressione di stringa valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di stringa.  
  
 **esempi**  
  
 L'esempio seguente illustra come usare UPPER in una query  
  
```  
SELECT UPPER("Abc")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funzioni di matrice  
 Le funzioni scalari seguenti eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Restituisce una matrice che rappresenta il risultato della concatenazione di due o più valori della matrice.  
  
 **Sintassi**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argomenti**  
  
-   `arr_expr`  
  
     È qualsiasi espressione di matrice valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione di matrice.  
  
 **esempi**  
  
 L'esempio seguente illustra come concatenare due matrici.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Restituisce un valore booleano che indica se la matrice contiene il valore specificato. Può specificare se la corrispondenza è completa o parziale. 

 **Sintassi**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argomenti**  
  
-   `arr_expr`  
  
     È qualsiasi espressione di matrice valida.  
  
-   `expr`  
  
     È qualsiasi espressione valida.  

-   `bool_expr`  
  
     È un'espressione booleana.       
  
 **Tipi restituiti**  
  
 Restituisce un valore booleano.  
  
 **esempi**  
  
 L'esempio seguente illustra come verificare l'appartenenza a una matrice usando ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": true, "$2": false}]  
```  

 L'esempio seguente illustra come verificare la corrispondenza di un JSON in una matrice usando ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Questo è il set di risultati.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Restituisce il numero di elementi dell'espressione di matrice specificato.  
  
 **Sintassi**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argomenti**  
  
-   `arr_expr`  
  
     È qualsiasi espressione di matrice valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica.  
  
 **esempi**  
  
 L'esempio seguente illustra come ottenere la lunghezza di una matrice usando ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Questo è il set di risultati.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Restituisce parte di un'espressione di matrice.
  
 **Sintassi**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argomenti**  
  
-   `arr_expr`  
  
     È qualsiasi espressione di matrice valida.  
  
-   `num_expr`  
  
     È qualsiasi espressione numerica valida.  
  
 **Tipi restituiti**  
  
 Restituisce un valore booleano.  
  
 **esempi**  
  
 L'esempio seguente illustra come ottenere una parte di una matrice usando ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 Questo è il set di risultati.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> Funzioni spaziali  
 Le seguenti funzioni scalari eseguono un'operazione su un valore di input di oggetto spaziale e restituiscono un valore numerico o booleano.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Restituisce la distanza tra le due espressioni GeoJSON punto, poligono o LineString.  
  
 **Sintassi**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argomenti**  
  
-   `spatial_expr`  
  
     È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione numerica che contiene la distanza. È espressa in metri per il sistema di riferimento predefinito.  
  
 **esempi**  
  
 L'esempio seguente indica come restituire tutti i documenti della famiglia che si trovano entro 30 km dalla posizione specificata usando la funzione predefinita ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Questo è il set di risultati.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Restituisce un'espressione booleana che indica se l'oggetto GeoJSON (punto, poligono o LineString) specificato nel primo argomento è all'interno dell'oggetto GeoJSON (punto, poligono o LineString) nel secondo argomento.  
  
 **Sintassi**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argomenti**  
  
-   `spatial_expr`  
  
     È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
 
-   `spatial_expr`  
  
     È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
  
 **Tipi restituiti**  
  
 Restituisce un valore booleano.  
  
 **esempi**  
  
 L'esempio seguente indica come trovare tutti i documenti della famiglia all'interno di un poligono usando ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Questo è il set di risultati.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Restituisce un'espressione booleana che indica se l'oggetto GeoJSON (punto, poligono o LineString) specificato nel primo argomento interseca l'oggetto GeoJSON (punto, poligono o LineString) nel secondo argomento.  
  
 **Sintassi**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argomenti**  
  
-   `spatial_expr`  
  
     È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
 
-   `spatial_expr`  
  
     È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
  
 **Tipi restituiti**  
  
 Restituisce un valore booleano.  
  
 **esempi**  
  
 L'esempio seguente indica come individuare tutte le aree che intersecano il poligono dato.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Questo è il set di risultati.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Restituisce un valore booleano che indica se l'espressione GeoJSON punto, poligono o LineString specificata è valida.  
  
 **Sintassi**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argomenti**  
  
-   `spatial_expr`  
  
     È qualsiasi espressione di punto GeoJSON, poligono o LineString valida.  
  
 **Tipi restituiti**  
  
 Restituisce un'espressione booleana.  
  
 **esempi**  
  
 Nell'esempio seguente viene illustrato come controllare se un punto è valido usando ST_VALID.  
  
 Ad esempio, questo punto ha un valore di latitudine che non rientra nell'intervallo valido di valori [-90, 90], quindi la query restituisce false.  
  
 Per i poligoni, la specifica GeoJSON richiede che l'ultima coppia di coordinate indicata corrisponda alla prima, in modo da creare una forma chiusa. I punti all'interno di un poligono devono essere specificati in senso antiorario. Un poligono specificato in senso orario rappresenta l'inverso dell'area al suo interno.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Questo è il set di risultati.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Restituisce un valore JSON che contiene un valore booleano valore se l'espressione GeoJSON punto, poligono o LineString specificata è valida e, se non valida, anche il motivo come valore stringa.  
  
 **Sintassi**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argomenti**  
  
-   `spatial_expr`  
  
     È qualsiasi espressione di punto GeoJSON o poligono valida.  
  
 **Tipi restituiti**  
  
 Restituisce un valore JSON che contiene un valore booleano valore se l'espressione punto o poligono GeoJSON specificata è valida e, se non valida, anche il motivo come valore stringa.  
  
 **esempi**  
  
 Nell'esempio seguente viene illustrato come controllare la validità (con i dettagli) usando ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Questo è il set di risultati.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Passaggi successivi  
 [Query e sintassi SQL per Azure Cosmos DB](sql-api-sql-query.md)   
 [Documentazione di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
