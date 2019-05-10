---
title: Sintassi del linguaggio SQL in Azure Cosmos DB
description: Questo articolo illustra la sintassi del linguaggio di query SQL utilizzata in Azure Cosmos DB, vari operatori e parole chiave disponibili in questo linguaggio.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 186e0365ae8aee3b7f92fcc06142e4d0496ffd08
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415448"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Informazioni di riferimento sul linguaggio SQL per Azure Cosmos DB 

Azure Cosmos DB supporta l'esecuzione di query sui documenti usando un linguaggio SQL (Structured Query Language) familiare come una grammatica per i documenti JSON gerarchici senza richiedere uno schema esplicito o la creazione di indici secondari. Questo articolo viene fornita la documentazione per la sintassi di linguaggio di query SQL usata negli account di API SQL. Per informazioni dettagliate sulla query di esempio SQL, vedere [esempi di query SQL in Cosmos DB](how-to-sql-query.md).  
  
Visitare il [Query Playground](https://www.documentdb.com/sql/demo), in cui è possibile provare Cosmos DB ed eseguire query SQL su un set di dati di esempio.  
  
## <a name="select-query"></a>Query SELECT  
Ogni query consiste in una clausola SELECT e clausole FROM e WHERE facoltative in base agli standard ANSI-SQL. In genere, per ogni query, l'origine nella clausola FROM viene enumerata, quindi viene applicato il filtro nella clausola WHERE sull'origine per recuperare un subset di documenti JSON. Infine, viene usata la clausola SELECT per proiettare i valori JSON richiesti nell'elenco selezionato. Per esempi, vedere [esempi di query SELECT](how-to-sql-query.md#SelectClause)
  
**Sintassi**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ] 
    [ OFFSET <offset_amount> LIMIT <limit_amount>]
```  
  
 **Osservazioni:**  
  
 Per i dettagli su ogni clausola, vedere le sezioni seguenti:  
  
-   [Clausola SELECT](#bk_select_query)    
-   [Clausola FROM](#bk_from_clause)    
-   [Clausola WHERE](#bk_where_clause)    
-   [Clausola ORDER BY](#bk_orderby_clause)  
-   [Clausola OFFSET limite](#bk_offsetlimit_clause)

  
Le clausole nell'istruzione SELECT devono essere ordinate come indicato sopra. Una clausola facoltativa può essere omessa. Ma se si usano clausole facoltative, è necessario specificarle nell'ordine corretto.  
  
### <a name="logical-processing-order-of-the-select-statement"></a>Ordine di elaborazione logico dell'istruzione SELECT  
  
L'ordine in cui vengono elaborate clausole è:  

1.  [Clausola FROM](#bk_from_clause)  
2.  [Clausola WHERE](#bk_where_clause)  
3.  [Clausola ORDER BY](#bk_orderby_clause)  
4.  [Clausola SELECT](#bk_select_query)
5.  [Clausola OFFSET limite](#bk_offsetlimit_clause)

Si noti che l'ordine è diverso da quello in cui appaiono nella sintassi. L'ordinamento è tale che tutti i nuovi simboli introdotti da una clausola elaborata sono visibili e possono essere usati nelle clausole elaborate successivamente. Ad esempio, gli alias dichiarati in una clausola FROM sono accessibili nelle clausole WHERE e SELECT.  

### <a name="whitespace-characters-and-comments"></a>Spazi vuoti e commenti  

Tutti i caratteri di spazio vuoto che non fanno parte di una stringa o un identificatore delimitato non fanno parte della grammatica del linguaggio e vengono ignorati durante l'analisi.  

Il linguaggio di query supporta i commenti in stile T-SQL come  

-   istruzione SQL `-- comment text [newline]`  

Anche se i commenti e gli spazi vuoti non hanno alcun significato nella grammatica, devono essere usati per separare i token. Ad esempio: `-1e5` è un token numero singolo, mentre `: – 1 e5` è un token meno seguito dal numero 1 e dall'identificatore e5.  

##  <a name="bk_select_query"></a> Clausola SELECT  
Le clausole nell'istruzione SELECT devono essere ordinate come indicato sopra. Una clausola facoltativa può essere omessa. Ma se si usano clausole facoltative, è necessario specificarle nell'ordine corretto. Per esempi, vedere [esempi di query SELECT](how-to-sql-query.md#SelectClause).

**Sintassi**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argomenti**  
  
- `<select_specification>`  

  Proprietà o valore da selezionare per il set di risultati.  
  
- `'*'`  

  Specifica che il valore deve essere recuperato senza apportare alcuna modifica. In particolare, se il valore elaborato è un oggetto, tutte le proprietà verranno recuperate.  
  
- `<object_property_list>`  
  
  Specifica l'elenco di proprietà da recuperare. Ogni valore restituito sarà un oggetto con le proprietà specificate.  
  
- `VALUE`  

  Specifica che deve essere recuperato il valore JSON anziché l'oggetto JSON completo. A differenza di `<property_list>` non esegue il wrapping del valore previsto in un oggetto.  
 
- `DISTINCT`
  
  Specifica che i duplicati di proprietà previste devono essere rimosso.  

- `<scalar_expression>`  

  Espressione che rappresenta il valore da calcolare. Vedere la sezione [Espressioni scalari](#bk_scalar_expressions) per informazioni dettagliate.  
  
**Osservazioni:**  
  
La sintassi di `SELECT *` è valida solo se la clausola FROM ha dichiarato esattamente un alias. `SELECT *` offre una proiezione dell'identità, che può essere utile se non è necessaria alcuna proiezione. SELECT * è valida solo se viene specificata la clausola FROM e se viene introdotta solo una singola origine di input.  
  
`SELECT <select_list>` e `SELECT *` sono "zucchero sintattico" e possono essere espressi in alternativa usando semplici istruzioni SELECT, come illustrato di seguito.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   Equivale a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   Equivale a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Vedere anche**  
  
[Espressioni scalari](#bk_scalar_expressions)  
[Clausola SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Clausola FROM  
Specifica l'origine o le origini unite in join. La clausola FROM è facoltativa, a meno che l'origine non sia filtrata o proiettata più avanti nella query. Lo scopo di questa query è specificare l'origine dati in base alla quale deve operare la query. Comunemente, l'origine è rappresentata dall'intero contenitore, ma è possibile specificare piuttosto un sottoinsieme del contenitore. Se questa clausola non viene specificata, le altre clausole verranno comunque eseguite come se la clausola FROM specificasse un singolo documento. Per esempi, vedere [esempi di clausola FROM](how-to-sql-query.md#FromClause)
  
**Sintassi**  
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
**Argomenti**  
  
- `<from_source>`  
  
  Specifica un'origine dati, con o senza un alias. Se non viene specificato, l'alias verrà dedotto da `<container_expression>` usando le seguenti regole:  
  
  -  Se l'espressione è un nome di contenitore, come alias verrà usato il nome del contenitore.  
  
  -  Se l'espressione è `<container_expression>` seguito da un nome di proprietà, come alias verrà usato il nome della proprietà. Se l'espressione è un nome di contenitore, come alias verrà usato il nome del contenitore.  
  
- AS `input_alias`  
  
  Specifica che l'oggetto `input_alias` è un set di valori restituiti dall'espressione di contenitore sottostante.  
 
- `input_alias` IN  
  
  Specifica che `input_alias` deve rappresentare il set di valori ottenuto eseguendo l'iterazione su tutti gli elementi di ogni matrice restituita dall'espressione di contenitore sottostante. Tutti i valori restituiti dall'espressione di contenitore sottostante che non sono matrici vengono ignorati.  
  
- `<container_expression>`  
  
  Specifica l'espressione di contenitore da usare per recuperare i documenti.  
  
- `ROOT`  
  
  Specifica che il documento deve essere recuperato dal contenitore predefinito attualmente connesso.  
  
- `container_name`  
  
  Specifica che il documento deve essere recuperato dal contenitore specificato. Il nome del contenitore deve corrispondere al nome del contenitore a cui si è attualmente connessi.  
  
- `input_alias`  
  
  Specifica che il documento deve essere recuperato dall'altra origine definita dall'alias indicato.  
  
- `<container_expression> '.' property_`  
  
  Specifica che il documento deve essere recuperato eseguendo l'accesso alla proprietà `property_name` o all'elemento di matrice array_index per tutti i documenti recuperati dall'espressione di contenitore specificata.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Specifica che il documento deve essere recuperato eseguendo l'accesso alla proprietà `property_name` o all'elemento di matrice array_index per tutti i documenti recuperati dall'espressione di contenitore specificata.  
  
**Osservazioni:**  
  
Tutti gli alias specificati o dedotti in `<from_source>(` devono essere univoci. La sintassi `<container_expression>.`nome proprietà equivale a `<container_expression>' ['"property_name"']'`. Tuttavia, la seconda sintassi può essere usata se un nome di proprietà contiene un carattere non identificatore.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Gestione proprietà mancanti, elementi di matrice mancanti e valori non definiti
  
Se un'espressione di contenitore accede alle proprietà o agli elementi di matrice e il valore non esiste, tale valore verrà ignorato e non elaborato ulteriormente.  
  
### <a name="container-expression-context-scoping"></a>Definizione dell'ambito per il contesto dell'espressione di contenitore  
  
Un'espressione di contenitore può avere come ambito un contenitore o un documento:  
  
-   Un'espressione ha un ambito contenitore se l'origine dell'espressione di contenitore sottostante è ROOT o `container_name`. Un'espressione di questo tipo rappresenta un set di documenti recuperati direttamente dal contenitore e non dipende dall'elaborazione di altre espressioni di contenitore.  
  
-   Un'espressione ha un ambito documento se l'origine dell'espressione di contenitore sottostante è `input_alias` introdotta in precedenza nella query. Tale espressione rappresenta un set di documenti ottenuti dalla valutazione dell'espressione di contenitore nell'ambito di ogni documento appartenente al set associato al contenitore con alias.  Il set risultante sarà un'unione di set ottenuti dalla valutazione dell'espressione di contenitore per ogni documento del set sottostante.  
  
### <a name="joins"></a>Join 
  
Nella versione corrente, Cosmos DB supporta gli inner join. Altre funzionalità di join saranno presto disponibili. 

Gli inner join generano un prodotto incrociato completo dei set che partecipano al join. Il risultato di un join a N vie è un set di tuple a N elementi, in cui ogni valore presente nella tupla è associato al set con alias che partecipa al join e l'accesso al set è possibile facendo riferimento a tale alias in altre clausole. Per esempi, vedere [esempi di parola chiave JOIN](how-to-sql-query.md#Joins)
  
La valutazione del join dipende dall'ambito del contesto dei set partecipanti:  
  
- Un join tra il set di contenitore A e il set di contenitore con ambito B genera un prodotto incrociato di tutti gli elementi presenti nei set A e B.
  
- Un join tra il set A e il set con ambito documento B genera un'unione di tutti i set ottenuti dalla valutazione di un set con ambito documento B per ogni documento del set A.  
  
  Nella versione corrente l'elaborazione delle query supporta al massimo un'espressione con ambito contenitore.  
  
### <a name="examples-of-joins"></a>Esempi di join  
  
Verrà ora esaminata la seguente clausola FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Consentire a ogni origine di definire `input_alias1, input_alias2, …, input_aliasN`. Questa clausola FROM restituisce un set di N tuple (tuple con N valori). Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias del contenitore sui rispettivi set.  
  
**Esempio 1** -2 origini  
  
- Consentire a `<from_source1>` di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source2>` di avere un ambito documento che fa riferimento a input_alias1 e di rappresentare i set:  
  
    {1, 2} per `input_alias1 = A,`  
  
    {3} per `input_alias1 = B,`  
  
    {4, 5} per `input_alias1 = C,`  
  
- La clausola FROM `<from_source1> JOIN <from_source2>` produrrà le tuple seguenti:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Esempio 2** -3 origini  
  
- Consentire a `<from_source1>` di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
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
  
**Esempio 3** -3 origini  
  
- Consentire a <from_source1> di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source1>` di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
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
 Specifica la condizione di ricerca per i documenti restituiti dalla query. Per esempi, vedere [esempi di clausola WHERE](how-to-sql-query.md#WhereClause)
  
 **Sintassi**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argomenti**  
  
- `<filter_condition>`  
  
   Specifica la condizione che deve essere soddisfatta per i documenti da restituire.  
  
- `<scalar_expression>`  
  
   Espressione che rappresenta il valore da calcolare. Vedere la sezione [Espressioni scalari](#bk_scalar_expressions) per informazioni dettagliate.  
  
  **Osservazioni:**  
  
  Affinché il documento venga restituito, un'espressione specificata come condizione di filtro deve restituire true. Solo un valore booleano true soddisferà la condizione, i valori non definiti, null, false, numero, matrice o oggetto non soddisfano la condizione.  
  
##  <a name="bk_orderby_clause"></a> Clausola ORDER BY  
 Specifica l'ordinamento dei risultati restituiti dalla query. Per esempi, vedere [esempi di clausola ORDER BY](how-to-sql-query.md#OrderByClause)
  
 **Sintassi**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
  
```  

 **Argomenti**  
  
- `<sort_specification>`  
  
   Specifica una proprietà o espressione in cui ordinare il set di risultati della query. Una colonna di ordinamento può essere specificata come un alias del nome o una proprietà.  
  
   È possibile specificare più proprietà. I nomi delle proprietà devono essere univoci. La sequenza delle proprietà di ordinamento nella clausola ORDER BY definisce l'organizzazione del set di risultati ordinato. Ovvero, il set di risultati viene ordinato in base alla prima proprietà e quindi l'elenco così ordinato viene ordinato in base alla seconda proprietà e così via.  
  
   I nomi delle proprietà a cui fa riferimento la clausola ORDER BY devono corrispondere a una proprietà nell'elenco di selezione o a una proprietà definita nella raccolta specificata nella clausola FROM senza ambiguità.  
  
- `<sort_expression>`  
  
   Specifica uno o più proprietà o le espressioni in cui ordinare il set di risultati di query.  
  
- `<scalar_expression>`  
  
   Vedere la sezione [Espressioni scalari](#bk_scalar_expressions) per informazioni dettagliate.  
  
- `ASC | DESC`  
  
   Specifica che i valori presenti nella colonna specificata devono essere ordinati in ordine crescente o decrescente. ASC (crescente) esegue l'ordinamento dal valore più basso a quello più alto. DESC (decrescente) esegue l'ordinamento dal valore più alto a quello più basso. L'ordinamento predefinito è ASC (crescente). I valori Null vengono considerati come i valori più bassi possibile.  
  
  **Osservazioni:**  
  
   La clausola ORDER BY richiede che i criteri di indicizzazione includano un indice per i campi da ordinare. Il runtime di query di Azure Cosmos DB supporta l'ordinamento con un nome di proprietà e non in base a proprietà calcolate. Azure Cosmos DB supporta più proprietà ORDER BY. Per eseguire una query con più proprietà ORDER BY, è necessario definire un [indice composto](index-policy.md#composite-indexes) nei campi da ordinare.


##  <a name=bk_offsetlimit_clause></a> Clausola OFFSET limite

Specifica il numero di elementi ignorati e il numero di elementi restituiti. Per esempi, vedere [esempi di clausola OFFSET limite](how-to-sql-query.md#OffsetLimitClause)
  
 **Sintassi**  
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
 **Argomenti**  
 
- `<offset_amount>`

   Specifica il numero intero di elementi che devono ignorare i risultati della query.


- `<limit_amount>`
  
   Specifica il numero intero di elementi che deve includere i risultati della query

  **Osservazioni:**  
  
  Nella clausola OFFSET limite sono necessari sia il numero OFFSET e il conteggio di limite. Se facoltativo `ORDER BY` viene utilizzata la clausola, il set di risultati viene prodotta eseguendo l'operazione FETCH sui valori ordinati. In caso contrario, la query restituirà un ordine fisso di valori.

##  <a name="bk_scalar_expressions"></a> Espressioni scalari  
 Un'espressione scalare è una combinazione di simboli e operatori che si può valutare per ottenere un singolo valore. Le espressioni semplici possono essere costanti, riferimenti a proprietà, riferimenti a elementi di matrice, riferimenti ad alias o chiamate di funzioni. Le espressioni semplici possono essere combinate in espressioni complesse usando gli operatori. Per esempi, vedere [esempi di espressioni scalari](how-to-sql-query.md#scalar-expressions)
  
 Per informazioni dettagliate sui valori che possono essere contenuti nelle espressioni scalari, vedere la sezione [Costanti](#bk_constants).  
  
 **Sintassi**  
  
```sql  
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
  
- `<constant>`  
  
   Rappresenta un valore costante. Per informazioni dettagliate, vedere la sezione [Costanti](#bk_constants).  
  
- `input_alias`  
  
   Rappresenta un valore definito dall'elemento `input_alias` introdotto nella clausola `FROM`.  
  Questo valore è sicuramente diverso da un valore **non definito**: i valori **non definiti** presenti nell'input verranno ignorati.  
  
- `<scalar_expression>.property_name`  
  
   Rappresenta un valore della proprietà di un oggetto. Se la proprietà non esiste o si fa riferimento alla proprietà per un valore che non è un oggetto, l'espressione restituisce un valore **non definito**.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Rappresenta un valore della proprietà con nome `property_name` o un elemento di matrice con indice `array_index` di un oggetto o una matrice. Se l'indice della proprietà o matrice non esiste o si fa riferimento all'indice della proprietà o matrice per un valore che non è un oggetto o una matrice, l'espressione restituisce un valore non definito.  
  
- `unary_operator <scalar_expression>`  
  
   Rappresenta un operatore applicato a un singolo valore. Per informazioni dettagliate, vedere la sezione [Operatori](#bk_operators).  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Rappresenta un operatore applicato a due valori. Per informazioni dettagliate, vedere la sezione [Operatori](#bk_operators).  
  
- `<scalar_function_expression>`  
  
   Rappresenta un valore definito da un risultato di una chiamata di funzione.  
  
- `udf_scalar_function`  
  
   Nome della funzione scalare definita dall'utente.  
  
- `builtin_scalar_function`  
  
   Nome della funzione scalare predefinita.  
  
- `<create_object_expression>`  
  
   Rappresenta un valore ottenuto creando un nuovo oggetto con proprietà specificate e i relativi valori.  
  
- `<create_array_expression>`  
  
   Rappresenta un valore ottenuto creando una nuova matrice con valori specificati come elementi  
  
- `parameter_name`  
  
   Rappresenta un valore del nome di parametro specificato. I nomi di parametro devono avere un singolo carattere \@ come primo carattere.  
  
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
|**arithmetic**|L'operatore prevede che gli input siano numerici. Anche l'output è un numero. Se uno degli input è **non definito** o di tipo diverso da un numero, il risultato è **non definito**.|  
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
|**arithmetic**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Addizione.<br /><br /> Sottrazione.<br /><br /> Moltiplicazione.<br /><br /> Divisione.<br /><br /> Modulazione.|  
|**bit per bit**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|OR bit per bit.<br /><br /> AND bit per bit.<br /><br /> XOR bit per bit.<br /><br /> Spostamento a sinistra.<br /><br /> Spostamento a destra.<br /><br /> Spostamento a destra riempimento zero.|  
|**logico**|**AND**<br /><br /> **OR**|Congiunzione logica. Restituisce **true** se entrambi gli argomenti sono **true**, altrimenti restituisce **false**.<br /><br /> Disgiunzione logica. Restituisce **true** se almeno un argomento è **true**, altrimenti restituisce **false**.|  
|**confronto**|**=**<br /><br /> **!=, &lt;&gt;**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Uguale a. Restituisce **true** se gli argomenti sono uguali, altrimenti restituisce **false**.<br /><br /> Diverso da. Restituisce **true** se gli argomenti non sono uguali, altrimenti restituisce **false**.<br /><br /> Maggiore di. Restituisce **true** se il primo argomento è maggiore del secondo, altrimenti restituisce **false**.<br /><br /> Maggiore o uguale a. Restituisce **true** se il primo argomento è maggiore o uguale al secondo, altrimenti restituisce **false**.<br /><br /> Minore di. Restituisce **true** se il primo argomento è minore del secondo, altrimenti restituisce **false**.<br /><br /> Minore o uguale a. Restituisce **true** se il primo argomento è minore o uguale al secondo, altrimenti restituisce **false**.<br /><br /> Unione. Restituisce il secondo argomento se il primo argomento è un valore **non definito**.|  
|**Stringa**|**&#124;&#124;**|Concatenazione. Restituisce una concatenazione di entrambi gli argomenti.|  
  
 **Operatori ternari:**  

|**Nome**|**Operatore**|**Dettagli**| 
|-|-|-|  
|Operatore ternario|?|Restituisce il secondo argomento se il primo argomento restituisce **true**, altrimenti restituisce il terzo argomento.|  

  
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
  
 In Cosmos DB i tipi di valori sono spesso sconosciuti finché non vengono recuperati dal database. Per supportare un'esecuzione efficiente delle query, gran parte degli operatori presentano rigorosi requisiti di tipi. Inoltre, gli operatori di per sé non eseguono conversioni implicite.  
  
 Ciò significa che una query come: SELECT * FROM ROOT r WHERE r.Age = 21 restituisce solo documenti con la proprietà Age uguale al numero 21. I documenti con la proprietà Age uguale alla stringa "21" o alla stringa "0021" non corrispondono, poiché l'espressione "21" = 21 restituisce un valore non definito. Questo consente un uso migliore degli indici, poiché la ricerca di un valore specifico, ovvero il numero 21, è più veloce rispetto alla ricerca di un numero indefinito di possibili corrispondenze (il numero 21 o le stringhe "21", "021", "21.0" e così via). Ciò si differenzia dal modo in cui JavaScript valuta gli operatori per valori di tipi diversi.  
  
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
  
```sql  
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
  
* `<undefined_constant>; undefined`  
  
  Rappresenta un valore non definito di tipo Undefined.  
  
* `<null_constant>; null`  
  
  Rappresenta un valore **null** di tipo **Null**.  
  
* `<boolean_constant>`  
  
  Rappresenta una costante di tipo Boolean.  
  
* `false`  
  
  Rappresenta un valore **false** di tipo booleano.  
  
* `true`  
  
  Rappresenta un valore **true** di tipo booleano.  
  
* `<number_constant>`  
  
  Rappresenta una costante.  
  
* `decimal_literal`  
  
  I valori letterali decimali sono numeri rappresentati usando la notazione decimale o la notazione scientifica.  
  
* `hexadecimal_literal`  
  
  I valori letterali esadecimali sono numeri rappresentati usando il prefisso "0x" seguito da una o più cifre esadecimali.  
  
* `<string_constant>`  
  
  Rappresenta una costante di tipo String.  
  
* `string _literal`  
  
  I valori letterali stringa sono stringhe Unicode rappresentate da una sequenza di zero o più caratteri Unicode o sequenze di escape. I valori letterali stringa sono racchiusi tra virgolette singole (apostrofo: ') o virgolette doppie (virgolette inglesi: ").  
  
  Sono consentite le sequenze di escape seguenti:  
  
|**Sequenza di escape**|**Descrizione**|**Carattere Unicode**|  
|-|-|-|  
|\\'|apostrofo (')|U + 0027|  
|\\"|virgolette doppie (")|U + 0022|  
|\\\ |barra rovesciata (\\)|U + 005C|  
|\\/|barra (/)|U + 002F|  
|\b|BACKSPACE|U + 0008|  
|\f|avanzamento carta|U + 000C|  
|\n|avanzamento riga|U + 000A|  
|\r|ritorno a capo|U + 000D|  
|\t|TAB|U + 0009|  
|\uXXXX|Carattere Unicode definito da 4 cifre esadecimali.|U + XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Linee guida sulle prestazioni delle query  
 Per poter eseguire una query in modo efficiente per un contenitore di grandi dimensioni, è necessario usare filtri che possano essere gestiti attraverso uno o più indici.  
  
 Per la ricerca negli indici vengono considerati questi filtri:  
  
- Usare l'operatore di uguaglianza (=) con un'espressione percorso documenti e una costante.  
  
- Usare gli operatori di intervallo (<, \<=, >, >=) con un'espressione percorso documenti e costanti numeriche.  
  
- Con espressione percorso documenti si indica qualsiasi espressione che identifica un percorso costante nei documenti provenienti dal contenitore di database a cui si fa riferimento.  
  
  **Espressione percorso documenti**  
  
  Le espressioni percorso documenti sono espressioni usate in un percorso di funzioni di valutazione dell'indicizzatore proprietà o matrice per un documento proveniente da documenti di un contenitore di database. Questo percorso può essere usato per identificare la posizione dei valori a cui si fa riferimento in un filtro direttamente all'interno dei documenti del contenitore di database.  
  
  Per essere considerata un'espressione percorso documenti, è necessario che un'espressione:  
  
1.  Faccia riferimento direttamente alla radice del contenitore.  
  
2.  Faccia riferimento all'indicizzatore di proprietà o matrici costanti di un'espressione percorso documenti  
  
3.  Faccia riferimento a un alias che rappresenta un'espressione percorso documenti.  
  
     **Convenzioni della sintassi**  
  
     La tabella seguente indica le convenzioni usate per descrivere la sintassi nel riferimento SQL seguente.  
  
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
 In Cosmos DB sono disponibili molte funzioni SQL predefinite. Le categorie di funzioni predefinite sono elencate di seguito.  
  
|Funzione|Descrizione|  
|--------------|-----------------|  
|[Funzioni matematiche](#bk_mathematical_functions)|Le funzioni matematiche eseguono un calcolo basato in genere su valori di input passati come argomenti e restituiscono un valore numerico.|  
|[Funzioni di controllo del tipo](#bk_type_checking_functions)|Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione nell'ambito delle query SQL.|  
|[Funzioni stringa](#bk_string_functions)|Le funzioni stringa eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano.|  
|[Funzioni di matrice](#bk_array_functions)|Le funzioni di matrice eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice.|
|[Funzioni data e ora](#bk_date_and_time_functions)|Le funzioni di data e ora consentono di ottenere la data UTC corrente e l'ora in due forme; timestamp numerico il cui valore è espresso in millisecondi o sotto forma di stringa conforme al formato ISO 8601 epoca Unix.|
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
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  Nell'esempio seguente vengono illustrati i risultati dell'uso della funzione ABS su tre numeri diversi.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Questo è il set di risultati.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Restituisce l'angolo, espresso in radianti, il cui coseno corrisponde all'espressione numerica specificata. Denominato anche arcocoseno.  
  
 **Sintassi**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce l'arcocoseno di -1.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 Questo è il set di risultati.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Restituisce l'angolo, espresso in radianti, il cui seno è l'espressione numerica specificata. Detta anche arcoseno.  
  
 **Sintassi**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce l'arcoseno di -1.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 Questo è il set di risultati.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Restituisce l'angolo, espresso in radianti, la cui tangente è l'espressione numerica specificata. Detta anche arcotangente.  
  
 **Sintassi**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce l'arcotangente del valore specificato.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 Questo è il set di risultati.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Restituisce il valore principale dell'arcotangente di y/x, espresso in radianti.  
  
 **Sintassi**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente calcola l'arcotangente per i componenti x e y specificati.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Questo è il set di risultati.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Restituisce il più piccolo valore integer maggiore di o uguale all'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente usa valori numerici positivi, negativi e zero con la funzione CEILING.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Questo è il set di risultati.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
 **Sintassi**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente calcola il coseno dell'angolo specificato.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 Questo è il set di risultati.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Restituisce la cotangente trigonometrica dell'angolo specificato, espresso in radianti, nell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente calcola la cotangente dell'angolo specificato.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 Questo è il set di risultati.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Restituisce l'angolo corrispondente in gradi di un angolo specificato in radianti.  
  
 **Sintassi**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce il numero di gradi di un angolo di PI/2 radianti.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Questo è il set di risultati.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Restituisce il valore integer più alto, minore di o uguale all'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente usa valori numerici positivi, negativi e zero con la funzione FLOOR.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Questo è il set di risultati.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Restituisce il valore esponenziale dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **Osservazioni:**  
  
  La costante **e** (2,718281 …) è la base dei logaritmi naturali.  
  
  L'esponente di un numero è la costante **e** elevata alla potenza del numero. Ad esempio, EXP(1.0) = e^1.0 = 2,71828182845905 e EXP(10) = e^10 = 22026,4657948067.  
  
  Il valore esponenziale del logaritmo naturale di un numero è il numero stesso: EXP (LOG (n)) = n. E il logaritmo naturale del valore esponenziale di un numero è il numero stesso: LOG (EXP (n)) = n.  
  
  **esempi**  
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il valore esponenziale della variabile specificata (10).  
  
```  
SELECT EXP(10) AS exp  
```  
  
 Questo è il set di risultati.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 L'esempio seguente restituisce il valore esponenziale del logaritmo naturale di 20 e il logaritmo naturale dell'esponente di 20. Poiché queste funzioni sono inverse l'una dell'altra, il valore restituito con arrotondamento per il calcolo a virgola mobile in entrambi i casi è 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Questo è il set di risultati.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Restituisce il logaritmo naturale dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
- `base`  
  
   Argomento numerico facoltativo che imposta la base per il logaritmo.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **Osservazioni**  
  
  Per impostazione predefinita, LOG() restituisce il logaritmo naturale. È possibile modificare la base del logaritmo e impostare un altro valore usando il parametro di base facoltativo.  
  
  Il logaritmo naturale è il logaritmo in base **e**, dove **e** è una costante irrazionale approssimativamente uguale a 2,718281828.  
  
  Il logaritmo naturale del valore esponenziale di un numero è il numero stesso: LOG( EXP( n ) ) = n. E il valore esponenziale del logaritmo naturale di un numero è il numero stesso: EXP( LOG( n ) ) = n.  
  
  **esempi**  
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il logaritmo della variabile specificata (10).  
  
```  
SELECT LOG(10) AS log  
```  
  
 Questo è il set di risultati.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 L'esempio seguente calcola il logaritmo per l'esponente di un numero.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Questo è il set di risultati.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Restituisce il logaritmo in base 10 dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **Osservazioni:**  
  
  Le funzioni LOG10 e POWER sono inversamente correlate tra loro. Ad esempio, 10 ^ LOG10(n) = n.  
  
  **esempi**  
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il valore LOG10 della variabile specificata (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 Questo è il set di risultati.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Restituisce il valore costante di pi greco.  
  
 **Sintassi**  
  
```  
PI ()  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce il valore di pi greco.  
  
```  
SELECT PI() AS pi 
```  
  
 Questo è il set di risultati.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Restituisce il valore dell'espressione specificata alla potenza specificata.  
  
 **Sintassi**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
- `y`  
  
   È la potenza a cui elevare `numeric_expression`.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  Nell'esempio seguente viene elevato un numero alla potenza di 3 (cubo del numero).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Questo è il set di risultati.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Restituisce radianti quando viene immessa un'espressione numerica, espresso in gradi.  
  
 **Sintassi**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente accetta alcuni angoli come input e restituisce i valori in radianti corrispondenti.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Questo è il set di risultati.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Restituisce un valore numerico, arrotondato al valore integer più vicino.  
  
 **Sintassi**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **Osservazioni:**
  
  L'operazione di arrotondamento eseguita segue punto medio arrotondamento lontano da zero. Se l'input è un'espressione numerica compresa tra due interi, il risultato sarà il valore integer più vicino lontano da zero.  
  
  |<numeric_expression>|Arrotondato|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
  **esempi**  
  
  L'esempio seguente arrotonda numeri positivi e negativi al numero intero più prossimo.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Questo è il set di risultati.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 Restituisce il segno positivo (+1), zero (0) o negativo (-1) dell'espressione numerica specificata.  
  
 **Sintassi**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce i valori SIGN dei numeri da -2 a 2.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Questo è il set di risultati.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Restituisce il seno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
 **Sintassi**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente calcola il seno dell'angolo specificato.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 Questo è il set di risultati.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Restituisce la radice quadrata del valore numerico specificato.  
  
 **Sintassi**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce le radici quadrate dei numeri da 1 a 3.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Questo è il set di risultati.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Restituisce il quadrato del valore numerico specificato.  
  
 **Sintassi**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce i quadrati dei numeri da 1 a 3.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Questo è il set di risultati.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Restituisce la tangente dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
 **Sintassi**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente calcola la tangente di PI()/2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Questo è il set di risultati.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Restituisce un valore numerico, troncato al valore integer più vicino.  
  
 **Sintassi**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argomenti**  
  
- `numeric_expression`  
  
   È un'espressione numerica.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente tronca numeri positivi e negativi al numero intero più prossimo.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Questo è il set di risultati.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
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
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Questo è il set di risultati.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un valore booleano.  
  
 **Sintassi**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argomenti**  
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Questo è il set di risultati.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore.  
  
 **Sintassi**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argomenti**  
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente verifica la presenza di una proprietà all'interno del documento JSON specificato. La prima parte restituisce true perché "a" è presente, ma la seconda restituisce false perché "b" è assente.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Questo è il set di risultati.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è nulla.  
  
 **Sintassi**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argomenti**  
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_NULL.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Questo è il set di risultati.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un numero.  
  
 **Sintassi**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argomenti**  
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Questo è il set di risultati.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un oggetto JSON.  
  
 **Sintassi**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argomenti**  
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Questo è il set di risultati.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un primitivo (stringa, valore booleano, numerico o null).  
  
 **Sintassi**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argomenti**  
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Questo è il set di risultati.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Restituisce un valore booleano che indica se il tipo di espressione specificata è una stringa.  
  
 **Sintassi**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argomenti**  
  
- `expression`  
  
   È qualsiasi espressione valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la funzione IS_STRING.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Questo è il set di risultati.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  
  
###  <a name="bk_string_functions"></a> Funzioni stringa  
 Le funzioni scalari seguenti eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa.  
  
 **Sintassi**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente restituisce la stringa concatenata dei valori specificati.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Questo è il set di risultati.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo.  
  
 **Sintassi**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  Nell'esempio seguente viene verificato se "abc" contiene "ab" e contiene "d".  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Questo è il set di risultati.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo.  
  
 **Sintassi**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente restituisce "abc" che termina con "b" e "bc".  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Questo è il set di risultati.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Restituisce la posizione iniziale della prima occorrenza della seconda stringa di espressione all'interno della prima espressione stringa specificata oppure -1 se la stringa non viene trovata.  
  
 **Sintassi**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente restituisce l'indice di diverse sottostringhe all'interno di "abc".  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Questo è il set di risultati.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Restituisce la parte sinistra di una stringa con il numero specificato di caratteri.  
  
 **Sintassi**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
- `num_expr`  
  
   È qualsiasi espressione numerica valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente restituisce la parte sinistra di "abc" per diversi valori di lunghezza.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Questo è il set di risultati.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 Restituisce il numero di caratteri dell'espressione stringa specificata.  
  
 **Sintassi**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente restituisce la lunghezza di una stringa.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Questo è il set di risultati.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli.  
  
 **Sintassi**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare LOWER in una query.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 Questo è il set di risultati.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Restituisce un'espressione stringa dopo aver rimosso gli spazi vuoti iniziali.  
  
 **Sintassi**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare LTRIM all'interno di una query.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Questo è il set di risultati.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 Sostituisce tutte le occorrenze di un valore stringa specificato con un altro valore stringa.  
  
 **Sintassi**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare REPLACE in una query.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Questo è il set di risultati.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Ripete un valore stringa in un numero di volte specificato.  
  
 **Sintassi**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
- `num_expr`  
  
   È qualsiasi espressione numerica valida. Se num_expr è negativo o non finito, il risultato è non definito.

  > [!NOTE]
  > La lunghezza massima del risultato è 10.000 caratteri, ad esempio (length(str_expr) * num_expr) <= 10.000.
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare REPLICATE in una query.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Questo è il set di risultati.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Restituisce l'inverso di un valore stringa.  
  
 **Sintassi**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare REVERSE in una query.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Questo è il set di risultati.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Restituisce la parte destra di una stringa con il numero specificato di caratteri.  
  
 **Sintassi**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
- `num_expr`  
  
   È qualsiasi espressione numerica valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente restituisce la parte destra di "abc" per diversi valori di lunghezza.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Questo è il set di risultati.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Restituisce un'espressione di stringa dopo aver rimosso gli spazi vuoti finali.  
  
 **Sintassi**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare RTRIM all'interno di una query.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Questo è il set di risultati.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo.  
  
 **Sintassi**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  L'esempio seguente verifica se la stringa "abc" inizia con "b" e "a".  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Questo è il set di risultati.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Restituisce l'espressione convertita in una matrice. Se l'espressione non può essere convertita, restituisce non definita.  
  
 **Sintassi**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   È qualsiasi espressione scalare valida deve essere valutata come un'espressione di matrice JSON. Si noti che i valori stringa annidata devono essere scritte con le virgolette doppie per essere valido. Per informazioni dettagliate sul formato JSON, vedere [json.org](https://json.org/)
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di matrice o non definito.  
  
  **esempi**  
  
  Nell'esempio seguente mostra il comportamento StringToArray in tipi diversi. 
  
 Di seguito è riportati esempi con un input valido.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Questo è il set di risultati.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Di seguito è riportato un esempio di input non valido. 
   
 Le virgolette singole all'interno della matrice non sono un file JSON valido.
Anche se sono valide all'interno di una query, non verranno analizzati alle matrici valide. Le stringhe all'interno della stringa di matrice sia necessario utilizzare caratteri di escape "[\\"\\"]" oppure la virgoletta circostante deve avere una sola ' [""]'.

```
SELECT
    StringToArray("['5','6','7']")
```

Questo è il set di risultati.

```
[{}]
```

Di seguito è riportati esempi di input non valido.
   
 L'espressione passata verrà analizzata come una matrice JSON. di seguito non valuta tipo matrice e restituire in questo modo non definito.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Questo è il set di risultati.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Restituisce l'espressione convertita in un valore booleano. Se l'espressione non può essere convertita, restituisce non definita.  
  
 **Sintassi**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   È qualsiasi espressione scalare valida deve essere valutata come un'espressione booleana.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana o non definito.  
  
  **esempi**  
  
  Nell'esempio seguente mostra il comportamento StringToBoolean in tipi diversi. 
 
 Di seguito è riportati esempi con un input valido.

Lo spazio vuoto è consentito solo prima o dopo "true"/ "false".

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Questo è il set di risultati.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

Di seguito è riportati esempi con input non valido.

 Valori booleani sono tra maiuscole e minuscole e devono essere scritte con tutti i caratteri minuscoli, ad esempio "true" e "false".

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Questo è il set di risultati.  
  
```  
[{}]
``` 

L'espressione passata verrà analizzata come un'espressione booleana; i dati di input non restituiscono il tipo booleano e restituire in questo modo non definito.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Questo è il set di risultati.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Restituisce l'espressione convertita su null. Se l'espressione non può essere convertita, restituisce non definita.  
  
 **Sintassi**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   È qualsiasi espressione scalare valida deve essere valutata come un'espressione null.
  
  **Tipi restituiti**  
  
  Restituisce un'espressione null o non definito.  
  
  **esempi**  
  
  Nell'esempio seguente mostra il comportamento StringToNull in tipi diversi. 

Di seguito è riportati esempi con un input valido.

 Lo spazio vuoto è consentito solo prima o dopo "null".

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Questo è il set di risultati.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

Di seguito è riportati esempi con input non valido.

Null viene fatta distinzione tra maiuscole e minuscole e devono essere scritte con tutti i caratteri minuscoli, ad esempio "null".

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Questo è il set di risultati.  
  
```  
[{}]
```  

L'espressione passata verrà analizzata come un'espressione null. non valutano i dati di input da digitare null e pertanto restituisce non definito.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Questo è il set di risultati.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Restituisce l'espressione convertita in un numero. Se l'espressione non può essere convertita, restituisce non definita.  
  
 **Sintassi**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   È qualsiasi espressione scalare valida deve essere valutata come un'espressione del numero JSON. I numeri in formato JSON devono essere un numero intero o a virgola mobile. Per informazioni dettagliate sul formato JSON, vedere [json.org](https://json.org/)  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica o non definito.  
  
  **esempi**  
  
  Nell'esempio seguente mostra il comportamento StringToNumber in tipi diversi. 

Lo spazio vuoto è consentito solo prima o dopo il numero.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Questo è il set di risultati.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

In JSON deve essere un numero valido da un numero intero o mobile numero a virgola.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Questo è il set di risultati.  
  
```  
{{}}
```  

L'espressione passata verrà analizzata come un'espressione numerica. i dati di input non valutano tipo numero e restituire in questo modo non definito. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Questo è il set di risultati.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Restituisce l'espressione convertita in un oggetto. Se l'espressione non può essere convertita, restituisce non definita.  
  
 **Sintassi**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   È qualsiasi espressione scalare valida deve essere valutata come un'espressione di oggetto JSON. Si noti che i valori stringa annidata devono essere scritte con le virgolette doppie per essere valido. Per informazioni dettagliate sul formato JSON, vedere [json.org](https://json.org/)  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di oggetto o non definito.  
  
  **esempi**  
  
  Nell'esempio seguente mostra il comportamento StringToObject in tipi diversi. 
  
 Di seguito è riportati esempi con un input valido.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Questo è il set di risultati.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Di seguito è riportati esempi con input non valido.
Anche se sono valide all'interno di una query, non verranno analizzati per oggetti validi. Le stringhe all'interno della stringa dell'oggetto sia necessario utilizzare caratteri di escape "{\\" una\\":\\" str\\"}" o l'offerta circostante deve avere una sola ' {"a": "str"}'.

Virgolette che racchiudono i nomi di proprietà non sono un file JSON valido.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Questo è il set di risultati.

```  
[{}]
```  

I nomi delle proprietà senza virgolette non sono un file JSON valido.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Questo è il set di risultati.

```  
[{}]
``` 

Di seguito è riportati esempi con input non valido.

 L'espressione passata verrà analizzata come oggetto JSON. tipo di oggetto e restituire in questo modo non definito non valutano i dati di input.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Questo è il set di risultati.

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 Restituisce parte di un'espressione stringa a partire dalla posizione in base al carattere zero specificata e continua fino alla lunghezza specificata o alla fine della stringa.  
  
 **Sintassi**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
- `num_expr`  
  
   È qualsiasi espressione numerica valida per indicare il carattere iniziale e finale.    
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente restituisce la sottostringa di "abc" a partire da 1 e per una lunghezza di 1 carattere.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Questo è il set di risultati.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Restituisce una rappresentazione di espressione scalare. 
  
 **Sintassi**  
  
```  
ToString(<expr>)
```  
  
 **Argomenti**  
  
- `expr`  
  
   È qualsiasi espressione scalare valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente mostra il comportamento di ToString con tipi diversi.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Questo è il set di risultati.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Con l'input seguente:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 L'esempio seguente illustra come usare ToString con altre funzioni di stringa come CONCAT.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

Questo è il set di risultati.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Con l'input seguente.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
L'esempio seguente illustra come usare ToString con altre funzioni di stringa come REPLACE.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
Questo è il set di risultati.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 Restituisce un'espressione stringa dopo aver rimosso gli spazi vuoti iniziali e finali.  
  
 **Sintassi**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare TRIM all'interno di una query.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Questo è il set di risultati.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli.  
  
 **Sintassi**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argomenti**  
  
- `str_expr`  
  
   È qualsiasi espressione di stringa valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di stringa.  
  
  **esempi**  
  
  L'esempio seguente illustra come usare UPPER in una query  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 Questo è il set di risultati.  
  
```  
[{"upper": "ABC"}]  
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
  
- `arr_expr`  
  
   È qualsiasi espressione di matrice valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di matrice.  
  
  **esempi**  
  
  L'esempio seguente illustra come concatenare due matrici.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Questo è il set di risultati.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Restituisce un valore booleano che indica se la matrice contiene il valore specificato. È possibile cercare una corrispondenza parziale o completa di un oggetto usando un'espressione booleana all'interno del comando. 

**Sintassi**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argomenti**  
  
- `arr_expr`  
  
   È qualsiasi espressione di matrice valida.  
  
- `expr`  
  
   È qualsiasi espressione valida.  

- `bool_expr`  
  
   È un'espressione booleana. Se è impostata su "true" e se il valore di ricerca specificato è un oggetto, il comando cerca una corrispondenza parziale (l'oggetto di ricerca è un sottoinsieme di uno degli oggetti). Se è impostata su "false", il comando cerca una corrispondenza completa di tutti gli oggetti all'interno dell’array. Se non è specificato, il valore predefinito è false. 
  
  **Tipi restituiti**  
  
  Restituisce un valore booleano.  
  
  **esempi**  
  
  L'esempio seguente illustra come verificare l'appartenenza a una matrice usando ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Questo è il set di risultati.  
  
```  
[{"b1": true, "b2": false}]  
```  

L'esempio seguente illustra come verificare la corrispondenza di un JSON in una matrice usando ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Questo è il set di risultati.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Restituisce il numero di elementi dell'espressione di matrice specificato.  
  
 **Sintassi**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argomenti**  
  
- `arr_expr`  
  
   È qualsiasi espressione di matrice valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  L'esempio seguente illustra come ottenere la lunghezza di una matrice usando ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Questo è il set di risultati.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Restituisce parte di un'espressione di matrice.
  
 **Sintassi**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argomenti**  
  
- `arr_expr`  
  
   È qualsiasi espressione di matrice valida.  
  
- `num_expr`  
  
   Indice numerico in base zero in corrispondenza del quale iniziare la matrice. È possibile usare valori negativi per specificare l'indice iniziale rispetto all'ultimo elemento della matrice, ad esempio -1 fa riferimento all'ultimo elemento nella matrice.  

- `num_expr`  

   Numero massimo di elementi nella matrice risultante.    

  **Tipi restituiti**  
  
  Restituisce un'espressione di matrice.  
  
  **esempi**  
  
  L'esempio seguente illustra come ottenere sezioni diverse di una matrice usando ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Questo è il set di risultati.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

###  <a name="bk_date_and_time_functions"></a> Funzioni data e ora
 Le funzioni scalari seguenti consentono di ottenere la data UTC corrente e l'ora in due forme; timestamp numerico il cui valore è espresso in millisecondi o sotto forma di stringa conforme al formato ISO 8601 epoca Unix. 

|||
|-|-|
|[GetCurrentDateTime](#bk_get_current_date_time)|[GetCurrentTimestamp](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GetCurrentDateTime
 Restituisce la data UTC corrente e l'ora sotto forma di stringa ISO 8601.
  
 **Sintassi**
  
```
GetCurrentDateTime ()
```
  
  **Tipi restituiti**
  
  Restituisce l'ora UTC data e ora ISO 8601 stringa valore corrente. 

  Questo valore viene espresso nel formato AAAA-MM-DDThh:mm:ss.sssZ dove:
  
  |||
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 = gennaio, ecc.)|
  |GG|giorno a due cifre del mese (da 01 a 31)|
  |M|signifier per inizio degli elementi temporali|
  |hh|ora a 2 cifre (da 00 a 23)|
  |MM|minuti a due cifre (da 00 a 59)|
  |ss|secondi a due cifre (da 00 a 59)|
  |. SSS|tre cifre del numero decimale frazioni di secondo|
  |Z|Designatore UTC (Coordinated Universal Time)||
  
  Per altre informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Osservazioni:**

  GetCurrentDateTime è una funzione non deterministica. 
  
  Il risultato restituito è UTC (Coordinated Universal Time).

  **esempi**  
  
  Nell'esempio seguente viene illustrato come ottenere l'ora della data UTC corrente usando la funzione predefinita GetCurrentDateTime.
  
```  
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Di seguito è riportato un set di risultati di esempio.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GetCurrentTimestamp
 Restituisce il numero di millisecondi trascorsi 00:00:00 giovedì, dell'1 gennaio 1970. 
  
 **Sintassi**  
  
```  
GetCurrentTimestamp ()  
```  
  
  **Tipi restituiti**  
  
  Restituisce un valore numerico, il numero corrente di millisecondi trascorsi dall'epoca Unix, ovvero il numero di millisecondi trascorsi 00:00:00 giovedì, dell'1 gennaio 1970.

  **Osservazioni:**

  GetCurrentTimestamp è una funzione non deterministica. 
  
  Il risultato restituito è UTC (Coordinated Universal Time).

  **esempi**  
  
  Nell'esempio seguente viene illustrato come ottenere il timestamp corrente usando la funzione predefinita GetCurrentTimestamp.
  
```  
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Di seguito è riportato un set di risultati di esempio.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```  

###  <a name="bk_spatial_functions"></a> Funzioni spaziali  
 Le seguenti funzioni scalari eseguono un'operazione su un valore di input di oggetto spaziale e restituiscono un valore numerico o booleano.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Restituisce la distanza tra le due espressioni GeoJSON punto, poligono o LineString.  
  
 **Sintassi**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argomenti**  
  
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
 
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
 
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   È qualsiasi espressione di punto GeoJSON, poligono o LineString valida.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana.  
  
  **esempi**  
  
  Nell'esempio seguente viene illustrato come controllare se un punto è valido usando ST_VALID.  
  
  Ad esempio, questo punto ha un valore di latitudine che non rientra nell'intervallo valido di valori [-90, 90], quindi la query restituisce false.  
  
  Per i poligoni, la specifica GeoJSON richiede che l'ultima coppia di coordinate indicata corrisponda alla prima, in modo da creare una forma chiusa. I punti all'interno di un poligono devono essere specificati in senso antiorario. Un poligono specificato in senso orario rappresenta l'inverso dell'area al suo interno.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Questo è il set di risultati.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Restituisce un valore JSON che contiene un valore booleano valore se l'espressione GeoJSON punto, poligono o LineString specificata è valida e, se non valida, anche il motivo come valore stringa.  
  
 **Sintassi**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Argomenti**  
  
- `spatial_expr`  
  
   È qualsiasi espressione di punto GeoJSON o poligono valida.  
  
  **Tipi restituiti**  
  
  Restituisce un valore JSON che contiene un valore booleano valore se l'espressione punto o poligono GeoJSON specificata è valida e, se non valida, anche il motivo come valore stringa.  
  
  **esempi**  
  
  Nell'esempio seguente viene illustrato come controllare la validità (con i dettagli) usando ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b  
```  
  
 Questo è il set di risultati.  
  
```  
[{  
  "b": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
 
## <a name="next-steps"></a>Passaggi successivi  

- [Query e sintassi SQL per Cosmos DB](how-to-sql-query.md)

- [Documentazione di Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
