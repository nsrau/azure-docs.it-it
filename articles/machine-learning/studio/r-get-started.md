---
title: Introduzione a R
titleSuffix: ML Studio (classic) - Azure
description: Usare questa esercitazione sulla programmazione R per iniziare a usare il linguaggio R con Azure Machine Learning Studio (classico) per creare una soluzione di previsione.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: f55b6e743ff82d4192cbdd91ba54c92efef432bc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838735"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Introduzione al linguaggio di programmazione R in Azure Machine Learning Studio (versione classica)

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introduzione

Questa esercitazione consente di iniziare a estendere Azure Machine Learning Studio (classico) usando il linguaggio di programmazione R. Seguire questa esercitazione sulla programmazione R per creare, testare ed eseguire codice R in studio (classico). Quando si esegue l'esercitazione, si creerà una soluzione di previsione completa usando il linguaggio R nella versione classica di studio.  

La versione classica di Azure Machine Learning Studio contiene molti moduli avanzati di machine learning e di manipolazione dei dati. Il potente linguaggio R è stato definito come la lingua franca dell'analisi. Fortunatamente, le analisi e la manipolazione dei dati nella versione classica di studio possono essere estese usando R. Questa combinazione fornisce la scalabilità e la facilità di distribuzione della versione classica di studio con la flessibilità e l'analisi approfondita di R.

### <a name="forecasting-and-the-dataset"></a>Previsione e set di dati

La previsione è un metodo analitico molto utile e ampiamente distribuito. Viene comunemente usata per vari scopi, dalla previsione di vendita di articoli stagionali alla definizione dei livelli di scorte ottimali, fino alla previsione di variabili macroeconomiche. In genere, inoltre, viene eseguita con modelli in serie temporale.

I dati delle serie temporali sono dati i cui valori contengono un indice temporale, che può essere regolare, ad esempio ogni minuto od ogni mese, o irregolare. Sui dati in serie temporale si basano i modelli in serie temporale. Il linguaggio di programmazione R contiene un'architettura flessibile ed estese funzioni di analisi per i dati in serie temporale.

In questa guida si lavorerà con i dati relativi alla produzione casearia in California e ai prezzi. Questi dati includono informazioni mensili sulla produzione di diversi prodotti caseari e il prezzo del latte intero, un materia prima di riferimento.

I dati usati in questo articolo, insieme agli script R, possono essere scaricati da [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). I dati nel file `cadairydata.csv` sono stati originariamente sintetizzati dalle informazioni disponibili presso la University of Wisconsin al [https://dairymarkets.com](https://dairymarkets.com).

### <a name="organization"></a>Organizzazione

In questo articolo verrà illustrato come creare, testare ed eseguire le analisi e la manipolazione dei dati codice R nella versione classica dell'ambiente Azure Machine Learning Studio.  

* Si esamineranno prima di tutto le nozioni di base sull'uso del linguaggio R nella versione classica di Azure Machine Learning Studio ambiente.
* Si procede quindi alla discussione di diversi aspetti dell'I/O per i dati, del codice R e della grafica nella versione classica di Azure Machine Learning Studio ambiente.
* In seguito, verrà creata la prima parte della soluzione di previsione tramite la compilazione di codice per la pulizia e la trasformazione dei dati.
* Con i dati preparati verrà eseguita un'analisi delle correlazioni tra più variabili nel set di dati.
* Infine, verrà creato un modello di previsione in serie temporale (stagionale) per la produzione di latte.

## <a id="mlstudio"></a>Interagire con il linguaggio R in Machine Learning Studio (versione classica)

Questa sezione illustra alcune nozioni di base sull'interazione con il linguaggio di programmazione R nell'ambiente Machine Learning Studio (classico). Il linguaggio R offre uno strumento potente per creare moduli personalizzati di analisi e manipolazione dei dati all'interno della versione classica dell'ambiente Azure Machine Learning Studio.

In questa guida useremo RStudio per sviluppare, testare ed eseguire il debug di codice R su piccola scala. Questo codice viene quindi tagliato e incollato in un modulo [Execute R script][execute-r-script] pronto per l'esecuzione nella versione classica di Machine Learning Studio.  

### <a name="the-execute-r-script-module"></a>Modulo Execute R Script

All'interno della versione classica di Machine Learning Studio, gli script R vengono eseguiti nel modulo [Execute R script][execute-r-script] . Un esempio del modulo [Execute R script][execute-r-script] nella versione classica di Machine Learning Studio è illustrato nella figura 1.

 ![Linguaggio di programmazione r: il modulo Execute R script selezionato in Machine Learning Studio (classico)](./media/r-quickstart/fig1.png)

*Figura 1. L'ambiente Machine Learning Studio (classico) che mostra il modulo Execute R script selezionato.*

Per fare riferimento alla figura 1, è possibile esaminare alcune delle parti principali dell'ambiente Machine Learning Studio (classico) per l'uso del modulo [Execute R script][execute-r-script] .

* I moduli dell'esperimento vengono visualizzati nel riquadro centrale.
* La finestra presente nella parte superiore del riquadro di destra consente di visualizzare e modificare gli script R,  
* Nella parte inferiore del riquadro destro vengono visualizzate alcune proprietà dello [script Execute R][execute-r-script]. È possibile visualizzare i log di errore e di output selezionando le aree appropriate di questo riquadro.

Si tratterà, ovviamente, di discutere lo [script Execute R][execute-r-script] in modo più dettagliato nella parte restante di questo articolo.

Quando si usano funzioni R complesse, è preferibile effettuare le operazioni di modifica, test e debug in RStudio. Come con qualsiasi altro software di sviluppo, inoltre, è opportuno estendere il codice in modo incrementale, così da poterlo verificare in piccoli e semplici casi di test. Quindi tagliare e incollare le funzioni nella finestra script R del modulo [Execute R script][execute-r-script] . Questo approccio consente di sfruttare sia il Integrated Development Environment RStudio (IDE) che la potenza della versione classica di Azure Machine Learning Studio.  

#### <a name="execute-r-code"></a>Eseguire il codice R

Qualsiasi codice R nel modulo [Execute R script][execute-r-script] verrà eseguito quando si esegue l'esperimento selezionando il pulsante **Run (Esegui** ). Al termine dell'esecuzione, nell'icona [Esegui script R][execute-r-script] verrà visualizzato un segno di spunta.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Codice R difensivo per Azure Machine Learning

Se si sta sviluppando codice R per un servizio Web con la versione classica di Azure Machine Learning Studio, è necessario pianificare in modo sicuro il modo in cui il codice gestirà un input di dati e le eccezioni imprevisti. Per maggiore chiarezza, per gli esempi di codice riportati in questa guida non tratterò in modo approfondito le modalità di verifica e gestione delle eccezioni. Tuttavia, verranno forniti diversi esempi di funzioni che prevedono l'uso della capacità di gestione delle eccezioni di R.  

Se è necessario un trattamento più completo della gestione delle eccezioni di R, è consigliabile leggere le sezioni applicabili del libro di Wickham elencate di seguito in [un'ulteriore lettura](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Eseguire il debug e il test di R in Machine Learning Studio (versione classica)

Come già anticipato, in genere è consigliabile eseguire il test e il debug del codice R creato su scala ridotta in RStudio. Tuttavia, in alcuni casi è necessario tenere traccia dei problemi relativi al codice R nello [script Execute R][execute-r-script] . Inoltre, è consigliabile verificare i risultati nella versione classica di Machine Learning Studio.

L'output dell'esecuzione del codice R e della versione classica di Azure Machine Learning Studio Platform si trova principalmente in output. log. ma alcune informazioni aggiuntive possono essere presenti in error.log.  

Se si verifica un errore nella versione classica di Machine Learning Studio durante l'esecuzione del codice R, il primo corso di azione dovrebbe essere esaminare Error. log. Questo file può contenere messaggi di errore utili a comprendere e correggere l'errore. Per visualizzare Error. log, selezionare **Visualizza log degli errori** nel **riquadro Proprietà** per lo [script Execute R][execute-r-script] contenente l'errore.

Ad esempio, ho eseguito il codice R seguente con una variabile y non definita in un modulo [Execute R script][execute-r-script] :

```R
x <- 1.0
z <- x + y
```

L'esecuzione del codice non riesce, generando così una condizione di errore. Selezionando **Visualizza log degli errori** nel **riquadro Proprietà** si produce la visualizzazione mostrata nella figura 2.

  ![Finestra di popup con messaggio di errore](./media/r-quickstart/fig2.png)

*Figura 2. Finestra di popup con il messaggio di errore.*

Per visualizzare il messaggio di errore di R, è necessario usare output.log. Selezionare [Esegui script R][execute-r-script] e quindi selezionare l'elemento **Visualizza output. log** nel **riquadro proprietà** a destra. Viene aperta una nuova finestra del browser e viene visualizzato quanto segue.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Questo messaggio di errore non contiene sorprese e identifica chiaramente il problema.

Per ispezionare il valore dei singoli oggetti in R; è possibile stampare i valori nel file output.log. Le regole per l'analisi dei valori di oggetto sono essenzialmente le stesse di una sessione R interattiva. Se, ad esempio, si digita un nome di variabile in una riga, il valore dell'oggetto verrà stampato nel file output.log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pacchetti in Machine Learning Studio (versione classica)

Studio viene fornito con più di 350 pacchetti di linguaggio R preinstallati. È possibile usare il codice seguente nel modulo [Execute R script][execute-r-script] per recuperare un elenco dei pacchetti preinstallati.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se al momento non si riesce a comprendere l'ultima riga del codice, continuare a leggere. Nella parte restante di questo articolo verrà illustrata dettagliatamente l'uso di R nell'ambiente di studio (classico).

### <a name="introduction-to-rstudio"></a>Introduzione a RStudio

RStudio è un IDE ampiamente usato per R. Utilizzerò RStudio per la modifica, il test e il debug di parte del codice R usato in questa guida. Una volta testato e pronto il codice R, è possibile semplicemente tagliare e incollare dall'editor RStudio in un modulo Machine Learning Studio (classico) [Execute R script][execute-r-script] .  

Se il linguaggio di programmazione R non è installato nel computer desktop, eseguirne ora l'installazione. Download gratuiti del linguaggio di programmazione R open source sono disponibili nel sistema CRAN (Comprehensive R Archive Network) all'indirizzo [https://www.r-project.org/](https://www.r-project.org/). Sono disponibili anche download per Windows, Mac OS e Linux/UNIX. Scegliere un mirror vicino e seguire le istruzioni di download. Il sistema CRAN contiene anche una serie di utili pacchetti di analisi e manipolazione di dati.

Se non si ha familiarità con RStudio, è consigliabile scaricare e installare la versione desktop. I download di RStudio per Windows, Mac OS e Linux/UNIX sono disponibili all'indirizzo http://www.rstudio.com/products/RStudio/. Seguire le indicazioni fornite per installare RStudio sul proprio desktop.  

Un'esercitazione introduttiva a RStudio è disponibile in [uso dell'IDE di rstudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Sono disponibili altre informazioni sull'uso di RStudio nella [Guida alla documentazione di rstudio riportata di](#appendixa) seguito.  

## <a id="scriptmodule"></a>Input e output dei dati nel modulo Execute R Script

In questa sezione verrà illustrato come ottenere i dati all'interno e all'esterno del modulo [Execute R script][execute-r-script] . Verrà esaminato come gestire i diversi tipi di dati in lettura e in uscita dal modulo [Execute R script][execute-r-script] .

Il codice completo per questa sezione è in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Caricare e controllare i dati in Machine Learning Studio (versione classica)

#### <a id="loading"></a>Caricare il set di dati

Si inizierà con il caricamento del file **csdairydata. csv** nella versione classica di Azure Machine Learning Studio.

1. Avviare la versione classica dell'ambiente Azure Machine Learning Studio.
1. Selezionare **+ nuovo** in basso a sinistra della schermata e selezionare **set di dati**.
1. Fare clic su **From Local File** (Da file locale) e quindi su **Browse** (Sfoglia) per selezionare il file.
1. Verificare di aver selezionato **Generic CSV file with header (.csv)** come tipo per il set di dati.
1. Selezionare il segno di spunta.
1. Dopo il caricamento del set di dati, verrà visualizzato il nuovo set di dati selezionando la scheda **set** di dati.  

#### <a name="create-an-experiment"></a>Creare un esperimento

Ora che i dati sono disponibili nella versione classica di Machine Learning Studio, è necessario creare un esperimento per eseguire l'analisi.  

1. Selezionare **+ nuovo** in basso a sinistra e selezionare **esperimento**, quindi **esperimento vuoto**.
1. È possibile denominare l'esperimento selezionandolo e modificando il titolo **Experiment created on...** nella parte superiore della pagina. Ad esempio, è possibile modificare il titolo in **CA Dairy Analysis**.
1. A sinistra della pagina dell'esperimento espandere **Saved Datasets** (Set di dati salvati) e quindi **My Datasets** (Set di dati personali). Verrà visualizzato il set di dati **cadairydata.csv** caricato in precedenza.
1. Trascinare **csdairydata.csv set di dati** nell'esperimento.
1. Nella casella **Cerca elementi dell'esperimento** nella parte superiore del riquadro sinistro digitare [Execute R script][execute-r-script]. Nell'elenco di ricerca viene visualizzato il modulo corrispondente.
1. Trascinare e rilasciare il modulo [Execute R script][execute-r-script] sul pallet.  
1. Connettere l'output del **set di dati csdairydata. csv** all'input più a sinistra (**DataSet1**) dello [script Execute R][execute-r-script].
1. **Non dimenticare di selezionare "Salva".**  

A questo punto, l'esperimento dovrebbe essere simile a quanto riportato nella Figura 3.

![L'esperimento CA Dairy Analysis con il set di dati e il modulo Execute R Script](./media/r-quickstart/fig3.png)

*Figura 3. Esperimento CA Dairy Analysis con il set di dati e il modulo Execute R Script.*

#### <a name="check-on-the-data"></a>Controllo dei dati

Diamo ora un'occhiata ai dati caricati nell'esperimento. Nell'esperimento selezionare l'output del **set di dati dati cadairydata. csv** e selezionare **Visualizza**. Dovrebbe apparire una tabella simile a quella riportata nella Figura 4.  

![Riepilogo del set di dati cadairydata.csv](./media/r-quickstart/fig4.png)

*Figura 4. Riepilogo del set di dati cadairydata.csv.*

In questa tabella sono presenti numerose informazioni utili. Vengono visualizzate diverse righe del set di dati. Se si seleziona una colonna, nella sezione relativa alle statistiche vengono visualizzate altre informazioni sulla colonna. La riga tipo di funzionalità, ad esempio, indica i tipi di dati a cui la versione classica di Azure Machine Learning Studio assegnata alla colonna. Avere un quadro d'insieme può essere di grande aiuto prima di iniziare la fase di analisi vera e propria.

### <a name="first-r-script"></a>Primo script R

Viene ora creato un semplice primo script R da provare nella versione classica di Azure Machine Learning Studio. Lo script seguente è stato creato e testato in RStudio.  

```R
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

A questo punto è necessario trasferire questo script alla versione classica di Azure Machine Learning Studio. Potrei semplicemente eseguire un'operazione di copia e incolla, ma in questo caso trasferirò lo script R tramite un file con estensione zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Input di dati nel modulo Execute R Script

Esaminiamo gli input per il modulo [Execute R script][execute-r-script] . In questo esempio vengono letti i dati caseari della California nel modulo [Execute R script][execute-r-script] .  

Sono disponibili tre possibili input per il modulo [Execute R script][execute-r-script] . È possibile usarne una o tutte, in base alle esigenze. È anche possibile usare uno script R senza alcun input.  

Di seguito vengono esaminati i singoli input, da sinistra a destra. È possibile visualizzare i nomi di ciascun input posizionando il cursore sull'input e leggendo la descrizione comando.  

#### <a name="script-bundle"></a>Script Bundle

L'input del bundle di script consente di passare il contenuto di un file zip al modulo [Execute R script][execute-r-script] . È possibile usare uno dei seguenti comandi per leggere i contenuti del file con estensione zip nel codice R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> La versione classica di Azure Machine Learning Studio considera i file nel file zip come se si trovassero nella directory src/, quindi è necessario anteporre al nome di tale directory i nomi di file. Ad esempio, se il file con estensione zip contiene i file `yourfile.R` e `yourData.rdata` nella radice del file con estensione zip, è necessario risolvere questi come `src/yourfile.R` e `src/yourData.rdata` quando si usano `source` e `load`.

È già stato illustrato il caricamento dei set di [dati in caricare il set di dati](#loading). Dopo aver creato e testato lo script R mostrato nella sezione precedente, procedere come segue:

1. Salvare lo script R in un file .R. Chiamerò il mio file di script "simpleplot.R". Ecco il contenuto.

   ```R
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

1. Creare un file con estensione zip e copiare al suo interno lo script creato. In Windows è possibile fare clic con il pulsante destro del mouse sul file e scegliere **Invia a**, quindi **cartella compressa**. Verrà creato un nuovo file con estensione zip contenente il file "simpleplot.R".

1. Aggiungere il file ai **set di impostazioni** nella versione classica di Machine Learning Studio, specificando il tipo come **zip**. Dovrebbe essere visualizzato il file con estensione zip nei set di dati.

1. Trascinare e rilasciare il file zip dai **set di impostazioni** nell' **area di disegno ml Studio (classica)** .

1. Connettere l'output dell'icona dei **dati zip** all'input del **bundle di script** del modulo [Execute R script][execute-r-script] .

1. Digitare la funzione `source()` con il nome del file zip nella finestra del codice per il modulo [Execute R script][execute-r-script] . In questo caso, digitare `source("src/simpleplot.R")`.  

1. Assicurarsi di selezionare **Salva**.

Una volta completati questi passaggi, il modulo [Execute r script][execute-r-script] eseguirà lo script r nel file zip durante l'esecuzione dell'esperimento. A questo punto l'esperimento dovrebbe avere un aspetto simile a quello riportato nella Figura 5.

![Esperimento con script R compresso](./media/r-quickstart/fig6.png)

*Figura 5. Esperimento con script R compresso.*

#### <a name="dataset1"></a>Dataset1

È possibile passare una tabella di dati rettangolare al codice R usando l'input Dataset1. Nello script semplice descritto, la funzione `maml.mapInputPort(1)` legge i dati dalla porta 1. I dati vengono poi assegnati a un nome di variabile del frame di dati nel codice. Nel nostro script semplice è la prima riga del codice a effettuare l'assegnazione.

```R
cadairydata <- maml.mapInputPort(1)
```

Eseguire l'esperimento selezionando il pulsante **Run (Esegui** ). Al termine dell'esecuzione, selezionare il modulo [Execute R script][execute-r-script] , quindi selezionare **Visualizza log di output** nel riquadro proprietà. Viene visualizzata una nuova pagina del browser che mostra i contenuti del file output.log. Scorrendo la pagina, dovrebbe essere visualizzato quanto segue.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Più in basso nella pagina sono disponibili altre informazioni sulle colonne, che avranno un aspetto simile al seguente.

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

Questi risultati sono essenzialmente quelli previsti, con 228 osservazioni e 9 colonne nel frame di dati. Sono riportati i nomi delle colonne, il tipo di dati R e un esempio di ciascuna colonna.

> [!NOTE]
> Questo stesso output stampato è comodamente disponibile nell'output del dispositivo R del modulo [Execute R script][execute-r-script] . Nella sezione successiva verranno illustrati gli output del modulo [Execute R script][execute-r-script] .  

#### <a name="dataset2"></a>Dataset2

Il comportamento della modalità di input Dataset2 è identico a quello di Dataset1. Consente infatti di passare nel codice R creato una seconda tabella rettangolare di dati. La funzione `maml.mapInputPort(2)`, con l'argomento 2, viene usata per passare questi dati.  

### <a name="execute-r-script-outputs"></a>Output del modulo Execute R Script

#### <a name="output-a-dataframe"></a>Output di un frame di dati

È possibile eseguire l'output dei contenuti di un frame di dati R come tabella rettangolare dalla porta Result Dataset1 usando la funzione `maml.mapOutputPort()` . Nello script R semplice descritto, questa operazione viene eseguita dalla riga seguente.

```
maml.mapOutputPort('cadairydata')
```

Dopo aver eseguito l'esperimento, selezionare la porta di output Result DataSet1 e quindi selezionare **Visualize (Visualizza**). I risultati dovrebbero essere simili a quanto visualizzato nella Figura 6.

![Visualizzazione dell'output dei dati caseari della California](./media/r-quickstart/fig7.png)

*Figura 6. Visualizzazione dell'output dei dati caseari della California.*

In questo caso l'output è identico all'input, come previsto.  

### <a name="r-device-output"></a>Output R Device

L'output del dispositivo del modulo [Execute R script][execute-r-script] contiene messaggi e output grafico. Alla porta di output R Device vengono inviati da R sia l'output standard sia i messaggi di errore standard.  

Per visualizzare l'output del dispositivo R, selezionare la porta e quindi su **Visualizza**. Nella Figura 7 sono riportati l'output standard e i messaggi di errore standard generati dallo script R.

![Output ed errore standard dalla porta R Device](./media/r-quickstart/fig8.png)

*Figura 7. Output ed errore standard dalla porta R Device.*

Scorrendo verso il basso è possibile visualizzare l'output grafico generato dallo script R, riportato nella Figura 8.  

![Output dei grafici dalla porta R Device](./media/r-quickstart/fig9.png)

*Figura 8. Output dei grafici dalla porta R Device.*  

## <a id="filtering"></a>Filtraggio e trasformazione dei dati

In questa sezione effettueremo alcune operazioni di filtraggio e trasformazione di base sui dati caseari della California. Al termine di questa sezione i dati saranno disponibili in un formato adatto alla creazione di un modello di analisi.  

In particolare, in questa sezione vengono eseguite diverse attività comuni di pulizia e trasformazione dei dati: trasformazione del tipo, filtraggio nei frame di dati, aggiunta di nuove colonne elaborate e trasformazioni dei valori. Queste procedure di base potrebbero essere utili per affrontare le numerose variazioni che spesso si generano nei problemi della vita reale.

Il codice R completo per questa sezione è disponibile in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Trasformazioni di tipi

Ora che è possibile leggere i dati caseari della California nel codice R nel modulo [Execute R script][execute-r-script] , è necessario assicurarsi che i dati nelle colonne abbiano il tipo e il formato previsti.  

R è un linguaggio tipizzato dinamicamente, in cui i tipi di dati vengono assegnati l'uno all'altro in base alle esigenze. Nel linguaggio R, i dati atomic possono essere di tipo numerico, logico e Char. Il tipo fattore viene usato invece per archiviare in modo compatto i dati relativi alle categorie. Per [ulteriori](#appendixb) informazioni sui tipi di dati nei riferimenti, vedere più avanti.

Quando i dati tabulari vengono letti in R da un'origine esterna, è sempre opportuno controllare nelle colonne i tipi risultanti. È possibile, ad esempio, che una colonna che sarebbe dovuta essere di tipo Char in realtà risulti di tipo fattore, o viceversa. In altri casi, una colonna che si pensa debba essere numerica viene rappresentata da dati di tipo carattere, ad esempio '1,23' anziché 1,23 come numero a virgola mobile.  

Fortunatamente, convertire un tipo in un altro tipo è piuttosto semplice, purché sia possibile il mapping. Ad esempio, non è possibile convertire "Nevada" in un valore numerico, ma è possibile convertirlo in un fattore (variabile di categoria). Un valore numerico 1, invece, può essere convertito sia nel carattere "1" sia in un fattore.  

La sintassi di queste conversioni è semplice: `as.datatype()`. Queste funzioni di conversione del tipo includono quanto segue.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Considerando i tipi di dati delle colonne di cui è stato eseguito l'input nella sezione precedente: tutte le colonne sono di tipo numerico, tranne quella etichettata 'Month', che è di tipo character. Proviamo quindi a convertire questa colonna in tipo fattore e controlliamo i risultati.  

La riga con cui è stata creata la matrice del grafico a dispersione è stata eliminata ed è stata aggiunta un riga per la conversione della colonna 'Month' in un fattore. Nel mio esperimento posso solo tagliare e incollare il codice R nella finestra del codice del modulo [Execute R script][execute-r-script] . È anche possibile aggiornare il file zip e caricarlo nella versione classica di Azure Machine Learning Studio, ma questa operazione richiede diversi passaggi.  

```R
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

Eseguiamo il codice e analizziamo il log di output per lo script R. I dati rilevanti del log sono illustrati nella Figura 9.

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

*Figura 9. Riepilogo del frame di dati con una variabile di fattore.*

Il tipo per Month sarà: '**Factor w/ 14 levels**'. Questo è ovviamente un problema poiché in un anno ci sono solo 12 mesi. È anche possibile controllare che in **Visualize** (Visualizza) il tipo della porta Result Dataset (Set di dati risultati) sia '**Categorical**' (Categorie).

Il problema è che la colonna "Month" non è stata codificata in modo sistematico. In alcuni casi, un mese viene chiamato aprile e in altri viene abbreviato come aprile. Per risolvere il problema, è possibile tagliare la stringa a 3 caratteri. La riga di codice dovrebbe ora apparire così:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Eseguire nuovamente l'esperimento e visualizzare il log di output. I risultati previsti sono illustrati nella Figura 10.  

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

*Figura 10. Riepilogo del frame di dati con il numero corretto di livelli di fattore.*

La variabile di fattore contiene ora 12 livelli.

### <a name="basic-data-frame-filtering"></a>Filtraggio di frame di dati di base

I frame di dati R supportano avanzate funzionalità di filtraggio. I set di dati, infatti, possono essere suddivisi in sottoinsiemi usando filtri logici su righe o colonne. In molto casi, tuttavia, sono necessari complessi criteri di filtro. I riferimenti in [ulteriori letture](#appendixb) di seguito contengono esempi completi di filtro dei frame di frame.  

Possiamo eseguire semplici operazioni di filtraggio anche sul nostro set di dati. Se si guardano le colonne nel frame di dati cadairydata, si può vedere che ci sono due colonne superflue. La prima colonna contiene solo un numero di riga, non molto utile. La seconda colonna, Year.Month, contiene le informazioni ridondanti. È possibile escludere facilmente queste colonne usando il seguente codice R.

> [!NOTE]
> Da questo punto in poi, illustrerò solo il codice aggiuntivo aggiunto nel modulo [Execute R script][execute-r-script] . Ogni nuova riga verrà aggiunta **prima** della funzione `str()`. Questa funzione viene usata per verificare i risultati nella versione classica di Azure Machine Learning Studio.

Aggiungo la riga seguente al codice R nel modulo [Execute R script][execute-r-script] .

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Eseguire questo codice nell'esperimento e verificare il risultato del log di output. Questi risultati vengono mostrati nella Figura 11.

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

*Figura 11. Riepilogo del frame di dati con due colonne rimosse.*

Ottime notizie! I risultati ottenuti sono quelli previsti.

### <a name="add-a-new-column"></a>Aggiungere una nuova colonna

Per la creazione di modelli in serie temporale sarà utile disporre di una colonna contenente i nomi dei mesi a partire dall'inizio della serie temporale. Creeremo quindi una nuova colonna denominata "Month.Count".

Per organizzare il codice, è necessario creare una prima funzione semplice, `num.month()`. La funzione verrà quindi applicata per creare una nuova colonna nel frame di dati. Il nuovo codice è il seguente.

```R
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

Eseguire l'esperimento aggiornato e usare il log di output per visualizzare i risultati. I risultati previsti sono illustrati nella Figura 12.

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

*Figura 12. Riepilogo del frame di dati con la colonna aggiuntiva.*

Sembra che tutto stia funzionando perfettamente. La nuova colonna con i valori previsti è stata creata correttamente nel frame di dati.

### <a name="value-transformations"></a>Trasformazioni di valori

In questa sezione eseguiremo semplici operazioni di trasformazione sui valori di alcune delle colonne del frame di dati. Il linguaggio R supporta trasformazioni di dati pressoché arbitrarie. I riferimenti in [ulteriori letture](#appendixb) di seguito contengono esempi completi.

Se si guardano i valori nei riepiloghi del frame di dati, si nota qualcosa di strano. In California si produce più gelato che latte? No, ovviamente no, sfortunatamente per gli amanti del gelato. Le unità di misura sono diverse. Il prezzo è espresso in unità di libbre statunitensi, il latte è espresso in unità da un milione di libbre statunitensi, il gelato in unità da 1000 galloni statunitensi e i fiocchi di latte in unità da 1000 libbre statunitensi. Supponendo che il gelato pesi circa 6,5 libbre al gallone, è facile eseguire la moltiplicazione per convertire questi valori in modo che siano espressi in unità da 1000 libbre.

Per il modello di previsione viene usato un modello moltiplicativo per le tendenze e l'adattamento stagionale dei dati. Una trasformazione logaritmica, infine, ci consente di usare un modello lineare, semplificando notevolmente il processo. Possiamo applicare la trasformazione logaritmica alla stessa funzione a cui è stato applicato il moltiplicatore.

Nel codice seguente viene definita una nuova funzione `log.transform()`, che viene poi applicata alle righe che contengono i valori numerici. La funzione R `Map()` viene usata per applicare la funzione `log.transform()` alle colonne selezionate del frame di dati. `Map()` è simile a `apply()`, ma permette l'uso di più di un elenco di argomenti nella funzione. Un elenco di moltiplicatori fornisce il secondo argomento alla funzione `log.transform()` . La funzione `na.omit()` viene usata come strumento di pulizia per assicurare che non ci siano valori mancanti o non definiti nel frame di dati.

```R
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
  ## If there is an exception, print the warningmessage to
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

La funzione `log.transform()` espleta quindi numerose operazioni. Gran parte del codice è progettato per controllare la presenza di potenziali problemi con gli argomenti o per gestire le eccezioni, che possono sempre verificarsi durante i calcoli. Solo poche righe del codice, quindi, eseguono effettivamente i calcoli.

L'obiettivo della programmazione difensiva consiste nel prevenire errori in una singola funzione che potrebbero bloccare l'intera elaborazione. Un errore improvviso in un'analisi di lunga durata può essere piuttosto frustrante per gli utenti. Per evitare questa situazione, è necessario scegliere valori restituiti predefiniti in grado di limitare i danni nell'elaborazione a valle. Viene inoltre generato un messaggio di avviso per informare gli utenti che si è verificato un errore.

Se non si ha familiarità con la programmazione difensiva in R, questo codice può sembrare eccessivamente complesso. Affronteremo di seguito i passaggi principali:

1. Viene definito un vettore di quattro messaggi. Questi messaggi vengono usati per comunicare informazioni relative a errori ed eccezioni che possono verificarsi nel codice.
2. Per ciascun caso restituirò il valore NA. Esistono diverse altre possibilità che potrebbero avere un minor numero di effetti collaterali. Ad esempio, potrei restituire un vettore di zeri o il vettore di input originale.
3. Vengono eseguiti controlli sugli argomenti alla funzione. In ogni caso, se viene rilevato un errore, la funzione `warning()` restituisce un valore predefinito e un messaggio. In questo caso viene usato `warning()` e non `stop()` perché quest'ultimo comporta l'arresto dell'esecuzione, che si vuole evitare. Osservare come abbia scritto questo codice in stile procedurale: un approccio funzionale sarebbe apparso in questo caso eccessivamente complesso e oscuro.
4. I calcoli del log vengono inclusi in `tryCatch()` in modo che le eccezioni non causino un arresto improvviso dell'esecuzione. Senza `tryCatch()` , la maggior parte degli errori generati dalle funzioni R producono un segnale di arresto, che interrompe l'esecuzione.

Eseguire questo codice R nel proprio esperimento e osservare l'output generato nel file output.log. Verranno visualizzati i valori trasformati delle quattro colonne nel log, come illustrato nella Figura 13.

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

*Figura 13. Riepilogo dei valori trasformati nel frame di dati.*

I valori sono stati trasformati correttamente. La produzione di latte supera ora di gran lunga la produzione di tutti gli altri prodotti caseari, ricordandoci come si stia ora usando una scala logaritmica .

A questo punto i nostri dati sono puliti e siamo pronti per qualche operazione di modeling. Esaminando il riepilogo della visualizzazione per l'output del set di dati risultante del modulo [Execute R script (Esegui script R][execute-r-script] ), si noterà che la colonna ' month ' è' CATEGORICAL ' con 12 valori univoci, come volevamo.

## <a id="timeseries"></a>Oggetti della serie temporale e analisi delle correlazioni

In questa sezione analizzeremo alcuni oggetti serie temporale di R e analizzeremo le correlazioni tra alcune delle variabili. Il nostro obiettivo è generare un frame di dati contenente informazioni di correlazione pairwise a vari intervalli.

Il codice R completo per questa sezione è in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Oggetti della serie temporale in R

Come già accennato, le serie temporali sono costituite da una serie di valori di dati indicizzati per data e ora. Gli oggetti serie temporale vengono usati in R per creare e gestire l'indice temporale. Questi oggetti offrono infatti una serie di vantaggi. Gli oggetti della serie temporale consentono di liberarsi delle numerose attività di gestione dei valori di indice della serie temporale incapsulati nell'oggetto. Consentono inoltre di usare i vari metodi messi a disposizione dalle serie temporali per operazioni di tracciamento, stampa, modeling, ecc.

In genere viene usata la classe di serie temporale POSIXct poiché è relativamente semplice e permette di misurare il tempo a partire dal 1° gennaio 1970. In questo esempio useremo quindi oggetti serie temporale di tipo POSIXct. Altre classi di oggetti serie temporale comunemente usate in R includono zoo e xts, serie temporali estensibili.

### <a name="time-series-object-example"></a>Esempio di oggetto della serie temporale

Iniziamo con il nostro esempio. Trascinare e rilasciare un **nuovo** modulo [Execute R script][execute-r-script] nell'esperimento. Connettere la porta di output DataSet1 risultante del modulo [Execute r script][execute-r-script] esistente alla porta di input DataSet1 del nuovo modulo [Execute r script][execute-r-script] .

Come nei primi esempi, man mano che si va avanti, in determinati punti verranno mostrate solo le righe aggiuntive incrementali del codice R per ogni passaggio.  

#### <a name="reading-the-dataframe"></a>Lettura del frame di dati

Come prima operazione, eseguiamo la lettura in un frame di dati e accertiamoci di ottenere i risultati previsti. A questo scopo, usare il codice seguente.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Eseguiamo ora l'esperimento. Il log della nuova forma Execute R Script dovrebbe essere simile a quello riportato nella Figura 14.

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

*Figura 14. Riepilogo del frame di dati nel modulo Execute R Script.*

I dati sono dei tipi e nel formato previsti. Osservare come la colonna "Month" sia di tipo fattore e contenga il numero di livelli previsto.

#### <a name="creating-a-time-series-object"></a>Creazione di un oggetto della serie temporale

È necessario aggiungere un oggetto serie temporale al nostro frame di dati. Sostituire il codice corrente con il seguente, che aggiunge una nuova colonna della classe POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

A questo punto, controllare il log. Dovrebbe essere simile a quello riportato nella Figura 15.

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

*Figura 15. Riepilogo del frame di dati con un oggetto della serie temporale.*

Dal riepilogo è possibile notare come la nuova colonna sia effettivamente di classe POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Esplorazione e trasformazione dei dati

In questa sezione vengono descritte alcune delle variabili in questo set di dati. A questo scopo ci avvarremo di una matrice di grafici a dispersione. La funzione `str()` nel codice R precedente viene sostituita con la riga seguente.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Eseguiamo il codice e vediamo cosa succede. Il grafico generato per la porta R Device dovrebbe essere simile a quello riportato nella Figura 16.

![Matrice del grafico a dispersione delle variabili selezionate](./media/r-quickstart/fig17.png)

*Figura 16. Matrice del grafico a dispersione delle variabili selezionate.*

È presente una struttura insolita nelle relazioni tra queste variabili. Questo può essere dovuto ad alcune tendenze nei dati o al fatto che le variabili non sono state standardizzate.

### <a name="correlation-analysis"></a>Analisi delle correlazioni

Per eseguire un'analisi di correlazione è necessario detrendizzare le variabili e standardizzarle. È possibile usare semplicemente la funzione `scale()` di R, che centra e scala le variabili e può essere eseguita più velocemente. In questo caso, tuttavia, preferisco mostrare un esempio di programmazione difensiva in R.

La funzione `ts.detrend()` mostrata di seguito esegue entrambe le operazioni. Le due seguenti righe di codice consentono di detrendizzare i dati e standardizzare i valori.

```R
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

La funzione `ts.detrend()` espleta quindi numerose operazioni. Gran parte del codice è progettato per controllare la presenza di potenziali problemi con gli argomenti o per gestire le eccezioni, che possono sempre verificarsi durante i calcoli. Solo poche righe del codice, quindi, eseguono effettivamente i calcoli.

Un esempio di programmazione difensiva è già stato illustrato in Trasformazioni di valori. Entrambi i blocchi di calcolo vengono inclusi in `tryCatch()`. Per alcuni errori è opportuno restituire il vettore di input originale, per altri è preferibile un vettore di zeri.  

Osservare come la regressione lineare usata per il detrending sia una regressione in serie temporale e la variabile indipendente un oggetto serie temporale.  

Una volta definito `ts.detrend()` , viene applicato alle variabili di interesse nel frame di dati. È necessario forzare l'elenco risultante creato da `lapply()` nel frame di dati usando `as.data.frame()`. A causa degli aspetti difensivi di `ts.detrend()`, la mancata elaborazione di una delle variabili non impedisce la corretto elaborazione delle altre.  

La riga di codice finale crea un grafico a dispersione pairwise. Dopo aver eseguito il codice R, i risultati del grafico a dispersione vengono mostrati nella Figura 17.

![Grafico a dispersione pairwise della serie temporale detrendizzata e standardizzata](./media/r-quickstart/fig18.png)

*Figura 17. Grafico a dispersione pairwise della serie temporale detrendizzata e standardizzata.*

È possibile confrontare questi risultati con quelli mostrati nella figura 16. Dopo aver eseguito il detrending e standardizzato le variabili, la struttura che rappresenta le relazioni tra le variabili è notevolmente semplificata.

Il codice per calcolare le correlazioni come oggetti CCF di R è il seguente.

```R
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

L'esecuzione di questo codice produce il log mostrato nella Figura 18.

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

*Figura 18. Elenco di oggetti CCF dall'analisi delle correlazioni pairwise.*

Per ogni intervallo è presente un valore di correlazione. Nessuno di essi, tuttavia, è abbastanza grande da essere significativo. Possiamo quindi concludere che ciascuna variabile può essere modellata in modo indipendente.

### <a name="output-a-dataframe"></a>Output di un frame di dati
Abbiamo elaborato le correlazioni pairwise come elenco di oggetti R ccf. Questo determina tuttavia un piccolo problema poiché la porta di output Result Dataset richiede inevitabilmente un frame di dati. Inoltre, l'oggetto CCF stesso è un elenco e sono richiesti solo i valori nel primo elemento di questo elenco, le correlazioni nei vari intervalli.

Il codice seguente estrae i valori di intervallo dall'elenco di oggetti CCF, che sono essi stessi degli elenchi.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

La prima riga di codice è un po' complessa da comprendere, quindi viene spiegata qui di seguito. Dall'interno verso l'esterno incontriamo:

1. L'operatore ' **[[** ' con l'argomento '**1**' seleziona il vettore delle correlazioni negli intervalli dal primo elemento dell'elenco di oggetti CCF.
2. La funzione `do.call()` applica la funzione `rbind()` agli elementi dell'elenco restituito da `lapply()`.
3. La funzione `data.frame()` forza il risultato prodotto da `do.call()` in un frame di dati.

I nomi di riga sono in una colonna del frame di dati. Questa operazione consente di mantenere i nomi di riga quando vengono restituiti dall' [esecuzione dello script R][execute-r-script].

L'esecuzione del codice produce l'output mostrato nella Figura 19 quando si **visualizza** l'output nella porta Result Dataset. I nomi di riga sono nella prima colonna, come previsto.

![Output dei risultati dall'analisi delle correlazioni](./media/r-quickstart/fig20.png)

*Figura 19. Output dei risultati dall'analisi delle correlazioni.*

## <a id="seasonalforecasting"></a>Esempio di serie temporale: previsione stagionale

Ora i dati sono in un formato adatto all'analisi ed è stato stabilito che non sono presenti correlazioni significative tra le variabili. Passiamo quindi alla creazione di un modello di previsione in serie temporale, che ci permetterà di prevedere la produzione di latte in California nei 12 mesi del 2013.

Il nostro modello previsionale sarà costituito da due componenti: trend e stagionale. La previsione completa è data dal prodotto di queste due componenti. Questo tipo di modello prende il nome di modello moltiplicativo, alternativo al modello additivo. Una trasformazione logaritmica è stata già applicata alle variabili di interesse, quindi l'analisi diventa più gestibile.

Il codice R completo per questa sezione è in [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Creazione del frame di dati per l'analisi

Per iniziare, aggiungere un **nuovo** modulo [Execute R script (Esegui script R][execute-r-script] ) all'esperimento. Connettere l'output del **set di dati dei risultati** del modulo [Execute R script][execute-r-script] esistente all'input **DataSet1** del nuovo modulo. Il risultato dovrebbe essere simile al contenuto della Figura 20.

![Esperimento con l'aggiunta del nuovo modulo Execute R Script](./media/r-quickstart/fig21.png)

*Figura 20. Esperimento con l'aggiunta del nuovo modulo Execute R Script.*

Come per l'analisi di correlazione appena completata, dobbiamo aggiungere una colonna con oggetto serie temporale POSIXct. A questo scopo, usare il codice seguente.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Eseguire il codice ed esaminare il log. Il risultato dovrebbe essere simile alla Figura 21.

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

*Figura 21. Riepilogo del frame di dati.*

Con questo risultato siamo pronti per avviare l'analisi.

### <a name="create-a-training-dataset"></a>Creare un set di dati di training

Con il frame di dati costruito dobbiamo creare un set di dati di addestramento, che comprenderà tutte le osservazioni, ad eccezione delle ultime 12, relative al 2013, che corrispondono al nostro set di dati di test. Il codice seguente suddivide il frame di dati in sottoinsiemi e crea grafici della produzione casearia e delle variabili di prezzo. Creerò quindi dei grafici per le quattro variabili di produzione e di prezzo. Una funzione anonima viene usata per definire alcuni argomenti per il tracciato e quindi eseguire l'iterazione nell'elenco degli altri due argomenti con `Map()`. Se pensate che un ciclo For sarebbe stato appropriato, avete ragione. Tuttavia, poiché R è un linguaggio funzionale, adotterò un approccio funzionale.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

L'esecuzione di questo codice produce la serie di grafici in serie temporale ottenuto dall'output R Device mostrato nella Figura 22. Notare che l'asse temporale è espresso in unità di date, uno dei vantaggi offerti dal metodo del grafico in serie temporale.

![Primo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/r-quickstart/unnamed-chunk-161.png)

![Secondo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/r-quickstart/unnamed-chunk-162.png)

![Terzo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/r-quickstart/unnamed-chunk-163.png)

![Quarto tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/r-quickstart/unnamed-chunk-164.png)

*Figura 22. Tracciati della serie temporale della produzione casearia in California e dei dati sui prezzi.*

### <a name="a-trend-model"></a>Modello di tendenza

Dopo aver creato un oggetto serie temporale e aver esaminato i dati, iniziamo a costruire un modello di trend per i dati di produzione di latte in California. A questo scopo, usiamo una regressione in serie temporale. Tuttavia, è chiaro dal tracciato che è necessario più di una pendenza e di un intercetta per modellare in modo accurato la tendenza osservata nei dati di training.

Data la scala ridotta dei dati, compilare il modello per la tendenza in RStudio, quindi tagliare e incollare il modello risultante nella versione classica di Azure Machine Learning Studio. RStudio fornisce infatti un ambiente interattivo adatto a questo tipo di analisi interattiva.

Come primo tentativo, eseguirò una regressione polinomiale con valori di potenza fino a 3. Esiste un pericolo reale di overfitting per questi tipi di modelli. È opportuno quindi evitare termini di ordine superiore. La funzione `I()` inibisce l'interpretazione dei contenuti (interpreta i contenuti così come sono) e consente di scrivere una funzione con interpretazione letterale in un'equazione di regressione.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Viene generato quanto segue.

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
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Da P values (`Pr(>|t|)`) in questo output, possiamo notare che il termine quadrato potrebbe non essere significativo. Verrà usata la funzione `update()` per modificare il modello eliminando il termine al quadrato.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Viene generato quanto segue.

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
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

L'aspetto è decisamente migliore adesso. Tutti i termini sono significativi. Il valore 2e-16, tuttavia, è un valore predefinito e non dovrebbe essere preso troppo sul serio  

Come test di integrità, effettueremo un grafico in serie temporale dei dati della produzione casearia in California con la curva di trend mostrata di seguito. Ho aggiunto il codice seguente nella versione classica di Azure Machine Learning Studio [Execute R script][execute-r-script] Model (non rstudio) per creare il modello ed eseguire un tracciato. Il risultato viene mostrato nella Figura 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dati sulla produzione di latte in California con il modello di tendenza visualizzato](./media/r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dati sulla produzione di latte in California con il modello di tendenza visualizzato.*

Sembra che il modello di trend si adatti perfettamente. Inoltre, non sembra che ci siano tracce di overfitting, ad esempio strani ondulamenti nella curva del modello.  

### <a name="seasonal-model"></a>Modello con stagionalità

Con un modello di tendenza disponibile, è necessario proseguire includendo gli effetti stagionali. Verrà usato il mese dell'anno come variabile fittizia nel modello lineare per acquisire l'effetto mese per mese. Tenere presente che, quando si introducono variabili di fattore in un modello, l'intercetta non deve essere elaborata. In caso contrario, la formula risulta eccessivamente specificata ed R eliminerà i fattori desiderati, lasciando invece il termine dell'intercetta.

Poiché il modello di tendenza è soddisfacente, è possibile usare la funzione `update()` per aggiungere i nuovi termini al modello esistente. Nella formula di aggiornamento, il valore -1 elimina il termine dell'intercetta. Continuiamo ora in RStudio.

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Viene generato quanto segue.

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
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vediamo come nel modello non sia più presente un termine dell'intercetta ma siano inclusi 12 fattori mese, proprio come ci aspettavamo.

Creiamo adesso un altro grafico in serie temporale dei dati di produzione casearia in California per verificare l'effettivo funzionamento del modello con stagionalità. Ho aggiunto il codice seguente nella versione classica di Azure Machine Learning Studio [Execute R script][execute-r-script] per creare il modello ed eseguire un tracciato.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

L'esecuzione di questo codice nella versione classica di Azure Machine Learning Studio produce il tracciato illustrato nella figura 24.

![Produzione di latte in California con un modello che include gli effetti stagionali](./media/r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produzione di latte in California con un modello che include gli effetti stagionali.*

La corrispondenza con i dati mostrati nella Figura 24 è piuttosto incoraggiante. Sia il trend sia l'effetto stagionale (variazione mensile) sembrano ragionevoli.

Come ulteriore verifica sul mostro modello, esaminiamo i valori residui. Il codice seguente calcola i valori previsti da due modelli, calcola i valori residui per il modello stagionale e infine esegue il traccia di tali valori per i dati di training.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Il grafico dei residui è illustrato nella Figura 25.

![Valori residui del modello stagionale per i dati di training](./media/r-quickstart/unnamed-chunk-21.png)

*Figura 25. Valori residui del modello stagionale per i dati di training.*

Questi valori residui sembrano ragionevoli. Non risalta alcuna struttura particolare, ad eccezione dell'effetto della recessione del biennio 2008-2009, che il nostro modello non riesce a rappresentare particolarmente bene.

Il tracciato mostrato nella Figura 25 è utile per rilevare i modelli dipendenti dal tempo nei valori residui. L'approccio esplicito di elaborare e tracciare graficamente i valori residui consente di metterli in ordine temporale nel grafico. D'altra parte, se fosse stato eseguito il tracciato di `milk.lm$residuals`, questo tracciato non sarebbe stato in ordine temporale.

È anche possibile usare `plot.lm()` per produrre una serie di tracciati diagnostici.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Questo codice produce una serie di tracciati diagnostici mostrati nella Figura 26.

![Primo tracciato diagnostico per il modello stagionale](./media/r-quickstart/unnamed-chunk-221.png)

![Secondo tracciato diagnostico per il modello stagionale](./media/r-quickstart/unnamed-chunk-222.png)

![Terzo tracciato diagnostico per il modello stagionale](./media/r-quickstart/unnamed-chunk-223.png)

![Quarto tracciato diagnostico per il modello stagionale](./media/r-quickstart/unnamed-chunk-224.png)

*Figura 26. Tracciati diagnostici per il modello stagionale.*

In questi grafici sono presenti alcuni punti di alta influenza, ma niente di particolarmente preoccupante. Nel grafico Normal Q-Q, inoltre, possiamo vedere come i valori residuali siano distribuiti in modo pressoché normale, presupposto importante per i modelli lineari.

### <a name="forecasting-and-model-evaluation"></a>Previsione e valutazione del modello

È rimasta un'ultima cosa da fare per poter completare il nostro esempio: elaborare le previsioni e valutare il grado di errore rispetto ai dati effettivi. La nostra previsione riguarda i 12 mesi del 2013. È possibile calcolare una misura di errore per questa previsione nei dati effettivi non compresi nel set di dati di training. Possiamo inoltre confrontare le prestazioni dei 18 anni di dati di addestramento con i 12 mesi dei dati di test.  

Per misurare le prestazioni di modelli in serie temporale viene usata, in genere, una serie di parametri. In questo caso verrà usata la radice dell'errore quadratico medio (RMS). La funzione seguente calcola l'errore RMS tra due serie.  

```R
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

Analogamente alla funzione `log.transform()` discussa nella sezione "Trasformazioni di valori", in questa funzione è presente molto codice per il controllo degli errori e per il ripristino delle eccezioni. I principi usati sono gli stessi. Il lavoro viene eseguito in due posizioni incluse in `tryCatch()`. Poiché stiamo usando i logaritmi dei valori, eseguiremo in primo luogo l'esponenziazione delle serie temporali. Passeremo quindi all'elaborazione della deviazione standard.  

Muniti di una funzione per calcolare la deviazione standard, creiamo ed eseguiamo l'output di un frame di dati contenente le deviazioni standard. Includiamo quindi i termini relativi al modello con trend e completiamo il modello con fattori stagionali. Il codice seguente esegue l'operazione usando i due modelli lineari costruiti.

```R
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

L'esecuzione di questo codice produce nella porta di output Result Dataset il contenuto illustrato nella Figura 27.

![Confronto di errori RMS per i modelli](./media/r-quickstart/fig26.png)

*Figura 27. Confronto di errori RMS per i modelli.*

Da questi risultati si evince che l'aggiunta di fattori stagionali al modello riduce in modo significativo l'errore RMS. Come previsto, inoltre, la deviazione standard per i dati di addestramento è leggermente inferiore rispetto alla previsione.

## <a id="appendixa"></a>Guida alla documentazione di RStudio

RStudio è molto ben documentato. Di seguito sono riportati alcuni collegamenti alle sezioni principali della documentazione di RStudio per iniziare.

* **Creazione di progetti** : è possibile organizzare e gestire il codice R in progetti usando rstudio. Per informazioni dettagliate, vedere [uso dei progetti](https://support.rstudio.com/hc/articles/200526207-Using-Projects) . Si consiglia di seguire queste istruzioni e creare un progetto per gli esempi di codice R in questo articolo.  
* La **modifica e l'esecuzione del codice r** : rstudio fornisce un ambiente integrato per la modifica e l'esecuzione di codice r. Per informazioni dettagliate, vedere [modifica ed esecuzione del codice](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) .
* **Debug** -rstudio include potenti funzionalità di debug. Per ulteriori informazioni su queste funzionalità, vedere [debug con rstudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) . Per informazioni sulle funzionalità di risoluzione dei problemi, vedere [risoluzione dei problemi](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)del punto di interruzione.

## <a id="appendixb"></a>Ulteriori informazioni

Questa esercitazione sulla programmazione R illustra le nozioni di base di ciò che è necessario per usare il linguaggio R con la versione classica di Azure Machine Learning Studio. Se non si ha familiarità con R, in CRAN sono disponibili due introduzioni:

* [R per principianti](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) di Emmanuel Paradis è un valido punto di partenza.  
* [Introduzione a R](https://cran.r-project.org/doc/manuals/R-intro.html) da W. N. Venables et. al. entra in un po' più approfondito.

Sono disponibili molti libri con informazioni introduttive su R. Di seguito sono elencati quelli che ritengo più utili:

* L' **arte della programmazione r: una panoramica della progettazione statistica del software** di Norman Matloff è un'ottima introduzione alla programmazione in R.  
* **R Cookbook** di Paul Teetor fornisce un problema e un approccio alla soluzione per l'uso di r.  
* **R in azione** di Robert Kabacoff è un altro interessante libro introduttivo. Il [sito Web Quick R](https://www.statmethods.net/) complementare è una risorsa utile.
* **R inferno** di Patrick Burns è un libro sorprendentemente divertente che tratta di una serie di argomenti complessi e difficili che possono verificarsi durante la programmazione in R. Il libro è disponibile gratuitamente a [R inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Per approfondimenti sugli argomenti avanzati in R, vedere il libro **Advanced r** di Hadley Wickham. La versione online di questo libro è disponibile gratuitamente all' [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/).

Un catalogo dei pacchetti di serie temporali R è disponibile in [Cran task view: Time Series Analysis](https://cran.r-project.org/web/views/TimeSeries.html). Per informazioni su specifici pacchetti di oggetti della serie temporale, fare riferimento alla documentazione dei singoli pacchetti.

Il libro **Time Series introduttivo** con r di Paul Cowpertwait e Andrew Metcalfe offre un'introduzione all'uso di r per l'analisi delle serie temporali. Numerosi esempi di R sono infine disponibili in una grande quantità di testi teorici.

Di seguito sono riportate alcune risorse Internet eccezionali:

* DataCamp insegna a usare R dal proprio browser con lezioni video ed esercizi sulla codifica. Sono disponibili anche esercitazioni interattive sulle ultime tecniche e pacchetti di R. Fai un' [esercitazione su R interattiva](https://www.datacamp.com/courses/introduction-to-r)gratuita.
* [Informazioni sulla programmazione R, la guida definitiva](https://www.programiz.com/r-programming) di Programiz.
* Una rapida [esercitazione su R](https://www.cyclismo.org/tutorial/R/) di Kelly Black di Clarkson University.
* Sono disponibili oltre 60 risorse R elencate alle [principali risorse di linguaggio r per migliorare le proprie competenze sui dati](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
