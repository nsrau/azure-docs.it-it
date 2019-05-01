---
title: Funzioni per le espressioni nella funzionalità di Mapping del flusso di dati di Azure Data Factory
description: Informazioni sulle funzioni di espressione di Mapping del flusso di dati.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 6b4df70114dd481566ae1a666c91c1c9b5bad86f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717011"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Espressioni di trasformazione dei dati nel flusso di dati di Mapping 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Funzioni di espressione

In Data Factory, usare il linguaggio delle espressioni della funzionalità di Mapping del flusso di dati per configurare le trasformazioni dei dati.

*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione contrassegna il modulo positivo della coppia di numeri.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola un valore del coseno inverso.
* ``acos(1) -> 0.0``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Questa espressione consente di aggiungere una coppia di stringhe o numeri. Aggiunge una data un numero di giorni. Aggiunge una matrice di tipo simile a un altro. 
* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``

Il **aggiungere** operatore è lo stesso come il **+** operatore.
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Questa espressione consente di aggiungere giorni a una data o timestamp. 
* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``

Il **addDays** operatore è lo stesso come il **+** operatore per le date.
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Questa espressione aggiunge mesi a una data o timestamp.
* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Si tratta di una logica **e** operatore. Questo è lo stesso come il **&&** operatore.
* ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola la funzione inversa del seno.
* ``asin(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola la tangente inversa.
* ``atan(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione restituisce l'angolo in radianti, tra l'asse x positivo di un piano e il punto che forniscono le coordinate.
* ``atan2(0, 0) -> 0.0``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione Ottiene la media dei valori di una colonna.
* ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la media dei valori di una colonna.
* ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>byName</code>
==============================
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Questa espressione consente di selezionare un valore di colonna dal nome del flusso. Se sono presenti più corrispondenze, viene restituita la prima corrispondenza. Se non viene trovata alcuna corrispondenza, l'espressione restituisce un valore NULL. Il valore restituito deve essere convertito da una delle funzioni di conversione di tipo (TO_DATE, TO_STRING...). I nomi di colonna sono noti in fase di progettazione devono essere risolto semplicemente in base al nome. Gli input calcolati non sono supportati, ma è possibile utilizzare le sostituzioni di parametro.

* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
*********************************
<code>byPosition</code>
==============================
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Questa espressione consente di selezionare un valore di colonna in base alla posizione relativa (in base 1) nel flusso. Se la posizione è compreso nell'intervallo, restituisce un valore NULL. Il valore restituito deve essere convertito da una delle funzioni di conversione di tipo (TO_DATE, TO_STRING e così via). Gli input calcolati non sono supportati, ma è possibile utilizzare le sostituzioni di parametro.

* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Basato su condizioni di alternative, questa espressione applica un valore o l'altro. 
* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``

Se il numero di input è pari, l'altro valore è NULL per l'ultima condizione.
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola la radice cubica di un numero.
* ``cbrt(8) -> 2.0``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione restituisce l'integer più piccolo non inferiore al numero.
* ``ceil(-0.1) -> 0``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Questa espressione concatena un numero variabile di stringhe. Il **concat** operatore è lo stesso come il **+** operatore con stringhe.
* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Questa espressione concatena un numero variabile di stringhe con un separatore. Il primo parametro è il separatore.
* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola un valore del coseno.
* ``cos(10) -> -0.83907152907``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola un coseno iperbolico di un valore.
* ``cosh(0) -> 1.0``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Questa espressione Ottiene il conteggio aggregato dei valori. Se le colonne facoltativo vengono specificato, NULL vengono ignorati i valori nel conteggio.
* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Questa espressione Ottiene il conteggio di aggregazione di valori distinct di un set di colonne.
* ``countDistinct(custId, custName) -> 60``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene il conteggio aggregato dei valori. Se la colonna facoltativa viene specificato, NULL vengono ignorati i valori nel conteggio.
* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la covarianza della popolazione tra due colonne.
* ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la covarianza della popolazione di due colonne.
* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la covarianza del campione di due colonne.
* ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la covarianza del campione di due colonne.
* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Questa espressione calcola l'hash CRC32 di un set di colonne di diversi tipi di dati primitivi, ha una lunghezza in bit i cui valori possono essere solo 0(256), 224, 256, 384 e 512. È possibile usare la **crc32** operatore per calcolare un'impronta digitale per una riga.
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
Questa funzione calcola la posizione di un valore relativo a tutti i valori nella partizione. Il risultato è il numero di righe precedente o uguale alla riga corrente nell'ordinamento della partizione, divisa per il numero totale di righe nella partizione finestra. I valori di valore equivalente nell'ordinamento restituiscono la stessa posizione.
* ``cumeDist() -> 1``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Questa espressione Ottiene la data corrente quando il processo viene avviato per l'esecuzione. 
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``

È possibile passare un fuso orario facoltativo nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il fuso orario locale è il valore predefinito.
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Questa espressione Ottiene il timestamp corrente quando viene avviato il processo per l'esecuzione con il fuso orario locale.
* ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Questa espressione Ottiene il timestamp corrente come ora UTC. 
* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``

È possibile passare un fuso orario facoltativo nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il fuso orario locale è il valore predefinito.
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Data una data, l'espressione Ottiene il giorno del mese.
* ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Data una data, l'espressione Ottiene il giorno della settimana. 
* ``dayOfWeek(toDate('2018-06-08')) -> 7``

I valori dei giorni sono i seguenti:
- 1 - domenica
- 2 - lunedì 
- ...
- 7 - domenica
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Data una data, l'espressione Ottiene il giorno dell'anno.
* ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione converte i radianti in gradi.
* ``degrees(3.141592653589793) -> 180``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Questa espressione calcola il rango di un valore in un gruppo di valori. Come risultato si ha uno più il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione. I valori non producono i gap nella sequenza. 
* ``denseRank(salesQtr, salesAmt) -> 1``

Il **denseRank** operatore funziona anche quando i dati non ordinati. Cerca una modifica dei valori.
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Questa espressione divide una coppia di numeri. Il **dividere** operatore è lo stesso come il **/** operatore.
* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Questa espressione controlla se la stringa termina con la stringa specificata.
* ``endsWith('great', 'eat') -> true``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Si tratta di un operatore di confronto uguale a. È lo stesso come il **==** operatore.
* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Il **equalsIgnoreCase** è un operatore di confronto uguale a cui Ignora maiuscole e minuscole. È lo stesso come il **<=>** operatore.
* ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Questa espressione calcola il fattoriale di un numero.
* ``factorial(5) -> 120``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
Sempre questa espressione restituisce un valore false. 
* ``isDiscounted == false()``
* ``isDiscounted() == false``

Usare la `syntax(false())` funzionare se una colonna viene denominata *false*.
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Questa espressione Ottiene il primo valore di un gruppo di colonne. Se si omette il secondo parametro `ignoreNulls`, si presuppone sia false.
* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione restituisce l'intero più grande che non è maggiore del numero.
* ``floor(-0.1) -> -1``
*********************************
<code>fromUTC</code>
==============================
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Questa espressione viene convertita in timestamp rispetto all'ora UTC. È possibile passare il fuso orario nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il valore predefinito è il fuso orario corrente.

* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Si tratta di un confronto **maggiore** operatore. È lo stesso come il **>** operatore.
* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Si tratta di un operatore di confronto maggiore-than-or-equal. Questo operatore è lo stesso come il **>=** operatore.
* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Questa espressione restituisce il valore massimo nell'elenco di valori come input. Restituisce NULL se tutti gli input sono NULL.
* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Questa espressione Ottiene il valore dell'ora di un timestamp. 
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``

È possibile passare un fuso orario facoltativo nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il fuso orario locale è il valore predefinito.
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Basato su una condizione, questa espressione applica un valore o l'altro. Se l'altro valore non viene specificato, viene considerato NULL. Entrambi i valori devono essere compatibili (numeric o string, ad esempio).
* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Questa espressione controlla per un elemento nella matrice.
* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione Converte la prima lettera di ogni parola in lettere maiuscole. Le parole sono identificate da separazioni gli spazi vuoti.
* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Questa espressione Trova la posizione (in base 1) della sottostringa all'interno di una stringa. Se la posizione non viene trovata, viene restituito 0. 
* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Questa espressione controlla le righe contrassegnate per l'eliminazione. 
* ``isDelete() -> true``
* ``isDelete(1) -> false``

Se la trasformazione richiede più di un flusso di input, è possibile passare l'indice del flusso (in base 1). Il valore predefinito per l'indice del flusso è 1.
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Questa espressione controlla le righe contrassegnate come errori.
* ``isError() -> true``
* ``isError(1) -> false``

Se la trasformazione richiede più di un flusso di input, è possibile passare l'indice del flusso (in base 1). Il valore predefinito per l'indice del flusso è 1.
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Questa espressione controlla le righe contrassegnate per essere ignorato.
* ``isIgnore() -> true``
* ``isIgnore(1) -> false``

Se la trasformazione richiede più di un flusso di input, è possibile passare l'indice del flusso (in base 1). Il valore predefinito per l'indice del flusso è 1.
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Questa espressione controlla le righe contrassegnate per l'inserimento. 
* ``isInsert() -> true``
* ``isInsert(1) -> false``

Se la trasformazione richiede più di un flusso di input, è possibile passare l'indice del flusso (in base 1). Il valore predefinito per l'indice del flusso è 1.
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Questa espressione controlla le righe corrispondente nella ricerca. 
* ``isMatch() -> true``
* ``isMatch(1) -> false``

Se la trasformazione richiede più di un flusso di input, è possibile passare l'indice del flusso (in base 1). Il valore predefinito per l'indice del flusso è 1.
*********************************
<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Questa espressione controlla il valore NULL.
* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Questa espressione controlla le righe contrassegnate per l'aggiornamento. 
* ``isUpdate() -> true``
* ``isUpdate(1) -> false``

Se la trasformazione richiede più di un flusso di input, è possibile passare l'indice del flusso (in base 1). Il valore predefinito per l'indice del flusso è 1.
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene curtosi di una colonna.
* ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene curtosi di una colonna.
* ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Questa espressione Ottiene il valore del primo parametro valutato *n* righe prima della riga corrente. Il secondo parametro è il numero di righe da controllare. Il valore predefinito è 1. Se non sono il numero di righe, viene restituito un valore null a meno che non si specifica un valore predefinito.
* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Questa espressione Ottiene l'ultimo valore di un gruppo di colonne. Se si omette il secondo parametro `ignoreNulls`, l'espressione restituisce `false`.
* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Data una data, l'espressione Ottiene l'ultima data del mese.
* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Questa espressione Ottiene il valore del primo parametro valutato *n* righe dopo la riga corrente. Il secondo parametro è il numero di righe da verificare. Il valore predefinito è 1. Se non sono il numero di righe, viene restituito un valore null a meno che non si specifica un valore predefinito.
* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Si tratta di un operatore di confronto minore-than-or-equal. È lo stesso come il **<=** operatore.
* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>left</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Questa espressione consente di estrarre una sottostringa iniziale in corrispondenza dell'indice 1 e Usa il numero di caratteri. Il **a sinistra** operatore è uguale **SOTTOSTRINGA (str, 1, n)**.
* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Questa espressione restituisce la lunghezza della stringa.
* ``length('kiddo') -> 5``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Si tratta di un confronto meno-operatore di maggioranza. È lo stesso come il **<** operatore.
* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Si tratta di un operatore di confronto minore-than-or-equal. È lo stesso come il **<=** operatore.
* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Questa espressione Ottiene la distanza Levenshtein tra due stringhe.
* ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
In questa espressione, il modello è una stringa che viene eseguita una ricerca letterale. 
* ``like('icecream', 'ice%') -> true``

Le eccezioni sono simboli speciali seguenti:  
* `_` Ciò corrisponde a qualsiasi carattere di input. È simile a `.` nelle espressioni regolari POSIX.
* `%` Ciò corrisponde a zero o più caratteri nell'input. È simile a questo simbolo `.*` nelle espressioni regolari POSIX.
* `''` Questo è il carattere di escape. Se un carattere di escape precede un simbolo speciale o un altro carattere di escape, per il carattere successivo viene stabilita una corrispondenza letterale. È possibile utilizzare l'escape di qualsiasi altro carattere.
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Questa espressione Trova la posizione (in base 1) della sottostringa all'interno di una stringa, iniziando in corrispondenza di una determinata posizione. Se si omette la posizione, la valutazione inizia all'inizio della stringa. Se la posizione non viene trovata, viene restituito 0. 
* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Questa espressione calcola il valore di registro. È possibile fornire una base facoltativa o un numero di Eulero, se viene usato.
* ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
L'espressione utilizza un base 10 per calcolare il valore del registro.
* ``log10(100) -> 2``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione imposta una stringa in lettere minuscole.
* ``lower('GunChus') -> 'gunchus'``
*********************************
<code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
La stringa finché la stringa usando il riempimento specificato, questa espressione a sinistra, fino raggiunge una determinata lunghezza. Se la stringa è uguale o maggiore della lunghezza, non ha considerato un'alcuna operazione (no-op).
* ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione a sinistra-oggetti rimossi dalla stringa di caratteri iniziali. Se non viene specificato un secondo parametro, l'espressione rimuove gli spazi vuoti. In caso contrario, rimuove il carattere che il secondo parametro specifica.
* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Questa espressione Ottiene il valore massimo di una colonna.
* ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene il valore massimo di una colonna.
* ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Questa espressione calcola il digest MD5 di un set di colonne diversi tipi di dati primitivi. Restituisce una stringa esadecimale a 32 caratteri. 
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``

È possibile usare la **md5** operatore per calcolare un'impronta digitale per una riga.
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione Ottiene la media dei valori di una colonna. Il **significare** operatore equivale a Media
* ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la media dei valori di una colonna. Il **meanIf** operatore è uguale **avgIf**.
* ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Questa espressione Ottiene il valore minimo di una colonna.
* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene il valore minimo di una colonna.
* ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Questa espressione sottrae numeri. Ad esempio, è possibile sottrarre un numero di giorni da una data. Il **meno** operatore è lo stesso come il **-** operatore.
* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Questa espressione Ottiene il valore dei minuti di un timestamp. 
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``

È possibile passare un fuso orario facoltativo nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il fuso orario locale è il valore predefinito.
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Il **mod** operatore contrassegna una coppia di modulo di numeri. Questo è lo stesso come il **%** operatore.
* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Questa espressione Ottiene il valore del mese di una data o timestamp.
* ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Questa espressione Ottiene il numero di mesi tra due date. 
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

È possibile passare un fuso orario facoltativo nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il fuso orario locale è il valore predefinito.
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Questa espressione Moltiplica una coppia di numeri. Il **moltiplicare** operatore è lo stesso come il * operatore.
* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Il **nTile** funzione divide le righe per ogni partizione finestra nelle *n* bucket, nell'intervallo compreso tra 1 e al massimo *n*. Valore di bucket differiscono per al massimo 1. Se il numero di righe nella partizione non divide uniformemente il numero di bucket, ogni valore rimanente viene distribuito in un bucket, a partire il primo bucket. 

* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``

Il **nTile** funzione è utile quando è necessario calcolare tertiles, stimare i quartili, decili e altre statistiche summary comuni. La funzione Calcola due variabili durante l'inizializzazione. Una riga aggiuntiva viene aggiunta a un bucket regolare. Entrambe le variabili sono basate sulle dimensioni della partizione corrente. 

Durante il calcolo, la funzione tiene traccia di numero di riga corrente, il numero di bucket corrente e il numero di riga in corrispondenza del quale il bucket cambierà (bucketThreshold). Quando il numero di righe raggiunge la soglia di bucket, il valore di bucket aumenta di uno. La soglia aumenta la dimensione del bucket, più uno aggiuntivo se viene riempito il bucket corrente.
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione NOT logico di un numero. Infatti, tra i numeri positivi per i numeri negativi e viceversa.
* ``negate(13) -> -13``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
Questa espressione restituisce la sequenza univoca successiva. Il numero è consecutivo solo all'interno di una partizione. Che è preceduto da `partitionId`.
* ``nextSequence() -> 12313112``
*********************************
<code>normalize</code>
==============================
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizzare il valore di stringa per separare i caratteri unicode accentati * ``normalize('boys') -> 'boys'``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Il **non** è un operatore di negazione logica.
* ``not(true) -> false``
* ``not(premium)``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Il **notEquals** operatore è un operatore di confronto not-equals. È lo stesso come il **! =** operatore.
* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
Questa espressione restituisce un valore NULL. 
* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``

Utilizzare la funzione **syntax(null())** se una colonna viene denominata *null*. Qualsiasi operazione che utilizza l'operatore NULL restituisce NULL.
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Il **oppure** operatore è logico **OR** operatore. È lo stesso come il **||** operatore.
* ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Il **pMod** operatore contrassegna il modulo positivo della coppia di numeri.
* ``pmod(-20, 8) -> 4``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione genera un numero alla potenza di un altro.
* ``power(10, 2) -> 100``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Questa espressione calcola il rango di un valore in un gruppo di valori. Come risultato si ha uno più il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione. I valori di producono i gap nella sequenza. 
* ``rank(salesQtr, salesAmt) -> 1``

Il **rank** operatore funziona anche quando i dati non ordinati. La ricerca di modifiche nei valori.
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Questa espressione consente di estrarre una sottostringa corrisponda per un modello di espressione regolare specificata. L'ultimo parametro identifica il gruppo di corrispondenza. Se si omette l'ultimo parametro, il valore predefinito è 1. 
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

Usare `<regex>`(virgoletta inversa) per confrontare una stringa senza escape.
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Questa espressione controlla se la stringa corrisponde al modello di espressione regolare specificata. 
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

Usare `<regex>`(virgoletta inversa) per confrontare una stringa senza escape.
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione sostituisce tutte le occorrenze di un modello regex con un'altra sottostringa nella stringa specificata.
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

Usare `<regex>`(virgoletta inversa) per confrontare una stringa senza escape.
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Questa espressione suddivide una stringa in base a un delimitatore basato su espressioni regolari. Restituisce una matrice di stringhe.
* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione sostituisce tutte le occorrenze di una sottostringa con un'altra sottostringa nella stringa specificata.
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione consente di invertire una stringa.
* ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Questa espressione estrae una sottostringa con numero di caratteri da destra. 
* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``

Il **a destra** funzione è lo stesso SOTTOSTRINGA (str, LENGTH(str) - n, n=10).
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Questa espressione controlla se la stringa corrisponde al modello di espressione regolare specificata.
* ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Dato un facoltativo di scalabilità e la modalità di arrotondamento facoltativa, questa espressione Arrotonda un numero. Se si omette la scala, il valore predefinito è 0. Se si omette la modalità, il valore predefinito è ROUND_HALF_UP(5). 

* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``

Questi sono i valori per l'arrotondamento di:
* 1 - ROUND_UP
* 2 - ROUND_DOWN
* 3 - ROUND_CEILING
* 4 - ROUND_FLOOR
* 5 - ROUND_HALF_UP
* 6 - ROUND_HALF_DOWN
* 7 - ROUND_HALF_EVEN
* 8 - ROUND_UNNECESSARY

*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
Questa espressione consente di assegnare una riga sequenza di numerazione delle righe in una finestra, a partire da 1.
* ``rowNumber() -> 1``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione a destra, fino la stringa di riempimento specificato finché la stringa raggiunge una determinata lunghezza. Se la stringa è uguale o maggiore della lunghezza, non ha considerato un'alcuna operazione (no-op).
* ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
<code>rtrim</code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione a destra-oggetti rimossi dalla stringa di caratteri iniziali. Se non si specifica il secondo parametro, l'espressione rimuove gli spazi vuoti. In caso contrario, rimuove qualsiasi carattere che specifica il secondo parametro.
* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Questa espressione Ottiene il secondo valore di una data. 
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``

È possibile passare un fuso orario facoltativo nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il fuso orario locale è il valore predefinito.
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Questa espressione calcola il digest SHA-1 di un set di colonne diversi tipi di dati primitivi. Restituisce una stringa esadecimale di 40 caratteri. 
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``

È possibile usare `sha1` per calcolare un'impronta digitale per una riga.
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Questa espressione calcola il digest SHA-2 di un set di colonne di diversi tipi di dati primitivi, ha una lunghezza in bit i cui valori possono essere solo 0(256), 224, 256, 384 e 512. 
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``

È possibile usare `sha2` per calcolare un'impronta digitale per una riga.
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola un valore del seno.
* ``sin(2) -> 0.90929742682``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola un valore del seno iperbolico.
* ``sinh(0) -> 0.0``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene l'asimmetria di una colonna.
* ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene l'asimmetria di una colonna.
* ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Questa espressione consente di estrarre un subset di una matrice da una posizione. La posizione è basata su 1. Se si omette la lunghezza, il valore predefinito è la fine della stringa.
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione Ottiene il codice soundex della stringa.
* ``soundex('genius') -> 'G520'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Questa espressione suddivide una stringa in base a un delimitatore. Restituisce una matrice di stringhe.
* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola la radice quadrata di un numero.
* ``sqrt(9) -> 3``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Questa espressione controlla se la stringa inizia con la stringa specificata.
* ``startsWith('great', 'gr') -> true``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la deviazione standard di una colonna.
* ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la deviazione standard di una colonna.
* ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la deviazione standard della popolazione di una colonna.
* ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la deviazione standard della popolazione di una colonna.
* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la deviazione standard del campione di una colonna.
* ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la deviazione standard del campione di una colonna.
* ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Questa espressione consente di sottrarre mesi da una data. 
* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
Il **subDays** operatore è lo stesso come il **-** operatore per Data.
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Questa espressione consente di sottrarre mesi dalla data o timestamp.
* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Questa espressione consente di estrarre una sottostringa di una certa lunghezza da una posizione. La posizione è basata su 1. Se si omette la lunghezza, il valore predefinito è la fine della stringa.
* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione Ottiene la somma di aggregazione di una colonna numerica.
* ``sum(col) -> value``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Questa espressione Ottiene la somma di aggregazione di valori distinti di una colonna numerica.
* ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la somma di aggregazione di una colonna numerica. È possibile basare la condizione su qualsiasi colonna.
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Basato sul criterio, questa espressione Ottiene la somma di aggregazione di una colonna numerica. È possibile basare la condizione su qualsiasi colonna.
* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola un valore tangente.
* ``tan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione calcola un valore della tangente iperbolico.
* ``tanh(0) -> 0.0``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Questa espressione consente di convertire un valore di `('t', 'true', 'y', 'yes', '1')` su true. La funzione converte `('f', 'false', 'n', 'no', '0')` su false. Per qualsiasi altro valore, restituito NULL.
* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Dato un formato di data facoltativo, l'espressione converte una stringa in una data. Java SimpleDateFormat per vedere tutti i formati di data possibile. 
* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``

Se si omette il formato della data, vengono accettate le combinazioni delle opzioni seguenti: [aaaa yyyy, aaaa-[M] M, d [d]-[M] M - AAAA-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *].
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Questa espressione converte qualsiasi stringa o numerica in un valore decimale. 
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

Se non si specifica di precisione e scala, il valore predefinito è (10,2). È possibile usare un formato decimale Java facoltativo per la conversione.

*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
Questa espressione converte qualsiasi stringa o numerica in un valore double. È possibile usare un formato decimale Java facoltativo per la conversione.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
Questa espressione converte qualsiasi stringa o numerica in un valore float. È possibile usare un formato decimale Java facoltativo per la conversione. 
* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``

Il **toFloat** funzione tronca qualsiasi valore double.
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
Questa espressione converte qualsiasi stringa o numerica in un valore intero. È possibile usare un formato decimale Java facoltativo per la conversione. 
* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``

Il **toInteger** funzione tronca qualsiasi long, float o double.
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
Questa espressione converte qualsiasi stringa o numerica in un valore long. È possibile usare un formato decimale Java facoltativo per la conversione. 
* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``

Il **toLong** funzione tronca qualsiasi float o Double.
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
Questa espressione converte qualsiasi stringa o numerica in un valore breve. È possibile usare un formato decimale Java facoltativo per la conversione. 
* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``

Il **toShort** funzione tronca qualsiasi integer, long, float o double.
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Questa espressione consente di convertire un tipo di dati primitivi in una stringa. 
* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``

È possibile specificare un formato per date e numeri. Se non si specifica un formato, viene utilizzata l'impostazione predefinita. Il formato predefinito è `yyyy-MM-dd`. I numeri di seguono il formato decimale di Java. Per tutti i formati di data possibili, vedere SimpleDateFormat Java. 
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Dato un formato di timestamp facoltativo, l'espressione converte una stringa in una data. 
* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``

Java SimpleDateFormat per vedere tutti i possibili formati. Se si omette il timestamp, il criterio predefinito `yyyy-[M]M-[d]d hh:mm:ss[.f...]` viene usato.
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Questa espressione converte il timestamp in UTC. 
* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``

È possibile passare un fuso orario facoltativo nel formato `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Il valore predefinito è il fuso orario corrente.
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione sostituisce un set di caratteri con un altro set di caratteri nella stringa. Caratteri hanno una sostituzione uno a uno.
* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Questa espressione rimuove una stringa di caratteri iniziali e finali. 
* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``

Se non si specifica il secondo parametro, la funzione rimuove gli spazi vuoti. In caso contrario, rimuove qualsiasi carattere che specifica il secondo parametro.

*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
Questa espressione restituisce sempre un `true` valore.  
* ``isDiscounted == true()``
* ``isDiscounted() == true``

Se è denominata colonna *true*, usare la funzione **syntax(true())**.
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Questa espressione corrisponde al tipo della colonna. 
* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``

Utilizzare questa funzione solo in espressioni di criteri: numero corrisponde a breve, integer, long, double, float o decimale, integrale corrisponde a breve, integer, corrispondenze frazionari, long double, float, decimal e corrispondenze date o timestamp tipo datetime.
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Questa espressione imposta una stringa in lettere maiuscole.
* ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la varianza di una colonna.
* ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la varianza di una colonna.
* ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la varianza della popolazione di una colonna.
* ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la varianza della popolazione di una colonna.
* ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Questa espressione Ottiene la varianza non distorta di una colonna.
* ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, questa espressione Ottiene la varianza non distorta di una colonna.
* ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Data una data, l'espressione Ottiene la settimana dell'anno.
* ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Questa espressione Usa l'operatore logico **xor** operatore. Questo operatore è lo stesso come il **^** operatore.
* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Data una data, questa espressione Ottiene il valore dell'anno.
* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare generatore di espressioni](concepts-data-flow-expression-builder.md).
