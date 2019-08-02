---
title: Funzioni di sistema
description: Informazioni sulle funzioni di sistema SQL in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b0e9c751d46f805af75196da464a39783c95ae6a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619984"
---
# <a name="system-functions"></a>Funzioni di sistema

 In Cosmos DB sono disponibili molte funzioni SQL predefinite. Le categorie di funzioni predefinite sono elencate di seguito.  
  
|Funzione|DESCRIZIONE|  
|--------------|-----------------|  
|[Funzioni matematiche](#mathematical-functions)|Le funzioni matematiche eseguono un calcolo basato in genere su valori di input passati come argomenti e restituiscono un valore numerico.|  
|[Funzioni di controllo del tipo](#type-checking-functions)|Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione nell'ambito delle query SQL.|  
|[Funzioni stringa](#string-functions)|Le funzioni stringa eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano.|  
|[Funzioni di matrice](#array-functions)|Le funzioni di matrice eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice.|
|[Funzioni di data e ora](#date-time-functions)|Le funzioni di data e ora consentono di ottenere la data e l'ora UTC correnti in due formati; timestamp numerico il cui valore è l'EPOCH UNIX in millisecondi o come stringa conforme al formato ISO 8601.|
|[Funzioni spaziali](#spatial-functions)|Le funzioni spaziali eseguono un'operazione su un valore di input di oggetto spaziale e restituiscono un valore numerico o booleano.|  

Di seguito è riportato un elenco di funzioni all'interno di ogni categoria:

| Gruppo di funzioni | Operazioni |
|---------|----------|
| Funzioni matematiche | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRONCA, ARCCOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANTI, RAND, SIN, TAN |
| Funzioni di controllo dei tipi | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funzioni stringa | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funzioni di matrice | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funzioni di data e ora | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| Funzioni spaziali | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se attualmente si usa una funzione definita dall'utente (UDF) per cui è ora disponibile una funzione predefinita, la funzione predefinita corrispondente sarà più veloce da eseguire e più efficiente.

La differenza principale tra funzioni Cosmos DB e funzioni SQL ANSI è che le funzioni Cosmos DB sono progettate per funzionare correttamente con dati senza schema e con schema misto. Se, ad esempio, una proprietà è mancante o presenta un valore non numerico come `unknown`, l'elemento viene ignorato anziché restituire un errore.

##  <a name="mathematical-functions"></a> Funzioni matematiche  

Le funzioni matematiche eseguono un calcolo basato su valori di input passati come argomenti e restituiscono un valore numerico.

È possibile eseguire query come nell'esempio seguente:

```sql
    SELECT VALUE ABS(-4)
```

Il risultato è:

```json
    [4]
```

Di seguito è riportata una tabella delle funzioni matematiche predefinite supportate.
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[RAND](#bk_rand)|
|[SIN](#bk_sin)|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|
|[SIGN](#bk_sign)|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
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
  
  L'operazione di arrotondamento eseguita segue l'arrotondamento a zero. Se l'input è un'espressione numerica che rientra esattamente tra due numeri interi, il risultato sarà il valore integer più vicino a zero.  
  
  |<numeric_expression>|Arrotondato|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0,5|1|
  |6,5000|7||
  
  **esempi**  
  
  L'esempio seguente arrotonda numeri positivi e negativi al numero intero più prossimo.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Questo è il set di risultati.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

#### <a name="bk_rand"></a>RAND
 Restituisce un valore numerico generato in modo casuale da [0, 1).
 
 **Sintassi**  
  
```  
RAND ()  
```  

  **Tipi restituiti**  
  
  Restituisce un'espressione numerica.  
  
  **esempi**  
  
  Nell'esempio seguente viene restituito un valore numerico generato in modo casuale.  
  
```  
SELECT RAND() AS rand 
```  
  
 Questo è il set di risultati.  
  
```  
[{"rand": 0.87860053195618093}]  
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

## <a id="type-checking-functions"></a>Funzioni di controllo dei tipi

Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione all'interno di una query SQL. È possibile utilizzare le funzioni di controllo del tipo per determinare i tipi di proprietà all'interno di elementi in tempo reale, quando sono variabili o sconosciute. Ecco una tabella delle funzioni di controllo dei tipi predefinite supportate:

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

## <a id="string-functions"></a>Funzioni stringa

Le funzioni scalari seguenti eseguono un'operazione su un valore di input stringa e restituiscono un valore stringa, numerico o booleano:
  
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
 Restituisce l'espressione convertita in una matrice. Se l'espressione non può essere convertita, restituisce undefined.  
  
 **Sintassi**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   Qualsiasi espressione scalare valida da valutare come espressione di matrice JSON. Si noti che i valori di stringa annidati devono essere scritti con virgolette doppie per essere validi. Per informazioni dettagliate sul formato JSON, vedere [JSON.org](https://json.org/)
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di matrice o undefined.  
  
  **esempi**  
  
  Nell'esempio seguente viene illustrato il comportamento di StringToArray tra tipi diversi. 
  
 Di seguito sono riportati esempi con input valido.

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
   
 Le virgolette singole all'interno della matrice non sono JSON valide.
Anche se sono valide all'interno di una query, non verranno analizzate in matrici valide. Per le stringhe all'interno della stringa di matrice deve essere preceduto\\da un carattere di escape "[\\" "]" oppure la virgoletta circostante deve essere singola "[" "]".

```
SELECT
    StringToArray("['5','6','7']")
```

Questo è il set di risultati.

```
[{}]
```

Di seguito sono riportati alcuni esempi di input non valido.
   
 L'espressione passata verrà analizzata come matrice JSON. gli elementi seguenti non restituiscono una matrice di tipi e pertanto restituiscono undefined.
   
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
 Restituisce l'espressione convertita in un valore booleano. Se l'espressione non può essere convertita, restituisce undefined.  
  
 **Sintassi**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   Qualsiasi espressione scalare valida da valutare come espressione booleana.  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione booleana o undefined.  
  
  **esempi**  
  
  Nell'esempio seguente viene illustrato il comportamento di StringToBoolean tra tipi diversi. 
 
 Di seguito sono riportati esempi con input valido.

Gli spazi vuoti sono consentiti solo prima o dopo "true"/"false".

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

Di seguito sono riportati esempi con input non valido.

 I valori booleani fanno distinzione tra maiuscole e minuscole e devono essere scritti con caratteri minuscoli, ad esempio "true" e "false".

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Questo è il set di risultati.  
  
```  
[{}]
``` 

L'espressione passata verrà analizzata come espressione booleana. questi input non restituiscono il tipo booleano e pertanto restituiscono undefined.

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
 Restituisce l'espressione convertita in null. Se l'espressione non può essere convertita, restituisce undefined.  
  
 **Sintassi**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   Qualsiasi espressione scalare valida da valutare come espressione null.
  
  **Tipi restituiti**  
  
  Restituisce un'espressione null o non definita.  
  
  **esempi**  
  
  Nell'esempio seguente viene illustrato il comportamento di StringToNull tra tipi diversi. 

Di seguito sono riportati esempi con input valido.

 Gli spazi vuoti sono consentiti solo prima o dopo "null".

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

Di seguito sono riportati esempi con input non valido.

Null fa distinzione tra maiuscole e minuscole e deve essere scritto con caratteri minuscoli, ovvero "null".

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Questo è il set di risultati.  
  
```  
[{}]
```  

L'espressione passata verrà analizzata come espressione null; questi input non restituiscono il tipo null e pertanto restituiscono undefined.

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
 Restituisce l'espressione convertita in un numero. Se l'espressione non può essere convertita, restituisce undefined.  
  
 **Sintassi**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   Qualsiasi espressione scalare valida da valutare come espressione numerica JSON. I numeri in JSON devono essere un numero intero o un punto a virgola mobile. Per informazioni dettagliate sul formato JSON, vedere [JSON.org](https://json.org/)  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione numerica o non definita.  
  
  **esempi**  
  
  Nell'esempio seguente viene illustrato il comportamento di StringToNumber tra tipi diversi. 

Gli spazi vuoti sono consentiti solo prima o dopo il numero.

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

In JSON un numero valido deve essere un numero intero o un numero a virgola mobile.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Questo è il set di risultati.  
  
```  
{{}}
```  

L'espressione passata verrà analizzata come espressione numerica. questi input non restituiscono il tipo Number e pertanto restituiscono undefined. 

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

####  <a name="bk_stringtoobject"></a>StringToObject  
 Restituisce l'espressione convertita in un oggetto. Se l'espressione non può essere convertita, restituisce undefined.  
  
 **Sintassi**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argomenti**  
  
- `expr`  
  
   Qualsiasi espressione scalare valida da valutare come espressione di oggetto JSON. Si noti che i valori di stringa annidati devono essere scritti con virgolette doppie per essere validi. Per informazioni dettagliate sul formato JSON, vedere [JSON.org](https://json.org/)  
  
  **Tipi restituiti**  
  
  Restituisce un'espressione di oggetto o non definita.  
  
  **esempi**  
  
  Nell'esempio seguente viene illustrato il comportamento di StringToObject tra tipi diversi. 
  
 Di seguito sono riportati esempi con input valido.

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

 Di seguito sono riportati esempi con input non valido.
Anche se sono valide all'interno di una query, non verranno analizzate in oggetti validi. Le stringhe all'interno della stringa dell'oggetto devono essere precedute\\da un carattere di\\Escape "\\{" a\\":" Str "}" oppure la virgoletta circostante deve essere singola ' {"a": "Str"}'.

Le virgolette singole che racchiudono i nomi delle proprietà non sono JSON valide.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Questo è il set di risultati.

```  
[{}]
```  

I nomi di proprietà senza virgolette circostanti non sono JSON validi.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Questo è il set di risultati.

```  
[{}]
``` 

Di seguito sono riportati esempi con input non valido.

 L'espressione passata verrà analizzata come un oggetto JSON. questi input non restituiscono un oggetto di tipo e pertanto restituiscono undefined.

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

## <a id="array-functions"></a>Funzioni di matrice

Le funzioni scalari seguenti eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice:
  
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
## <a id="date-time-functions"></a>Funzione data e ora

Le funzioni scalari seguenti consentono di ottenere la data e l'ora UTC correnti in due formati; timestamp numerico il cui valore è l'EPOCH UNIX in millisecondi o come stringa conforme al formato ISO 8601. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 Restituisce la data e l'ora UTC correnti come stringa ISO 8601.
  
 **Sintassi**
  
```
GETCURRENTDATETIME ()
```
  
  **Tipi restituiti**
  
  Restituisce la data e l'ora UTC correnti del valore stringa ISO 8601. 

  Questa operazione è espressa nel formato AAAA-MM-GGThh: mm: SS. sssZ dove:
  
  |||
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 = gennaio e così via)|
  |GG|giorno del mese a due cifre (da 01 a 31)|
  |T|significato per l'inizio degli elementi Time|
  |hh|ora a due cifre (da 00 a 23)|
  |MM|minuti a due cifre (da 00 a 59)|
  |SS|secondi a due cifre (da 00 a 59)|
  |. sss|tre cifre di frazioni decimali di secondo|
  |Z|Indicatore UTC (Coordinated Universal Time)||
  
  Per ulteriori informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Osservazioni:**

  GETCURRENTDATETIME è una funzione non deterministica. 
  
  Il risultato restituito è UTC (Coordinated Universal Time).

  **esempi**  
  
  Nell'esempio seguente viene illustrato come ottenere la data e ora UTC correnti utilizzando la funzione predefinita GetCurrentDateTime.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Di seguito è riportato un esempio di set di risultati.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a>GETCURRENTTIMESTAMP
 Restituisce il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970. 
  
 **Sintassi**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Tipi restituiti**  
  
  Restituisce un valore numerico, il numero corrente di millisecondi trascorsi dal periodo UNIX, ovvero il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

  **Osservazioni:**

  GETCURRENTTIMESTAMP è una funzione non deterministica.
  
  Il risultato restituito è UTC (Coordinated Universal Time).

  **esempi**  
  
  Nell'esempio seguente viene illustrato come ottenere il timestamp corrente utilizzando la funzione predefinita GetCurrentTimestamp.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Di seguito è riportato un esempio di set di risultati.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Funzioni spaziali

Cosmos DB supporta le seguenti funzioni predefinite di Open Geospatial Consortium (OGC) per l'esecuzione di query geospaziali. Le seguenti funzioni scalari eseguono un'operazione su un valore di input di oggetto spaziale e restituiscono un valore numerico o booleano.  
  
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

- [Introduzione a Azure Cosmos DB](introduction.md)
- [UDF](sql-query-udfs.md)
- [Aggregati](sql-query-aggregates.md)
