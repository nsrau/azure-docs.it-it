---
title: Usare R con Machine Learning Studio (classico)-Azure
description: Usare questa esercitazione sulla programmazione R per iniziare a usare Azure Machine Learning Studio (classico) in R per creare una soluzione di previsione.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: bca2f0229a15f44ff8f3589a9c1e80032036b97c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95507201"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Introduzione a Azure Machine Learning Studio (classico) in R

**si applica a:** ![ Si tratta di un segno di spunta, che indica che questo articolo si applica alla Machine Learning Studio (classica). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (classico) si ![ tratta di una X, il che significa che questo articolo si applica al Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

<!-- Stephen F Elston, Ph.D. -->
Questa esercitazione illustra come usare Azure Machine Learning Studio (classico) per creare, testare ed eseguire codice R. Alla fine, sarà presente una soluzione di previsione completa.

> [!div class="checklist"]
> * Creare codice per la pulizia e la trasformazione dei dati.
> * Analizzare le correlazioni tra diverse variabili del set di dati.
> * Creare un modello di previsione delle serie temporali stagionali per la produzione di latte.


Machine Learning Studio (classico) contiene molti moduli avanzati di machine learning e di manipolazione dei dati. Con il linguaggio di programmazione R, questa combinazione fornisce la scalabilità e la facilità di distribuzione di Machine Learning Studio (classico) con la flessibilità e l'analisi approfondita di R.

La previsione è un metodo analitico ampiamente utilizzato e utile. Gli usi comuni variano dalla stima delle vendite di elementi stagionali e dalla determinazione dei livelli di inventario ottimali per la stima delle variabili macroeconomiche. In genere, inoltre, viene eseguita con modelli in serie temporale. I dati delle serie temporali sono dati i cui valori contengono un indice temporale, che L'indice temporale può essere regolare, ad esempio ogni mese o ogni minuto. L'indice temporale può anche essere irregolare. Sui dati in serie temporale si basano i modelli in serie temporale. Il linguaggio di programmazione R contiene un'architettura flessibile ed estese funzioni di analisi per i dati in serie temporale.

## <a name="get-the-data"></a>Ottenere i dati

In questa esercitazione vengono usati i dati relativi alla produzione casearia in California e ai prezzi, che includono informazioni mensili sulla produzione di diversi prodotti caseari e sul prezzo del latte grasso, che è un prodotto di benchmark.

I dati usati in questo articolo, insieme agli script R, possono essere scaricati da [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). I dati nel file `cadairydata.csv` sono stati originariamente sintetizzati in base alle informazioni disponibili nel sito della University of Wisconsin [Dairy Markets](https://dairymarkets.com).

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interagire con il linguaggio R in Machine Learning Studio (versione classica)

Questa sezione illustra alcune nozioni di base sull'interazione con il linguaggio di programmazione R nell'ambiente Machine Learning Studio (classico). Il linguaggio R offre uno strumento potente per creare moduli personalizzati di analisi e manipolazione dei dati all'interno dell'ambiente Machine Learning Studio (classico).

RStudio verrà usato per sviluppare, testare ed eseguire il debug di codice R su scala ridotta. Questo codice viene quindi tagliato e incollato in un modulo [Execute R script][execute-r-script] pronto per l'esecuzione in Machine Learning Studio (classico).

### <a name="the-execute-r-script-module"></a>Modulo Execute R Script

All'interno Machine Learning Studio (classico), gli script R vengono eseguiti nel modulo [Execute R script][execute-r-script] . Di seguito è riportato un esempio del modulo [Execute R script][execute-r-script] in Machine Learning Studio (classico).

 ![Screenshot che mostra il linguaggio di programmazione R: il modulo Execute R script selezionato in Machine Learning Studio (classico).](./media/r-quickstart/fig1.png)

L'immagine precedente illustra alcune delle parti principali dell'ambiente Machine Learning Studio (classico) per l'uso del modulo [Execute R script][execute-r-script] :

* I moduli dell'esperimento vengono visualizzati nel riquadro centrale.
* La parte superiore del riquadro destro contiene una finestra che è possibile usare per visualizzare e modificare gli script R.
* Nella parte inferiore del riquadro destro vengono visualizzate alcune proprietà dello [script Execute R][execute-r-script]. È possibile visualizzare i log degli errori e di output selezionando le aree appropriate di questo riquadro.

Il resto di questo articolo illustra più dettagliatamente lo [script Execute R][execute-r-script] .

Quando si lavora con funzioni R complesse, è consigliabile modificare, testare ed eseguire il debug in RStudio. Come per qualsiasi sviluppo software, estendere il codice in modo incrementale e testarlo in piccoli test case semplici. Quindi tagliare e incollare le funzioni nella finestra dello script R del modulo [Execute R Script][execute-r-script]. Questo approccio consente di sfruttare sia il Integrated Development Environment RStudio (IDE) che la potenza di Machine Learning Studio (versione classica).

#### <a name="execute-r-code"></a>Eseguire il codice R

Qualsiasi codice R nel modulo [Execute R script][execute-r-script] verrà eseguito quando si esegue l'esperimento selezionando il pulsante **Run (Esegui** ). Al termine dell'esecuzione, viene visualizzato un segno di spunta nell'icona [Esegui script R][execute-r-script] .

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Codifica R difensiva per Machine Learning Studio (versione classica)

Se si sta sviluppando codice R per un servizio Web usando Machine Learning Studio (versione classica), è necessario pianificare in modo sicuro il modo in cui il codice gestirà un input di dati e le eccezioni imprevisti. Per garantire una maggiore chiarezza, non sono stati inclusi molti metodi di controllo o gestione delle eccezioni nella maggior parte degli esempi di codice illustrati. Come si procede, verranno forniti diversi esempi di funzioni usando la funzionalità di gestione delle eccezioni di R.

Se è necessario un trattamento più completo della gestione delle eccezioni di R, vedere le sezioni applicabili del libro di Wickham elencate in [ulteriori letture](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Eseguire il debug e il test di R in Machine Learning Studio (versione classica)

Testare ed eseguire il debug del codice R su una scala ridotta in RStudio. Esistono anche casi in cui è necessario tenere traccia dei problemi del codice R nello [script Execute R][execute-r-script] . Inoltre, è consigliabile verificare i risultati in Machine Learning Studio (classico).

L'output dell'esecuzione del codice R e della piattaforma Machine Learning Studio (classica) si trova principalmente in output. log. Alcune informazioni aggiuntive sono in Error. log.

Se si verifica un errore in Machine Learning Studio (classico) durante l'esecuzione del codice R, il primo corso di azione dovrebbe essere esaminare Error. log. Questo file può contenere messaggi di errore utili a comprendere e correggere l'errore. Per visualizzare Error. log, selezionare **Visualizza log degli errori** nel riquadro proprietà per lo [script Execute R][execute-r-script] che contiene l'errore.

Ad esempio, è stato eseguito il codice R seguente con una variabile y non definita in un modulo [Execute R script][execute-r-script] .

```r
x <- 1.0
z <- x + y
```

Non è possibile eseguire questo codice, che comporta una condizione di errore. Selezionando **Visualizza log degli errori** nel riquadro proprietà viene visualizzata la seguente visualizzazione.

  ![Screenshot che mostra un messaggio di errore popup.](./media/r-quickstart/fig2.png)

Per visualizzare il messaggio di errore di R, è necessario usare output.log. Selezionare il modulo [Execute R script][execute-r-script] , quindi selezionare l'elemento **View output. log** nel riquadro proprietà a destra. Viene visualizzata una nuova finestra del browser e viene visualizzato il messaggio di errore seguente.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Questo messaggio di errore non contiene sorprese e identifica chiaramente il problema.

Per ispezionare il valore dei singoli oggetti in R; è possibile stampare i valori nel file output.log. Le regole per l'analisi dei valori di oggetto sono essenzialmente le stesse di una sessione R interattiva. Se ad esempio si immette un nome di variabile in una riga, il valore dell'oggetto verrà stampato nel file output. log.

#### <a name="packages-in-machine-learning-studio-classic"></a>Pacchetti in Machine Learning Studio (versione classica)

Machine Learning Studio (classico) è dotato di più di 350 pacchetti di linguaggio R preinstallati. Per recuperare un elenco dei pacchetti preinstallati, è possibile usare il codice seguente nel modulo [Execute R Script][execute-r-script].

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se al momento non si riesce a comprendere l'ultima riga del codice, continuare a leggere. Nella parte restante di questo articolo verrà illustrato dettagliatamente l'uso di R nell'ambiente Machine Learning Studio (classico).

### <a name="introduction-to-rstudio"></a>Introduzione a RStudio

RStudio è un IDE ampiamente usato per R. RStudio verrà usato per modificare, testare ed eseguire il debug di parte del codice R usato in questa guida. Una volta testato e pronto il codice R, è possibile tagliare e incollare dall'editor RStudio in un modulo Machine Learning Studio (classico) [Execute R script][execute-r-script] .

Se il linguaggio di programmazione R non è installato nel computer desktop, eseguirlo ora. Il download gratuito del linguaggio R Open Source è disponibile nella [rete di archiviazione r completa (Cran)](https://www.r-project.org/). Sono disponibili download per Windows, macOS e Linux/UNIX. Scegliere un mirror vicino e seguire le istruzioni per il download. Inoltre, CRAN contiene molti utili pacchetti di analisi e manipolazione dei dati.

Se non si ha familiarità con RStudio, è necessario scaricare e installare la versione desktop. È possibile trovare i download di RStudio per Windows, macOS e Linux/UNIX in [rstudio](http://www.rstudio.com/products/RStudio/). Seguire le indicazioni fornite per installare RStudio sul proprio desktop.

Un'esercitazione introduttiva a RStudio è disponibile in [uso dell'IDE di rstudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Per ulteriori informazioni sull'utilizzo di RStudio, vedere la [Guida alla documentazione di rstudio](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Input e output dei dati nel modulo Execute R Script

In questa sezione verrà illustrato come ottenere i dati all'interno e all'esterno del modulo [Execute R script][execute-r-script] . Verrà inoltre esaminato come gestire i diversi tipi di dati in lettura e in uscita dal modulo [Execute R script][execute-r-script] .

Il codice completo per questa sezione è in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Caricare e controllare i dati

#### <a name="load-the-dataset"></a><a id="loading"></a>Caricare il set di dati

Si inizierà caricando il file di **csdairydata.csv** in Machine Learning Studio (classico).

1. Avviare l'ambiente Machine Learning Studio (classico).
1. Selezionare **+ nuovo** nella parte inferiore sinistra della schermata e selezionare **DataSet**.
1. Selezionare **da file locale** e quindi selezionare **Sfoglia** per selezionare il file.
1. Assicurarsi di aver selezionato un **file CSV generico con intestazione (. csv)** come tipo per il set di dati.
1. Selezionare il segno di spunta.
1. Al termine del caricamento del set di dati, il nuovo set di dati dovrebbe essere visualizzato quando si seleziona la scheda **set** di dati.

#### <a name="create-an-experiment"></a>Creare un esperimento

Ora che sono presenti alcuni dati in Machine Learning Studio (classico), è necessario creare un esperimento per eseguire l'analisi.  

1. Selezionare **+ nuovo** nella parte inferiore sinistra della schermata e selezionare **esperimento**  >  **vuoto** esperimento.
1. Assegnare un nome all'esperimento selezionando e modificando l' **esperimento creato sul** titolo nella parte superiore della pagina. Ad esempio, modificarlo in **CA Dairy Analysis**.
1. A sinistra della pagina dell'esperimento selezionare **DataSets set** di DataSet salvati  >  **My Datasets**. Verrà visualizzato il file **cadairydata.csv** caricato in precedenza.
1. Trascinare **csdairydata.csv set di dati** nell'esperimento.
1. Nella casella **Cerca elementi dell'esperimento** nella parte superiore del riquadro sinistro, immettere [Esegui script R][execute-r-script]. Il modulo verrà visualizzato nell'elenco di ricerca.
1. Trascinare il modulo [Execute R script][execute-r-script] sul pallet.
1. Connettere l'output del **set di daticsdairydata.csv** all'input più a sinistra (**DataSet1**) dello [script Execute R][execute-r-script].
1. Selezionare **Salva**.

A questo punto, l'esperimento dovrebbe avere un aspetto simile a questo esempio.

![Diagramma che illustra l'esperimento CA Dairy Analysis con il set di dati e il modulo Execute R script.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Controllo dei dati

Diamo un'occhiata ai dati caricati nell'esperimento. Nell'esperimento selezionare l'output del **set di daticadairydata.csv** e selezionare **Visualizza**. Verrà visualizzata una schermata simile a questo riepilogo.

![Screenshot che mostra un riepilogo del set di dati cadairydata.csv.](./media/r-quickstart/fig4.png)

Questa visualizzazione Mostra numerose informazioni utili. È possibile visualizzare le prime righe del set di dati. Se si seleziona una colonna, nella sezione **statistiche** vengono visualizzate ulteriori informazioni sulla colonna. La riga tipo di **funzionalità** , ad esempio, Mostra i tipi di dati Machine Learning Studio (classico) assegnati alla colonna. Prima di iniziare a eseguire operazioni gravi, controllare questa visualizzazione.

### <a name="first-r-script"></a>Primo script R

Viene ora creato un semplice primo script R da sperimentare all'interno Machine Learning Studio (classico). È stato creato e testato lo script seguente in RStudio.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

A questo punto è necessario trasferire questo script Machine Learning Studio (versione classica). È possibile tagliare e incollare, ma in questo caso trasferire lo script R tramite un file zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Input di dati nel modulo Execute R Script

Osservare ora gli input per il modulo [Execute R Script][execute-r-script]. In questo esempio verranno letti i dati caseari della California nel modulo [Execute R script][execute-r-script] .

Esistono tre possibili input per il modulo [Execute R Script][execute-r-script]. È possibile usare uno o tutti questi input, a seconda dell'applicazione. È anche possibile usare uno script R che non accetta alcun input.

Di seguito vengono esaminati i singoli input, da sinistra a destra. È possibile visualizzare i nomi di ciascun input posizionando il cursore sull'input e leggendo la descrizione comando.

#### <a name="script-bundle"></a>Bundle di script

L'input del bundle di script consente di passare il contenuto di un file zip nel modulo [Execute R script][execute-r-script] . È possibile usare uno dei seguenti comandi per leggere i contenuti del file con estensione zip nel codice R.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (versione classica) considera i file nel file zip come se si trovassero nella directory src/, quindi è necessario anteporre al nome di tale directory i nomi di file. Se, ad esempio, il file zip contiene i file `yourfile.R` e `yourData.rdata` nella radice del file zip, questi file vengono indirizzati come `src/yourfile.R` e `src/yourData.rdata` quando si utilizzano `source` e `load` .

È già stato illustrato il caricamento dei set di [dati in caricare il set di dati](#loading). Dopo aver creato e testato lo script R illustrato nella sezione precedente, seguire questa procedura.

1. Salvare lo script R in un file .R. Il file script viene chiamato **SimplePlot. R**. Ecco il file:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Creare un file zip e copiare lo script in questo file zip. In Windows è possibile fare clic con il pulsante destro del mouse sul file e scegliere **Invia a**  >  **cartella compressa**. Questa azione crea un nuovo file zip che contiene **SimplePlot. File R** .

1. Aggiungere il file ai **set di impostazioni** nel Machine Learning Studio (classico) e specificare il tipo come **zip**. Dovrebbe essere visualizzato il file con estensione zip nei set di dati.

1. Trascinare il file zip dai **set di impostazioni** nell' **area di disegno ml Studio (classica)**.

1. Connettere l'output dell'icona dei **dati zip** all'input **Script Bundle** del modulo [Execute R Script][execute-r-script].

1. Immettere la `source()` funzione con il nome del file zip nella finestra del codice per il modulo [Execute R script][execute-r-script] . In questo caso, è stato immesso `source("src/simpleplot.R")` .

1. Selezionare **Salva**.

Al termine di questi passaggi, il modulo [Execute r script][execute-r-script] esegue lo script r nel file zip durante l'esecuzione dell'esperimento. A questo punto, l'esperimento dovrebbe avere un aspetto simile a questo esempio.

![Diagramma che illustra un esperimento usando uno script R compresso.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>Dataset1

È possibile passare una tabella di dati rettangolare al codice R usando l'input Dataset1. In questo semplice script la `maml.mapInputPort(1)` funzione legge i dati dalla porta 1. I dati vengono poi assegnati a un nome di variabile del frame di dati nel codice. In questo semplice script la prima riga di codice esegue l'assegnazione.

```r
cadairydata <- maml.mapInputPort(1)
```

Eseguire l'esperimento selezionando il pulsante **Run (Esegui** ). Al termine dell'esecuzione, selezionare il modulo [Execute R script][execute-r-script] , quindi selezionare **Visualizza log di output** nel riquadro proprietà. Viene visualizzata una nuova pagina del browser che mostra i contenuti del file output.log. Quando si scorre verso il basso, verrà visualizzato un output simile al seguente.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Più avanti nella pagina sono disponibili informazioni più dettagliate sulle colonne, che hanno un aspetto simile all'output seguente.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Questi risultati sono essenzialmente quelli previsti, con 228 osservazioni e 9 colonne nel frame di dati. È possibile visualizzare i nomi delle colonne, il tipo di dati R e un esempio di ogni colonna.

> [!NOTE]
> Lo stesso output stampato è disponibile anche dall'output R Device del modulo [Execute R Script][execute-r-script]. Nella sezione successiva verranno illustrati gli output del modulo [Execute R script][execute-r-script] .  

#### <a name="dataset2"></a>Dataset2

Il comportamento della modalità di input Dataset2 è identico a quello di Dataset1. Usando questo input, è possibile passare una seconda tabella rettangolare di dati nel codice R. La funzione `maml.mapInputPort(2)`, con l'argomento 2, viene usata per passare questi dati.  

### <a name="execute-r-script-outputs"></a>Output del modulo Execute R Script

#### <a name="output-a-dataframe"></a>Output di un frame di dati

È possibile eseguire l'output dei contenuti di un frame di dati R come tabella rettangolare dalla porta Result Dataset1 usando la funzione `maml.mapOutputPort()` . In questo semplice script R, questa azione viene eseguita dalla riga seguente.

```r
maml.mapOutputPort('cadairydata')
```

Dopo aver eseguito l'esperimento, selezionare la porta di output DataSet1 risultato, quindi selezionare **Visualizza**. Verrà visualizzata una schermata simile all'esempio seguente.

![Screenshot che mostra la visualizzazione dell'output dei dati caseari della California.](./media/r-quickstart/fig7.png)

In questo caso l'output è identico all'input, come previsto.

### <a name="r-device-output"></a>Output R Device

L'output Device del modulo [Execute R Script][execute-r-script] contiene messaggi e output grafico. Alla porta di output R Device vengono inviati da R sia l'output standard sia i messaggi di errore standard.

Per visualizzare l'output del dispositivo R, selezionare la porta e quindi selezionare **Visualizza**. Qui viene visualizzato l'output standard e l'errore standard dello script R.

![Screenshot che mostra l'output standard e l'errore standard dalla porta R Device.](./media/r-quickstart/fig8.png)

Scorrendo verso il basso, viene visualizzato l'output grafico dello script R.

![Screenshot che mostra l'output grafico dalla porta R Device.](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtraggio e trasformazione dei dati

In questa sezione verranno eseguite alcune operazioni di filtro e trasformazione dei dati di base sui dati caseari della California. Alla fine di questa sezione, i dati saranno disponibili in un formato appropriato per la compilazione di un modello analitico.

In particolare, in questa sezione verranno eseguite diverse attività comuni di pulizia e trasformazione dei dati: trasformazione del tipo, applicazione di filtri ai frame di dati, aggiunta di nuove colonne calcolate e trasformazioni di valori. Queste procedure di base potrebbero essere utili per affrontare le numerose variazioni che spesso si generano nei problemi della vita reale.

Il codice R completo per questa sezione è disponibile in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Trasformazioni di tipi

Ora che è possibile leggere i dati caseari della California nel codice R del modulo [Execute R Script][execute-r-script], è necessario verificare che i dati nelle colonne abbiano il tipo e il formato previsti.

R è un linguaggio tipizzato dinamicamente, in cui i tipi di dati vengono assegnati l'uno all'altro in base alle esigenze. I tipi di dati atomici in R includono numeric, Logical e character. Il tipo fattore viene usato invece per archiviare in modo compatto i dati relativi alle categorie. Per ulteriori informazioni sui tipi di dati, vedere i riferimenti in [ulteriori letture](#appendixb).

Quando i dati tabulari vengono letti in R da un'origine esterna, è sempre consigliabile controllare i tipi risultanti nelle colonne. È possibile che si desideri una colonna di tipo carattere, ma in molti casi la colonna viene visualizzata come fattore o viceversa. In altri casi, una colonna che si pensa debba essere numerica viene rappresentata da dati di tipo carattere, ad esempio "1,23" anziché 1,23 come numero a virgola mobile.

Fortunatamente, è facile convertire un tipo in un altro, purché sia possibile eseguire il mapping. Ad esempio, non è possibile convertire "Nevada" in un valore numerico, ma è possibile convertirlo in un fattore (variabile categorica). Come altro esempio, è possibile convertire un valore numerico 1 in un carattere "1" o un fattore.

La sintassi di queste conversioni è semplice: `as.datatype()`. Queste funzioni di conversione dei tipi includono le funzioni seguenti:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Esaminando i tipi di dati delle colonne inserite nella sezione precedente, tutte le colonne sono di tipo numerico. L'eccezione è la colonna denominata "month", che è di tipo character. Convertire questo tipo in un fattore e testare i risultati.

È stata eliminata la riga in cui è stata creata la matrice del tracciato a dispersione e viene aggiunta una riga per convertire la colonna Month in un fattore. In questo esperimento verrà tagliato e incollato il codice R nella finestra del codice del modulo [Execute R script][execute-r-script] . È anche possibile aggiornare il file zip e caricarlo in Machine Learning Studio (classico), ma questa opzione richiede diversi passaggi.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Eseguiamo il codice e analizziamo il log di output per lo script R. Di seguito sono riportati i dati rilevanti del log.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Il tipo per month dovrebbe ora indicare i **livelli di fattore w/14**. Questo tipo è un problema perché sono presenti solo 12 mesi nell'anno. È anche possibile verificare che il tipo in **visualizzazione** della porta del set di dati di risultati sia **categorico**.

Il problema è che la colonna month non è stata codificata sistematicamente. In alcuni casi, un mese viene chiamato aprile e in altri è abbreviato come aprile. Per risolvere il problema, è possibile tagliare la stringa a tre caratteri. La riga di codice ha ora un aspetto simile all'esempio seguente.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Eseguire di nuovo l'esperimento e visualizzare il log di output. I risultati previsti sono illustrati di seguito.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


La variabile di fattore contiene ora 12 livelli.

### <a name="basic-dataframe-filtering"></a>Filtro dataframe di base

I frame di dati R supportano avanzate funzionalità di filtraggio. I set di dati, infatti, possono essere suddivisi in sottoinsiemi usando filtri logici su righe o colonne. In molto casi, tuttavia, sono necessari complessi criteri di filtro. Per esempi completi di filtro dei frame di frame, vedere i riferimenti in [ulteriori letture](#appendixb).

Nel set di dati è necessario eseguire un filtro. Se si esaminano le colonne nel dataframe dati cadairydata, verranno visualizzate due colonne non necessarie. La prima colonna include un numero di riga, che non è molto utile. La seconda colonna, Year.Month, contiene le informazioni ridondanti. È possibile escludere facilmente queste colonne usando il seguente codice R.

> [!NOTE]
> A questo punto, in questa sezione verrà illustrato il codice aggiuntivo aggiunto al modulo [Execute R script][execute-r-script] . Ogni nuova riga verrà aggiunta *prima* della `str()` funzione. Questa funzione viene usata per verificare i risultati in Machine Learning Studio (classico).

Viene aggiunta la riga seguente al codice R nel modulo [Execute R script][execute-r-script] .

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Eseguire questo codice nell'esperimento e controllare il risultato dal log di output. Questi risultati sono riportati di seguito.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Si ottengono ora i risultati previsti.

### <a name="add-a-new-column"></a>Aggiungere una nuova colonna

Per creare modelli Time Series, sarà utile avere una colonna contenente i mesi dall'inizio della serie temporale. Creeremo la nuova colonna Month. Count.

Per semplificare l'organizzazione del codice, verrà creata la prima funzione semplice, `num.month()` . Questa funzione verrà quindi applicata per creare una nuova colonna nel dataframe. Il nuovo codice è il seguente:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

A questo punto, eseguire l'esperimento aggiornato e usare il log di output per visualizzare i risultati. Questi risultati sono riportati di seguito.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Sembra che tutto stia funzionando perfettamente. La nuova colonna con i valori previsti è stata creata correttamente nel frame di dati.

### <a name="value-transformations"></a>Trasformazioni di valori

In questa sezione verranno eseguite alcune semplici trasformazioni sui valori in alcune delle colonne del dataframe. Il linguaggio R supporta trasformazioni di dati pressoché arbitrarie. Per ulteriori esempi, vedere i riferimenti in [ulteriori letture](#appendixb).

Se si esaminano i valori nei riepiloghi del frame di frame, verrà visualizzato un elemento strano qui. In California si produce più gelato che latte? No, ovviamente. Il problema è che le unità sono diverse. Il prezzo è in unità di sterline statunitensi, il latte è in unità di 1 milione sterline statunitensi, il gelato è in unità da 1.000 galloni statunitensi e cottage cheese è in unità da 1.000 libbre statunitensi. Supponendo che il gelato possa pesare circa 6,5 libbre per gallone, possiamo eseguire facilmente la moltiplicazione per convertire questi valori in modo che siano tutti in unità uguali a 1.000 libbre.

Per il modello di previsione, viene usato un modello moltiplicativo per la tendenza e la regolazione stagionale di questi dati. Una trasformazione log consente di usare un modello lineare, che semplifica questo processo. Possiamo applicare la trasformazione logaritmica alla stessa funzione a cui è stato applicato il moltiplicatore.

Nel codice seguente viene definita una nuova funzione,, che `log.transform()` viene applicata alle righe che contengono i valori numerici. La funzione R `Map()` viene usata per applicare la funzione `log.transform()` alle colonne selezionate del frame di dati. La `Map()` funzione è simile a `apply()` , ma consente la funzione per più di un elenco di argomenti. Un elenco di moltiplicatori fornisce il secondo argomento alla funzione `log.transform()` . La `na.omit()` funzione viene usata come un po' di pulizia per assicurarsi che non siano presenti valori mancanti o non definiti nel frame di frame.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Si è verificato un po' nella `log.transform()` funzione. Gran parte del codice è progettato per controllare la presenza di potenziali problemi con gli argomenti o per gestire le eccezioni, che possono sempre verificarsi durante i calcoli. Solo poche righe di questo codice eseguono i calcoli.

L'obiettivo della programmazione difensiva è impedire l'errore di una singola funzione che impedisce l'elaborazione. Un errore improvviso di un'analisi con esecuzione prolungata può risultare frustrante per gli utenti. Per evitare questa situazione, è necessario scegliere valori restituiti predefiniti in grado di limitare i danni nell'elaborazione a valle. Viene inoltre generato un messaggio di avviso per informare gli utenti che si è verificato un errore.

Se non si usa la programmazione difensiva in R, tutto questo codice potrebbe essere travolgente. Verranno esaminati i passaggi principali:

1. Viene definito un vettore di quattro messaggi. Questi messaggi vengono usati per comunicare informazioni relative a errori ed eccezioni che possono verificarsi nel codice.
1. Viene restituito il valore NA per ogni case. Esistono diverse altre possibilità che potrebbero avere un minor numero di effetti collaterali. È possibile, ad esempio, restituire un vettore di zeri o il vettore di input originale.
1. Vengono eseguiti controlli sugli argomenti alla funzione. In ogni caso, se viene rilevato un errore, la funzione `warning()` restituisce un valore predefinito e un messaggio. Si sta usando `warning()` anziché `stop()` perché il secondo terminerà l'esecuzione, che è quello che si sta tentando di evitare. Questo codice è scritto in uno stile procedurale, perché in questo caso un approccio funzionale sembra complesso e oscuro.
1. I calcoli dei log sono racchiusi in in `tryCatch()` modo che le eccezioni non provochino un'interruzione brusca dell'elaborazione. Senza `tryCatch()` , la maggior parte degli errori generati dalle funzioni R comporta un segnale di arresto.

Eseguire questo codice R nel proprio esperimento e osservare l'output generato nel file output.log. Verranno ora visualizzati i valori trasformati delle quattro colonne nel log, come illustrato di seguito.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

I valori sono stati trasformati correttamente. La produzione di latte ora supera molto la produzione di tutti gli altri prodotti caseari, richiamando la scalabilità del log.

A questo punto, i dati vengono puliti e sono pronti per la modellazione. Se si esamina il riepilogo della visualizzazione per l'output del set di dati dei risultati del modulo [Execute R script (Esegui script R][execute-r-script] ), si noterà che la colonna Month è categorica con 12 valori univoci, che è esattamente quello che volevamo.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Oggetti della serie temporale e analisi delle correlazioni

In questa sezione verranno esaminati alcuni oggetti serie temporali di base di R e verranno analizzate le correlazioni tra alcune variabili. L'obiettivo è quello di generare un frame di dati contenente le informazioni di correlazione pairwise in diversi ritardi.

Il codice R completo per questa sezione è in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Oggetti della serie temporale in R

Come già accennato, le serie temporali sono costituite da una serie di valori di dati indicizzati per data e ora. Gli oggetti serie temporale vengono usati in R per creare e gestire l'indice temporale. Questi oggetti offrono infatti una serie di vantaggi. Gli oggetti della serie temporale consentono di liberarsi delle numerose attività di gestione dei valori di indice della serie temporale incapsulati nell'oggetto. Inoltre, gli oggetti della serie temporale consentono di usare i molti metodi della serie temporale per tracciare, stampare, modellare e così via.

In genere viene usata la classe di serie temporale POSIXct poiché è relativamente semplice e permette di misurare il tempo a partire dal 1° gennaio 1970. In questo esempio verranno usati gli oggetti della serie temporale POSIXct. Altre classi di oggetti della serie temporale R molto diffuse includono Zoo e XTS (Extensible Time Series).

### <a name="time-series-object-example"></a>Esempio di oggetto della serie temporale

Iniziamo con il nostro esempio. Trascinare un nuovo modulo [Execute R script][execute-r-script] nell'esperimento. Connettere la porta di output Result Dataset1 del modulo [Execute R Script][execute-r-script] esistente alla porta di input Dataset1 del nuovo modulo [Execute R Script][execute-r-script].

Come per i primi esempi, si procederà nell'esempio. In alcuni punti verranno mostrate solo le righe aggiuntive incrementali del codice R in ogni passaggio.

#### <a name="read-the-dataframe"></a>Leggere il frame di frame

Come prima operazione, eseguiamo la lettura in un frame di dati e accertiamoci di ottenere i risultati previsti. A questo scopo, usare il codice seguente.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Eseguiamo ora l'esperimento. Il log della nuova forma Execute R script dovrebbe essere simile a questo esempio.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

I dati sono dei tipi e nel formato previsti. La colonna Month è ora di tipo Factor e presenta il numero previsto di livelli.

#### <a name="create-a-time-series-object"></a>Creazione di un oggetto serie temporale

È necessario aggiungere un oggetto serie temporale al nostro frame di dati. Sostituire il codice corrente con il codice seguente, che aggiunge una nuova colonna della classe POSIXct.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

A questo punto, controllare il log. Dovrebbe essere simile a questo esempio.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Dal riepilogo è possibile notare come la nuova colonna sia effettivamente di classe POSIXct.

### <a name="explore-and-transform-the-data"></a>Esplorare e trasformare i dati

In questa sezione vengono descritte alcune delle variabili in questo set di dati. Una matrice di grafici a dispersione è un modo efficace per produrre un rapido aspetto. La `str()` funzione nel codice R precedente verrà sostituita con la riga seguente.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Eseguire questo codice e vedere cosa accade. Il tracciato prodotto alla porta R device dovrebbe essere simile a questo esempio.

![Screenshot che mostra la matrice di grafici a dispersione delle variabili selezionate.](./media/r-quickstart/fig17.png)

Esiste una struttura dispari nelle relazioni tra queste variabili. Forse questa struttura deriva dalle tendenze nei dati e dal fatto che non sono state standardizzate le variabili.

### <a name="correlation-analysis"></a>Analisi delle correlazioni

Per eseguire l'analisi delle correlazioni, è necessario sia la detendenza che la standardizzazione delle variabili. È possibile usare semplicemente la funzione `scale()` di R, che centra e scala le variabili e può essere eseguita più velocemente. Tuttavia, esaminiamo un esempio di programmazione difensiva in R.

La `ts.detrend()` funzione seguente esegue entrambe queste operazioni. Le due seguenti righe di codice consentono di detrendizzare i dati e standardizzare i valori.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Si è verificato un po' nella `ts.detrend()` funzione. Gran parte del codice è progettato per controllare la presenza di potenziali problemi con gli argomenti o per gestire le eccezioni, che possono sempre verificarsi durante i calcoli. Solo poche righe del codice, quindi, eseguono effettivamente i calcoli.

È già stato illustrato un esempio di programmazione difensiva nelle trasformazioni di valori. Entrambi i blocchi di calcolo vengono inclusi in `tryCatch()`. Per alcuni errori è opportuno restituire il vettore di input originale. In altri casi, viene restituito un vettore di zeri.

Osservare come la regressione lineare usata per il detrending sia una regressione in serie temporale e la variabile indipendente un oggetto serie temporale.

Dopo che `ts.detrend()` è stato definito, viene applicato alle variabili di interesse nel dataframe. È necessario assegnare l'elenco risultante creato da `lapply()` ai dati nel frame di dati tramite `as.data.frame()` . A causa degli aspetti difensivi di `ts.detrend()` , l'impossibilità di elaborare una delle variabili non impedisce l'elaborazione corretta degli altri.

L'ultima riga di codice crea un grafico a dispersione pairwise. Dopo aver eseguito il codice R, i risultati del grafico a dispersione vengono visualizzati qui.

![Screenshot che mostra il tracciato a dispersione pairwise della serie temporale detrended e standardizzata.](./media/r-quickstart/fig18.png)

È possibile confrontare questi risultati con quelli mostrati nell'esempio precedente. Dopo aver eseguito il detrending e standardizzato le variabili, la struttura che rappresenta le relazioni tra le variabili è notevolmente semplificata.

Il codice per calcolare le correlazioni come oggetti CCF di R è il seguente.

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

L'esecuzione di questo codice produce il log riportato qui.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

È presente un valore di correlazione per ogni intervallo. Nessuno di essi, tuttavia, è abbastanza grande da essere significativo. È possibile concludere che è possibile modellare ogni variabile in modo indipendente.

### <a name="output-a-dataframe"></a>Output di un frame di dati
Sono state calcolate le correlazioni pairwise come un elenco di oggetti di R CCF. Questo determina tuttavia un piccolo problema poiché la porta di output Result Dataset richiede inevitabilmente un frame di dati. Inoltre, l'oggetto CCF è a sua volta un elenco e si desiderano solo i valori nel primo elemento dell'elenco, ovvero le correlazioni nei vari ritardi.

Il codice seguente estrae i valori di intervallo dall'elenco di oggetti CCF, che sono essi stessi degli elenchi.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

La prima riga di codice è un po' complicata e alcune spiegazioni potrebbero essere utili per comprenderlo. Lavorando dall'interno all'esterno, abbiamo:

1. L'operatore **[[** con l'argomento **1** seleziona il vettore delle correlazioni in corrispondenza dei ritardi dal primo elemento dell'elenco di oggetti CCF.
1. La funzione `do.call()` applica la funzione `rbind()` agli elementi dell'elenco restituito da `lapply()`.
1. La funzione `data.frame()` forza il risultato prodotto da `do.call()` in un frame di dati.

I nomi di riga sono in una colonna del frame di dati. In questo modo, i nomi delle righe vengono conservati quando vengono restituiti dall' [esecuzione dello script R][execute-r-script].

L'esecuzione del codice produce l'output visualizzato qui quando si seleziona **Visualizza** per visualizzare l'output nella porta del set di dati risultante. I nomi di riga sono nella prima colonna, come previsto.

![Screenshot che mostra l'output dei risultati dall'analisi di correlazione.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Esempio di serie temporale: previsioni stagionali

I dati sono ora in un formato appropriato per l'analisi e sono stati rilevati non esistono correlazioni significative tra le variabili. Passiamo quindi alla creazione di un modello di previsione in serie temporale, Con questo modello si prevede di prevedere la produzione di latte in California per i 12 mesi 2013.

Il nostro modello previsionale sarà costituito da due componenti: trend e stagionale. La previsione completa è data dal prodotto di queste due componenti. Questo tipo di modello prende il nome di modello moltiplicativo, alternativo al modello additivo. È già stata applicata una trasformazione log alle variabili di interesse, che rendono questa analisi gestibile.

Il codice R completo per questa sezione è in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>Creare il frame di frame per l'analisi

Per iniziare, aggiungere un nuovo modulo [Execute R script (Esegui script R][execute-r-script] ) all'esperimento. Connettere l'output Result Dataset del modulo [Execute R Script][execute-r-script] all'input **Dataset1** del nuovo modulo. Il risultato dovrebbe essere simile a questo esempio.

![Diagramma che illustra l'esperimento con il nuovo modulo Execute R script aggiunto.](./media/r-quickstart/fig21.png)

Come per l'analisi di correlazione appena completata, dobbiamo aggiungere una colonna con oggetto serie temporale POSIXct. Nel codice seguente viene aggiunta la colonna.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Eseguire questo codice ed esaminare il log. Il risultato dovrebbe essere simile a questo esempio.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Con questo risultato, è possibile iniziare l'analisi.

### <a name="create-a-training-dataset"></a>Creare un set di dati di training

Con il frame di dati costruito, è necessario creare un set di dati di training. che comprenderà tutte le osservazioni, ad eccezione delle ultime 12, relative al 2013, che corrispondono al nostro set di dati di test. Il codice seguente suddivide il frame di dati in sottoinsiemi e crea grafici della produzione casearia e delle variabili di prezzo. Vengono quindi creati i tracciati delle quattro variabili di produzione e di prezzo. Una funzione anonima viene usata per definire alcuni argomenti per il tracciato e quindi eseguire l'iterazione nell'elenco degli altri due argomenti con `Map()`. Se si sta pensando che un ciclo for avrebbe funzionato correttamente qui, si è corretti. Tuttavia, poiché R è un linguaggio funzionale, si sta esaminando un approccio funzionale.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

L'esecuzione del codice produce la serie di tracciati della serie temporale dall'output del dispositivo R illustrato qui. L'asse temporale si trova in unità di date, che è un vantaggio notevole del metodo del tracciato della serie temporale.

![Primo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi.](./media/r-quickstart/unnamed-chunk-161.png)

![Secondo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi.](./media/r-quickstart/unnamed-chunk-162.png)

![Terzo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi.](./media/r-quickstart/unnamed-chunk-163.png).

![Quarto tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Modello di tendenza

Ora che è stato creato un oggetto serie temporale ed è stato esaminato i dati, iniziamo a costruire un modello di tendenza per i dati di produzione di latte in California. È possibile usare una regressione in serie temporali. È chiaro dal tracciato che è necessario avere più di una pendenza e intercettare per modellare accuratamente la tendenza osservata nei dati di training.

Data la scala ridotta dei dati, il modello viene compilato per la tendenza in RStudio, quindi il modello risultante viene tagliato e incollato in Machine Learning Studio (classico). RStudio fornisce infatti un ambiente interattivo adatto a questo tipo di analisi interattiva.

Come primo tentativo, si tenterà una regressione polinomiale con potenze fino a tre. C'è un vero rischio di overfitting di questi tipi di modelli. È preferibile evitare termini di ordine elevato. La `I()` funzione impedisce l'interpretazione del contenuto (interpreta il contenuto così come è) e consente di scrivere una funzione letteralmente interpretata in un'equazione di regressione.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Questa funzione genera l'output seguente.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 **_
## Time              1.63e-09   1.72e-10    9.47   <2e-16 _*_
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 _  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

Da P values ( `Pr(>|t|)` ) in questo output, possiamo notare che il termine quadrato potrebbe non essere significativo. Si userà la `update()` funzione per modificare questo modello eliminando il termine quadrato.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Questa funzione genera l'output seguente.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 **_
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Questo output ha un aspetto migliore. Tutti i termini sono significativi. Il valore 2e-16 è un valore predefinito e non deve essere preso in considerazione.  

Come test di integrità, effettueremo un grafico in serie temporale dei dati della produzione casearia in California con la curva di trend mostrata di seguito. Il codice seguente è stato aggiunto nel Machine Learning Studio (classico) [Execute R script][execute-r-script] Model (non rstudio) per creare il modello ed eseguire un tracciato. Il risultato è illustrato nell'esempio seguente.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dati sulla produzione di latte in California con il modello di tendenza visualizzato.](./media/r-quickstart/unnamed-chunk-18.png)

Sembra che il modello di trend si adatti perfettamente. Inoltre, non sembra esserci una prova di overfitting, ad esempio le Wiggle dispari nella curva del modello.

### <a name="seasonal-model"></a>Modello con stagionalità

Con un modello di tendenza disponibile, è necessario proseguire includendo gli effetti stagionali. Il mese dell'anno verrà utilizzato come variabile fittizia nel modello lineare per acquisire l'effetto mensile. Quando si introducono variabili di fattore in un modello, l'intercettazione non deve essere calcolata. Se non si esegue questa operazione, la formula viene eccessivamente specificata e R eliminerà uno dei fattori desiderati, ma manterrà il termine di intercettazione.

Poiché è presente un modello di tendenza soddisfacente, è possibile usare la `update()` funzione per aggiungere i nuovi termini al modello esistente. Nella formula di aggiornamento, il valore -1 elimina il termine dell'intercetta. Continuiamo ora in RStudio.

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Questa funzione genera l'output seguente.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 **_
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 _*_
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 _*_
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 _*_
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 _*_
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 _*_
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 _*_
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 _*_
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 _*_
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 _*_
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 _*_
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Vediamo come nel modello non sia più presente un termine dell'intercetta ma siano inclusi 12 fattori mese, Questo risultato è esattamente quello che volevamo vedere.

Creiamo adesso un altro grafico in serie temporale dei dati di produzione casearia in California per verificare l'effettivo funzionamento del modello con stagionalità. È stato aggiunto il codice seguente nello [script di esecuzione R][execute-r-script] di Machine Learning Studio (classico) per creare il modello ed eseguire un tracciato.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

L'esecuzione di questo codice in Machine Learning Studio (classico) produce il tracciato illustrato qui.

![Produzione di latte in California con un modello che include gli effetti stagionali.](./media/r-quickstart/unnamed-chunk-20.png)

L'adattamento ai dati illustrati in questo esempio è piuttosto incoraggiante. Sia il trend sia l'effetto stagionale (variazione mensile) sembrano ragionevoli.

Come ulteriore verifica sul mostro modello, esaminiamo i valori residui. Il codice seguente calcola i valori previsti da due modelli, calcola i valori residui per il modello stagionale e infine esegue il traccia di tali valori per i dati di training.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Il tracciato residuo viene visualizzato qui.

![Residui del modello stagionale per i dati di training.](./media/r-quickstart/unnamed-chunk-21.png)

Questi valori residui sembrano ragionevoli. Non esiste alcuna struttura particolare, ad eccezione dell'effetto della ricessione 2008-2009, che il modello non tiene conto particolarmente bene.

Il tracciato illustrato in questo esempio è utile per rilevare eventuali modelli dipendenti dal tempo nei residui. L'approccio esplicito per calcolare e tracciare i residui utilizzati pone i residui nell'ordine temporale sul tracciato. Se avessimo tracciato `milk.lm$residuals` , il tracciato non sarebbe stato in ordine temporale.

È anche possibile usare `plot.lm()` per produrre una serie di tracciati diagnostici.

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Questo codice produce una serie di tracciati diagnostici illustrati negli esempi seguenti.

![Primo tracciato diagnostico per il modello stagionale.](./media/r-quickstart/unnamed-chunk-221.png)

![Secondo tracciato diagnostico per il modello stagionale.](./media/r-quickstart/unnamed-chunk-222.png)

![Terzo tracciato diagnostico per il modello stagionale.](./media/r-quickstart/unnamed-chunk-223.png)

![Quarto tracciato diagnostico per il modello stagionale.](./media/r-quickstart/unnamed-chunk-224.png)

In questi grafici sono presenti alcuni punti di alta influenza, ma niente di particolarmente preoccupante. Inoltre, è possibile osservare dal normale tracciato Q-Q che i residui sono vicini alla distribuzione normalmente, un presupposto importante per i modelli lineari.

### <a name="forecasting-and-model-evaluation"></a>Previsione e valutazione del modello

Per completare l'esempio è sufficiente un'altra operazione. elaborare le previsioni e valutare il grado di errore rispetto ai dati effettivi. La nostra previsione riguarda i 12 mesi del 2013. È possibile calcolare una misura di errore per questa previsione con i dati effettivi che non fanno parte del set di dati di training. Possiamo inoltre confrontare le prestazioni dei 18 anni di dati di addestramento con i 12 mesi dei dati di test.

Per misurare le prestazioni di modelli in serie temporale viene usata, in genere, una serie di parametri. In questo caso, verrà usato l'errore radice radice media (RMS). La funzione seguente calcola l'errore RMS tra due serie.

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Come per la `log.transform()` funzione descritta nella sezione "trasformazioni di valori", in questa funzione sono presenti numerosi controlli degli errori e codice di recupero delle eccezioni. I principi usati sono gli stessi. Il lavoro viene eseguito in due posizioni incluse in `tryCatch()`. In primo luogo, le serie temporali sono exponentiated, perché abbiamo lavorato con i log dei valori. Passeremo quindi all'elaborazione della deviazione standard.

Dotato di una funzione per misurare l'errore RMS, compilare e restituire un dataframe che contiene gli errori RMS. Verranno inclusi solo i termini per il modello di tendenza e il modello completo con fattori stagionali. Il codice seguente esegue il processo usando i due modelli lineari costruiti.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

L'esecuzione di questo codice produce l'output visualizzato qui nella porta di output del set di risultati.

![Screenshot che mostra il confronto degli errori RMS per i modelli.](./media/r-quickstart/fig26.png)

Da questi risultati si evince che l'aggiunta di fattori stagionali al modello riduce in modo significativo l'errore RMS. Come previsto, inoltre, la deviazione standard per i dati di addestramento è leggermente inferiore rispetto alla previsione.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Guida alla documentazione di RStudio

RStudio è ben documentato. Di seguito sono riportati alcuni collegamenti alle sezioni principali della documentazione di RStudio per iniziare.

* **Creare progetti**: è possibile organizzare e gestire il codice R in progetti usando rstudio. Per ulteriori informazioni, vedere [utilizzo di progetti](https://support.rstudio.com/hc/articles/200526207-Using-Projects). Seguire queste istruzioni e creare un progetto per gli esempi di codice R in questo articolo.
* **Modificare ed eseguire codice r**: rstudio fornisce un ambiente integrato per la modifica e l'esecuzione di codice r. Per altre informazioni, vedere [modifica ed esecuzione di codice](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Debug**: rstudio include potenti funzionalità di debug. Per ulteriori informazioni su queste funzionalità, vedere [debug con rstudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio). Per informazioni sulle funzionalità di risoluzione dei problemi, vedere [risoluzione dei problemi](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)del punto di interruzione.

## <a name="further-reading"></a><a id="appendixb"></a>Ulteriori informazioni

Questa esercitazione sulla programmazione R illustra le nozioni di base di ciò che è necessario per usare il linguaggio R con Machine Learning Studio (classico). Se non si ha familiarità con R, sono disponibili due introduzioni in CRAN:

* [R per principianti](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) di Emmanuel Paradis è un valido punto di partenza.
* [Introduzione a R](https://cran.r-project.org/doc/manuals/R-intro.html) da W. N. Venables et al. entra in maggiore profondità.

Sono disponibili molti libri su R che consentono di iniziare:

* **Arte della programmazione r: una panoramica della progettazione di software statistici** di Norman Matloff è un'ottima introduzione alla programmazione in R.
* **R Cookbook** di Paul Teetor offre un approccio a problemi e soluzioni per l'uso di r.
* **R in azione** di Robert Kabacoff è un altro interessante libro introduttivo. Il [sito Web Quick R](https://www.statmethods.net/) complementare è una risorsa utile.
* **R inferno** di Patrick Burns è un libro sorprendentemente umoristico che tratta una serie di argomenti complessi e difficili che possono verificarsi durante la programmazione in R. Il libro è disponibile gratuitamente a [R inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* **Advanced r** di Hadley Wickham fornisce un'approfondita comprensione degli argomenti avanzati in r. La versione online di questo libro è disponibile gratuitamente presso [Advanced R](http://adv-r.had.co.nz/).
* La **serie temporale introduttiva con r** di Paul Cowpertwait e Andrew Metcalfe offre un'introduzione all'uso di r per l'analisi delle serie temporali. Numerosi esempi di R sono infine disponibili in una grande quantità di testi teorici.

Di seguito sono riportate alcune risorse Internet eccezionali:

* [Visualizzazione attività Cran: l'analisi della serie temporale](https://cran.r-project.org/web/views/TimeSeries.html) include un catalogo dei pacchetti di serie temporali R. Per informazioni su specifici pacchetti di oggetti della serie temporale, vedere la documentazione relativa a tale pacchetto.
* [Introduzione a r](https://www.datacamp.com/courses/introduction-to-r) è un corso interattivo gratuito di datacamp che insegna a r nella comodità del browser con lezioni video e esercizi di codifica. Sono disponibili anche esercitazioni interattive sulle ultime tecniche e pacchetti di R.
* [Informazioni sulla programmazione R, la guida definitiva](https://www.datamentor.io/r-programming/) di datamentor.
* [R Coder](https://r-coder.com/) ha esercitazioni dettagliate su r e un corso di r gratuito per principianti.
* [R tutorial](https://www.cyclismo.org/tutorial/R/) di Kelly Black di Clarkson University è un'esercitazione rapida.
* [Le principali risorse di linguaggio R per migliorare le competenze sui dati](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) sono elencate più di 60 risorse r.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
