---
title: Funzioni di espressione nella funzionalità flusso di dati di mapping di Azure Data Factory
description: Informazioni sulle funzioni di espressione in mapping del flusso di dati.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 27d968aa5202fbeb38be9a2416514d2185c1d8b9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436749"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Espressioni di trasformazione dei dati nel flusso di dati di mapping 



## <a name="expression-functions"></a>Funzioni di espressione

In Data Factory utilizzare il linguaggio delle espressioni della funzionalità del flusso di dati di mapping per configurare le trasformazioni dei dati.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Valore assoluto di un numero.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore inverso del coseno * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Aggiunge una coppia di stringhe o numeri. Aggiunge una data a un numero di giorni. Aggiunge una durata a un timestamp. Aggiunge una matrice di tipo simile a un'altra. Uguale all'operatore + * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Aggiunge giorni a una data o a un timestamp. Uguale all'operatore + per date * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2<br/><br/>
Aggiungere mesi a una data o un timestamp. Facoltativamente, è possibile passare un fuso orario * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Operatore AND logico. Uguale a & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcola un valore seno inverso * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Calcola un valore tangente inverso * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Restituisce l'angolo, espresso in radianti, tra l'asse x positivo di un piano e il punto fornito dalle coordinate * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la media dei valori di una colonna * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio ottiene la media dei valori di una colonna * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Seleziona un valore di colonna in base al nome nel flusso. È possibile passare un nome di flusso facoltativo come secondo argomento. Se sono presenti più corrispondenze, viene restituita la prima corrispondenza. Se nessuna corrispondenza restituisce un valore NULL. Il valore restituito deve essere di tipo convertito da una delle funzioni di conversione dei tipi (TO_DATE, TO_STRING...).  I nomi di colonna noti in fase di progettazione devono essere risolti solo in base al nome. Gli input calcolati non sono supportati, ma è possibile usare le sostituzioni di parametro * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Seleziona un valore di colonna in base alla relativa posizione (basata su 1) nel flusso. Se la posizione è fuori limite, viene restituito un valore NULL. Il valore restituito deve essere di tipo convertito da una delle funzioni di conversione dei tipi (TO_DATE, TO_STRING...) Gli input calcolati non sono supportati, ma è possibile usare le sostituzioni di parametro * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
In base alle condizioni alternative, applica un valore o l'altro. Se il numero di input è pari, l'altro viene impostato come NULL per l'ultima condizione * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Calcolare la radice del cubo di un numero * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
Restituisce l'Integer più piccolo non inferiore al numero * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2<br/><br/>
Restituisce il primo valore not null da un set di input. Tutti gli input devono essere dello stesso tipo * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Confronta due valori dello stesso tipo. Restituisce un numero intero negativo se value1 < value2, 0 se value1 = = value2, valore positivo se value1 > value2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Concatena un numero variabile di stringhe. Uguale all'operatore + con le stringhe * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena un numero variabile di stringhe con un separatore. Il primo parametro è il separatore * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Restituisce true se qualsiasi elemento nella matrice fornita restituisce true nel predicato fornito. Contains prevede un riferimento a un elemento nella funzione predicato come #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcola un valore coseno * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
Calcola un coseno iperbolico di un valore * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il conteggio aggregato dei valori. Se si specifica la colonna o le colonne facoltative, i valori NULL vengono ignorati nel conteggio * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Ottiene il conteggio aggregato dei valori distinct di un set di colonne * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene il conteggio aggregato dei valori. Se la colonna facoltativa è specificata, ignora i valori NULL nel conteggio * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la covarianza della popolazione tra due colonne * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la covarianza della popolazione di due colonne * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la covarianza di esempio di due colonne * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la covarianza di esempio di due colonne * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Calcola l'hash CRC32 di un set di colonne di diversi tipi di dati primitivi, data una lunghezza in bit che può avere solo i valori 0(256), 224, 256, 384 e 512. Può essere usato per calcolare un'impronta digitale per una riga * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
La funzione CumeDist calcola la posizione di un valore rispetto a tutti i valori nella partizione. Il risultato è il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione diviso per il numero totale di righe nella partizione di finestra. Qualsiasi valore Tie nell'ordinamento restituirà la stessa posizione.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Ottiene la data corrente quando viene avviata l'esecuzione del processo. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il fuso orario locale viene usato come valore predefinito. Vedere SimpleDateFormat di Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Ottiene il timestamp corrente quando l'esecuzione del processo inizia con il fuso orario locale * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il timestamp corrente come ora UTC. Se si vuole che l'ora corrente venga interpretata in un fuso orario diverso dal fuso orario del cluster, è possibile passare un fuso orario facoltativo nel formato ' GMT ',' PST ',' UTC ',' America/Cayman '. Il valore predefinito è il fuso orario corrente. Vedere SimpleDateFormat di Java per i formati disponibili. Usare [SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) per convertire l'ora UTC in un fuso orario diverso usando fromUTC ().
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ottiene il giorno del mese in base a una data * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il giorno della settimana in base a una data specificata. 1-domenica, 2-lunedì..., 7-sabato * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il giorno dell'anno in base a una data * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Durata in millisecondi per il numero di giorni * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
Converte i radianti in gradi * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
Calcola la classificazione di un valore in un gruppo di valori. Come risultato si ha uno più il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione. I valori non produrranno spazi nella sequenza. Dense Rank funziona anche quando i dati non sono ordinati e cerca le modifiche nei valori * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Divide coppie di numeri. Uguale all'operatore/* ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se la stringa termina con la stringa fornita * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Operatore equals di confronto. Uguale a = = operatore * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Operatore equals di confronto senza distinzione tra maiuscole e minuscole. Uguale a < = > operator * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcola il fattoriale di un numero * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
Restituisce sempre un valore false. Utilizzare la sintassi della funzione (false ()) se è presente una colonna denominata ' false ' * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Filtra gli elementi dalla matrice che non soddisfano il predicato specificato. Il filtro prevede un riferimento a un elemento nella funzione predicato come #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene il primo valore di un gruppo di colonne. Se il secondo parametro ignoreNulls viene omesso, si presuppone che sia false * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Restituisce il valore integer più grande non superiore al numero * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2<br/><br/>
Esegue la conversione al timestamp dall'ora UTC. Facoltativamente, è possibile passare il fuso orario nel formato ' GMT ',' PST ',' UTC ',' America/Cayman '. Il valore predefinito è il SimpleDateFormat corrente di timezoneRefer Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operatore greater di confronto. Uguale a > operator * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Operatore greater than o equal di confronto. Uguale a > = operatore * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Restituisce il valore più elevato tra l'elenco di valori come input ignorando i valori null. Restituisce null se tutti gli input sono null * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Verifica la presenza di un valore di colonna in base al nome nel flusso. È possibile passare un nome di flusso facoltativo come secondo argomento.  I nomi di colonna noti in fase di progettazione devono essere risolti solo in base al nome. Gli input calcolati non sono supportati, ma è possibile usare le sostituzioni di parametro * ``hasColumn('parent')`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il valore dell'ora di un timestamp. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il fuso orario locale viene usato come valore predefinito. Vedere SimpleDateFormat di Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di ore * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
Esegue il mapping di ogni elemento della matrice a un nuovo elemento usando l'espressione fornita. Map prevede un riferimento a un elemento nella funzione Expression come #item e un riferimento all'indice dell'elemento come #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
In base alla condizione specifica, applica un valore o l'altro. Se other non è specificato, viene considerato NULL. Entrambi i valori devono essere compatibili (numeric, String...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Verifica se il valore non è NULL e restituisce else. restituisce l'alternativa. Esegue il test di tutti gli input finché non trova il primo valore non null * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Verifica se un elemento si trova nella matrice * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Converte la prima lettera di ogni parola in lettere maiuscole. Le parole vengono identificate come separate da spazi vuoti * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
Trova la posizione (in base 1) della sottostringa all'interno di una stringa. Se non viene trovato, viene restituito 0 * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Controlla se la riga è contrassegnata per l'eliminazione. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se la riga è contrassegnata come errore. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se la riga è contrassegnata per essere ignorata. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se la riga è contrassegnata per l'inserimento. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se per la riga viene trovata una corrispondenza. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se il valore è NULL * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se la riga è contrassegnata per l'aggiornamento. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se la riga è contrassegnata per l'inserimento. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene l'curtosi di una colonna * ``kurtosis(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene il curtosi di una colonna * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il valore del primo parametro valutato in n righe prima della riga corrente. Il secondo parametro è il numero di righe di cui eseguire la ricerca e il valore predefinito è 1. Se non sono presenti tutte le righe, viene restituito un valore null a meno che non venga specificato un valore predefinito * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene l'ultimo valore di un gruppo di colonne. Se il secondo parametro ignoreNulls viene omesso, si presuppone che sia false * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene l'ultima data del mese in base a una data * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il valore del primo parametro valutato in n righe dopo la riga corrente. Il secondo parametro è il numero di righe da ricercare e il valore predefinito è 1. Se non sono presenti tutte le righe, viene restituito un valore null a meno che non venga specificato un valore predefinito * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Operatore lesser than o equal di confronto. Uguale a < = operatore * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Estrae una sottostringa iniziale in corrispondenza dell'indice 1 con il numero di caratteri specificato. Uguale a substring (Str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Restituisce la lunghezza della stringa * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2<br/><br/>
Operatore less di confronto. Uguale a < operator * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Operatore lesser than o equal di confronto. Uguale a < = operatore * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene la distanza Levenshtein tra due stringhe * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
Il modello è una stringa che corrisponde letteralmente a una stringa. Le eccezioni sono i simboli speciali seguenti: _ corrisponde a un carattere qualsiasi nell'input (simile a. nelle espressioni regolari posix) % corrisponde a zero o più caratteri nell'input (simile a .* nelle espressioni regolari posix).
Il carattere di escape è ". Se un carattere di escape precede un simbolo speciale o un altro carattere di escape, per il carattere successivo viene stabilita una corrispondenza letterale. Non è possibile aggiungere caratteri di escape a qualsiasi altro carattere.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Trova la posizione (in base 1) della sottostringa all'interno di una stringa a partire da una determinata posizione. Se si omette la posizione, viene considerato l'inizio della stringa. Se non viene trovato, viene restituito 0 * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Calcola il valore del logaritmo. È possibile specificare una base facoltativa ELSE un numero di Eulero se usato * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Calcola il valore di log in base a 10 @no__t base-0 @ no__t-1 @ no__t-2<br/><br/>
Caratteri minuscoli stringa * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
Riempie a sinistra la stringa in base al riempimento specificato fino a raggiungere una determinata lunghezza. Se la stringa è uguale o maggiore della lunghezza, viene tagliata fino alla lunghezza * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2'' LPAD (' Dumbo ', 8,' < >')->' < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
Rimuove i caratteri iniziali dal lato sinistro di una stringa. Se il secondo parametro non è specificato, rimuove lo spazio vuoto. In caso contrario, viene ritagliato qualsiasi carattere specificato nel secondo parametro * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Esegue il mapping di ogni elemento della matrice a un nuovo elemento usando l'espressione fornita. Map prevede un riferimento a un elemento nella funzione Expression come #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene il valore massimo di una colonna * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene il valore massimo di una colonna * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Calcola il digest MD5 di un set di colonne di diversi tipi di dati primitivi e restituisce una stringa esadecimale a 32 caratteri. Può essere usato per calcolare un'impronta digitale per una riga * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la media dei valori di una colonna. Uguale a AVG * ``mean(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la media dei valori di una colonna. Uguale a avgIf * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il valore dei millisecondi di una data. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il fuso orario locale viene usato come valore predefinito. Vedere SimpleDateFormat di Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di millisecondi * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il valore minimo di una colonna * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
In base a un criterio, ottiene il valore minimo di una colonna * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Sottrae numeri. Sottrarre un numero di giorni da una data. Sottrarre durata da un timestamp. Sottrarre due timestamp per ottenere la differenza in millisecondi. Uguale all'operatore-* ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Ottiene il valore dei minuti di un timestamp. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il fuso orario locale viene usato come valore predefinito. Vedere SimpleDateFormat di Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di minuti * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
Calcola il modulo di coppie di numeri. Uguale all'operatore% * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene il valore del mese di una data o di un timestamp * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene il numero di mesi tra due date. È possibile arrotondare il calcolo. È possibile passare un fuso orario facoltativo nel formato ' GMT ',' PST ',' UTC ',' America/Cayman '. Il fuso orario locale viene usato come valore predefinito. Vedere SimpleDateFormat di Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Moltiplica coppie di numeri. Uguale all'operatore * * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
La funzione NTile divide le righe per ogni partizione di finestra in `n` bucket compresi tra 1 e un valore massimo `n`. I valori dei bucket si differenzieranno per un massimo di 1. Se il numero di righe nella partizione non può essere diviso equamente in base al numero di bucket, i valori rimanenti verranno distribuiti uno per bucket, a partire dal primo bucket. La funzione NTile è utile per il calcolo di tertiles, quartili, decili e altre statistiche di riepilogo comuni. La funzione calcola due variabili durante l'inizializzazione: alla dimensione di un bucket regolare verrà aggiunta una riga aggiuntiva. Entrambe le variabili si basano sulle dimensioni della partizione corrente. Durante il processo di calcolo, la funzione tiene traccia del numero di righe corrente, del numero di bucket corrente e del numero di righe in corrispondenza del quale il bucket cambierà (bucketThreshold). Quando il numero di righe corrente raggiunge la soglia del bucket, il valore del bucket viene incrementato di uno e la soglia viene aumentata in base alla dimensione del bucket (più un valore aggiuntivo, in caso di riempimento del bucket corrente).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Nega un numero. Converte i numeri positivi in negativo e viceversa * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
Restituisce la sequenza univoca successiva. Il numero è consecutivo solo all'interno di una partizione ed è preceduto da partitionId * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2<br/><br/>
Normalizzare il valore della stringa per separare i caratteri Unicode accentati * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
Operatore di negazione logica * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Operatore not equals di confronto. Uguale a! = operatore * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Restituisce un valore NULL. Usare la sintassi della funzione (null()) se è presente una colonna denominata 'null'. Qualsiasi operazione utilizzata da comporterà un valore NULL * ``isNull('dumbo' + null) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Operatore OR logico. Uguale a | | * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcola il modulo positivo di coppie di numeri.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Restituisce l'ID di partizione corrente in cui si trova la riga di input * ``partitionId()`` @ no__t-1 @ no__t-2<br/><br/>
Eleva un numero alla potenza di un altro * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Calcola la classificazione di un valore in un gruppo di valori. Come risultato si ha uno più il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione. I valori produrranno spazi nella sequenza. Rango funziona anche quando i dati non sono ordinati e cerca le modifiche nei valori * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Accumula gli elementi in una matrice. Per reduce si prevede un riferimento a un accumulatore e un elemento nella prima funzione di espressione come #acc e #item e si prevede che il valore risultante sia #result da utilizzare nella seconda funzione di espressione * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Estrae una sottostringa corrispondente per un modello di espressione regolare specificato. L'ultimo parametro identifica il gruppo di corrispondenza e, se omesso, viene usato il valore predefinito 1. Usare ' <regex>' (virgolette indietro) per trovare la corrispondenza con una stringa senza eseguire l'escape * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Verifica se la stringa corrisponde al modello di espressione regolare specificato. Usare ' <regex>' (virgolette indietro) per trovare la corrispondenza con una stringa senza eseguire l'escape * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Sostituire tutte le occorrenze di un modello Regex con un'altra sottostringa nella stringa specificata usare ' <regex>' (virgolette indietro) per trovare la corrispondenza con una stringa senza eseguire l'escape * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Suddivide una stringa in base a un delimitatore basato su Regex e restituisce una matrice di stringhe * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Sostituire tutte le occorrenze di una sottostringa con un'altra sottostringa nella stringa specificata. Se l'ultimo parametro viene omesso, il valore predefinito è una stringa vuota * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Inverte una stringa * ``reverse('gunchus') -> 'suhcnug'`` @ no__t-1 @ no__t-2<br/><br/>
Estrae una sottostringa finale con il numero di caratteri specificato. Uguale a substring (Str, LENGTH (STR)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controlla se la stringa corrisponde al modello Regex specificato * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Arrotonda un numero in base a una scala facoltativa e una modalità di arrotondamento facoltativa. Se la scala viene omessa, il valore predefinito è 0.  Se la modalità viene omessa, il valore predefinito è ROUND_HALF_UP (5). I valori per l'arrotondamento includono 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Assegna una numerazione di righe sequenziali per le righe in una finestra che inizia con 1 * ``rowNumber()`` @ no__t-1 @ no__t-2<br/><br/>
Riempie a destra la stringa in base al riempimento specificato fino a raggiungere una determinata lunghezza. Se la stringa è uguale o maggiore della lunghezza, viene tagliata fino alla lunghezza * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
Rimuove i caratteri finali dal lato destro di una stringa. Se il secondo parametro non è specificato, rimuove lo spazio vuoto. In caso contrario, viene ritagliato qualsiasi carattere specificato nel secondo parametro * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene il valore dei secondi di una data. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il fuso orario locale viene usato come valore predefinito. Vedere SimpleDateFormat di Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di secondi * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
Calcola il digest SHA-1 di un set di colonne di diversi tipi di dati primitivi e restituisce una stringa esadecimale a 40 caratteri. Può essere usato per calcolare un'impronta digitale per una riga * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
Calcola il digest SHA-2 del set di colonne di tipi di dati primitivi variabili in base a una lunghezza di bit che può essere solo di valori 0 (256), 224, 256, 384, 512. Può essere usato per calcolare un'impronta digitale per una riga * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
Calcola un valore seno * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2<br/><br/>
Calcola un valore seno iperbolico * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene l'inclinazione di una colonna * ``skewness(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene l'inclinazione di una colonna * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Estrae un subset di una matrice da una posizione. La posizione è in base 1. Se la lunghezza viene omessa, il valore predefinito è fine della stringa * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Ordina la matrice utilizzando la funzione di predicato fornita. Sort prevede un riferimento a due elementi consecutivi nella funzione Expression come #item1 e #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ottiene il codice SOUNDEX per la stringa * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
Suddivide una stringa in base a un delimitatore e restituisce una matrice di stringhe * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
Calcola la radice quadrata di un numero * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
Verifica se la stringa inizia con la stringa fornita * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la deviazione standard di una colonna * ``stdDev(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la deviazione standard di una colonna * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la deviazione standard della popolazione di una colonna * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la deviazione standard della popolazione di una colonna * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la deviazione standard di esempio di una colonna * ``stddevSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la deviazione standard di esempio di una colonna * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Sottrarre i mesi da una data o un timestamp. Uguale all'operatore-per date * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2<br/><br/>
Sottrarre i mesi da una data o un timestamp * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
Estrae una sottostringa di una determinata lunghezza da una posizione. La posizione è in base 1. Se la lunghezza viene omessa, il valore predefinito è fine della stringa * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Ottiene la somma aggregata di una colonna numerica * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la somma aggregata dei valori distinct di una colonna numerica * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
In base a criteri, ottiene la somma aggregata di una colonna numerica. La condizione può essere basata su qualsiasi colonna * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
In base a criteri, ottiene la somma aggregata di una colonna numerica. La condizione può essere basata su qualsiasi colonna * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcola un valore tangente * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Calcola un valore di tangente iperbolico * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Converte qualsiasi tipo numerico/Data/timestamp/stringa in rappresentazione binaria * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
Converte un valore di (' T',' true ',' y ',' Yes ',' 1') in true e (' f ',' false ',' n'',' No ',' 0') in false e NULL per qualsiasi altro valore * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte la stringa di data di input in date utilizzando un formato di data di input facoltativo. Vedere SimpleDateFormat di Java per i formati disponibili. Se il formato della data di input viene omesso, il formato predefinito è aaaa-[M] M-[d] d. Formati accettati: [aaaa, aaaa-[M] M, aaaa-[M] M-[d] d, aaaa-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Converte un valore numerico o stringa in un valore decimale. Se precisione e scalabilità non sono specificate, per la conversione viene usato il valore predefinito (10,2). Per la conversione è possibile usare un formato decimale Java facoltativo. Un formato facoltativo per le impostazioni locali sotto forma di linguaggio BCP47, ad esempio en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Converte un valore numerico o stringa in un valore double. Per la conversione è possibile usare un formato decimale Java facoltativo. Un formato facoltativo per le impostazioni locali sotto forma di linguaggio BCP47, ad esempio en-US, de, zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Converte un valore numerico o stringa in un valore float. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca qualsiasi valore Double * ``toFloat(123.45) -> 123.45f`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte qualsiasi valore numerico o stringa in un valore integer. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca qualsiasi valore Long, float, Double * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte un valore numerico o stringa in un valore long. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca gli eventuali valori float, Double * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte un valore numerico o stringa in un valore short. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca qualsiasi Integer, Long, float, Double * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte un tipo di dati primitivo in una stringa. Per numeri e date è possibile specificare un formato. Se non è specificato, viene selezionato il valore predefinito del sistema. Per i numeri viene usato il formato decimale Java. Vedere SimpleDateFormat Java per tutti i formati di data possibili; il formato predefinito è AAAA-MM-GG * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Converte una stringa in un timestamp in base a un formato timestamp facoltativo. Vedere Java SimpleDateFormat per tutti i formati possibili. Se il timestamp viene omesso, viene utilizzato il modello predefinito. aaaa-[M] M-[d] d HH: mm: SS [. f...]. È possibile passare un fuso orario facoltativo nel formato ' GMT ',' PST ',' UTC ',' America/Cayman '. Timestamp supporta la precisione fino al millisecondo con il valore di SimpleDateFormat di 999Refer Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Converte il timestamp in UTC. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il valore predefinito è il SimpleDateFormat corrente di timezoneRefer Java per i formati disponibili. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Sostituisce un set di caratteri con un altro set di caratteri nella stringa. I caratteri hanno una sostituzione da 1 a 1 * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Rimuove i caratteri iniziali e finali di una stringa. Se il secondo parametro non è specificato, rimuove lo spazio vuoto. In caso contrario, viene ritagliato qualsiasi carattere specificato nel secondo parametro * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Restituisce sempre un valore true. Usare la sintassi della funzione (true ()) se è presente una colonna denominata ' true ' * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Trova corrispondenze tra tipi di colonne. Può essere utilizzato solo nelle espressioni di modello. Number corrisponde a short, Integer, Long, Double, float o Decimal, le corrispondenze integrali Short, Integer, Long, frazionarie corrisponde a Double, float, Decimal e DateTime corrisponde alla data o al tipo di timestamp * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Maiuscole una stringa * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
Restituisce l'UUID generato * ``uuid()`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la varianza di una colonna * ``variance(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la varianza di una colonna * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la varianza della popolazione di una colonna * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la varianza della popolazione di una colonna * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la varianza non distorta di una colonna * ``varianceSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
In base a un criterio, ottiene la varianza non distorta di una colonna * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Ottiene la settimana dell'anno specificata una data * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Durata in millisecondi per il numero di settimane * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
Operatore XOR logico. Uguale a ^ operator * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Ottiene il valore dell'anno di una data * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare il generatore di espressioni](concepts-data-flow-expression-builder.md).
