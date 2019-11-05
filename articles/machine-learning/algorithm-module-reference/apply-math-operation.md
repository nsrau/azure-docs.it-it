---
title: Applica operazione matematica
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Apply Math Operation nel servizio Azure Machine Learning per applicare un'operazione matematica ai valori di colonna in una pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: a2f3665355cc2023aaf4b66c9207aaff4a3bc6db
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493940"
---
# <a name="apply-math-operation"></a>Applica operazione matematica

Questo articolo descrive un modulo di Azure Machine Learning Designer (anteprima).

Usare l'operazione Apply Math per creare calcoli applicati a colonne numeriche nel set di dati di input. 

Le operazioni matematiche supportate includono funzioni aritmetiche comuni come la moltiplicazione e la divisione, le funzioni trigonometriche, un'ampia gamma di funzioni di arrotondamento e funzioni speciali usate in data science come le funzioni gamma e Error.  

Dopo aver definito un'operazione ed eseguito la pipeline, i valori vengono aggiunti al set di dati. A seconda di come viene configurato il modulo, è possibile:

+ Aggiungere i risultati al set di dati. Questa operazione è particolarmente utile quando si verifica il risultato dell'operazione.
+ Sostituire i valori delle colonne con i nuovi valori calcolati.
+ Genera una nuova colonna per i risultati e non Visualizza i dati originali. 

Cercare l'operazione necessaria in queste categorie:  

- [Basic](#basic-math-operations)  
  
     Le funzioni della categoria **Basic** possono essere usate per modificare un singolo valore o una colonna di valori. Ad esempio, è possibile ottenere il valore assoluto di tutti i numeri in una colonna oppure calcolare la radice quadrata di ogni valore in una colonna.  
  
-   [Confronta](#comparison-operations)  
  
      Le funzioni nella categoria **Confronta** sono tutte utilizzate per il confronto: è possibile eseguire un confronto a livello di coppia dei valori in due colonne oppure è possibile confrontare ogni valore di una colonna con una costante specificata. È ad esempio possibile confrontare le colonne per determinare se i valori sono uguali in due set di impostazioni. In alternativa, è possibile usare una costante, ad esempio un valore massimo consentito, per trovare gli outlier in una colonna numerica.  
  
-   [Operazioni](#arithmetic-operations)  
  
     La categoria **operazioni** include le funzioni matematiche di base: addizione, sottrazione, moltiplicazione e divisione. È possibile utilizzare colonne o costanti. Ad esempio, è possibile aggiungere il valore nella colonna A al valore nella colonna B. In alternativa, è possibile sottrarre una costante, ad esempio una media calcolata in precedenza, da ogni valore nella colonna A.  
  
-   [Arrotondamento](#rounding-operations)  
  
     La categoria di **arrotondamento** include una serie di funzioni per l'esecuzione di operazioni quali l'arrotondamento, il soffitto, il piano e il troncamento a diversi livelli di precisione. È possibile specificare il livello di precisione per i numeri decimali e interi.  
  
-   [Speciali](#special-math-functions)  
  
     La categoria **speciale** include funzioni matematiche utilizzate in modo particolare in Data Science, ad esempio integrali ellittici e la funzione di errore gaussiana.  
  
-   [Trigonometriche](#trigonometric-functions)  
  
     La categoria **trigonometrica** include tutte le funzioni trigonometriche standard. È possibile, ad esempio, convertire i radianti in gradi o funzioni di calcolo come la tangente in radianti o gradi.
     Queste funzioni sono unaria, ovvero accettano una singola colonna di valori come input, applicano la funzione trigonometrica e restituiscono come risultato una colonna di valori.  È pertanto necessario assicurarsi che la colonna di input sia il tipo appropriato e che contenga il tipo corretto di valori per l'operazione specificata.   

## <a name="how-to-configure-apply-math-operation"></a>Come configurare l'operazione Apply Math  

Il modulo **Apply Math Operation** richiede un set di dati contenente almeno una colonna contenente solo numeri. I numeri possono essere discreti o continui, ma devono essere di un tipo di dati numerico, non di una stringa.

È possibile applicare la stessa operazione a più colonne numeriche, ma tutte le colonne devono trovarsi nello stesso set di dati. 

Ogni istanza di questo modulo può eseguire un solo tipo di operazione alla volta. Per eseguire operazioni matematiche complesse, potrebbe essere necessario concatenare più istanze del modulo **Apply Math Operation** .  
  
1.  Aggiungere il modulo **Apply Math Operation** alla pipeline.

1. Connettere un set di dati che contiene almeno una colonna numerica.  

1.  Selezionare una o più colonne di origine in cui eseguire il calcolo.   
  
    - Qualsiasi colonna scelta deve essere un tipo di dati numerico. 
    - L'intervallo di dati deve essere valido per l'operazione matematica selezionata. In caso contrario, può verificarsi un errore o un risultato NaN (non un numero). Ad esempio, ln (-1,0) è un'operazione non valida e restituisce un valore di `NaN`.
  
1.  Fare clic su **categoria** per selezionare il **tipo** di operazione matematica da eseguire.
    
1. Scegliere un'operazione specifica dall'elenco nella categoria.
  
1.  Impostare parametri aggiuntivi richiesti per ogni tipo di operazione.  
  
1.  Usare l'opzione **modalità output** per indicare come si vuole che venga generata l'operazione matematica: 

    - **Accoda**. Tutte le colonne utilizzate come input sono incluse nel set di dati di output, oltre a una colonna aggiuntiva che contiene i risultati dell'operazione matematica.
    - **Inserire**. I valori nelle colonne utilizzate come input vengono sostituiti con i nuovi valori calcolati. 
    - **ResultOnly**. Viene restituita una singola colonna contenente i risultati dell'operazione matematica.
  
1.  Eseguire la pipeline.  
  
## <a name="results"></a>Risultati

Se si generano i risultati utilizzando le opzioni **Append** o **ResultOnly** , le intestazioni di colonna del set di dati restituito indicano l'operazione e le colonne utilizzate. Se, ad esempio, si confrontano due colonne usando l'operatore **Equals** , i risultati saranno simili ai seguenti:  
  
-   **Equals (Col2_Col1)** , che indica che è stato eseguito il test di col2 in col1.  
-   **Equals (Col2_ $10)** , che indica che è stata confrontata la colonna 2 con la costante 10.  

Anche se si usa l'opzione **InPlace** , i dati di origine non vengono eliminati o modificati. la colonna nel set di dati originale è ancora disponibile nella finestra di progettazione. Per visualizzare i dati originali, è possibile connettere il modulo [Aggiungi colonne](add-columns.md) e aggiungerlo all'output di **Apply Math Operation**.  
    
## <a name="basic-math-operations"></a>Operazioni matematiche di base 

Le funzioni nella categoria **Basic** accettano in genere un singolo valore da una colonna, eseguono l'operazione predefinita e restituiscono un singolo valore. Per alcune funzioni, è possibile specificare una costante o un set di colonne come secondo argomento.  
  
 Azure Machine Learning supporta le funzioni seguenti nella categoria di **base** :  

### <a name="abs"></a>ABS

Restituisce il valore assoluto delle colonne selezionate.  
  
### <a name="atan2"></a>Atan2

Restituisce una tangente inversa a quattro quadranti.  

Consente di selezionare le colonne che contengono le coordinate del punto. Per il secondo argomento, che corrisponde alla coordinata x, è anche possibile specificare una costante.  

Corrisponde alla funzione ATAN2 in MATLAB.  

### <a name="conj"></a>CONJ

Restituisce il coniugato per i valori nella colonna selezionata.  

### <a name="cuberoot"></a>CubeRoot

Calcola la radice del cubo per i valori nella colonna selezionata.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Calcola il fattoriale doppio per i valori nella colonna selezionata. Il doppio fattoriale è un'estensione della funzione fattoriale normale e viene indicato come x!!.  

### <a name="eps"></a>EPS

Restituisce la dimensione del gap tra il valore corrente e il successivo numero a precisione doppia più alto. Corrisponde alla funzione EPS in MATLAB.  
  
### <a name="exp"></a>Exp

Restituisce e elevato alla potenza del valore nella colonna selezionata. Corrisponde alla funzione EXP di Excel.  

### <a name="exp2"></a>Exp2

Restituisce l'esponenziale in base 2 degli argomenti, risolvendo per y = x * 2<sup>t</sup> , dove t è una colonna di valori che contiene esponenti.  

In **Column set**selezionare la colonna che contiene i valori dell'esponente t.

Per **exp2** è possibile specificare un secondo argomento x, che può essere una costante o un'altra colonna di valori. Nel **secondo tipo di argomento**, indicare se si fornirà il moltiplicatore x come costante o un valore in una colonna.  

Se ad esempio si seleziona una colonna con i valori {0,1,2,3,4,5} sia per il moltiplicatore che per l'esponente, la funzione restituisce {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Restituisce l'esponente negativo per i valori nella colonna selezionata.  

### <a name="factorial"></a>Fattoriale
Restituisce il fattoriale per i valori nella colonna selezionata.  

### <a name="hypotenuse"></a>Ipotenusa
Calcola l'ipotenusa per un triangolo in cui la lunghezza di un lato viene specificata come una colonna di valori e la lunghezza del secondo lato viene specificata come costante o come due colonne.  

### <a name="ln"></a>Ln

Restituisce il logaritmo naturale per i valori nella colonna selezionata.  

### <a name="lnplus1"></a>LnPlus1

Restituisce il logaritmo naturale più uno per i valori nella colonna selezionata.  

### <a name="log"></a>Log

Restituisce il log dei valori nella colonna selezionata, data la base specificata.  

È possibile specificare la base (il secondo argomento) come costante o selezionando un'altra colonna di valori.  

### <a name="log10"></a>Log10

Restituisce i valori logaritmo in base 10 per la colonna selezionata.  

### <a name="log2"></a>Log2

Restituisce i valori logaritmo in base 2 per la colonna selezionata.  

### <a name="nthroot"></a>NthRoot
Restituisce la radice ennesima del valore, usando un valore n specificato dall'utente.  

Selezionare le colonne per cui si desidera calcolare la radice utilizzando l'opzione **columnstore** .  

In **secondo tipo di argomento**selezionare un'altra colonna contenente la radice oppure specificare una costante da usare come radice.  

Se il secondo argomento è una colonna, ogni valore della colonna viene utilizzato come valore di n per la riga corrispondente. Se il secondo argomento è una costante, digitare il valore di n nella casella di testo del **secondo argomento** .
### <a name="pow"></a>POW

Calcola X elevato alla potenza di Y per ognuno dei valori della colonna selezionata.  

Selezionare innanzitutto le colonne che contengono la **base**, che deve essere un valore float, usando l'opzione **columnstore** .  

In **secondo tipo di argomento**selezionare la colonna contenente l'esponente oppure specificare una costante da usare come esponente.  

Se il secondo argomento è una colonna, ogni valore della colonna viene usato come esponente per la riga corrispondente. Se il secondo argomento è una costante, digitare il valore dell'esponente nella casella di testo **secondo argomento** .  

### <a name="sqrt"></a>Sqrt

Restituisce la radice quadrata dei valori nella colonna selezionata.  

### <a name="sqrtpi"></a>SqrtPi

Per ogni valore nella colonna selezionata, moltiplica il valore per pi greco e quindi restituisce la radice quadrata del risultato.  

### <a name="square"></a>Square

Piazza i valori della colonna selezionata.  

## <a name="comparison-operations"></a>Operazioni di confronto  

Utilizzare le funzioni di confronto nella finestra di progettazione Azure Machine Learning ogni volta che è necessario testare due set di valori l'uno rispetto all'altro. Ad esempio, in una pipeline potrebbe essere necessario eseguire queste operazioni di confronto:  

- Valutare una colonna del modello di punteggi di probabilità rispetto a un valore soglia.
- Determinare se due set di risultati sono uguali. Per ogni riga diversa, aggiungere un flag FALSE che può essere utilizzato per ulteriori operazioni di elaborazione o filtro.  

### <a name="equalto"></a>EqualTo

Restituisce true se i valori sono uguali.  

### <a name="greaterthan"></a>GreaterThan

Restituisce true se i valori in **Column set** sono maggiori della costante specificata o maggiore dei valori corrispondenti nella colonna di confronto.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Restituisce true se i valori in **Column set** sono maggiori o uguali alla costante specificata oppure sono maggiori o uguali ai valori corrispondenti nella colonna di confronto.  

### <a name="lessthan"></a>LessThan

Restituisce true se i valori in **Column set** sono minori della costante specificata o minori dei valori corrispondenti nella colonna di confronto.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Restituisce true se i valori in **Column set** sono minori o uguali alla costante specificata oppure sono minori o uguali ai valori corrispondenti nella colonna di confronto.  

### <a name="notequalto"></a>NotEqualTo

Restituisce true se i valori in **Column set** non sono uguali alla costante o alla colonna di confronto e restituisce false se sono uguali.  

### <a name="pairmax"></a>PairMax

Restituisce il valore maggiore, ovvero il valore in **Column set** o il valore nella costante o nella colonna di confronto.  

### <a name="pairmin"></a>PairMin

Restituisce il valore minore, ovvero il valore in **Column set** o il valore nella costante o nella colonna di confronto  
  
##  <a name="arithmetic-operations"></a>Operazioni aritmetiche   

Include le operazioni aritmetiche di base: addizione e sottrazione, divisione e moltiplicazione.  Poiché la maggior parte delle operazioni sono binarie, richiedendo due numeri, è necessario innanzitutto scegliere l'operazione e quindi scegliere la colonna o i numeri da usare nel primo e nel secondo argomento.

L'ordine in cui si scelgono le colonne per divisione e sottrazione può sembrare poco intuitivo. Tuttavia, per semplificare la comprensione dei risultati, l'intestazione di colonna fornisce il nome dell'operazione e l'ordine in cui le colonne sono state utilizzate.

Operazione|Num1|Num2|Colonna risultati|Valore risultato|
----|----|----|----|----
|Addizione|1|5|Aggiungi (Num2_Num1)| 4|
|Moltiplicazione|1|5|Multiplo (Num2_Num1)|5|
|Sottrazione|1|5|Sottrazione (Num2_Num1)|4|
|Sottrazione|0|1|Sottrazione (Num2_Num1)|0|
|Divisione|1|5|Divisione (Num2_Num1)|5|
|Divisione|0|1|Divisione (Num2_Num1)|Infinity|

### <a name="add"></a>Add

Specificare le colonne di origine utilizzando il **set di colonne**e quindi aggiungere a tali valori un numero specificato nel **secondo argomento**.  

Per aggiungere i valori in due colonne, scegliere una o più colonne utilizzando **set**di colonne, quindi scegliere una seconda colonna utilizzando il **secondo argomento**.  

### <a name="divide"></a>Divisione

Divide i valori in **Column set** mediante una costante o i valori di colonna definiti nel **secondo argomento**.  In altre parole, si sceglie innanzitutto il divisore, quindi il dividendo. Il valore di output è il quoziente.

### <a name="multiply"></a>Moltiplicazione

Moltiplica i valori in **Column set** in base ai valori della costante o della colonna specificati.  

### <a name="subtract"></a>Sottrazione

Specificare la colonna di valori su cui operare ( *minuendo*) scegliendo una colonna diversa utilizzando l'opzione **set di colonne** . Specificare quindi il numero da sottrarre ( *sottraendo*) utilizzando l'elenco a discesa **secondo argomento** . È possibile scegliere una costante o una colonna di valori.

##  <a name="rounding-operations"></a>Operazioni di arrotondamento 

Azure Machine Learning Designer supporta un'ampia gamma di operazioni di arrotondamento. Per molte operazioni, è necessario specificare la quantità di precisione da usare durante l'arrotondamento. È possibile utilizzare un livello di precisione statico, specificato come costante, oppure è possibile applicare un valore di precisione dinamica ottenuto da una colonna di valori.  

- Se si usa una costante, impostare **tipo di precisione** su **costante** e quindi digitare il numero di cifre come Integer nella casella di testo **precisione costante** . Se si digita un valore diverso da Integer, il modulo non genera un errore, ma i risultati possono essere imprevisti.  

- Per usare un valore di precisione diverso per ogni riga del set di dati, impostare **tipo di precisione** su set di **colonne**, quindi scegliere la colonna che contiene i valori di precisione appropriati.  

### <a name="ceiling"></a>Ceiling

Restituisce il limite massimo per i valori in **Column set**.  

### <a name="ceilingpower2"></a>CeilingPower2

Restituisce il limite al quadrato per i valori in **Column set**.  

### <a name="floor"></a>Piano

Restituisce la superficie per i valori in **Column set**, alla precisione specificata.  

### <a name="mod"></a>Mod

Restituisce la parte frazionaria dei valori in **Column set**, alla precisione specificata.  

### <a name="quotient"></a>Quoziente

Restituisce la parte frazionaria dei valori in **Column set**, alla precisione specificata.  

### <a name="remainder"></a>Resto

Restituisce il resto per i valori in **Column set**.  

### <a name="rounddigits"></a>RoundDigits

Restituisce i valori in **Column set**, arrotondati dalla regola 4/5 al numero di cifre specificato.  

### <a name="rounddown"></a>RoundDown

Restituisce i valori in **Column set**, arrotondati per difetto al numero di cifre specificato.  

### <a name="roundup"></a>RoundUp

Restituisce i valori in **Column set**, arrotondati per eccesso al numero di cifre specificato.  

### <a name="toeven"></a>ToEven

Restituisce i valori in **Column set**, arrotondati al numero pari intero più vicino.  

### <a name="toodd"></a>ToOdd

Restituisce i valori in **Column set**, arrotondati al numero dispari intero più vicino.  

### <a name="truncate"></a>Troncare

Tronca i valori in **Column set** rimuovendo tutte le cifre non consentite dalla precisione specificata.  
  
## <a name="special-math-functions"></a>Funzioni matematiche speciali

Questa categoria include funzioni matematiche specializzate spesso utilizzate in data science. Se non specificato diversamente, la funzione è unaria e restituisce il calcolo specificato per ogni valore della colonna o delle colonne selezionate.  

### <a name="beta"></a>Beta

Restituisce il valore della funzione beta di Eulero.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Restituisce il valore dell'integrale ellittico incompleto.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Restituisce il valore dell'integrale ellittico completo (K).  

### <a name="erf"></a>ERF

Restituisce il valore della funzione Error.  

La funzione Error (denominata anche funzione di errore di Gauss) è una funzione speciale della forma Sigma usata nella probabilità per descrivere la diffusione.  

### <a name="erfc"></a>Erfc

Restituisce il valore della funzione di errore complementare.  

Erfc è definito come 1 – ERF (x).  

### <a name="erfscaled"></a>ErfScaled

Restituisce il valore della funzione di errore ridimensionata.  

La versione ridimensionata della funzione Error può essere usata per evitare l'underflow aritmetico.  

### <a name="erfinverse"></a>ErfInverse

Restituisce il valore della funzione ERF inversa.  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Restituisce il valore dell'integrale esponenziale.  

### <a name="gamma"></a>Gamma

Restituisce il valore della funzione gamma.  

### <a name="gammaln"></a>GammaLn

Restituisce il logaritmo naturale della funzione gamma.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Restituisce il valore della funzione Gamma incompleta regolarizzata.  

Questa funzione accetta un secondo argomento, che può essere specificato come una costante o una colonna di valori.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Restituisce il valore della funzione Gamma incompleta regolarizzata inversa.  

Questa funzione accetta un secondo argomento, che può essere specificato come una costante o una colonna di valori.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Restituisce il valore della funzione Gamma incompleta regolarizzata.  

Questa funzione accetta un secondo argomento, che può essere specificato come una costante o una colonna di valori.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Restituisce il valore della funzione gamma completa generalizzata inversa generalizzata.

Questa funzione accetta un secondo argomento, che può essere specificato come una costante o una colonna di valori.  

### <a name="polygamma"></a>Poligamma

Restituisce il valore della funzione poligamma.  

Questa funzione accetta un secondo argomento, che può essere specificato come una costante o una colonna di valori.

##  <a name="trigonometric-functions"></a>Funzioni trigonometriche 

Questa categoria iIncludes la maggior parte delle importanti funzioni trigonometriche e inverse. Tutte le funzioni trigonometriche sono unarie e non richiedono argomenti aggiuntivi.  

### <a name="acos"></a>Acos

Calcola l'arcoseno per i valori della colonna.  

### <a name="acosdegree"></a>AcosDegree

Calcola l'arcoseno dei valori della colonna, in gradi.  

### <a name="acosh"></a>Acosh

Calcola l'arcoseno iperbolico dei valori della colonna.  

### <a name="acot"></a>Acot

Calcola il arcotangente dei valori della colonna.  

### <a name="acotdegrees"></a>AcotDegrees

Calcola il arcotangente dei valori della colonna, in gradi.  

### <a name="acoth"></a>Acoth

Calcola l'arcotangente iperbolico dei valori della colonna.  

### <a name="acsc"></a>ACSC

Calcola il Arccosecant dei valori della colonna.  

### <a name="acscdegrees"></a>AcscDegrees

Calcola il Arccosecant dei valori della colonna, in gradi.  
### <a name="asec"></a>ASEC

Calcola il arcosecante dei valori della colonna.  

### <a name="asecdegrees"></a>AsecDegrees

Calcola il arcosecante dei valori della colonna, in gradi.  

### <a name="asech"></a>Asech

Calcola l'arcosecante iperbolico dei valori della colonna.  

### <a name="asin"></a>Asin

Calcola il arcoseno dei valori della colonna.  

### <a name="asindegrees"></a>AsinDegrees

Calcola il arcoseno dei valori della colonna, in gradi.  

### <a name="asinh"></a>Asinh

Calcola il arcoseno iperbolico per i valori della colonna.  

### <a name="atan"></a>Atan

Calcola il arcotangente dei valori della colonna.  

### <a name="atandegrees"></a>AtanDegrees

Calcola il arcotangente dei valori della colonna, in gradi.  

### <a name="atanh"></a>Atanh

Calcola l'arcotangente iperbolico dei valori della colonna.  

### <a name="cos"></a>Cos

Calcola il coseno dei valori della colonna.  

### <a name="cosdegrees"></a>CosDegrees

Calcola il coseno per i valori di colonna, in gradi.  

### <a name="cosh"></a>Cosh

Calcola il coseno iperbolico per i valori della colonna.  

### <a name="cot"></a>Culla

Calcola la cotangente per i valori della colonna.  

### <a name="cotdegrees"></a>CotDegrees

Calcola la cotangente per i valori di colonna, in gradi.  

### <a name="coth"></a>Coth
Calcola la cotangente iperbolica per i valori di colonna.  

### <a name="csc"></a>CSC

Calcola la cosecante per i valori della colonna.  

### <a name="cscdegrees"></a>CscDegrees

Calcola la cosecante per i valori di colonna, in gradi.  

### <a name="csch"></a>Csch

Calcola la cosecante iperbolica per i valori della colonna.  

### <a name="degreestoradians"></a>DegreesToRadians

Converte i gradi in radianti.  

### <a name="sec"></a>Sec

Calcola la secante dei valori della colonna.  

### <a name="asecdegrees"></a>aSecDegrees

Calcola la secante per i valori di colonna, in gradi.  

### <a name="asech"></a>aSech

Calcola la secante iperbolica dei valori della colonna.  

### <a name="sign"></a>Sign

Restituisce il segno dei valori della colonna.  

### <a name="sin"></a>Peccato

Calcola il seno dei valori della colonna.  

### <a name="sinc"></a>Sinc

Calcola il valore seno-coseno dei valori della colonna.  

### <a name="sindegrees"></a>SinDegrees

Calcola il seno per i valori di colonna, in gradi.  

### <a name="sinh"></a>Sinh

Calcola il seno iperbolico dei valori della colonna.  

### <a name="tan"></a>Tan

Calcola la tangente dei valori della colonna.  

### <a name="tandegrees"></a>TanDegrees

Calcola la tangente per l'argomento, in gradi.  

### <a name="tanh"></a>Tanh

Calcola la tangente iperbolica dei valori della colonna.  
  
## <a name="technical-notes"></a>Note tecniche

Prestare attenzione quando si seleziona più di una colonna come secondo operatore. I risultati sono facili da comprendere se l'operazione è semplice, ad esempio l'aggiunta di una costante a tutte le colonne. 

Si supponga che il set di dati includa più colonne e che il set di dati venga aggiunto a se stesso. Nei risultati ogni colonna viene aggiunta a se stessa, come indicato di seguito:  
  
|Num1|Num2|Num3|Aggiungi (Num1_Num1)|Aggiungi (Num2_Num2)|Aggiungi (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Se è necessario eseguire calcoli più complessi, è possibile concatenare più istanze di **Apply Math Operation**. Ad esempio, è possibile aggiungere due colonne usando un'istanza di **Apply Math Operation**e quindi usare un'altra istanza di **Apply Math Operation** per dividere la somma per una costante in modo da ottenere la media.  
  
In alternativa, usare uno dei moduli seguenti per eseguire tutti i calcoli contemporaneamente, usando SQL, R o script Python:
 
+ [Esegui script R](execute-r-script.md)
+ [Esegui script Python](execute-python-script.md)
+ [Applica trasformazione SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
