---
title: Applicare operazioni matematiche
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Applica operazione matematica in Azure Machine Learning per applicare un'operazione matematica ai valori di colonna in una pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456744"
---
# <a name="apply-math-operation"></a>Applicare operazioni matematiche

Questo articolo descrive un modulo della finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare l'opzione Applica operazione matematica per creare calcoli applicati alle colonne numeriche nel set di dati di input. 

Le operazioni matematiche supportate includono funzioni aritmetiche comuni come la moltiplicazione e la divisione, funzioni trigonometriche, una varietà di funzioni di arrotondamento e funzioni speciali utilizzate nell'analisi scientifica dei dati, ad esempio le funzioni gamma ed error.  

Dopo aver definito un'operazione ed eseguito la pipeline, i valori vengono aggiunti al set di dati. A seconda di come si configura il modulo, è possibile:

+ Aggiungere i risultati al set di dati. Ciò è particolarmente utile quando si verifica il risultato dell'operazione.
+ Sostituire i valori delle colonne con i nuovi valori calcolati.
+ Generare una nuova colonna per i risultati e non visualizzare i dati originali. 

Cerca l'operazione che ti serve in queste categorie:  

- [Base](#basic-math-operations)  
  
     Le funzioni della categoria **Basic** possono essere utilizzate per modificare un singolo valore o una singola colonna di valori. Ad esempio, è possibile ottenere il valore assoluto di tutti i numeri in una colonna o calcolare la radice quadrata di ogni valore in una colonna.  
  
-   [Confronta](#comparison-operations)  
  
      Le funzioni della categoria **Confronta** vengono tutte utilizzate per il confronto: è possibile eseguire un confronto coppie dei valori in due colonne oppure confrontare ogni valore di una colonna con una costante specificata. Ad esempio, è possibile confrontare le colonne per determinare se i valori sono gli stessi in due set di dati. In alternativa, è possibile utilizzare una costante, ad esempio un valore massimo consentito, per trovare gli outlier in una colonna numerica.  
  
-   [Operazioni](#arithmetic-operations)  
  
     La categoria **Operazioni** include le funzioni matematiche di base: addizione, sottrazione, moltiplicazione e divisione. È possibile utilizzare colonne o costanti. Ad esempio, è possibile aggiungere il valore nella colonna A al valore nella colonna B.For example, you might add the value in Column A to the value in Column B. In alternativa, è possibile sottrarre una costante, ad esempio una media calcolata in precedenza, da ogni valore nella colonna A.  
  
-   [Rounding](#rounding-operations)  
  
     La categoria **Arrotondamento** include una serie di funzioni per l'esecuzione di operazioni quali arrotondamento, soffitto, pavimento e troncamento a vari livelli di precisione. È possibile specificare il livello di precisione sia per i numeri decimali che per i numeri interi.  
  
-   [Speciale](#special-math-functions)  
  
     La categoria **Speciale** include funzioni matematiche che vengono utilizzate in modo particolare nell'analisi scientifica dei dati, ad esempio gli integrali ellittici e la funzione di errore gaussiana.  
  
-   [Funzioni trigonometriche](#trigonometric-functions)  
  
     La categoria **Trigonometrica** include tutte le funzioni trigonometriche standard. Ad esempio, è possibile convertire i radianti in gradi o calcolare funzioni quali la tangente in radianti o gradi.
     Queste funzioni sono unirie, ovvero accettano una singola colonna di valori come input, applicano la funzione trigonometrica e restituiscono una colonna di valori come risultato.  Pertanto è necessario assicurarsi che la colonna di input sia il tipo appropriato e contenga il tipo corretto di valori per l'operazione specificata.   

## <a name="how-to-configure-apply-math-operation"></a>Come configurare L'applicazione dell'operazione matematica  

Il modulo **Applica operazione matematica** richiede un set di dati che contiene almeno una colonna contenente solo numeri. I numeri possono essere discreti o continui, ma devono essere di un tipo di dati numerico, non di una stringa.

È possibile applicare la stessa operazione a più colonne numeriche, ma tutte le colonne devono trovarsi nello stesso set di dati. 

Ogni istanza di questo modulo può eseguire un solo tipo di operazione alla volta. Per eseguire operazioni matematiche complesse, potrebbe essere necessario concatenare diverse istanze del modulo **Applica operazione matematica.**  
  
1.  Aggiungere il modulo **Applica operazione matematica** alla pipeline.

1. Connettere un set di dati che contiene almeno una colonna numerica.  

1.  Selezionare una o più colonne di origine in cui eseguire il calcolo.   
  
    - Qualsiasi colonna scelta deve essere un tipo di dati numerico. 
    - L'intervallo di dati deve essere valido per l'operazione matematica selezionata. In caso contrario, può verificarsi un errore o un risultato NaN (non un numero). Ad esempio, Ln(-1.0) è un'operazione non `NaN`valida e restituisce un valore pari a .
  
1.  Fare clic su **Categoria** per selezionare il **tipo** di operazione matematica da eseguire.
    
1. Scegliere un'operazione specifica dall'elenco in tale categoria.
  
1.  Impostare parametri aggiuntivi richiesti per ogni tipo di operazione.  
  
1.  Utilizzare l'opzione **Modalità** di output per indicare come si desidera generare l'operazione matematica: 

    - **Append**. Tutte le colonne utilizzate come input sono incluse nel set di dati di output, più una colonna aggiuntiva viene aggiunta che contiene i risultati dell'operazione matematica.
    - **Inplace**. I valori nelle colonne utilizzate come input vengono sostituiti con i nuovi valori calcolati. 
    - **ResultOnly**. Viene restituita una singola colonna contenente i risultati dell'operazione matematica.
  
1.  Inviare la pipeline.  
  
## <a name="results"></a>Risultati

Se si generano i risultati utilizzando le opzioni **Append** o **ResultOnly,** le intestazioni di colonna del set di dati restituito indicano l'operazione e le colonne utilizzate. Ad esempio, se si confrontano due colonne utilizzando l'operatore **Equals,** i risultati saranno simili ai seguente:  
  
-   **Equals(Col2_Col1)**, che indica che Col2 è stato testato rispetto a Col1.  
-   **Uguale a (Col2_ -10)**, a indicare che è stata confrontata la colonna 2 con la costante 10.  

Anche se si utilizza l'opzione **Inplace,** i dati di origine non vengono eliminati o modificati; la colonna nel set di dati originale è ancora disponibile nella finestra di progettazione. Per visualizzare i dati originali, è possibile collegare il modulo [Aggiungi colonne](add-columns.md) e unirlo all'output di Applica **operazione matematica**.  
    
## <a name="basic-math-operations"></a>Operazioni matematiche di base 

Le funzioni nella categoria **Basic** in genere accettano un singolo valore da una colonna, eseguono l'operazione predefinita e restituiscono un singolo valore. Per alcune funzioni, è possibile specificare una costante o un set di colonne come secondo argomento.  
  
 Azure Machine Learning supporta le funzioni seguenti nella categoria **Basic:Azure** Machine Learning supports the following functions in the Basic category:  

### <a name="abs"></a>Abs

Restituisce il valore assoluto delle colonne selezionate.  
  
### <a name="atan2"></a>Atan2

Restituisce una tangente inversa a quattro quadranti.  

Selezionare le colonne che contengono le coordinate del punto. Per il secondo argomento, che corrisponde alla coordinata x, è anche possibile specificare una costante.  

Corrisponde alla funzione ATAN2 in Matlab.  

### <a name="conj"></a>Conj

Restituisce il coniugato per i valori nella colonna selezionata.  

### <a name="cuberoot"></a>CubeRoot

Calcola la radice del cubo per i valori nella colonna selezionata.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Calcola il doppio fattoriale per i valori nella colonna selezionata. Il doppio fattoriale è un'estensione della normale funzione fattoriale, ed è indicato come x!!.  

### <a name="eps"></a>Eps

Restituisce la dimensione del gap tra il valore corrente e il successivo numero a precisione doppia più alto. Corrisponde alla funzione EPS in Matlab.  
  
### <a name="exp"></a>Exp

Restituisce e elevato alla potenza del valore nella colonna selezionata. Equivale alla funzione EXP di Excel.  

### <a name="exp2"></a>Exp2

Restituisce l'esponenziale in base 2 degli argomenti, risolvendo per y , x e 2<sup>t,</sup> dove t è una colonna di valori contenenti esponenti.  

In **Set**di colonne selezionare la colonna che contiene i valori dell'esponente t.

Per **Exp2** è possibile specificare un secondo argomento x, che può essere una costante o un'altra colonna di valori. In **Second argument type**indica se si fornirà il moltiplicatore x come costante o come valore in una colonna.  

Ad esempio, se si seleziona {0,1,2,3,4,5} una colonna con i valori sia per il moltiplicatore che per l'esponente, la funzione restituisce 0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Restituisce l'esponente negativo per i valori nella colonna selezionata.  

### <a name="factorial"></a>Fattoriale
Restituisce il fattoriale per i valori nella colonna selezionata.  

### <a name="hypotenuse"></a>Hypotenuse
Calcola l'ipotenusa per un triangolo in cui la lunghezza di un lato è specificata come colonna di valori e la lunghezza del secondo lato viene specificata come costante o come due colonne.  

### <a name="ln"></a>Ri

Restituisce il logaritmo naturale per i valori nella colonna selezionata.  

### <a name="lnplus1"></a>LnPlus1

Restituisce il logaritmo naturale più uno per i valori nella colonna selezionata.  

### <a name="log"></a>File di log

Restituisce il logaritmo dei valori nella colonna selezionata secondo la base specificata.  

È possibile specificare la base (il secondo argomento) come costante o selezionando un'altra colonna di valori.  

### <a name="log10"></a>Log10

Restituisce i valori del logaritmo in base 10 per la colonna selezionata.  

### <a name="log2"></a>Log2

Restituisce i valori del logaritmo in base 2 per la colonna selezionata.  

### <a name="nthroot"></a>NthRoot
Restituisce l'ennesima radice del valore, utilizzando una n specificata.  

Selezionare le colonne per le quali si desidera calcolare la radice, utilizzando l'opzione **ColumnSet.**  

In **Second argument type**selezionare un'altra colonna contenente la radice o specificare una costante da utilizzare come radice.  

Se il secondo argomento è una colonna, ogni valore nella colonna viene utilizzato come valore di n per la riga corrispondente. Se il secondo argomento è una costante, digitare il valore di n nella casella di testo **Secondo argomento.**
### <a name="pow"></a>Pow

Calcola X elevato alla potenza di Y per ognuno dei valori nella colonna selezionata.  

Selezionare innanzitutto le colonne che contengono **base**, che deve essere un float, utilizzando l'opzione **ColumnSet** .  

In **Second argument type**selezionare la colonna che contiene l'esponente o specificare una costante da utilizzare come esponente.  

Se il secondo argomento è una colonna, ogni valore della colonna viene usato come esponente per la riga corrispondente. Se il secondo argomento è una costante, digitare il valore per l'esponente nella casella di testo **Secondo argomento.**  

### <a name="sqrt"></a>Sqrt

Restituisce la radice quadrata dei valori nella colonna selezionata.  

### <a name="sqrtpi"></a>SqrtPi

Per ogni valore nella colonna selezionata, moltiplica il valore per pi greco e quindi restituisce la radice quadrata del risultato.  

### <a name="square"></a>Square

Seleziona i valori nella colonna selezionata.  

## <a name="comparison-operations"></a>Operazioni di confronto  

Usare le funzioni di confronto in Progettazione Azure Machine Learning ogni volta che è necessario testare due set di valori uno contro l'altro. Ad esempio, in una pipeline potrebbe essere necessario eseguire queste operazioni di confronto:For example, in a pipeline you might need to do these comparison operations:  

- Valutare un modello di colonna di punteggi di probabilità rispetto a un valore di soglia.
- Determinare se due set di risultati sono uguali. Per ogni riga diversa, aggiungere un flag FALSE che può essere utilizzato per un'ulteriore elaborazione o filtro.  

### <a name="equalto"></a>EqualTo

Restituisce True se i valori sono uguali.  

### <a name="greaterthan"></a>GreaterThan

Restituisce True se i valori nel **set di** colonne sono maggiori della costante specificata o maggiori dei valori corrispondenti nella colonna di confronto.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Restituisce True se i valori nel **set di** colonne sono maggiori o uguali alla costante specificata oppure maggiori o uguali ai valori corrispondenti nella colonna di confronto.  

### <a name="lessthan"></a>LessThan

Restituisce True se i valori nel **set di** colonne sono minori della costante specificata o minori dei valori corrispondenti nella colonna di confronto.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Restituisce True se i valori nel **set di** colonne sono minori o uguali alla costante specificata oppure minori o uguali ai valori corrispondenti nella colonna di confronto.  

### <a name="notequalto"></a>NotEqualTo

Restituisce True se i valori in **Column set** non sono uguali alla costante o alla colonna di confronto, altrimenti restituisce False.  

### <a name="pairmax"></a>PairMax

Restituisce il valore maggiore: il valore in **Column set** oppure il valore nella costante o nella colonna di confronto.  

### <a name="pairmin"></a>PairMin

Restituisce il valore minore, ovvero il valore in **Set** di colonne o il valore nella colonna costante o di confronto  
  
##  <a name="arithmetic-operations"></a>Operazioni aritmetiche   

Include le operazioni aritmetiche di base: addizione e sottrazione, divisione e moltiplicazione.  Poiché la maggior parte delle operazioni sono binarie, richiedono due numeri, è innanzitutto possibile scegliere l'operazione e quindi scegliere la colonna o i numeri da utilizzare nel primo e nel secondo argomento.

L'ordine in cui si scelgono le colonne per la divisione e la sottrazione potrebbe sembrare controintuitivo; Tuttavia, per semplificare la comprensione dei risultati, l'intestazione di colonna fornisce il nome dell'operazione e l'ordine in cui sono state utilizzate le colonne.

Operazione|Num1|Num2|Colonna dei risultati|Valore del risultato|
----|----|----|----|----
|Addizione|1|5|Aggiungi(Num2_Num1)| 4|
|Moltiplicazione|1|5|Multiplo (Num2_Num1)|5|
|Sottrazione|1|5|Sottrai (Num2_Num1)|4|
|Sottrazione|0|1|Sottrai (Num2_Num1)|0|
|Divisione|1|5|Dividi(Num2_Num1)|5|
|Divisione|0|1|Dividi(Num2_Num1)|Infinity|

### <a name="add"></a>Add

Specificare le colonne di origine utilizzando **Set di colonne**, quindi aggiungere a tali valori un numero specificato in Second **argument**.  

Per aggiungere i valori in due colonne, scegliere una o più colonne utilizzando **Set**di colonne , quindi scegliere una seconda colonna utilizzando **Secondo argomento**.  

### <a name="divide"></a>Divisione

Divide i valori in **Column impostati** per una costante o per i valori di colonna definiti in **Second argument**.  In altre parole, si sceglie prima il divisore e poi il dividendo. Il valore di output è il quoziente.

### <a name="multiply"></a>Moltiplicazione

Moltiplica i valori di **Column set** per la costante o i valori di colonna specificati.  

### <a name="subtract"></a>Sottrazione

Specificare la colonna di valori su cui operare (il *minuto*), scegliendo una colonna diversa, utilizzando l'opzione **Set** di colonne. Specificare quindi il numero da sottrarre (il *subtrahend*) utilizzando l'elenco a discesa **Secondo argomento.** È possibile scegliere una costante o una colonna di valori.

##  <a name="rounding-operations"></a>Operazioni di arrotondamento 

La finestra di progettazione di Azure Machine Learning supporta un'ampia gamma di operazioni di arrotondamento. Per molte operazioni, è necessario specificare la quantità di precisione da utilizzare durante l'arrotondamento. È possibile utilizzare un livello di precisione statica, specificato come costante, oppure è possibile applicare un valore di precisione dinamica ottenuto da una colonna di valori.  

- Se si utilizza una costante, **impostare Tipo** di precisione su **Costante** e quindi digitare il numero di cifre come numero intero nella casella di testo **Precisione costante.** Se si digita un valore non intero, il modulo non genera un errore, ma i risultati possono essere imprevisti.  

- Per utilizzare un valore di precisione diverso per ogni riga del dataset, impostare **Tipo di precisione** su **ColumnSet**, quindi scegliere la colonna contenente i valori di precisione appropriati.  

### <a name="ceiling"></a>Ceiling

Restituisce il limite massimo per i valori in **Column set**.  

### <a name="ceilingpower2"></a>CeilingPower2

Restituisce il limite massimo al quadrato per i valori in **Column set**.  

### <a name="floor"></a>Piano

Restituisce il limite minimo per i valori in **Column set** con la precisione specificata.  

### <a name="mod"></a>Mod

Restituisce la parte frazionaria dei valori in **Column set** con la precisione specificata.  

### <a name="quotient"></a>Quoziente

Restituisce la parte frazionaria dei valori in **Column set** con la precisione specificata.  

### <a name="remainder"></a>Resto

Restituisce il resto per i valori in **Column set**.  

### <a name="rounddigits"></a>RoundDigits

Restituisce i valori in **Column set**, arrotondati per la regola 4/5 al numero di cifre specificato.  

### <a name="rounddown"></a>RoundDown

Restituisce i valori in **Column set**, arrotondati per difetto al numero di cifre specificato.  

### <a name="roundup"></a>RoundUp

Restituisce i valori in **Column set**, arrotondati per eccesso al numero di cifre specificato.  

### <a name="toeven"></a>ToEven

Restituisce i valori in **Column set**, arrotondati al numero pari intero più vicino.  

### <a name="toodd"></a>ToOdd

Restituisce i valori in **Column set**, arrotondati al numero dispari intero più vicino.  

### <a name="truncate"></a>Truncate

Tronca i valori in **Column set** rimuovendo tutte le cifre per difetto non consentite dalla precisione specificata.  
  
## <a name="special-math-functions"></a>Funzioni matematiche speciali

Questa categoria include funzioni matematiche specializzate spesso utilizzate nell'analisi scientifica dei dati. Se non diversamente specificato, la funzione è unaria e restituisce il calcolo specificato per ogni valore nella colonna o nelle colonne selezionate.  

### <a name="beta"></a>Beta

Restituisce il valore della funzione beta di Eulero.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Restituisce il valore dell'integrale ellittico incompleto.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Restituisce il valore dell'integrale ellittico completo (K).  

### <a name="erf"></a>Erf

Restituisce il valore della funzione di errore.  

La funzione di errore (denominata anche funzione di errore di Gauss) è una funzione speciale di forma sigmoide usata nella probabilità per descrivere la diffusione.  

### <a name="erfc"></a>Erfc

Restituisce il valore della funzione di errore complementare.  

Erfc è definito come 1 – erf(x).  

### <a name="erfscaled"></a>ErfScaled

Restituisce il valore della funzione di errore in scala.  

La versione in scala della funzione di errore consente di evitare l'underflow aritmetico.  

### <a name="erfinverse"></a>ErfInverse

Restituisce il valore della funzione inversa di erf.  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Restituisce il valore dell'integrale esponenziale Ei.  

### <a name="gamma"></a>Gamma

Restituisce il valore della funzione gamma.  

### <a name="gammaln"></a>GammaLn

Restituisce il logaritmo naturale della funzione gamma.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Restituisce il valore della funzione gamma incompleta regolarizzata.  

Questa funzione accetta un secondo argomento, che può essere fornito come costante o colonna di valori.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Restituisce il valore della funzione gamma incompleta regolarizzata inversa.  

Questa funzione accetta un secondo argomento, che può essere fornito come costante o colonna di valori.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Restituisce il valore della funzione gamma incompleta regolarizzata.  

Questa funzione accetta un secondo argomento, che può essere fornito come costante o colonna di valori.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Restituisce il valore della funzione gamma incompleta regolarizzata generalizzata inversa.

Questa funzione accetta un secondo argomento, che può essere fornito come costante o colonna di valori.  

### <a name="polygamma"></a>PolyGamma

Restituisce il valore della funzione poligamma.  

Questa funzione accetta un secondo argomento, che può essere fornito come costante o colonna di valori.

##  <a name="trigonometric-functions"></a>Funzioni trigonometriche 

Questa categoria iInclude la maggior parte delle importanti funzioni trigonometriche e inverse. Tutte le funzioni trigonometriche sono unarie e non richiedono argomenti aggiuntivi.  

### <a name="acos"></a>Acos

Calcola l'arcocoseno per i valori della colonna.  

### <a name="acosdegree"></a>AcosDegree

Calcola l'arcocoseno dei valori della colonna, in gradi.  

### <a name="acosh"></a>Acosh

Calcola l'arcocoseno iperbolico dei valori della colonna.  

### <a name="acot"></a>Acot

Calcola l'arcocotangente dei valori della colonna.  

### <a name="acotdegrees"></a>AcotDegrees

Calcola l'arcocotangente dei valori della colonna, in gradi.  

### <a name="acoth"></a>Acoth

Calcola l'arcocotangente iperbolica dei valori della colonna.  

### <a name="acsc"></a>Acsc

Calcola l'arcocosecante dei valori della colonna.  

### <a name="acscdegrees"></a>AcscDegrees

Calcola l'arcocosecante dei valori della colonna, in gradi.  
### <a name="asec"></a>Asec

Calcola l'arcosecante dei valori della colonna.  

### <a name="asecdegrees"></a>AsecDegrees

Calcola l'arcosecante dei valori della colonna, in gradi.  

### <a name="asech"></a>Asech

Calcola l'arcosecante iperbolico dei valori della colonna.  

### <a name="asin"></a>Asin

Calcola l'arcoseno dei valori della colonna.  

### <a name="asindegrees"></a>AsinDegrees

Calcola l'arcoseno dei valori della colonna, in gradi.  

### <a name="asinh"></a>Asinh

Calcola l'arcosina iperbolica per i valori della colonna.  

### <a name="atan"></a>Atan

Calcola l'arcotangente dei valori della colonna.  

### <a name="atandegrees"></a>AtanDegrees

Calcola l'arcotangente dei valori della colonna, in gradi.  

### <a name="atanh"></a>Atanh

Calcola l'arcotangente iperbolica dei valori della colonna.  

### <a name="cos"></a>Cos

Calcola il coseno dei valori della colonna.  

### <a name="cosdegrees"></a>CosDegrees

Calcola il coseno per i valori della colonna, in gradi.  

### <a name="cosh"></a>Cosh

Calcola il coseno iperbolico per i valori della colonna.  

### <a name="cot"></a>Cot

Calcola la cotangente per i valori della colonna.  

### <a name="cotdegrees"></a>CotDegrees

Calcola la cotangente per i valori della colonna, in gradi.  

### <a name="coth"></a>Coth
Calcola la cotangente iperbolica per i valori della colonna.  

### <a name="csc"></a>Csc

Calcola la cosecante per i valori della colonna.  

### <a name="cscdegrees"></a>CscDegrees

Calcola la cosecante per i valori della colonna, in gradi.  

### <a name="csch"></a>Csch

Calcola la cosecante iperbolica per i valori della colonna.  

### <a name="degreestoradians"></a>DegreesToRadians

Converte i gradi in radianti.  

### <a name="sec"></a>Sec

Calcola la secante dei valori della colonna.  

### <a name="asecdegrees"></a>aSecDegrees (Grado)

Calcola la secante per i valori della colonna, in gradi.  

### <a name="asech"></a>aSech

Calcola la secante iperbolica dei valori della colonna.  

### <a name="sign"></a>Sign

Restituisce il segno dei valori della colonna.  

### <a name="sin"></a>Sin

Calcola il sine dei valori della colonna.  

### <a name="sinc"></a>Sinc

Calcola il valore sine-cosine dei valori della colonna.  

### <a name="sindegrees"></a>SinDegrees

Calcola il sine per i valori della colonna, in gradi.  

### <a name="sinh"></a>Sinh

Calcola il seno iperbolico dei valori della colonna.  

### <a name="tan"></a>Tan

Calcola la tangente dei valori di colonna.  

### <a name="tandegrees"></a>TanDegrees

Calcola la tangente per l'argomento, in gradi.  

### <a name="tanh"></a>Tanh

Calcola la tangente iperbolica dei valori della colonna.  
  
## <a name="technical-notes"></a>Note tecniche

Prestare attenzione quando si selezionano più colonne come secondo operatore. I risultati sono facili da capire se l'operazione è semplice, ad esempio l'aggiunta di una costante a tutte le colonne. 

Si supponga che il set di dati abbia più colonne e di aggiungere il set di dati a se stesso. Nei risultati, ogni colonna viene aggiunta a se stessa, come indicato di seguito:In the results, each column is added to itself, as follows:  
  
|Num1|Num2|Num3|Aggiungi(Num1_Num1)|Aggiungi(Num2_Num2)|Aggiungi(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Se è necessario eseguire calcoli più complessi, è possibile concatenare più istanze di **Applica operazione matematica**. È possibile ad esempio aggiungere due colonne tramite un'istanza di **Apply Math Operation**, quindi usare un'altra istanza di **Apply Math Operation** per dividere la somma per una costante in modo da ottenere la media.  
  
In alternativa, usare uno dei moduli seguenti per eseguire tutti i calcoli contemporaneamente, usando lo script SQL, R o Python:
 
+ [Execute R Script](execute-r-script.md)
+ [Eseguire lo script Python](execute-python-script.md)
+ [Applicare la trasformazione SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
