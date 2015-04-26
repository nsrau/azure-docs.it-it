<properties title="Quick start guide to R for Azure Machine Learning Studio" pageTitle="Guida introduttiva a R per Azure Machine Learning Studio" description="Informazioni su come usare R in Azure Machine Learning Studio." metaKeywords="R azure, r machine learning, azure r machin elearning" services="machine-learning" solutions="" documentationCenter="" authors="larryfr" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="larryfr" />

#Guida introduttiva a R per Azure ML

Stephen F Elston, PhD.

##  Introduzione

Azure ML contiene numerosi moduli avanzati di apprendimento automatico e manipolazione dei dati.  Il potente linguaggio R è stato definito come la lingua franca dell'analisi.  Fortunatamente, in Azure ML è possibile estendere l'analisi e la manipolazione dei dati con il linguaggio R. In questo modo, infatti, è possibile combinare la scalabilità e la semplicità di distribuzione di Azure ML con la flessibilità e la profondità di analisi del linguaggio R.

Questo documento consente di iniziare rapidamente la procedura di estensione di Azure ML usando il linguaggio R. Fornisce infatti le informazioni necessarie per creare, testare ed eseguire il codice R nell'ambito di Azure ML. Nel corso della guida verrà creata una soluzione di previsione completa usando il linguaggio R in Azure ML.  

###Previsione e il set di dati

La previsione è un metodo analitico molto utile e ampiamente distribuito. Viene comunemente usata per vari scopi, dalla previsione di vendita di articoli stagionali alla definizione dei livelli di scorte ottimali, fino alla previsione di variabili macroeconomiche. In genere, inoltre, viene eseguita con modelli in serie temporale.

I dati in serie temporale sono dati in cui i valori hanno un indice temporale, che può essere regolare, ad esempio ogni minuto od ogni mese, o irregolare. Sui dati in serie temporale si basano i modelli in serie temporale. Il linguaggio R contiene un'architettura flessibile ed estese funzioni di analisi per i dati in serie temporale.

In questa esercitazione prenderemo in esame la produzione casearia in California, con i relativi dati sui prezzi.  I dati a nostra disposizione comprenderanno informazioni mensili sulla produzione di vari prodotti caseari e il prezzo del latte intero, un prodotto di base di riferimento.

I dati usati in questo articolo, insieme agli script R, possono essere [scaricati qui][download]. Questi dati sono stati originariamente sintetizzati da informazioni pubblicate dalla University of Wisconsin all'indirizzo http://future.aae.wisc.edu/tab/production.html.

###	Organizzazione

Durante le varie fasi della guida verranno fornite istruzioni per creare, testare ed eseguire codice R per l'analisi e la manipolazione di dati in ambiente Azure ML.  

* In primo luogo, verranno illustrati i concetti di base inerenti all'utilizzo del linguaggio R in ambiente Azure ML Studio.

* Verranno quindi presentati vari aspetti delle funzioni di input/output relative ai dati, al codice R e ai grafici in ambiente Azure ML.

* In seguito, verrà creata la prima parte della soluzione di previsione tramite la compilazione di codice per la pulizia e la trasformazione dei dati.

* Con i dati così preparati, verrà eseguita un'analisi delle correlazioni tra alcune delle variabili presenti nel set di dati.

* Infine, verrà creato un modello di previsione in serie temporale (stagionale) per la produzione di latte.

##Sommario

* [Interazione con R in ML Studio](#mlstudio)

* [Input e output di dati nel modulo Execute R Script](#scriptmodule)

* [Filtraggio e trasformazione dei dati](#filtering)

* [Oggetti serie temporale e analisi delle correlazioni](#timeseries)

* [Esempio di serie temporale: previsione stagionale](#seasonalforecasting)

* [Appendice A - Guida a RStudio](#appendixa)

* [Appendice B - Letture di approfondimento](#appendixb)

##<a id="mlstudio"></a>Interazione con R in ML Studio

Questa sezione illustra i concetti di base dell'interazione con R in ambiente ML Studio.  Il linguaggio R costituisce un potente strumento per la creazione di analisi personalizzate e moduli di manipolazione dei dati in ambiente Azure ML.

In questa guida useremo RStudio per sviluppare, testare ed eseguire il debug di codice R su piccola scala.  Il codice verrà quindi tagliato e incollato in un modulo Execute R Script di ML Studio, pronto per l'esecuzione.  

###Modulo Execute R Script

All'interno di ML Studio, gli script R vengono eseguiti nel modulo Execute R Script.  Nella Figura 1 è riportato un esempio del modulo Execute R Script di ML Studio.

 ![The ML Studio environment showing the Execute R Script module selected][1]

* Figura 1. Ambiente ML Studio con il modulo Execute R Script selezionato.*

Facendo riferimento alla Figura 1, esaminiamo le parti dell'ambiente ML Studio di maggiore importanza per l'interazione con il modulo Execute R Script.

- I moduli dell'esperimento vengono visualizzati nel riquadro centrale.

- La finestra presente nella parte superiore del riquadro di destra consente di visualizzare e modificare gli script R,  

- mentre nella parte inferiore del riquadro sono riportate alcune proprietà del modulo Execute R Script. È possibile visualizzare e i log degli errori e di output facendo clic sulle rispettive opzioni di questo riquadro.

Il modulo Execute R Script verrà trattato in dettaglio nelle parti successive di questo documento.

Quando si usano funzioni R complesse, è preferibile effettuare le operazioni di modifica, test e debug in RStudio. Come con qualsiasi altro software di sviluppo, inoltre, è opportuno estendere il codice in modo incrementale, così da poterlo verificare in piccoli e semplici casi di test e, se funziona correttamente, tagliarlo e incollarlo nella finestra dello script R del modulo Execute R Script.  Questo approccio consente infatti di sfruttare sia l'ambiente RStudio IDE sia la potenza di Azure ML.  

####Esecuzione del codice R

Nel modulo Execute R Script, qualsiasi porzione di codice R viene eseguita nel momento in cui si avvia l'esperimento facendo clic sul pulsante **Esegui**.  Al termine dell'esecuzione, viene visualizzato un segno di spunta in corrispondenza dell'icona di Execute R Script.

####Codifica R difensiva per Azure ML

Se, ad esempio, si sta usando Azure ML per sviluppare codice R per un servizio Web, è opportuno pianificare come il codice riuscirà a gestire eccezioni e input di dati imprevisti. Per maggiore chiarezza, per gli esempi di codice riportati in questa guida non tratterò in modo approfondito le modalità di verifica e gestione delle eccezioni.  Nel corso della guida, tuttavia, esporrò diversi esempi di funzioni che sfruttano la capacità di gestione delle eccezioni di R.  

Per maggiori informazioni sulla gestione delle eccezioni di R, vedere le sezioni dedicate all'argomento nel libro di Wickham elencato in [Appendice B - Letture di approfondimento](#appendixb).


####Debug e testing di R in ML Studio

Come già anticipato, in genere è consigliabile eseguire il test e il debug del codice R creato su scala ridotta in RStudio.  In alcuni casi, tuttavia, è necessario monitorare i problemi inerenti al codice R direttamente in Execute R Script.  È inoltre buona abitudine verificare i risultati in ML Studio.

L'output generato dall'esecuzione del codice R e riprodotto sulla piattaforma Azure ML è riportato essenzialmente nel log di output. Alcune informazioni aggiuntive, tuttavia, sono disponibili anche nel log degli errori.  

Se si verifica un errore in Studio ML durante l'esecuzione di codice R, è opportuno in primo luogo aprire il log degli errori, poiché contiene messaggi utili a comprendere e correggere l'errore.  Per visualizzare il log degli errori, fare clic su **Visualizza log degli errori** nel **riquadro delle proprietà** per identificare il modulo Execute R Script che contiene l'errore.

In un modulo Execute R Script, ad esempio, ho eseguito il seguente codice R con una variabile y non definita.

```r
x <- 1.0
z <- x + y
```

L'esecuzione del codice non riesce, generando così una condizione di errore. Facendo clic su **Visualizza log degli errori** nel **riquadro delle proprietà** viene generata la schermata mostrata nella Figura 2:

  ![Error message pop up][2]

* Figura 2. Finestra di popup con il messaggio di errore.*

È necessario, a questo punto, aprire il log di output per visualizzare il messaggio di errore di R. Fare clic sul modulo Execute R Script e quindi sull'elemento **Visualizza log di output** nel **riquadro delle proprietà** sulla destra. Viene visualizzata una nuova finestra del browser con il seguente contenuto:


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

In R, per ispezionare il valore di qualsiasi oggetto è possibile stamparlo nel file output.log.  Le regole per l'analisi dei valori di oggetto sono essenzialmente le stesse di una sessione R interattiva.  Se, ad esempio, si digita un nome di variabile in una riga, il valore dell'oggetto verrà stampato nel file output.log.  

####Pacchetti di ML Studio

Azure ML viene fornito con oltre 350 pacchetti R preinstallati.  Per visualizzare l'elenco dei pacchetti preinstallati, è possibile immettere il seguente codice nel modulo Execute R Script.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se al momento non si riesce a comprendere l'ultima riga del codice, continuare a leggere.  La parte restante del documento sarà incentrata principalmente sull'utilizzo del linguaggio R in ambiente Azure ML.

###	Introduzione a RStudio

RStudio è un ambiente di sviluppo integrato molto diffuso per il linguaggio R. In questa guida di avvio rapido useremo RStudio per eseguire operazioni di modifica, test e debug del codice R creato.  Dopo che il codice R è stato testato ed è pronto per l'uso, è sufficiente tagliarlo e incollarlo dall'editor di RStudio in un modulo Execute R Script di ML Studio.  

Consiglio di installare R nel proprio computer se non è già installato. Download gratuiti del software R open source sono disponibili nel sistema CRAN (Comprehensive R Archive Network) all'indirizzo http://www.r-project.org/. Sono disponibili download per Windows, MacOS e Linux/UNIX. Scegliere un mirror vicino e seguire le istruzioni di download.   Il sistema CRAN contiene anche una serie di utili pacchetti di analisi e manipolazione di dati.

Se non si ha familiarità con RStudio, è consigliabile scaricare e installare la versione desktop. È possibile trovare i download di RStudio per Windows, MacOS e Linux/UNIX all'indirizzo http://www.rstudio.com/products/RStudio/. Seguire le indicazioni fornite per installare RStudio sul proprio desktop.  

Un tutorial di introduzione a RStudio è disponibile all'indirizzo https://support.rstudio.com/hc/it-it/sections/200107586-Using-RStudio.

Informazioni aggiuntive sull'utilizzo di RStudio sono inoltre disponibili nell'[Appendice A][appendixa].  

##<a id="scriptmodule"></a>Input e output di dati nel modulo Execute R Script

Questa sezione descrive come eseguire l'input e l'output di dati nel modulo Execute R Script.  In particolare, analizzeremo le modalità in cui viene gestita la lettura di vari tipi di dati in ingresso e in uscita dal modulo Execute R Script.

Il codice completo per questa sezione è contenuto nel file .zip scaricato in precedenza.

###Caricamento e controllo di dati in ML Studio

####<a id="loading"></a>Caricamento del set di dati

Inizieremo caricando il file **csdairydata.csv** in Azure ML Studio.

- Avviare l'ambiente Azure ML Studio.

- Fare clic sul segno + nell'angolo inferiore sinistro della schermata e selezionare **Set di dati**.

- Fare clic su **Sfoglia** per selezionare il file.

- Accertarsi che sia selezionato il tipo **File CSV generico con intestazione (.csv)**.

- Fare clic sul segno di spunta.

- Dovrebbe essere possibile visualizzare il nuovo set di dati facendo clic sulla scheda **Set di dati**.  

####Creazione di un esperimento

Ora che disponiamo di un certo numero di dati in ML Studio, dobbiamo creare un esperimento per eseguire l'analisi effettiva.  

- Fare clic sul segno + nell'angolo inferiore sinistro e selezionare **Esperimento**

- Assegnare un titolo all'esperimento.  Chiamerò il mio esperimento **CA Dairy Analysis** (Analisi casearia CA).

- Cercare il set di dati appena caricato.

- Trascinare il **set di dati csdairydata.csv** nell'esperimento.  

- Nella casella **Cerca elementi esperimento** disponibile nella parte superiore del riquadro di sinistra, digitare **Execute R Script**.  Nell'elenco di ricerca viene visualizzato il modulo corrispondente.

- Trascinare il modulo Execute R Script nel pallet.  

- Collegare l'output del **set di dati csdairydata.csv** all'input più a sinistra (**Dataset1**) del modulo **Execute R Script**.

- **Non dimenticare di fare clic sul pulsante Salva**. 

A questo punto l'esperimento dovrebbe avere un aspetto simile a quello della Figura 3.

![The CA Dairy Analysis Experiment with dataset and Execute R Script module][3]

* Figura 3.  L'esperimento CA Dairy Analysis con il set di dati e il modulo Execute R Script.*

####Controllo dei dati

Diamo ora un'occhiata ai dati caricati nell'esperimento.  Nell'esperimento fare doppio clic sull'output del **set di dati cadairydata.csv** e selezionare **visualizza**.   Dovrebbe apparire una tabella simile a quella riportata nella Figura 4.  

![The summary of the cadairydata.csv data set][4]

* Figura 4.  Riepilogo del set di dati cadairydata.csv.*

In questa tabella sono presenti numerose informazioni utili.  La riga **Tipo di funzionalità** mostra i tipi di dati che Azure ML Studio assegna alle colonne nel nostro set di dati.  È inoltre visibile il primo gruppo di righe del set di dati.  Avere un quadro d'insieme può essere di grande aiuto prima di iniziare la fase di analisi vera e propria.

###	Primo script R

Creiamo adesso un primo semplice script R per avviare l'esperimento in Azure ML Studio.  Io ho creato e testato il seguente script in RStudio:  

```r
## Only one of the following two lines should be used
## If running in ML Studio use the first line with maml.mapInputPort().
## If in RStudio used the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure ML Studio.
maml.mapOutputPort('cadairydata')
```

Adesso devo trasferire lo script in Azure ML Studio.  Potrei semplicemente eseguire un'operazione di copia e incolla,  ma in questo caso trasferirò lo script R tramite un file zip.

###	Input di dati nel modulo Execute R Script

Analizziamo ora le possibili modalità di input nel modulo Execute R Script.  In questo esempio leggeremo i dati caseari della California nel modulo Execute R Script.  

Per il modulo Execute R Script sono disponibili tre modalità di input.  È possibile usarne una o tutte, in base alle esigenze.  Sarebbe anche possibile usare uno script R, che non richiede alcuna operazione di input.  

Esaminiamo quindi ciascuna delle modalità di input, da sinistra a destra. È possibile visualizzare i nomi delle modalità di input posizionando il cursore sopra ognuna di esse e leggendo la descrizione comando.  

####	Script Bundle

La modalità di input Script Bundle consente di trasferire il contenuto di un file zip nel modulo Execute R Script.  Per leggere il contenuto del file zip nel proprio codice R è possibile usare uno dei seguenti comandi:

```r
source("src/yourfile.R") # Reads an zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [WACOM.NOTE] Azure ML gestisce i file contenuti nello zip come se si trovassero nella directory src/; è quindi necessario aggiungere ai nomi dei file un prefisso corrispondente al nome della directory.  

Informazioni sul caricamento di set di dati sono già state fornite nella sezione [Caricamento del set di dati](#loading).  Dopo aver creato e testato lo script R illustrato nella sezione precedente, procedere come segue:

1. Salvare lo script R in un file .R.  Chiamerò il mio file di script "simpleplot.R".  

2.  Creare un file zip e copiare al suo interno lo script creato.

3.	Aggiungere il file ai **set di dati** di ML Studio, specificando **zip** come tipo di file. Il file zip dovrebbe apparire ora tra i set di dati disponibili.

4.	Trascinare il file zip dai **set di dati** all'**area di disegno di ML Studio**.

5.	Collegare l'output dell'icona relativa ai **dati zip** nella modalità di input **Script Bundle** del modulo **Execute R script**.

6.	Digitare la funzione `source()` con il nome del file zip nella finestra di codice relativa al modulo **Execute R Script**.  Nel mio caso, ho digitato `source("src/SimplePlot.R")`.  

7.	Non dimenticare di fare clic su **Salva**.

Al termine di questa procedura, il modulo Execute R Script eseguirà lo script R presente nel file zip durante l'esecuzione dell'esperimento. A questo punto l'esperimento dovrebbe avere un aspetto simile a quello riportato nella Figura 5.

![Experiment using zipped R script][6]

* Figura 5. Esperimento con uno script R compresso.*

####Dataset1

È possibile passare una tabella rettangolare di dati nel codice R usando la modalità di input Dataset1.  Nel nostro script semplice, la funzione `maml.mapInputPort(1)` legge i dati dalla porta 1.  I dati vengono quindi assegnati a un nome di variabile di frame di dati all'interno del codice.  Nel nostro script semplice è la prima riga del codice a effettuare l'assegnazione.

```r
cadairydata <- maml.mapInputPort(1)
```

Eseguire l'esperimento facendo clic sul pulsante **Esegui**.  Al termine dell'esecuzione, fare clic sul modulo Execute R Script, quindi su **Visualizza log di output** nel riquadro delle proprietà.  Nel browser viene visualizzata una nuova pagina con il contenuto del file Output.log.  Scorrendo verso il basso dovrebbe apparire una porzione di testo simile alla seguente:

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

Questi risultati sono essenzialmente quelli previsti, con 228 osservazioni e 9 colonne nel frame di dati.  Sono riportati i nomi delle colonne, il tipo di dati R e un esempio di ciascuna colonna.

> [WACOM.NOTE] Questo output di stampa è disponibile anche nell'ambito dell'output della porta R Device del modulo Execute R Script.  Affronteremo gli output del modulo Execute R nella sezione successiva.  

####Dataset2

Il comportamento della modalità di input Dataset2 è identico a quello di Dataset1.  Consente infatti di passare nel codice R creato una seconda tabella rettangolare di dati.  Per il trasferimento dei dati viene usata la funzione `maml.mapInputPort(2)` con l'argomento 2.  

###Output del modulo Execute R Script

####Output di un frame di dati

Usando la funzione `maml.mapOutputPort()` è possibile eseguire l'output del contenuto di un frame di dati R come tabella rettangolare tramite la porta Result Dataset1.  Nel nostro script R semplice questa operazione può essere eseguita con la riga seguente:

```r
maml.mapOutputPort('cadairydata')
```

Dopo aver eseguito l'esperimento, fare clic sulla porta di output Result Dataset1 e quindi su **Visualizza**.  Dovrebbe apparire una tabella simile a quella riportata nella Figura 6.

![The visualization of the output of the California dairy data][7]

* Figura 6. Visualizzazione dell'output dei dati caseari della California.*

In questo caso l'output è identico all'input, come previsto.  

###	Output R Device

L'output Device del modulo Execute R Script contiene messaggi e output grafico.  Alla porta di output R Device vengono inviati da R sia l'output standard sia i messaggi di errore standard.  

Per visualizzare l'output R Device, fare clic sulla porta e quindi su **Visualizza**.  Nella Figura 7 sono riportati l'output standard e i messaggi di errore standard generati dallo script R.

![Standard output and standard error from the R Device port][8]

* Figura 7. Output standard e messaggi di errore standard trasmessi dalla porta R Device.*

Scorrendo verso il basso è possibile visualizzare l'output grafico generato dallo script R, riportato nella Figura 8.  

![Graphics output from the R Device port][9]

* Figura 8. Output grafico trasmesso dalla porta R Device.*  

##<a id="filtering"></a>Filtraggio e trasformazione dei dati

In questa sezione effettueremo alcune operazioni di filtraggio e trasformazione di base sui dati caseari della California.  Al termine di questa sezione i dati saranno disponibili in un formato adatto alla creazione di un modello di analisi.  

In particolare, effettueremo alcune attività comuni di pulizia e trasformazione di dati, trasformazione di tipi, filtraggio su frame di dati, aggiunta di nuove colonne elaborate e trasformazione di valori. Queste procedure di base potrebbero essere utili per affrontare le numerose variazioni che spesso si generano nei problemi della vita reale.

Il codice R completo per questa sezione è disponibile nel file zip scaricato in precedenza.

###	Trasformazioni di tipi

Adesso che siamo in grado di leggere i dati caseari della California nel codice R del modulo Execute R Script, dobbiamo verificare che i dati delle colonne siano del tipo e nel formato desiderati.  

R è un linguaggio tipizzato dinamicamente, in cui i tipi di dati vengono assegnati l'uno all'altro in base alle esigenze. Nel linguaggio R, i dati atomic possono essere di tipo numerico, logico e Char.  Il tipo fattore viene usato invece per archiviare in modo compatto i dati relativi alle categorie.  Per altre informazioni sui tipi di dati, vedere i riferimenti elencati nell'[Appendice B - Letture di approfondimento](#appendixb).

Quando i dati tabulari vengono letti in R da un'origine esterna, è sempre opportuno controllare nelle colonne i tipi risultanti.  È possibile, ad esempio, che una colonna che sarebbe dovuta essere di tipo Char in realtà risulti di tipo fattore, o viceversa.  In altri casi, una colonna che ci si aspetterebbe di tipo numerico in realtà è rappresentata da dati di tipo Char, ad esempio "1.23" anziché "1,23" espresso come numero a virgola mobile.  

Fortunatamente, convertire un tipo in un altro tipo è piuttosto semplice, purché sia possibile il mapping.  Ad esempio, non è possibile convertire "Nevada" in un valore numerico, ma è possibile convertirlo in un fattore (variabile di categoria).  Un valore numerico 1, invece, può essere convertito sia nel carattere "1" sia in un fattore.  

La sintassi per queste conversioni è semplice: `as.datatype()`.  Le funzioni di conversione dei tipi comprendono:

* `as.numeric()`

* `as.character()`

* `as.logical()`

* `as.factor()`

Diamo ora un'occhiata ai tipi di dati delle colonne elaborate nella sezione precedente. Tutte le colonne sono di tipo numerico, fatta eccezione per le colonne con etichetta "Month", che sono di tipo Char.  Proviamo quindi a convertire questa colonna in tipo fattore e controlliamo i risultati.  

Ho eliminato la riga che ha creato la matrice di grafici a dispersione e aggiunto una riga che converte la colonna "Month" in fattore.  Nel mio esperimento mi limiterò a tagliare il codice R e a copiarlo nella finestra di codice del modulo Execute R Script.  Sarebbe possibile anche aggiungere il file zip e caricarlo in Azure ML Studio, ma questa procedura richiederebbe diversi passaggi.  

```r
## Only one of the following two lines should be used
## If running in ML Studio use the first line with maml.mapInputPort().
## If in RStudio used the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure ML Studio.
maml.mapOutputPort('cadairydata')
```

Eseguiamo il codice e analizziamo l'output. Facendo clic su **Visualizza** nel menu di R Device viene generato l'output mostrato nella Figura 9.

![Summary of the dataframe with a factor variable][10]

* Figura 9. Riepilogo del frame di dati con variabile di fattore.*

Il tipo relativo alla colonna "Month" dovrebbe essere "**Factor w/ 14 levels**".  Questo è ovviamente un problema poiché in un anno ci sono solo 12 mesi. È possibile anche controllare che, facendo clic sul pulsante **Visualizza** relativo alla porta Result Dataset, sia mostrato il tipo "**Categorical**".

Il problema è che la colonna "Month" non è stata codificata in modo sistematico.  In alcuni casi, infatti, per identificare un mese è possibile usare sia il nome completo, ad esempio Aprile, sia l'abbreviazione, ad esempio Apr. È possibile risolvere questo problema tagliando la stringa a 3 caratteri.  La riga di codice dovrebbe ora apparire così:

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Eseguire nuovamente l'esperimento e **visualizzare** l'output della porta R Device. I risultati dovrebbero essere simili al contenuto della Figura 10.  

![Summary of the dataframe with correct number of factor levels][11]

* Figura 10. Riepilogo del frame di dati con il numero di livelli di fattore corretto*

La variabile di fattore contiene ora 12 livelli.

###Filtraggio di frame di dati di base

I frame di dati R supportano avanzate funzionalità di filtraggio.  I set di dati, infatti, possono essere suddivisi in sottoinsiemi usando filtri logici su righe o colonne. In molto casi, tuttavia, sono necessari complessi criteri di filtro. I riferimenti elencati nell'[Appendice B - Letture di approfondimento](#appendixb) contengono numerosi esempi di filtraggio di frame di dati.  

Possiamo eseguire semplici operazioni di filtraggio anche sul nostro set di dati. Osservando le colonne nel frame di dati cadariydata, vedremo che ci sono due colonne non necessarie.  La prima colonna contiene solo un numero di riga, non molto utile. La seconda colonna, Year.Month, contiene informazioni ridondanti.  È possibile escludere facilmente queste colonne usando il seguente codice R.

> [WACOM.NOTE] In questa sezione, d'ora in avanti mostrerò solo il codice aggiuntivo inserito nel modulo Execute R Script.  Aggiungerò ogni nuova riga **prima** della funzione `str()`. Con questa funzione, invece, verificherò i miei risultati in Azure ML Studio.

Al codice R fin qui creato nel modulo Execute R Script aggiungo la riga seguente.

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Eseguire questo codice nell'esperimento e verificare i risultati facendo clic sul pulsante **Visualizza** relativo alla porta R Device. I risultati previsti sono visualizzati nella Figura 11.

![The summary of the dataframe with two columns removed][12]

* Figura 11.  Riepilogo del frame di dati con due colonne rimosse.*

Ottime notizie! I risultati ottenuti sono quelli previsti.

###Aggiunta di una nuova colonna

Per la creazione di modelli in serie temporale sarà utile disporre di una colonna contenente i nomi dei mesi a partire dall'inizio della serie temporale. Creeremo quindi una nuova colonna denominata "Month.Count".

Per semplificare l'organizzazione del codice creeremo inoltre la nostra prima semplice funzione: `num.month()`.  Applicheremo quindi la funzione per creare una nuova colonna nel frame di dati. Il nuovo codice avrà il seguente aspetto:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series.
num.month <- function(Year, Month) {
  ## Find the starting year.
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series.
  12 * (Year - min.year) + Month - 1
}

# Compute the new column for the dataframe.
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Eseguire l'esperimento aggiornato e fare clic sull'opzione **Visualizza** relativa alla porta R Device per visualizzare i risultati. I risultati previsti sono illustrati nella Figura 12.

![Summary of the dataframe with the additional column][13]

* Figura 12. Riepilogo del frame di dati con la colonna aggiuntiva.*

Sembra che tutto stia funzionando perfettamente.  Abbiamo ora la nuova colonna con i valori previsti nel frame di dati.

###Trasformazioni di valori

In questa sezione eseguiremo semplici operazioni di trasformazione sui valori di alcune delle colonne del frame di dati.  Il linguaggio R supporta trasformazioni di dati pressoché arbitrarie.  I riferimenti elencati nell'[Appendice B - Letture di approfondimento](#appendixb) contengono numerosi esempi di trasformazioni di valori.

Se si esaminano i valori contenuti nei riepiloghi del frame di dati si noterà qualcosa di strano. In California si produce più gelato che latte?  No, ovviamente no, sfortunatamente per gli amanti del gelato. Le unità di misura sono diverse. Il prezzo è espresso in libbre statunitensi, il latte in milioni di libbre statunitensi, il gelato in migliaia di galloni statunitensi e i fiocchi di latte in migliaia di libbre statunitensi. Supponendo che il gelato pesi circa 6,5 libbre per gallone, possiamo facilmente eseguire la moltiplicazione per convertire questi valori in modo che siano tutti espressi nella stessa unità di misura, ovvero in migliaia di libbre.

Per il nostro modello previsionale, inoltre, ci serviamo di un modello moltiplicativo per eseguire un aggiustamento stagionale dei dati. Una trasformazione logaritmica, infine, ci consente di usare un modello lineare, semplificando notevolmente il processo.  Possiamo applicare la trasformazione logaritmica alla stessa funzione a cui è stato applicato il moltiplicatore.

Nel codice seguente definisco una nuova funzione, `log.transform()`, e la applico alle righe contenenti i valori numerici. La funzione R `Map()` consente invece di applicare la funzione `log.transform()` alle colonne selezionate del frame di dati.  La funzione `Map()` è analoga alla funzione `apply()`, rispetto alla quale consente di usare più di un elenco di argomenti. Un elenco di moltiplicatori, inoltre, fornisce il secondo argomento alla funzione `log.transform()`. La funzione `na.omit()`, infine, viene usata un po' come strumento di pulizia per garantire che nel frame di dati non ci siano valori mancanti o non definiti.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments.
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch.
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA.
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data.
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Dalla funzione `log.transform()` hanno origine diverse operazioni.  La maggior parte del codice è destinata a controllare che non siano presenti problemi con gli argomenti o a gestire eventuali eccezioni che possono verificarsi durante le operazioni di calcolo. Solo poche righe del codice, quindi, eseguono effettivamente i calcoli.

L'obiettivo della programmazione difensiva è impedire che l'esito negativo di una funzione possa compromettere la continuazione dell'intero processo di elaborazione.  Un errore improvviso in un'analisi di lunga durata sarebbe infatti piuttosto frustrante per gli utenti. Per evitare questa situazione, devono essere scelti valori restituiti predefiniti che, in caso di errore, limitino i danni nei processi di elaborazione successivi.  Viene inoltre generato un messaggio di avviso per informare gli utenti che si è verificato un errore.

Se non si ha familiarità con la programmazione difensiva in R, questo codice può sembrare eccessivamente complesso. Affronteremo di seguito i passaggi principali:

1. Viene definito un vettore di quattro messaggi usati per comunicare informazioni su alcuni degli errori e delle eccezioni che si possono verificare all'interno del codice.

2.  Per ciascun caso restituirò il valore NA.  Esistono tuttavia molte altre possibilità con effetti collaterali inferiori.  Ad esempio, potrei restituire un vettore di zeri o il vettore di input originale.

3.  Vengono eseguiti controlli sugli argomenti alla funzione.  Per ciascun caso, nel momento in cui viene identificato un errore, viene restituito un valore predefinito e viene generato un messaggio dalla funzione `warning()`. Uso `warning()` anziché `stop()` poiché quest'ultimo interromperebbe l'esecuzione, che è esattamente ciò che sto cercando di evitare. Osservare come abbia scritto questo codice in stile procedurale: un approccio funzionale sarebbe apparso in questo caso eccessivamente complesso e oscuro.

4.  Viene eseguito il wrapping dei calcoli logaritmici nella funzione `tryCatch()`, in modo che eventuali eccezioni non determinino un arresto improvviso del processo di elaborazione. Senza la funzione `tryCatch()`, infatti, la maggiore parte degli errori generati dalla funzione R risulterebbe in un segnale di stop, interrompendo di fatto il processo.

Eseguire questo codice R nel proprio esperimento e osservare l'output generato nel file output.log.  Facendo clic su **Visualizza** nel menu di R Device sarà possibile osservare i valori delle quattro colonne trasformati, come mostrato nella Figura 13.

![The summary of the transformed values in the dataframe][14]

* Figura 13. Riepilogo dei valori trasformati nel frame di dati.*

I valori sono stati trasformati correttamente.  La produzione di latte supera ora di gran lunga la produzione di tutti gli altri prodotti caseari, ricordandoci come si stia ora usando una scala logaritmica .

A questo punto i nostri dati sono puliti e siamo pronti per qualche operazione di modeling.  Esaminando la visualizzazione di riepilogo dell'output generato dalla porta Result Dataset del modulo Execute R Script, si noterà che la colonna "Month" è di tipo "Categorical" con 12 valori univoci, come desiderato.

##<a id="timeseries"></a>Oggetti serie temporale e analisi delle correlazioni

In questa sezione analizzeremo alcuni oggetti serie temporale di R e analizzeremo le correlazioni tra alcune delle variabili. Il nostro obiettivo è generare un frame di dati contenente informazioni di correlazione pairwise a vari intervalli.

Il codice R completo per questa sezione è disponibile nel file zip scaricato in precedenza.

###Oggetti serie temporale in R

Come già accennato, le serie temporali sono costituite da una serie di valori di dati indicizzati per data e ora.  Gli oggetti serie temporale vengono usati in R per creare e gestire l'indice temporale. Questi oggetti offrono infatti una serie di vantaggi. In primo luogo, liberano gli utenti dai numerosi dettagli di gestione dei valori di indice della serie temporale che sono incapsulati nell'oggetto. Consentono inoltre di usare i vari metodi messi a disposizione dalle serie temporali per operazioni di tracciamento, stampa, modeling, ecc.

In genere viene usata la classe di serie temporale POSIXct poiché è relativamente semplice e permette di misurare il tempo a partire dal 1° gennaio 1970.  In questo esempio useremo quindi oggetti serie temporale di tipo POSIXct. Altre classi di oggetti serie temporale comunemente usate in R includono zoo e xts, serie temporali estensibili.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

###	Esempio di oggetto serie temporale

Iniziamo con il nostro esempio. Trascinare un **nuovo modulo Execute R Script** nell'esperimento.  Collegare la porta di output Result Dataset 1 del modulo Execute R Script esistente alla porta di input Dataset 1 del nuovo modulo Execute R Script.

Come per il primo esempio, procedendo con lo svolgimento della procedura mostrerò, per ogni passaggio, solo le righe aggiuntive incrementali del codice R.  

####	Lettura del frame di dati

Come prima operazione, eseguiamo la lettura in un frame di dati e accertiamoci di ottenere i risultati previsti. Digitiamo quindi il seguente codice:

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Eseguiamo ora l'esperimento.  L'output visualizzato per la porta R Device dovrebbe essere simile a quello riportato nella Figura 14.

![Summary of the dataframe in the Execute R Script module][15]

* Figura 14. Riepilogo del frame di dati nel modulo Execute R Script.*

Questi dati sono del tipo e nel formato previsti.  Osservare come la colonna "Month" sia di tipo fattore e contenga il numero di livelli previsto.

####Creazione di un oggetto serie temporale

È necessario aggiungere un oggetto serie temporale al nostro frame di dati.  Sostituiamo quindi il codice corrente con il seguente, che aggiunge una nuova colonna di classe POSIXct.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Verifichiamo ora l'output della porta R Device. Dovrebbe essere simile a quello riportato nella Figura 15.

![Summary of the dataframe with a time series object][16]

* Figura 15. Riepilogo del frame di dati con un oggetto serie temporale.*

Dal riepilogo è possibile notare come la nuova colonna sia effettivamente di classe POSIXct.

###Esplorazione e trasformazione di dati

Esploriamo adesso alcune delle variabili presenti in questo set di dati.  A questo scopo ci avvarremo di una matrice di grafici a dispersione. Sostituisco quindi la funzione `str()` nel precedente codice R con la riga seguente:

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Eseguiamo il codice e vediamo cosa succede.  Il grafico generato per la porta R Device dovrebbe essere simile a quello riportato nella Figura 16.

![Scatterplot matrix of selected variables][17]

* Figura 16. Matrice di grafici a dispersione di alcune variabili.*

È possibile notare una struttura un po' insolita nelle relazioni tra queste variabili.  Questo può essere dovuto ad alcune tendenze nei dati o al fatto che le variabili non sono state standardizzate.

###Analisi di correlazione

Per eseguire un'analisi di correlazione è necessario detrendizzare le variabili e standardizzarle. A questo scopo, potremmo semplicemente usare la funzione R `scale()`, che centra e ridimensiona le variabili e può essere eseguita più velocemente.  In questo caso, tuttavia, preferisco mostrare un esempio di programmazione difensiva in R.

La funzione `ts.detrend()` illustrata di seguito esegue entrambe le operazioni. Le due seguenti righe di codice consentono di detrendizzare i dati e standardizzare i valori.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to detrend and standardize a time series.

  ## Define some messages if they are NULL.  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases.
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit.
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit.
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit.
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct.
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## Detrent the time series using a linear model.
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest.
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships.
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Anche in questo caso, dalla funzione `ts.detrend()` hanno origine diverse operazioni.  La maggior parte del codice è destinata a controllare che non siano presenti problemi con gli argomenti o a gestire eventuali eccezioni che possono verificarsi durante le operazioni di calcolo. Solo poche righe del codice, quindi, eseguono effettivamente i calcoli.

Abbiamo già ampiamente fornito un esempio di programmazione difensiva nella sezione [Trasformazioni di valori](#valuetransformations). Viene eseguito il wrapping di entrambi i blocchi di calcolo nella funzione `tryCatch()`.  Per alcuni errori è opportuno restituire il vettore di input originale, per altri è preferibile un vettore di zeri.  

Osservare come la regressione lineare usata per il detrending sia una regressione in serie temporale e la variabile indipendente un oggetto serie temporale.  

Una volta definita, la funzione `ts.detrend()` viene applicata alle variabili di interesse per il nostro frame di dati. Usando la funzione `as.data.frame()`, dobbiamo quindi assegnare al frame di dati l'elenco risultante dalla funzione `lapply()`.  Grazie agli aspetti difensivi della funzione `ts.detrend()`, l'eventuale impossibilità di elaborare una delle variabili non impedisce la corretta elaborazione delle altre.  

La riga di codice finale crea un grafico a dispersione pairwise. I risultati del grafico a dispersione dopo aver eseguito il codice R sono illustrati nella Figura 17.

![Pairwise scatterplot of de-trended and standardized time series][18]

* Figura 17. Grafico a dispersione pairwise delle serie temporali standardizzate e detrendizzate.*

È possibile confrontare questi risultati con quelli illustrati nella Figura 17. Dopo aver eseguito il detrending e standardizzato le variabili, la struttura che rappresenta le relazioni tra le variabili è notevolmente semplificata.

Per elaborare le correlazioni come oggetti R ccf è possibile usare il codice seguente:

```r
## A function to compute pairwise correlations from a
## list of time series value vectors.
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwaise indices.
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects.
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Eseguendo questo codice viene generato l'output mostrato nella Figura 18.

![List of ccf objects from the pairwise correlation analysis][19]

* Figura 18. Elenco degli oggetti ccf generati dall'analisi di correlazione pairwise.*

Per ogni intervallo è presente un valore di correlazione.  Nessuno di essi, tuttavia, è abbastanza grande da essere significativo. Possiamo quindi concludere che ciascuna variabile può essere modellata in modo indipendente.

###Output di un frame di dati

Abbiamo elaborato le correlazioni pairwise come elenco di oggetti R ccf. Questo determina tuttavia un piccolo problema poiché la porta di output Result Dataset richiede inevitabilmente un frame di dati.  L'oggetto ccf, inoltre, è di per sé un elenco e a noi servono solo i valori nel primo elemento dell'elenco, mentre le correlazioni a vari intervalli.

I codice seguente estrae i valori di intervallo dall'elenco di oggetti ccf, costituiti da elenchi:

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names.
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line only works in Azure ML.
## when running in RStudio, this code will result in an error.
#maml.mapOutputPort('outframe')
```

La prima riga di codice è un po' ingannevole e per capirla è necessario qualche chiarimento. Dall'interno verso l'esterno incontriamo:

1.  L'operatore "**[[**" con l'argomento "**1**": seleziona il vettore di correlazioni ai vari intervalli, a partire dal primo elemento dell'elenco oggetto ccf.

2.  La funzione `do.call()` applica la funzione `rbind()` agli elementi dell'elenco restituito da `lapply()`.

3.  La funzione `data.frame()` assegna il risultato prodotto da `do.call()` a un frame di dati.

Osservare come i nomi di riga si trovino in una colonna del frame di dati. Questo permette infatti di preservarli nel momento il modulo Execute R Script ne esegue l'output.

L'esecuzione del codice produce l'output illustrato nella Figura 29, che è possibile ottenere facendo clic sul pulsante **Visualizza** relativo alla porta Result Dataset. I nomi di riga si trovano nella prima colonna, come previsto.

![Results output from the correlation analysis][20]

* Figura 29. Risultati generati dall'analisi di correlazione.*

##<a id="seasonalforecasting"></a>Esempio di serie temporale: previsione stagionale

I nostri dati si trovano ora in un formato idoneo per l'analisi e abbiamo stabilito che non esistono correlazioni significative tra le variabili.  Passiamo quindi alla creazione di un modello di previsione in serie temporale,  che ci permetterà di prevedere la produzione di latte in California nei 12 mesi del 2013.

Il nostro modello previsionale sarà costituito da due componenti: trend e stagionale.  La previsione completa è data dal prodotto di queste due componenti.  Questo tipo di modello prende il nome di modello moltiplicativo, alternativo al modello additivo. Abbiamo già applicato una trasformazione logaritmica alle variabili di interesse, rendendo l'analisi più duttile.

Il codice R completo per questa sezione è disponibile nel file zip scaricato in precedenza.

###	Creazione del frame di dati per l'analisi

Iniziamo aggiungendo un **nuovo modulo Execute R Script** all'esperimento.  Colleghiamo quindi l'output **Result Dataset** del modello Execute R Script esistente alla modalità di input **Dataset1** del nuovo modulo.  Il risultato dovrebbe essere simile al contenuto della Figura 20.

![The experiment with the new Execute R Script module added][21]

* Figura 20. L'esperimento con il nuovo modulo Execute R Script aggiunto.*

Come per l'analisi di correlazione appena completata, dobbiamo aggiungere una colonna con oggetto serie temporale POSIXct. A questo scopo, usare il codice seguente:

```r
# If running in ML Studio uncomment the first line with maml.mapInputPort().
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Eseguire il codice e osservare l'output della porta R Device. Il risultato dovrebbe essere simile alla Figura 21.

![A summary of the dataframe][22]

* Figura 21. Riepilogo del frame di dati.*

Con questo risultato siamo pronti per avviare l'analisi.

###Creazione di un set di dati di addestramento

Con il frame di dati costruito dobbiamo creare un set di dati di addestramento, che comprenderà tutte le osservazioni, ad eccezione delle ultime 12, relative al 2013, che corrispondono al nostro set di dati di test. Il codice seguente suddivide il frame di dati in sottoinsiemi e crea grafici della produzione casearia e delle variabili di prezzo. Creerò quindi dei grafici per le quattro variabili di produzione e di prezzo. Con una funzione anonima definirò alcuni argomenti per il grafico e scorrerò sull'elenco degli altri due argomenti con la funzione `Map()`. Se pensate che un ciclo For sarebbe stato appropriato, avete ragione. Tuttavia, poiché R è un linguaggio funzionale, adotterò un approccio funzionale.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

L'esecuzione di questo codice produce la serie di grafici in serie temporale ottenuto dall'output R Device mostrato nella Figura 22. Notare che l'asse temporale è espresso in unità di date, uno dei vantaggi offerti dal metodo del grafico in serie temporale.

![First of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-161.png)

![Second of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-162.png)

![Third of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-163.png)

![Fourth of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-164.png)

* Figura 22. Grafici in serie temporale della produzione casearia in California e dei dati di prezzo.*

###	Modello con trend

Dopo aver creato un oggetto serie temporale e aver esaminato i dati, iniziamo a costruire un modello di trend per i dati di produzione di latte in California. A questo scopo, usiamo una regressione in serie temporale.  Dal grafico, tuttavia, appare chiaro che saranno necessari più valori di pendenza e intercetta per modellare correttamente il trend osservato nei dati di addestramento.

Data la scala ridotta dei dati, creerò il modello per il trend in RStudio e taglierò il modello risultante per incollarlo in Azure ML. RStudio fornisce infatti un ambiente interattivo adatto a questo tipo di analisi interattiva.

Come primo tentativo, eseguirò una regressione polinomiale con valori di potenza fino a 3.  Esiste infatti un rischio reale di overfitting (sovradattamento) per questo tipo di modelli. È opportuno quindi evitare termini di ordine superiore.  La funzione `I()` impedisce l'interpretazione dei contenuti (interpreta i contenuti così come sono) e consente di scrivere una funzione interpretata letteralmente in un'equazione di regressione.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Viene generato il seguente contenuto:

```
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
```	

Dai valori P (Pr(>|t|)) presenti in questo output possiamo osservare come il termine al quadrato possa non essere significativo.  Userò quindi la funzione `update()` per modificare il modello eliminando il termine al quadrato.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Viene generato il seguente contenuto:

```
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
```

L'aspetto è decisamente migliore adesso.  Tutti i termini sono significativi.  Il valore 2e-16, tuttavia, è un valore predefinito e non dovrebbe essere preso troppo sul serio  

Come test di integrità, effettueremo un grafico in serie temporale dei dati della produzione casearia in California con la curva di trend mostrata di seguito.  Per creare il modello ed eseguire il grafico, ho aggiunto il codice seguente nel modello Execute R Script di Azure ML (non in RStudio).

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![CA milk production data with trend model shown](./media/machine-learning-r-quickstart/unnamed-chunk-18.png)

* Figura 23. Dati di produzione di latte in California con il modello di trend illustrato.*

Sembra che il modello di trend si adatti perfettamente. Inoltre, non sembra che ci siano tracce di overfitting, ad esempio strani ondulamenti nella curva del modello.  

###Modello con stagionalità

Dopo aver creato un modello con trend, possiamo ora spingerci oltre e includere gli effetti stagionali. Nel modello lineare useremo il mese dell'anno come variabile fittizia per acquisire l'effetto mese per mese. Tenere presente che, quando si introducono variabili di fattore in un modello, l'intercetta non deve essere elaborata.  In caso contrario, la formula risulta eccessivamente specificata ed R eliminerà i fattori desiderati, lasciando invece il termine dell'intercetta.

Poiché disponiamo ora di un modello con trend soddisfacente, possiamo ora usare la funzione `update()` per aggiungere nuovi termini al modello esistente. Nella formula di aggiornamento, il valore -1 elimina il termine dell'intercetta.  Continuiamo ora in RStudio.

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Viene generato il seguente contenuto:

```
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
```

Vediamo come nel modello non sia più presente un termine dell'intercetta ma siano inclusi 12 fattori mese,  proprio come ci aspettavamo.

Creiamo adesso un altro grafico in serie temporale dei dati di produzione casearia in California per verificare l'effettivo funzionamento del modello con stagionalità.  Per creare il modello e generare il grafico, ho aggiunto il codice seguente nel modulo Execute R Script di Azure ML:

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

L'esecuzione di questo codice in Azure ML produce il grafico illustrato nella Figura 24.

![CA milk production with model including seasonal effects](./media/machine-learning-r-quickstart/unnamed-chunk-20.png)

* Figura 24. Produzione di latte in California con un modello che include effetti stagionali.*

La corrispondenza con i dati mostrati nella Figura 24 è piuttosto incoraggiante.  Sia il trend sia l'effetto stagionale (variazione mensile) sembrano ragionevoli.

Come ulteriore verifica sul mostro modello, esaminiamo i valori residui. Il codice seguente elabora i valori previsti dai nostri due modelli, elabora quindi i valori residui per il modello con stagionalità e traccia infine questi valori per i dati di addestramento.

```r
## Compute predictions from our models.
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Il grafico dei residui è illustrato nella Figura 25.

![Residuals of the seasonal model for the training data](./media/machine-learning-r-quickstart/unnamed-chunk-21.png)

* Figura 25. Valori residui per il modello con stagionalità relativo ai dati di addestramento.*

Questi valori residui sembrano ragionevoli.  Non risalta alcuna struttura particolare, ad eccezione dell'effetto della recessione del biennio 2008-2009, che il nostro modello non riesce a rappresentare particolarmente bene.

Il grafico illustrato nella Figura 25 è utile per identificare eventuali schemi correlati al tempo nei valori residui. L'approccio esplicito di elaborare e tracciare graficamente i valori residui consente di metterli in ordine temporale nel grafico.  D'altra parte, se avessi tracciato `milk.lm$residuals`, il grafico non sarebbe stato in ordine temporale.

È possibile usare anche la funzione `plot.lm()` per produrre una serie di grafici diagnostici:

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Questo codice produce una serie di grafici diagnostici, come illustrato nella Figura 26.

![First of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-221.png)

![Second of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-222.png)

![Third of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-223.png)

![Fourth of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-224.png)

* Figura 26. Grafici diagnostici per il modello con stagionalità.*

In questi grafici sono presenti alcuni punti di alta influenza, ma niente di particolarmente preoccupante. Nel grafico Normal Q-Q, inoltre, possiamo vedere come i valori residuali siano distribuiti in modo pressoché normale, presupposto importante per i modelli lineari.

###Previsione e valutazione del modello

È rimasta un'ultima cosa da fare per poter completare il nostro esempio:  elaborare le previsioni e valutare il grado di errore rispetto ai dati effettivi.  La nostra previsione riguarda i 12 mesi del 2013. Per questa previsione, possiamo elaborare una valutazione del grado di errore sui dati effettivi non compresi nel set di dati di addestramento. Possiamo inoltre confrontare le prestazioni dei 18 anni di dati di addestramento con i 12 mesi dei dati di test.  

Per misurare le prestazioni di modelli in serie temporale viene usata, in genere, una serie di parametri. Nel nostro caso useremo la deviazione standard. La funzione seguente elabora la deviazione standard tra due serie:  

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors.

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments.
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
    warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy.
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

 ## Compute predictions from our models.
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe.
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Come con la funzione `log.transform()` analizzata nella sezione relativa alla trasformazione di valori, anche in questa funzione è presente una gran quantità di codice di controllo degli errori e recupero delle eccezioni.  I principi usati sono gli stessi. Il lavoro viene svolto in due posizioni racchiuse in `tryCatch()`.  Poiché stiamo usando i logaritmi dei valori, eseguiremo in primo luogo l'esponenziazione delle serie temporali. Passeremo quindi all'elaborazione della deviazione standard.  

Muniti di una funzione per calcolare la deviazione standard, creiamo ed eseguiamo l'output di un frame di dati contenente le deviazioni standard. Includiamo quindi i termini relativi al modello con trend e completiamo il modello con fattori stagionali. A questo scopo, usiamo il codice seguente con i due modelli lineari costruiti in precedenza:

```r
## Compute the RMS error in a dataframe.
## Include the row names in the first column so they will
## appear in the output of the Execute R Script.
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
## Azure ML Studio.
maml.mapOutputPort('RMS.df')
```

L'esecuzione di questo codice produce nella porta di output Result Dataset il contenuto illustrato nella Figura 27.

![Comparison of RMS error for the models][26]

* Figura 27. Confronto della deviazione standard per i modelli.*

Da questi risultati, è possibile vedere come, aggiungendo i fattori stagionali al modello, la deviazione standard si riduce notevolmente.  Come previsto, inoltre, la deviazione standard per i dati di addestramento è leggermente inferiore rispetto alla previsione.

##<a id="appendixa"></a>APPENDICE A - Guida a RStudio

RStudio viene fornito con una documentazione molto dettagliata. In questa appendice mi limiterò quindi a fornire alcuni link alle sezioni principali della documentazione RStudio, utili per acquisire familiarità con questo prodotto.

1.	Creazione di progetti

	RStudio consente di organizzare e gestire codice R nei progetti.  Documentazione sull'utilizzo dei progetti è disponibile all'indirizzo https://support.rstudio.com/hc/it-it/articles/200526207-Using-Projects.

	Consiglio di seguire queste indicazioni e creare un progetto per gli esempi di codice R riportati in questo documento.  

2.	Modifica ed esecuzione di codice R

	RStudio offre un ambiente integrato per la modifica e l'esecuzione di codice R.  Documentazione su questo argomento è disponibile all'indirizzo https://support.rstudio.com/hc/it-it/articles/200484448-Editing-and-Executing-Code.

3.	Debug

	RStudio include avanzate funzionalità di debug.  Documentazione su queste funzionalità è disponibile all'indirizzo https://support.rstudio.com/hc/it-it/articles/200713843-Debugging-with-RStudio.

	Documentazione sulle funzionalità di risoluzione dei punti di interruzione è disponibili all'indirizzo https://support.rstudio.com/hc/it-it/articles/200534337-Breakpoint-Troubleshooting.

##<a id="appendixb"></a>APPENDICE B - Letture di approfondimento

Se non si ha familiarità con R, in CRAN sono disponibili due utili documenti introduttivi.

- "R for Beginners" di Emmanuel Paradis, un ottimo punto di inizio: http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  

- "An Introduction to R" di W. N. Venables et al, che fornisce informazioni un po' più approfondite: http://cran.r-project.org/doc/manuals/R-intro.html.

Sono stati inoltre pubblicati molti libri sul linguaggio R.  Di seguito sono elencati quelli che ritengo più utili:

- "The Art of R Programming; A Tour of Statistical Software Design" di Norman Matloff, un'eccellente introduzione alla programmazione in R.  

- "R Cookbook" di Paul Teetor, che affronta il linguaggio R con un approccio di tipo problema-soluzione.  

- "R in Action" di Robert Ka
- bacoff, un altro libro introduttivo molto interessante.  Il sito Web "Quick R" costituisce inoltre una risorsa di grande utilità: http://www.statmethods.net/.

- "R Inferno" di Patrick Burns, un libro sorprendentemente divertente che affronta alcune difficoltà che è possibile incontrare durante la programmazione in R. Il libro è disponibile gratuitamente all'indirizzo http://www.burns-stat.com/documents/books/the-r-inferno/.

- Se si desidera un approfondimento su aspetti più avanzati del linguaggio R, potrà essere utile il libro "Advanced R" di Hadley Wickham.  La versione online del libro è disponibile all'indirizzo http://adv-r.had.co.nz/.

Un catalogo di pacchetti in serie temporale di R, utili per le analisi in serie temporale, sono disponibili nella sezione Task View di CRAN.: http://cran.r-project.org/web/views/TimeSeries.html. For information on specific time series object packages, you should refer to the documentation for that package.

Il libro "Introductory Time Series with R" di Paul Cowpertwait e Andrew Metcalfe fornisce una panoramica sull'uso di R per le analisi in serie temporale.  Numerosi esempi di R sono infine disponibili in una grande quantità di testi teorici.

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

<!--HONumber=35.2-->
