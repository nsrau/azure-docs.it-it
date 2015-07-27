<properties
	pageTitle="Esercitazione con guida rapida per il linguaggio R per Azure Machine Learning | Microsoft Azure"
	description="Usare questa esercitazione sulla programmazione R per iniziare a usare rapidamente il linguaggio R con Azure Machine Learning Studio per creare una soluzione di previsione."
	services="machine-learning"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/26/2015"
	ms.author="larryfr"/>

# Esercitazione con guida rapida per il linguaggio di programmazione R per Azure Machine Learning

Stephen F Elston, Ph.D.

##  Introduzione

Questa esercitazione con guida rapida consente di iniziare rapidamente a estendere Azure Machine Learning usando il linguaggio di programmazione R. Seguire questa esercitazione sulla programmazione R per creare, testare ed eseguire il codice R in Azure Machine Learning. Usando questa esercitazione, sarà possibile creare una soluzione completa di previsione con il linguaggio R in Azure Machine Learning.

Microsoft Azure Machine Learning contiene molti moduli validi per Machine Learning e per la manipolazione dei dati. Il potente linguaggio R è stato definito come la lingua franca dell'analisi. Per fortuna, le analisi e la manipolazione dei dati Azure Machine Learning possono essere estese usando R. Questa combinazione fornisce la scalabilità e la facilità di distribuzione di Azure Machine Learning con la flessibilità e l'analisi approfondita di R.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


###Previsione e set di dati

La previsione è un metodo analitico molto utile e ampiamente distribuito. Viene comunemente usata per vari scopi, dalla previsione di vendita di articoli stagionali alla definizione dei livelli di scorte ottimali, fino alla previsione di variabili macroeconomiche. In genere, inoltre, viene eseguita con modelli in serie temporale.

I dati delle serie temporali sono dati i cui valori contengono un indice temporale, che può essere regolare, ad esempio ogni minuto od ogni mese, o irregolare. Sui dati in serie temporale si basano i modelli in serie temporale. Il linguaggio di programmazione R contiene un'architettura flessibile ed estese funzioni di analisi per i dati in serie temporale.

In questa esercitazione prenderemo in esame la produzione casearia in California, con i relativi dati sui prezzi. Questi dati includono informazioni mensili sulla produzione di diversi prodotti caseari e il prezzo del latte intero, un materia prima di riferimento.

I dati usati nell'articolo, insieme agli script R, possono essere [scaricati qui][download]. I dati sono stati sintetizzati in origine dalle informazioni rese disponibili dalla University of Wisconsin all'indirizzo http://future.aae.wisc.edu/tab/production.html.

###	Organizzazione

La procedura sarà formata da diversi passaggi che consentiranno di apprendere come creare, testare ed eseguire il codice R per le analisi e la manipolazione dei dati nell'ambiente di Azure Machine Learning.

* Prima di tutto, verranno illustrate le nozioni di base dell'uso del linguaggio R nell'ambiente di Azure Machine Learning Studio.

* Quindi, verranno discussi diversi aspetti relativi all'I/O dei dati, del codice R e dei grafici nell'ambiente di Azure Machine Learning.

* In seguito, verrà creata la prima parte della soluzione di previsione tramite la compilazione di codice per la pulizia e la trasformazione dei dati.

* Con i dati preparati verrà eseguita un'analisi delle correlazioni tra più variabili nel set di dati.

* Infine, verrà creato un modello di previsione in serie temporale (stagionale) per la produzione di latte.

##<a id="mlstudio"></a>Interagire con il linguaggio R in Machine Learning Studio

Questa sezione illustra alcune nozioni di base relative all'interazione con il linguaggio di programmazione R nell'ambiente di Machine Learning Studio. Il linguaggio R fornisce uno strumento potente per creare moduli personalizzati di analisi e manipolazione di dati nell'ambiente di Azure Machine Learning.

In questa guida useremo RStudio per sviluppare, testare ed eseguire il debug di codice R su piccola scala. Questo codice viene tagliato e incollato in un modulo [Execute R Script][execute-r-script] in Machine Learning Studio, pronto per l'esecuzione.

###Modulo Execute R Script

In Machine Learning Studio gli script R vengono eseguiti nel modulo [Execute R Script][execute-r-script]. Un esempio del modulo [Execute R Script][execute-r-script] in Machine Learning Studio è mostrato nella Figura 1.

 ![Linguaggio di programmazione R: il modulo Execute R Script selezionato in Machine Learning Studio][1]

*Figura 1. Ambiente di Machine Learning Studio che mostra il modulo Execute R Script selezionato.*

Facendo riferimento alla Figura 1, osservare alcune parti di chiave dell'ambiente di Machine Learning Studio da usare con il modulo [Execute R Script][execute-r-script].

- I moduli dell'esperimento vengono visualizzati nel riquadro centrale.

- La finestra presente nella parte superiore del riquadro di destra consente di visualizzare e modificare gli script R,

- mentre la parte inferiore del riquadro di destra mostra alcune proprietà di [Execute R Script][execute-r-script]. È possibile visualizzare e i log degli errori e di output facendo clic sulle rispettive opzioni di questo riquadro.

Il modulo [Execute R Script][execute-r-script] verrà trattato in dettaglio nelle parti successive di questo documento.

Quando si usano funzioni R complesse, è preferibile effettuare le operazioni di modifica, test e debug in RStudio. Come con qualsiasi altro software di sviluppo, inoltre, è opportuno estendere il codice in modo incrementale, così da poterlo verificare in piccoli e semplici casi di test e, se funziona correttamente, tagliarlo e incollarlo nella finestra dello script R del modulo [Execute R Script][execute-r-script]. Questo approccio consente di sfruttare sia l'ambiente di sviluppo integrato (IDE) di RStudio che la potenza di Azure Machine Learning.

####Eseguire il codice R

Qualsiasi codice R nel modulo [Execute R Script][execute-r-script] verrà eseguito quando si esegue l'esperimento facendo clic sul pulsante **Run**. Al termine dell'esecuzione, verrà visualizzato un segno di spunta sull'icona [Execute R Script][execute-r-script].

####Codice R difensivo per Azure Machine Learning

Se si sviluppa un codice R per, ad esempio, un servizio Web usando Azure Machine Learning, è necessario pianificare in che modo il codice R gestirà gli input di dati imprevisti e le eccezioni. Per maggiore chiarezza, per gli esempi di codice riportati in questa guida non tratterò in modo approfondito le modalità di verifica e gestione delle eccezioni. Tuttavia, verranno forniti diversi esempi di funzioni che prevedono l'uso della capacità di gestione delle eccezioni di R.

Per informazioni più complete sulla gestione delle eccezioni di R, si consiglia di leggere le sezioni rilevanti del libro di Wickham elencate in [Appendice B - Letture di approfondimento](#appendixb).


####Eseguire il debug e testare R in Machine Learning Studio

Come già anticipato, in genere è consigliabile eseguire il test e il debug del codice R creato su scala ridotta in RStudio. Tuttavia, in alcuni casi è necessario tenere traccia dei problemi relativi al codice R nello stesso modulo [Execute R Script][execute-r-script]. Inoltre, si consiglia di controllare i risultati in Machine Learning Studio.

L'output dell'esecuzione del codice R e della piattaforma di Azure Machine Learning è disponibile principalmente in output.log, ma alcune informazioni aggiuntive possono essere presenti in error.log.

Se si verifica un errore in Machine Learning Studio mentre si esegue il codice R, è necessario innanzitutto analizzare error.log. Questo file può contenere messaggi di errore utili a comprendere e correggere l'errore. Per visualizzare error.log, fare clic su **View error log** nel **riquadro delle proprietà** del modulo [Execute R Script][execute-r-script] che contiene l'errore.

Ad esempio, si esegue il codice R riportato di seguito con una variabile y non definita in un modulo [Execute R Script][execute-r-script]:

	x <- 1.0
	z <- x + y

L'esecuzione del codice non riesce, generando così una condizione di errore. Facendo clic su **View error log** nel **riquadro delle proprietà** viene visualizzato quanto mostrato nella Figura 2.

  ![Finestra di popup con messaggio di errore][2]

*Figura 2. Finestra di popup con il messaggio di errore.*

Per visualizzare il messaggio di errore di R, è necessario usare output.log. Fare clic su [Execute R Script][execute-r-script], quindi selezionare l'elemento **View output.log** nel **riquadro delle proprietà** sulla destra. Viene aperta una nuova finestra del browser e viene visualizzato quanto segue.


	[ModuleOutput] [1] 14000
	[ModuleOutput]
	[ModuleOutput] Loading objects:
	[ModuleOutput]
	[ModuleOutput]   port1
	[ModuleOutput]
	[ModuleOutput] [1] "Loading variable port1..."
	[ModuleOutput]
	[ModuleOutput] Error in eval(expr, envir, enclos) : object 'y' not found

Questo messaggio di errore non contiene sorprese e identifica chiaramente il problema.

Per ispezionare il valore dei singoli oggetti in R; è possibile stampare i valori nel file output.log. Le regole per l'analisi dei valori di oggetto sono essenzialmente le stesse di una sessione R interattiva. Se, ad esempio, si digita un nome di variabile in una riga, il valore dell'oggetto verrà stampato nel file output.log.

####Pacchetti in Machine Learning Studio

Azure Machine Learning viene fornito con più di 350 pacchetti di linguaggio R preinstallati. Per visualizzare l'elenco dei pacchetti preinstallati, è possibile usare il seguente codice nel modulo [Execute R Script][execute-r-script].

	data.set <- data.frame(installed.packages())
	maml.mapOutputPort("data.set")

Se al momento non si riesce a comprendere l'ultima riga del codice, continuare a leggere. Nel resto del documento vengono discussi diversi aspetti dell'uso di R nell'ambiente di Azure Machine Learning.

###	Introduzione a RStudio

RStudio è un ambiente IDE molto usato per R. RStudio verrà usato per la modifica, i test e il debug di alcuni codici R usati in questa guida introduttiva. Dopo il test, quando il codice R è pronto, tagliarlo e incollarlo dall'editor di RStudio a un modulo [Execute R Script][execute-r-script] di Machine Learning Studio.

Se il linguaggio di programmazione R non è installato nel computer desktop, eseguirne ora l'installazione. Download gratuiti del linguaggio di programmazione R open source sono disponibili nel sistema CRAN (Comprehensive R Archive Network) all'indirizzo [http://www.r-project.org/](http://www.r-project.org/). Sono disponibili anche download per Windows, Mac OS e Linux/UNIX. Scegliere un mirror vicino e seguire le istruzioni di download. Il sistema CRAN contiene anche una serie di utili pacchetti di analisi e manipolazione di dati.

Se non si ha familiarità con RStudio, è consigliabile scaricare e installare la versione desktop. I download di RStudio per Windows, Mac OS e Linux/UNIX sono disponibili anche all'indirizzo http://www.rstudio.com/products/RStudio/. Seguire le indicazioni fornite per installare RStudio sul proprio desktop.

Un'esercitazione di introduzione a RStudio è disponibile all'indirizzo https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Altre informazioni sull'uso di RStudio sono disponibili nell'[Appendice A][appendixa].

##<a id="scriptmodule"></a>Input e output dei dati nel modulo Execute R Script

Questa sezione descrive come eseguire l'input e l'output dei dati nel modulo [Execute R Script][execute-r-script]. Verrà esaminato come gestire i diversi tipi di dati in ingresso e in uscita nel modulo [Execute R Script][execute-r-script].

Il codice completo per questa sezione è contenuto nel file .zip scaricato in precedenza.

###Caricare e controllare i dati in Machine Learning Studio

####<a id="loading"></a>Caricare il set di dati

Prima di tutto, caricare il file **csdairydata.csv** in Azure Machine Learning Studio.

- Avviare l'ambiente di Azure Machine Learning Studio.

- Fare clic su + nell'angolo in basso a sinistra dello schermo e selezionare **Dataset**.

- Selezionare **Da file locale**, e quindi **Sfoglia** per selezionare il file.

- Verificare di aver selezionato **Generic CSV file with header (.csv)** come tipo per il set di dati.

- Fare clic sul segno di spunta.

- Dopo aver caricato il set di dati, il nuovo set di dati viene visualizzato facendo clic sulla scheda **Set di dati**.

####Creare un esperimento

Ora che sono presenti dei dati in Machine Learning Studio, è necessario creare un esperimento per eseguire l'analisi.

- Fare clic su + nell'angolo in basso a sinistra dello schermo e selezionare **Experiment**, quindi **Blank Experiment**.

- È possibile denominare l'esperimento selezionandolo e modificando il titolo **Experiment created on...** nella parte superiore della pagina. Ad esempio, è possibile modificare il titolo in **CA Dairy Analysis**.

- A sinistra della pagina dell’esperimento, espandere **Saved Datasets**, e quindi **My Datasets**. Verrà visualizzato il set di dati **cadairydata.csv** caricato in precedenza.

- Trascinare **csdairydata.csv set di dati** nell'esperimento.

- Nella casella **Search experiment items** nella parte superiore del riquadro a sinistra, digitare [Execute R Script][execute-r-script]. Nell'elenco di ricerca viene visualizzato il modulo corrispondente.

- Trascinare il modulo [Execute R Script][execute-r-script] nel pallet.

- Connettere l'output del **set di dati csdairydata.csv** all'input più a sinistra (**Dataset1**) del modulo [Execute R Script][execute-r-script].

- **Non dimenticare di fare clic su 'Save'.**

A questo punto, l'esperimento dovrebbe essere simile a quanto riportato nella Figura 3.

![L'esperimento CA Dairy Analysis con il set di dati e il modulo Execute R Script][3]

*Figura 3. Esperimento CA Dairy Analysis con il set di dati e il modulo Execute R Script.*

####Controllo dei dati

Diamo ora un'occhiata ai dati caricati nell'esperimento. Nell'esperimento fare clic sull'output del **set di dati cadairydata.csv** e selezionare **visualize**. Dovrebbe apparire una tabella simile a quella riportata nella Figura 4.

![Riepilogo del set di dati cadairydata.csv][4]

*Figura 4. Riepilogo del set di dati cadairydata.csv.*

In questa tabella sono presenti numerose informazioni utili. La riga **Feature Type** mostra i tipi di dati che Azure Machine Learning Studio assegna alle colonne nel set di dati. Vengono visualizzate anche diverse righe del set di dati. Avere un quadro d'insieme può essere di grande aiuto prima di iniziare la fase di analisi vera e propria.

###	Primo script R

Creare un primo, semplice script R da provare con Azure Machine Learning Studio. Lo script seguente è stato creato e testato in RStudio.

	## Only one of the following two lines should be used
	## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
	## If in RStudio, use the second line with read.csv()
	cadairydata <- maml.mapInputPort(1)
	# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
	str(cadairydata)
	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('cadairydata')

Ora è necessario trasferirlo in Azure Machine Learning Studio. Potrei semplicemente eseguire un'operazione di copia e incolla, ma in questo caso trasferirò lo script R tramite un file zip.

###	Input di dati nel modulo Execute R Script

Si considerino ora gli input del modulo [Execute R Script][execute-r-script]. In questo esempio verranno letti i dati caseari della California nel modulo [Execute R Script][execute-r-script].

Esistono tre possibili input per il modulo [Execute R Script][execute-r-script]. È possibile usarne una o tutte, in base alle esigenze. È anche possibile usare uno script R senza alcun input.

Di seguito vengono esaminati i singoli input, da sinistra a destra. È possibile visualizzare i nomi di ciascun input posizionando il cursore sull'input e leggendo la descrizione comando.

#### Script Bundle

L'input Script Bundle consente di passare i contenuti di un file ZIP nel modulo [Execute R Script][execute-r-script]. È possibile usare uno dei seguenti comandi per leggere i contenuti del file ZIP nel codice R.

	source("src/yourfile.R") # Reads a zipped R script
	load("src/yourData.rdata") # Reads a zipped R data file

> [AZURE.NOTE]Azure Machine Learning gestisce i file nello ZIP come se fossero nella directory src/, quindi è necessario inserire questo nome della directory come prefisso nei nomi file.

Il caricamento dei set di dati è già stato discusso in [Caricamento del set di dati](#loading). Dopo aver creato e testato lo script R mostrato nella sezione precedente, procedere come segue:

1. Salvare lo script R in un file .R. Chiamerò il mio file di script "simpleplot.R".  

2.  Creare un file zip e copiare al suo interno lo script creato.

3.	Aggiungere il file ai **set di dati** in Machine Learning Studio, specificando il tipo **zip**. Dovrebbe essere visualizzato il file ZIP nei set di dati.

4.	Trascinare il file ZIP dai **set di dati** all'**area di disegno di ML Studio**.

5.	Connettere l'output dell'icona dei **dati zip** all'input **Script Bundle** del modulo [Execute R Script][execute-r-script].

6.	Digitare la funzione `source()` con il nome del file ZIP nella finestra del codice per il modulo [Execute R Script][execute-r-script]. In questo caso, digitare `source("src/SimplePlot.R")`.

7.	Fare clic su **Save**.

Dopo aver completato questi passaggi, il modulo [Execute R Script][execute-r-script] esegue lo script R nel file ZIP quando viene eseguito l'esperimento. A questo punto l'esperimento dovrebbe avere un aspetto simile a quello riportato nella Figura 5.

![Esperimento con script R compresso][6]

*Figura 5. Esperimento con script R compresso.*

#### Dataset1

È possibile passare una tabella di dati rettangolare al codice R usando l'input Dataset1. Nello script semplice descritto, la funzione `maml.mapInputPort(1)` legge i dati dalla porta 1. I dati vengono poi assegnati a un nome di variabile del frame di dati nel codice. Nel nostro script semplice è la prima riga del codice a effettuare l'assegnazione.

	cadairydata <- maml.mapInputPort(1)

Eseguire l'esperimento facendo clic sul pulsante **Run**. Quando l'esecuzione termina, fare clic sul modulo [Execute R Script][execute-r-script], quindi su **View output log** nel riquadro delle proprietà. Viene visualizzata una nuova pagina del browser che mostra i contenuti del file output.log. Scorrendo la pagina, dovrebbe essere visualizzato quanto segue.

	[ModuleOutput] [1] "Loading variable port1..."
	[ModuleOutput]
	[ModuleOutput] 'data.frame':	228 obs. of  9 variables:
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

> [AZURE.NOTE]Lo stesso output stampato è disponibile anche dall'output R Device del modulo [Execute R Script][execute-r-script]. Gli output del modulo [Execute R Script][execute-r-script] verranno discussi nella sezione successiva.

####Dataset2

Il comportamento della modalità di input Dataset2 è identico a quello di Dataset1. Consente infatti di passare nel codice R creato una seconda tabella rettangolare di dati. La funzione `maml.mapInputPort(2)`, con l'argomento 2, viene usata per passare questi dati.

###Output del modulo Execute R Script

####Output di un frame di dati

È possibile eseguire l'output dei contenuti di un frame di dati R come tabella rettangolare dalla porta Result Dataset1 usando la funzione `maml.mapOutputPort()`. Nello script R semplice descritto, questa operazione viene eseguita dalla riga seguente.

	maml.mapOutputPort('cadairydata')

Dopo aver eseguito l'esperimento, fare clic sulla porta di output Result Dataset1, quindi scegliere **Visualize**. I risultati dovrebbero essere simili a quanto visualizzato nella Figura 6.

![Visualizzazione dell'output dei dati caseari della California][7]

*Figura 6. Visualizzazione dell'output dei dati caseari della California.*

In questo caso l'output è identico all'input, come previsto.

###	Output R Device

L'output Device del modulo [Execute R Script][execute-r-script] contiene l'output dei messaggi e dei grafici. Alla porta di output R Device vengono inviati da R sia l'output standard sia i messaggi di errore standard.

Per visualizzare l' output R Device, fare clic sulla porta e su **Visualize**. Nella Figura 7 sono riportati l'output standard e i messaggi di errore standard generati dallo script R.

![Output ed errore standard dalla porta R Device][8]

*Figura 7. Output ed errore standard dalla porta R Device.*

Scorrendo verso il basso è possibile visualizzare l'output grafico generato dallo script R, riportato nella Figura 8.

![Output dei grafici dalla porta R Device][9]

*Figura 8. Output dei grafici dalla porta R Device.*

##<a id="filtering"></a>Filtraggio e trasformazione dei dati

In questa sezione effettueremo alcune operazioni di filtraggio e trasformazione di base sui dati caseari della California. Al termine di questa sezione i dati saranno disponibili in un formato adatto alla creazione di un modello di analisi.

In particolare, in questa sezione vengono eseguite diverse attività comuni di pulizia e trasformazione dei dati: trasformazione del tipo, filtraggio nei frame di dati, aggiunta di nuove colonne elaborate e trasformazioni dei valori. Queste procedure di base potrebbero essere utili per affrontare le numerose variazioni che spesso si generano nei problemi della vita reale.

Il codice R completo per questa sezione è disponibile nel file zip scaricato in precedenza.

###	Trasformazioni di tipi

Ora che è possibile leggere i dati caseari della California nel codice R del modulo [Execute R Script][execute-r-script], è necessario verificare che i dati nelle colonne siano del tipo e del formato previsti.

R è un linguaggio tipizzato dinamicamente, in cui i tipi di dati vengono assegnati l'uno all'altro in base alle esigenze. Nel linguaggio R, i dati atomic possono essere di tipo numerico, logico e Char. Il tipo fattore viene usato invece per archiviare in modo compatto i dati relativi alle categorie. Altre informazioni sui tipi di dati sono disponibili nei riferimenti dell'[Appendice B - Letture di approfondimento](#appendixb).

Quando i dati tabulari vengono letti in R da un'origine esterna, è sempre opportuno controllare nelle colonne i tipi risultanti. È possibile, ad esempio, che una colonna che sarebbe dovuta essere di tipo Char in realtà risulti di tipo fattore, o viceversa. In altri casi, una colonna che si pensa debba essere numerica viene rappresentata da dati di tipo carattere, ad esempio '1,23' anziché 1,23 come numero a virgola mobile.

Fortunatamente, convertire un tipo in un altro tipo è piuttosto semplice, purché sia possibile il mapping. Ad esempio, non è possibile convertire "Nevada" in un valore numerico, ma è possibile convertirlo in un fattore (variabile di categoria). Un valore numerico 1, invece, può essere convertito sia nel carattere "1" sia in un fattore.

La sintassi di queste conversioni è semplice: `as.datatype()`. Queste funzioni di conversione del tipo includono quanto segue.

* `as.numeric()`

* `as.character()`

* `as.logical()`

* `as.factor()`

Considerando i tipi di dati delle colonne di cui è stato eseguito l'input nella sezione precedente: tutte le colonne sono di tipo numerico, tranne quella etichettata 'Month', che è di tipo character. Proviamo quindi a convertire questa colonna in tipo fattore e controlliamo i risultati.

La riga con cui è stata creata la matrice del grafico a dispersione è stata eliminata ed è stata aggiunta un riga per la conversione della colonna 'Month' in un fattore. Nell'esperimento è sufficiente tagliare e incollare il codice R nella finestra del codice del modulo [Execute R Script][execute-r-script]. È anche possibile aggiornare il file ZIP e caricarlo in Azure Machine Learning Studio, ma sono necessari diversi passaggi.

	## Only one of the following two lines should be used
	## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
	## If in RStudio, use the second line with read.csv()
	cadairydata <- maml.mapInputPort(1)
	# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
	## Ensure the coding is consistent and convert column to a factor
	cadairydata$Month <- as.factor(cadairydata$Month)
	str(cadairydata) # Check the result
	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('cadairydata')

Eseguiamo il codice e analizziamo l'output. L'output da **Visualize** nel menu R Device è mostrato nella Figura 9.

![Riepilogo del frame di dati con una variabile di fattore][10]

*Figura 9. Riepilogo del frame di dati con una variabile di fattore.*

Il tipo per Month sarà: '**Factor w/ 14 levels**'. Questo è ovviamente un problema poiché in un anno ci sono solo 12 mesi. È anche possibile controllare che il tipo in **Visualize** della porta Result Dataset sia '**Categorical**'.

Il problema è che la colonna "Month" non è stata codificata in modo sistematico. Un mese può essere denominato Aprile in alcuni casi e abbreviato in Apr. in altri. Per risolvere il problema, è possibile ridurre la stringa a tre caratteri. La riga di codice dovrebbe ora apparire così:

	## Ensure the coding is consistent and convert column to a factor
	cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Eseguire di nuovo l'esperimento e **visualizzare** l'output nella porta R Device con i risultati mostrati nella Figura 10.

![Riepilogo del frame di dati con il numero corretto di livelli di fattore][11]

*Figura 10. Riepilogo del frame di dati con il numero corretto di livelli di fattore.*

La variabile di fattore contiene ora 12 livelli.

###Filtraggio di frame di dati di base

I frame di dati R supportano avanzate funzionalità di filtraggio. I set di dati, infatti, possono essere suddivisi in sottoinsiemi usando filtri logici su righe o colonne. In molto casi, tuttavia, sono necessari complessi criteri di filtro. I riferimenti nell'[Appendice B - Letture di approfondimento](#appendixb) contengono numerosi esempi di filtraggio dei frame di dati.

Possiamo eseguire semplici operazioni di filtraggio anche sul nostro set di dati. Se si guardano le colonne nel frame di dati cadairydata, si può vedere che ci sono due colonne superflue. La prima colonna contiene solo un numero di riga, non molto utile. La seconda colonna, Year.Month, contiene le informazioni ridondanti. È possibile escludere facilmente queste colonne usando il seguente codice R.

> [AZURE.NOTE]Nel resto della sezione verrà mostrato solo l'ulteriore codice aggiunto al modulo [Execute R Script][execute-r-script]. Tutte le nuove righe verranno aggiunte **prima** della funzione `str()`. Questa funzione viene usata per verificare i risultati in Azure Machine Learning Studio.

La riga seguente viene aggiunta al codice R nel modulo [Execute R Script][execute-r-script].

	# Remove two columns we do not need
	cadairydata <- cadairydata[, c(-1, -2)]

Eseguire questo codice nell'esperimento e controllare il risultato con **Visualize** nella porta R Device. Questi risultati vengono mostrati nella Figura 11.

![Riepilogo del frame di dati con due colonne rimosse][12]

*Figura 11. Riepilogo del frame di dati con due colonne rimosse.*

Ottime notizie! I risultati ottenuti sono quelli previsti.

###Aggiungere una nuova colonna

Per la creazione di modelli in serie temporale sarà utile disporre di una colonna contenente i nomi dei mesi a partire dall'inizio della serie temporale. Creeremo quindi una nuova colonna denominata "Month.Count".

Per organizzare il codice, è necessario creare una prima funzione semplice, `num.month()`. La funzione verrà quindi applicata per creare una nuova colonna nel frame di dati. Il nuovo codice è il seguente.

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

A questo punto, eseguire l'esperimento aggiornato e usare **Visualize** nella porta R Device per visualizzare i risultati. I risultati previsti sono illustrati nella Figura 12.

![Riepilogo del frame di dati con la colonna aggiuntiva][13]

*Figura 12. Riepilogo del frame di dati con la colonna aggiuntiva.*

Sembra che tutto stia funzionando perfettamente. La nuova colonna con i valori previsti è stata creata correttamente nel frame di dati.

###Trasformazioni di valori

In questa sezione eseguiremo semplici operazioni di trasformazione sui valori di alcune delle colonne del frame di dati. Il linguaggio R supporta trasformazioni di dati pressoché arbitrarie. I riferimenti nell'[Appendice B - Letture di approfondimento](#appendixb) contengono numerosi esempi.

Se si guardano i valori nei riepiloghi del frame di dati, si nota qualcosa di strano. In California si produce più gelato che latte? No, ovviamente no, sfortunatamente per gli amanti del gelato. Le unità di misura sono diverse. Il prezzo è espresso in unità di libbre statunitensi, il latte è espresso in unità da un milione di libbre statunitensi, il gelato in unità da 1.000 galloni statunitensi e i fiocchi di latte in unità da 1.000 libbre statunitensi. Supponendo che il gelato pesi circa 6,5 libbre al gallone, è facile eseguire la moltiplicazione per convertire questi valori in modo che siano espressi in unità da 1.000 libbre.

Per il modello di previsione viene usato un modello moltiplicativo per le tendenze e l'adattamento stagionale dei dati. Una trasformazione logaritmica, infine, ci consente di usare un modello lineare, semplificando notevolmente il processo. Possiamo applicare la trasformazione logaritmica alla stessa funzione a cui è stato applicato il moltiplicatore.

Nel codice seguente viene definita una nuova funzione `log.transform()`, che viene poi applicata alle righe che contengono i valori numerici. La funzione R `Map()` viene usata per applicare la funzione `log.transform()` alle colonne selezionate del frame di dati. `Map()` è simile a `apply()` ma consente più elenchi di argomenti nella funzione. Un elenco di moltiplicatori fornisce il secondo argomento alla funzione `log.transform()`. La funzione `na.omit()` viene usata come strumento di pulizia per assicurare che non ci siano valori mancanti o non definiti nel frame di dati.

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

La funzione `log.transform()` espleta quindi numerose operazioni. Gran parte del codice è progettato per controllare la presenza di potenziali problemi con gli argomenti o per gestire le eccezioni, che possono sempre verificarsi durante i calcoli. Solo poche righe del codice, quindi, eseguono effettivamente i calcoli.

L'obiettivo della programmazione difensiva consiste nel prevenire errori in una singola funzione che potrebbero bloccare l'intera elaborazione. Un errore improvviso in un'analisi di lunga durata può essere piuttosto frustrante per gli utenti. Per evitare questa situazione, è necessario scegliere valori restituiti predefiniti in grado di limitare i danni nell'elaborazione a valle. Viene inoltre generato un messaggio di avviso per informare gli utenti che si è verificato un errore.

Se non si ha familiarità con la programmazione difensiva in R, questo codice può sembrare eccessivamente complesso. Affronteremo di seguito i passaggi principali:

1. Viene definito un vettore di quattro messaggi. Questi messaggi vengono usati per comunicare informazioni relative a errori ed eccezioni che possono verificarsi nel codice.

2. Per ciascun caso restituirò il valore NA. Esistono diverse altre possibilità che potrebbero avere un minor numero di effetti collaterali. Ad esempio, potrei restituire un vettore di zeri o il vettore di input originale.

3. Vengono eseguiti controlli sugli argomenti alla funzione. In ogni caso, se viene rilevato un errore, la funzione `warming()` restituisce un valore predefinito e un messaggio. In questo caso viene usato `warning()` e non `stop()` perché quest'ultimo comporta l'arresto dell'esecuzione, che si vuole evitare. Osservare come abbia scritto questo codice in stile procedurale: un approccio funzionale sarebbe apparso in questo caso eccessivamente complesso e oscuro.

4. I calcoli del log vengono inclusi in `tryCatch()` in modo che le eccezioni non causino un arresto improvviso dell'esecuzione. Senza `tryCatch()`, la maggior parte degli errori generati dalle funzioni R producono un segnale di arresto, che interrompe l'esecuzione.

Eseguire questo codice R nel proprio esperimento e osservare l'output generato nel file output.log. Verranno visualizzati i valori trasformati delle quattro colonne usando **Visualize** dal menu R Device, come mostrato nella Figura 13.

![Riepilogo dei valori trasformati nel frame di dati][14]

*Figura 13. Riepilogo dei valori trasformati nel frame di dati.*

I valori sono stati trasformati correttamente. La produzione di latte supera ora di gran lunga la produzione di tutti gli altri prodotti caseari, ricordandoci come si stia ora usando una scala logaritmica .

A questo punto i nostri dati sono puliti e siamo pronti per qualche operazione di modeling. Se si guarda il riepilogo della visualizzazione per l'output Result Dataset del modulo [Execute R Script][execute-r-script], si vede che la colonna 'Month' è 'Categorical' con 12 valori univoci, ossia il risultato desiderato.

##<a id="timeseries"></a>Oggetti della serie temporale e analisi delle correlazioni

In questa sezione analizzeremo alcuni oggetti serie temporale di R e analizzeremo le correlazioni tra alcune delle variabili. Il nostro obiettivo è generare un frame di dati contenente informazioni di correlazione pairwise a vari intervalli.

Il codice R completo per questa sezione è disponibile nel file zip scaricato in precedenza.

###Oggetti della serie temporale in R

Come già accennato, le serie temporali sono costituite da una serie di valori di dati indicizzati per data e ora. Gli oggetti serie temporale vengono usati in R per creare e gestire l'indice temporale. Questi oggetti offrono infatti una serie di vantaggi. Gli oggetti della serie temporale consentono di liberarsi delle numerose attività di gestione dei valori di indice della serie temporale incapsulati nell'oggetto. Consentono inoltre di usare i vari metodi messi a disposizione dalle serie temporali per operazioni di tracciamento, stampa, modeling, ecc.

In genere viene usata la classe di serie temporale POSIXct poiché è relativamente semplice e permette di misurare il tempo a partire dal 1° gennaio 1970. In questo esempio useremo quindi oggetti serie temporale di tipo POSIXct. Altre classi dell'oggetto della serie temporale R ampiamente usate comprendono zoo e xts, la serie temporale estendibile. <!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

###	Esempio di oggetto della serie temporale

Iniziamo con il nostro esempio. Trascinare un **nuovo** modulo [Execute R Script][execute-r-script] nell'esperimento. Connettere la porta di output Result Dataset1 del modulo [Execute R Script][execute-r-script] esistente alla porta di input Dataset1 del nuovo modulo [Execute R Script][execute-r-script].

Come nei primi esempi, man mano che si va avanti, in determinati punti verranno mostrate solo le righe aggiuntive incrementali del codice R per ogni passaggio.

####	Lettura del frame di dati

Come prima operazione, eseguiamo la lettura in un frame di dati e accertiamoci di ottenere i risultati previsti. A questo scopo, usare il codice seguente.

	# Comment the following if using RStudio
	cadairydata <- maml.mapInputPort(1)
	str(cadairydata) # Check the results

Eseguiamo ora l'esperimento. L'output visualizzato per la porta R Device dovrebbe essere simile a quello riportato nella Figura 14.

![Riepilogo del frame di dati nel modulo Execute R Script][15]

*Figura 14. Riepilogo del frame di dati nel modulo Execute R Script.*

I dati sono dei tipi e nel formato previsti. Osservare come la colonna "Month" sia di tipo fattore e contenga il numero di livelli previsto.

####Creazione di un oggetto della serie temporale

È necessario aggiungere un oggetto serie temporale al nostro frame di dati. Sostituire il codice corrente con il seguente, che aggiunge una nuova colonna della classe POSIXct.

	# Comment the following if using RStudio
	cadairydata <- maml.mapInputPort(1)

	## Create a new column as a POSIXct object
	Sys.setenv(TZ = "PST8PDT")
	cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

	str(cadairydata) # Check the results

Verifichiamo ora l'output della porta R Device. Dovrebbe essere simile a quello riportato nella Figura 15.

![Riepilogo del frame di dati con un oggetto della serie temporale][16]

*Figura 15. Riepilogo del frame di dati con un oggetto della serie temporale.*

Dal riepilogo è possibile notare come la nuova colonna sia effettivamente di classe POSIXct.

###Esplorazione e trasformazione dei dati

In questa sezione vengono descritte alcune delle variabili in questo set di dati. A questo scopo ci avvarremo di una matrice di grafici a dispersione. La funzione `str()` nel codice R precedente viene sostituita con la riga seguente.

	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Eseguiamo il codice e vediamo cosa succede. Il grafico generato per la porta R Device dovrebbe essere simile a quello riportato nella Figura 16.

![Matrice del grafico a dispersione delle variabili selezionate][17]

*Figura 16. Matrice del grafico a dispersione delle variabili selezionate.*

È presente una struttura insolita nelle relazioni tra queste variabili. Questo può essere dovuto ad alcune tendenze nei dati o al fatto che le variabili non sono state standardizzate.

###Analisi delle correlazioni

Per eseguire un'analisi di correlazione è necessario detrendizzare le variabili e standardizzarle. È possibile usare semplicemente la funzione `scale()` di R, che centra e scala le variabili e può essere eseguita più velocemente. In questo caso, tuttavia, preferisco mostrare un esempio di programmazione difensiva in R.

La funzione `ts.detrend()` mostrata di seguito esegue entrambe le operazioni. Le due seguenti righe di codice consentono di detrendizzare i dati e standardizzare i valori.

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

La funzione `ts.detrend()` espleta quindi numerose operazioni. Gran parte del codice è progettato per controllare la presenza di potenziali problemi con gli argomenti o per gestire le eccezioni, che possono sempre verificarsi durante i calcoli. Solo poche righe del codice, quindi, eseguono effettivamente i calcoli.

Un esempio di programmazione difensiva è già stato illustrato in [Trasformazioni di valori](#valuetransformations). Entrambi i blocchi di calcolo vengono inclusi in `tryCatch()`. Per alcuni errori è opportuno restituire il vettore di input originale, per altri è preferibile un vettore di zeri.

Osservare come la regressione lineare usata per il detrending sia una regressione in serie temporale e la variabile indipendente un oggetto serie temporale.

Una volta definito `ts.detrend()`, viene applicato alle variabili di interesse nel frame di dati. È necessario forzare l'elenco risultante creato da `lapply()` nel frame di dati usando `as.data.frame()`. A causa degli aspetti difensivi di `ts.detrend()`, la mancata elaborazione di una delle variabili non impedisce la corretto elaborazione delle altre.

La riga di codice finale crea un grafico a dispersione pairwise. Dopo aver eseguito il codice R, i risultati del grafico a dispersione vengono mostrati nella Figura 17.

![Grafico a dispersione pairwise della serie temporale detrendizzata e standardizzata][18]

*Figura 17. Grafico a dispersione pairwise della serie temporale detrendizzata e standardizzata.*

È possibile confrontare questi risultati con quelli illustrati nella Figura 17. Dopo aver eseguito il detrending e standardizzato le variabili, la struttura che rappresenta le relazioni tra le variabili è notevolmente semplificata.

Il codice per calcolare le correlazioni come oggetti CCF di R è il seguente.

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

Eseguendo questo codice viene generato l'output mostrato nella Figura 18.

![Elenco di oggetti CCF dall'analisi delle correlazioni pairwise][19]

*Figura 18. Elenco di oggetti CCF dall'analisi delle correlazioni pairwise.*

Per ogni intervallo è presente un valore di correlazione. Nessuno di essi, tuttavia, è abbastanza grande da essere significativo. Possiamo quindi concludere che ciascuna variabile può essere modellata in modo indipendente.

###Output di un frame di dati

Abbiamo elaborato le correlazioni pairwise come elenco di oggetti R ccf. Questo determina tuttavia un piccolo problema poiché la porta di output Result Dataset richiede inevitabilmente un frame di dati. Inoltre, l'oggetto CCF stesso è un elenco e sono richiesti solo i valori nel primo elemento di questo elenco, le correlazioni nei vari intervalli.

Il codice seguente estrae i valori di intervallo dall'elenco di oggetti CCF, che sono essi stessi degli elenchi.

	df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

	c.names <- c("-1 lag", "0 lag", "+1 lag")
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
	## The following line works only in Azure Machine Learning
	## When running in RStudio, this code will result in an error
	#maml.mapOutputPort('outframe')

La prima riga di codice è un po' complessa da comprendere, quindi viene spiegata qui di seguito. Dall'interno verso l'esterno incontriamo:

1.  L'operatore '**[[**' con argomento '**1**' seleziona il vettore delle correlazioni nei vari intervalli dal primo elemento dell'elenco di oggetti CCF.

2.  La funzione `do.call()` applica la funzione `rbind()` agli elementi dell'elenco restituito da `lapply()`.

3.  La funzione `data.frame()` forza il risultato prodotto da `do.call()` in un frame di dati.

I nomi di riga sono in una colonna del frame di dati. In questo modo vengono preservati durante l'output dal modulo [Execute R Script][execute-r-script].

L'esecuzione del codice produce l'output mostrato nella Figura 19 quando si **visualizza** l'output nella porta Result Dataset. I nomi di riga sono nella prima colonna, come previsto.

![Output dei risultati dall'analisi delle correlazioni][20]

*Figura 19. Output dei risultati dall'analisi delle correlazioni.*

##<a id="seasonalforecasting"></a>Esempio di serie temporale: previsione stagionale

Ora i dati sono in un formato adatto all'analisi ed è stato stabilito che non sono presenti correlazioni significative tra le variabili. Passiamo quindi alla creazione di un modello di previsione in serie temporale, che ci permetterà di prevedere la produzione di latte in California nei 12 mesi del 2013.

Il nostro modello previsionale sarà costituito da due componenti: trend e stagionale. La previsione completa è data dal prodotto di queste due componenti. Questo tipo di modello prende il nome di modello moltiplicativo, alternativo al modello additivo. Una trasformazione logaritmica è stata già applicata alle variabili di interesse, quindi l'analisi diventa più gestibile.

Il codice R completo per questa sezione è disponibile nel file zip scaricato in precedenza.

###	Creazione del frame di dati per l'analisi

Iniziare aggiungendo un **nuovo** modulo [Execute R Script][execute-r-script] all'esperimento. Connettere l'output **Result Dataset** del modulo [Execute R Script][execute-r-script] esistente all'input **Dataset1** del nuovo modulo. Il risultato dovrebbe essere simile al contenuto della Figura 20.

![Esperimento con l'aggiunta del nuovo modulo Execute R Script][21]

*Figura 20. Esperimento con l'aggiunta del nuovo modulo Execute R Script.*

Come per l'analisi di correlazione appena completata, dobbiamo aggiungere una colonna con oggetto serie temporale POSIXct. A questo scopo, usare il codice seguente.

	# If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
	cadairydata <- maml.mapInputPort(1)

	## Create a new column as a POSIXct object
	Sys.setenv(TZ = "PST8PDT")
	cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

	str(cadairydata)

Eseguire il codice e osservare l'output della porta R Device. Il risultato dovrebbe essere simile alla Figura 21.

![Riepilogo del frame di dati][22]

*Figura 21. Riepilogo del frame di dati.*

Con questo risultato siamo pronti per avviare l'analisi.

###Creare un set di dati di training

Con il frame di dati costruito dobbiamo creare un set di dati di addestramento, che comprenderà tutte le osservazioni, ad eccezione delle ultime 12, relative al 2013, che corrispondono al nostro set di dati di test. Il codice seguente suddivide il frame di dati in sottoinsiemi e crea grafici della produzione casearia e delle variabili di prezzo. Creerò quindi dei grafici per le quattro variabili di produzione e di prezzo. Una funzione anonima viene usata per definire alcuni argomenti per il tracciato e quindi eseguire l'iterazione nell'elenco degli altri due argomenti con `Map()`. Se pensate che un ciclo For sarebbe stato appropriato, avete ragione. Tuttavia, poiché R è un linguaggio funzionale, adotterò un approccio funzionale.

	cadairytrain <- cadairydata[1:216, ]

	Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
	               "Log CA Ice Cream Production, 1000s lb",
	               "Log CA Milk Production 1000s lb",
	               "Log North CA Milk Milk Fat Price per 1000 lb")

	Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

L'esecuzione di questo codice produce la serie di grafici in serie temporale ottenuto dall'output R Device mostrato nella Figura 22. Notare che l'asse temporale è espresso in unità di date, uno dei vantaggi offerti dal metodo del grafico in serie temporale.

![Primo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/machine-learning-r-quickstart/unnamed-chunk-161.png)

![Secondo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/machine-learning-r-quickstart/unnamed-chunk-162.png)

![Terzo tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/machine-learning-r-quickstart/unnamed-chunk-163.png)

![Quarto tracciato della serie temporale della produzione casearia in California e dei dati sui prezzi](./media/machine-learning-r-quickstart/unnamed-chunk-164.png)

*Figura 22. Tracciati della serie temporale della produzione casearia in California e dei dati sui prezzi.*

###	Modello di tendenza

Dopo aver creato un oggetto serie temporale e aver esaminato i dati, iniziamo a costruire un modello di trend per i dati di produzione di latte in California. A questo scopo, usiamo una regressione in serie temporale. Tuttavia, è chiaro dal tracciato che è necessario più di una pendenza e di un intercetta per modellare in modo accurato la tendenza osservata nei dati di training.

Considerata la scala ridotta dei dati, il modello per la tendenza verrà compilato in RStudio, quindi il modello risultante verrà tagliato e incollato in Azure Machine Learning. RStudio fornisce infatti un ambiente interattivo adatto a questo tipo di analisi interattiva.

Come primo tentativo, eseguirò una regressione polinomiale con valori di potenza fino a 3. Esiste un pericolo reale di overfitting per questi tipi di modelli. È opportuno quindi evitare termini di ordine superiore. La funzione `I()` inibisce l'interpretazione dei contenuti (interpreta i contenuti così come sono) e consente di scrivere una funzione con interpretazione letterale in un'equazione di regressione.

	milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
	summary(milk.lm)

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
	## Multiple R-squared:  0.941,	Adjusted R-squared:  0.94
	## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Dai valori P (Pr(>|t|)) presenti in questo output possiamo osservare come il termine al quadrato possa non essere significativo. Verrà usata la funzione `update()` per modificare il modello eliminando il termine al quadrato.

	milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
	summary(milk.lm)

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

Come test di integrità, effettueremo un grafico in serie temporale dei dati della produzione casearia in California con la curva di trend mostrata di seguito. È stato aggiunto il seguente codice al modulo [Execute R Script][execute-r-script] di Azure Machine Learning (non RStudio) per creare il modello ed eseguire un tracciato. Il risultato viene mostrato nella Figura 23.

	milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

	plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
	lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Dati sulla produzione di latte in California con il modello di tendenza visualizzato](./media/machine-learning-r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dati sulla produzione di latte in California con il modello di tendenza visualizzato.*

Sembra che il modello di trend si adatti perfettamente. Inoltre, non sembra che ci siano tracce di overfitting, ad esempio strani ondulamenti nella curva del modello.

###Modello con stagionalità

Con un modello di tendenza disponibile, è necessario proseguire includendo gli effetti stagionali. Verrà usato il mese dell'anno come variabile fittizia nel modello lineare per acquisire l'effetto mese per mese. Tenere presente che, quando si introducono variabili di fattore in un modello, l'intercetta non deve essere elaborata. In caso contrario, la formula risulta eccessivamente specificata ed R eliminerà i fattori desiderati, lasciando invece il termine dell'intercetta.

Poiché il modello di tendenza è soddisfacente, è possibile usare la funzione `update()` per aggiungere i nuovi termini al modello esistente. Nella formula di aggiornamento, il valore -1 elimina il termine dell'intercetta. Continuiamo ora in RStudio.

	milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
	summary(milk.lm2)

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
	## Multiple R-squared:     1,	Adjusted R-squared:     1
	## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vediamo come nel modello non sia più presente un termine dell'intercetta ma siano inclusi 12 fattori mese, proprio come ci aspettavamo.

Creiamo adesso un altro grafico in serie temporale dei dati di produzione casearia in California per verificare l'effettivo funzionamento del modello con stagionalità. Il codice seguente è stato aggiunto al modulo [Execute R Script][execute-r-script] di Azure Machine Learning per creare il modello ed eseguire un tracciato.

	milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

	plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
	lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

L'esecuzione di questo codice in Azure Machine Learning produce il tracciato mostrato nella Figura 24.

![Produzione di latte in California con un modello che include gli effetti stagionali](./media/machine-learning-r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produzione di latte in California con un modello che include gli effetti stagionali.*

La corrispondenza con i dati mostrati nella Figura 24 è piuttosto incoraggiante. Sia il trend sia l'effetto stagionale (variazione mensile) sembrano ragionevoli.

Come ulteriore verifica sul mostro modello, esaminiamo i valori residui. Il codice seguente calcola i valori previsti da due modelli, calcola i valori residui per il modello stagionale e infine esegue il traccia di tali valori per i dati di training.

	## Compute predictions from our models
	predict1  <- predict(milk.lm, cadairydata)
	predict2  <- predict(milk.lm2, cadairydata)

	## Compute and plot the residuals
	residuals <- cadairydata$Milk.Prod - predict2
	plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

Il grafico dei residui è illustrato nella Figura 25.

![Valori residui del modello stagionale per i dati di training](./media/machine-learning-r-quickstart/unnamed-chunk-21.png)

*Figura 25. Valori residui del modello stagionale per i dati di training.*

Questi valori residui sembrano ragionevoli. Non risalta alcuna struttura particolare, ad eccezione dell'effetto della recessione del biennio 2008-2009, che il nostro modello non riesce a rappresentare particolarmente bene.

Il tracciato mostrato nella Figura 25 è utile per rilevare i modelli dipendenti dal tempo nei valori residui. L'approccio esplicito di elaborare e tracciare graficamente i valori residui consente di metterli in ordine temporale nel grafico. D'altra parte, se fosse stato eseguito il tracciato di `milk.lm$residuals`, questo tracciato non sarebbe stato in ordine temporale.

È anche possibile usare `plot.lm()` per produrre una serie di tracciati diagnostici.

	## Show the diagnostic plots for the model
	plot(milk.lm2, ask = FALSE)

Questo codice produce una serie di tracciati diagnostici mostrati nella Figura 26.

![Primo tracciato diagnostico per il modello stagionale](./media/machine-learning-r-quickstart/unnamed-chunk-221.png)

![Secondo tracciato diagnostico per il modello stagionale](./media/machine-learning-r-quickstart/unnamed-chunk-222.png)

![Terzo tracciato diagnostico per il modello stagionale](./media/machine-learning-r-quickstart/unnamed-chunk-223.png)

![Quarto tracciato diagnostico per il modello stagionale](./media/machine-learning-r-quickstart/unnamed-chunk-224.png)

*Figura 26. Tracciati diagnostici per il modello stagionale.*

In questi grafici sono presenti alcuni punti di alta influenza, ma niente di particolarmente preoccupante. Nel grafico Normal Q-Q, inoltre, possiamo vedere come i valori residuali siano distribuiti in modo pressoché normale, presupposto importante per i modelli lineari.

###Previsione e valutazione del modello

È rimasta un'ultima cosa da fare per poter completare il nostro esempio: elaborare le previsioni e valutare il grado di errore rispetto ai dati effettivi. La nostra previsione riguarda i 12 mesi del 2013. È possibile calcolare una misura di errore per questa previsione nei dati effettivi non compresi nel set di dati di training. Possiamo inoltre confrontare le prestazioni dei 18 anni di dati di addestramento con i 12 mesi dei dati di test.

Per misurare le prestazioni di modelli in serie temporale viene usata, in genere, una serie di parametri. In questo caso verrà usata la radice dell'errore quadratico medio (RMS). La funzione seguente calcola l'errore RMS tra due serie.

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

Analogamente alla funzione `log.transform()` discussa nella sezione "Trasformazioni di valori", in questa funzione è presente molto codice per il controllo degli errori e per il ripristino delle eccezioni. I principi usati sono gli stessi. Il lavoro viene eseguito in due posizioni incluse in `tryCatch()`. Poiché stiamo usando i logaritmi dei valori, eseguiremo in primo luogo l'esponenziazione delle serie temporali. Passeremo quindi all'elaborazione della deviazione standard.

Muniti di una funzione per calcolare la deviazione standard, creiamo ed eseguiamo l'output di un frame di dati contenente le deviazioni standard. Includiamo quindi i termini relativi al modello con trend e completiamo il modello con fattori stagionali. Il codice seguente esegue l'operazione usando i due modelli lineari costruiti.

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
	## Azure Machine Learning Studio
	maml.mapOutputPort('RMS.df')

L'esecuzione di questo codice produce nella porta di output Result Dataset il contenuto illustrato nella Figura 27.

![Confronto di errori RMS per i modelli][26]

*Figura 27. Confronto di errori RMS per i modelli.*

Da questi risultati si evince che l'aggiunta di fattori stagionali al modello riduce in modo significativo l'errore RMS. Come previsto, inoltre, la deviazione standard per i dati di addestramento è leggermente inferiore rispetto alla previsione.

##<a id="appendixa"></a>APPENDICE A: Guida a RStudio

RStudio viene fornito con una documentazione molto dettagliata. In questa appendice mi limiterò quindi a fornire alcuni link alle sezioni principali della documentazione RStudio, utili per acquisire familiarità con questo prodotto.

1.	Creazione di progetti

	È possibile organizzare e gestire il codice R nei progetti usando RStudio. La documentazione relativa all'uso dei progetti è disponibile all'indirizzo https://support.rstudio.com/hc/articles/200526207-Using-Projects.

	Consiglio di seguire queste indicazioni e creare un progetto per gli esempi di codice R riportati in questo documento.

2.	Modifica ed esecuzione di codice R

	RStudio offre un ambiente integrato per la modifica e l'esecuzione di codice R. La documentazione è disponibile all'indirizzo https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.

3.	Debug

	RStudio include avanzate funzionalità di debug. La documentazione per queste funzionalità è disponibile all'indirizzo https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.

	Le funzionalità per la risoluzione degli errori relativi al punto di interruzione sono documentate all'indirizzo https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

##<a id="appendixb"></a>APPENDICE B: Letture di approfondimento

Questa esercitazione sulla programmazione R illustra le nozioni di base sull'ambito di utilizzo del linguaggio R con Azure Machine Learning Studio. Se non si ha familiarità con R, in CRAN sono disponibili due introduzioni:

- R for Beginners di Emmanuel Paradis, per iniziare, all'indirizzo http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  

- An Introduction to R di W. N. Venables et. al., con informazioni un po' più approfondite, all'indirizzo http://cran.r-project.org/doc/manuals/R-intro.html.

Sono disponibili molti libri con informazioni introduttive su R. Di seguito sono elencati quelli che ritengo più utili:

- The Art of R Programming: A Tour of Statistical Software Design di Norman Matloff, un'eccellente introduzione alla programmazione in R.  

- R Cookbook di Paul Teetor, che offre un approccio problema-soluzione all'uso di R.

- R in Action di Robert Kabacoff, un altro libro introduttivo valido. È molto utile anche il sito Web collegato Quick R, disponibile all'indirizzo http://www.statmethods.net/.

- R Inferno di Patrick Burns è un libro decisamente divertente che si occupa di diversi argomenti complessi in cui ci si può imbattere durante la programmazione in R. Il libro è disponibile gratuitamente all'indirizzo http://www.burns-stat.com/documents/books/the-r-inferno/.

- Per informazioni più dettagliate sugli argomenti avanzati relativi a R, vedere il libro Advanced R di Hadley Wickham. La versione online del libro è disponibile gratuitamente all'indirizzo http://adv-r.had.co.nz/.

Un catalogo di pacchetti delle serie temporali di R è disponibile in CRAN Task View per l'analisi delle serie temporali: http://cran.r-project.org/web/views/TimeSeries.html. Per informazioni su specifici pacchetti di oggetti della serie temporale, fare riferimento alla documentazione dei singoli pacchetti.

Il libro Introductory Time Series with R di Paul Cowpertwait e Andrew Metcalfe fornisce un'introduzione all'uso di R per l'analisi delle serie temporali. Numerosi esempi di R sono infine disponibili in una grande quantità di testi teorici.

Alcune importanti risorse su Internet:

- DataCamp: DataCamp insegna a usare R dal proprio browser con lezioni video ed esercizi sulla codifica. Sono disponibili anche esercitazioni interattive sulle ultime tecniche e pacchetti di R. Per esercitazioni interattive di R gratuite, visitare https://www.datacamp.com/courses/introduction-to-r  

- A quick R tutorial di Kelly Black della Clarkson University http://www.cyclismo.org/tutorial/R/

- Più di 60 risorse elencate all'indirizzo http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/machine-learning-r-quickstart/fig1.png
[2]: ./media/machine-learning-r-quickstart/fig2.png
[3]: ./media/machine-learning-r-quickstart/fig3.png
[4]: ./media/machine-learning-r-quickstart/fig4.png
[5]: ./media/machine-learning-r-quickstart/fig5.png
[6]: ./media/machine-learning-r-quickstart/fig6.png
[7]: ./media/machine-learning-r-quickstart/fig7.png
[8]: ./media/machine-learning-r-quickstart/fig8.png
[9]: ./media/machine-learning-r-quickstart/fig9.png
[10]: ./media/machine-learning-r-quickstart/fig10.png
[11]: ./media/machine-learning-r-quickstart/fig11.png
[12]: ./media/machine-learning-r-quickstart/fig12.png
[13]: ./media/machine-learning-r-quickstart/fig13.png
[14]: ./media/machine-learning-r-quickstart/fig14.png
[15]: ./media/machine-learning-r-quickstart/fig15.png
[16]: ./media/machine-learning-r-quickstart/fig16.png
[17]: ./media/machine-learning-r-quickstart/fig17.png
[18]: ./media/machine-learning-r-quickstart/fig18.png
[19]: ./media/machine-learning-r-quickstart/fig19.png
[20]: ./media/machine-learning-r-quickstart/fig20.png
[21]: ./media/machine-learning-r-quickstart/fig21.png
[22]: ./media/machine-learning-r-quickstart/fig22.png

[26]: ./media/machine-learning-r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

<!---HONumber=July15_HO3-->