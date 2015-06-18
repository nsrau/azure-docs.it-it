<properties 
	pageTitle="Creare un semplice esperimento in Machine Learning Studio | Azure" 
	description="Come creare un esperimento per eseguire il training e il test di un modello semplice in Azure Machine Learning Studio" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="garye"/>

#Creare un semplice esperimento in Azure Machine Learning Studio 
 
Un test di analisi predittiva è costituito, essenzialmente, da componenti per *creare un modello*, *sottoporlo a training* e *assegnargli un punteggio e sottoporlo a test*. È possibile combinare questi componenti per creare un esperimento che accetti i dati, li utilizzi per eseguire il training del modello e applichi il modello ai nuovi dati. È anche possibile aggiungere moduli per pre-elaborare i dati e selezionare funzionalità, suddividere i dati in set di training e di test e quindi valutare o eseguire la convalida incrociata della qualità del modello.  

In questo articolo verrà utilizzato Azure Machine Learning Studio per sviluppare e ripetere un semplice esperimento di analisi predittiva. Per aprire Machine Learning Studio, fare clic sul seguente collegamento: [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Per ulteriori informazioni sui concetti introduttivi di Machine Learning Studio, vedere [Microsoft Azure Machine Learning Studio Home](https://studio.azureml.net/). 
 

##Cinque passaggi per la creazione di un esperimento 

Di seguito vengono riportati i cinque passaggi di base da poter seguire per compilare un test in Machine Learning Studio per creare, eseguire il training classificare il modello:  

- Creazione di un modello 
	- [Passaggio 1: ottenere i dati]
	- [Passaggio 2: pre-elaborare i dati]
	- [Passaggio 3: definire le funzionalità]
- Eseguire il training del modello 
	- [Passaggio 4: scegliere e applicare un algoritmo di apprendimento]
- Classificare e testare il modello 
	- [Passaggio 5: stimare i nuovi dati] 

[Passaggio 1: ottenere i dati]: #step-1-get-data
[Passaggio 2: pre-elaborare i dati]: #step-2-preprocess-data
[Passaggio 3: definire le funzionalità]: #step-3-define-features
[Passaggio 4: scegliere e applicare un algoritmo di apprendimento]: #step-4-choose-and-apply-a-learning-algorithm
[Passaggio 5: stimare i nuovi dati]: #step-5-predict-over-new-data 

In questo esempio verranno esaminate nel dettaglio tutte le fasi della creazione di un modello di regressione che utilizza dati di esempio relativi alle automobili. L'obiettivo è eseguire la stima del prezzo di un'automobile usando diverse variabili, come la marca e le specifiche tecniche. 

### Passaggio 1: ottenere i dati

In Machine Learning Studio sono disponibili numerosi set di dati di esempio ed è possibile importare dati da diverse origini. Per questo esempio, verrà usato il set di dati di esempio, **Dati di prezzo delle automobili (non elaborati)**, che rappresenta i dati relativi ai prezzi delle automobili.

1. Avviare un nuovo esperimento facendo clic su **+NUOVO** nella parte inferiore della finestra di Machine Learning Studio, selezionare **ESPERIMENTO**, quindi selezionare "Esperimento vuoto". Selezionare il nome dell'esperimento predefinito nella parte superiore dell'area di disegno e denominarlo in modo significativo, ad esempio, **Stima dei prezzi delle automobili**.

2. A sinistra dell'area di disegno dell'esperimento è presente una tavolozza di set di dati e moduli. Digitare **automobile** nella casella di ricerca nella parte superiore della tavolozza per individuare il set di dati con l'etichetta **Dati di prezzo delle automobili (non elaborati)**. 

	![Palette search][screen1a]

3. Trascinare il set di dati nell'area di disegno dell'esperimento. 

	![Dataset][screen1]

Per visualizzare l'aspetto dei dati, fare clic sulla porta di output nella parte inferiore del set di dati delle automobili e selezionare **Visualizza**. Le variabili del set di dati vengono visualizzate sotto forma di colonne e ogni istanza di un'automobile viene visualizzata sotto forma di riga. La colonna all'estrema destra (colonna 26 e intitolata "prezzo") è la variabile di destinazione che verrà stimata. 

![Dataset visualization][screen1b]

Chiudere la finestra di visualizzazione facendo clic sulla "**x**" nell'angolo superiore destro.

### Passaggio 2: pre-elaborare i dati

Prima di poter analizzare un set di dati è in genere necessario pre-elaborarlo. È possibile che si sia notata l'assenza di valori nelle colonne di diverse righe. Per analizzare i dati, i valori mancanti devono essere eliminati. In questo caso verranno rimosse le righe con i valori mancanti. Inoltre, la colonna **normalized-losses** contiene una grande percentuale di valori mancanti, pertanto questa colonna verrà esclusa completamente dal modello. 

> [AZURE.TIP] La pulizia dei valori mancanti dai dati di input è un prerequisito all'uso della maggior parte dei moduli. 

Innanzitutto, verrà rimossa la colonna **normalized-losses** e quindi tutte le righe con dati mancanti. 

1. Digitare **colonne progetto** nella casella di ricerca nella parte superiore del riquadro di modulo per trovare il modulo **Colonne progetto**, quindi trascinarlo nell'area di disegno dell'esperimento e connetterlo alla porta di output del set di dati di **Dati relativi ai prezzi delle automobili (non elaborati)**. Questo modulo consente di selezionare le colonne di dati da includere o escludere nel modello. 

2. Selezionare il modulo **Colonne progetto** e fare clic su **Avvia selettore colonne** nel riquadro delle proprietà. 

	- Assicurarsi che nell'elenco a discesa dei filtri **Inizia con**, sia selezionato **Tutte le colonne**. In questo modo **Colonne progetto** esamina tutte le colonne, tranne quelle che verranno escluse. 
	- Nella riga successiva selezionare **Escludi** e **nomi colonne**, quindi fare clic all'interno della casella di testo. Verrà visualizzato un elenco di colonne. Selezionare **normalized-losses** e verrà aggiunto alla casella di testo. 
	- Fare clic sul pulsante del segno di spunta (OK) per chiudere il selettore di colonne.

    ![Select columns][screen3]
	
	Il riquadro delle proprietà per **Colonne progetto** indica che verranno esaminate tutte le colonne del set di dati ad eccezione di **normalized-losses**. 

    ![Project Columns properties][screen4]

    > [AZURE.TIP] È possibile aggiungere un commento a un modulo facendo doppio clic sul modulo e immettendo del testo. In tal modo sarà possibile individuare subito l'operazione eseguita dal modulo nell'esperimento. In questo caso, fare doppio clic sul modulo **Colonne progetto** e immettere il commento "Escludi normalized-losses". 

3. Trascinare il modulo **Strumento di pulitura valori mancanti** nell'area di disegno dell'esperimento e connetterlo al modulo **Colonne progetto**. Nel riquadro **Proprietà**, selezionare **Rimuovi intera riga** in **Per valori mancanti** per pulire i dati rimuovendo le righe con valori mancanti. Fare doppio clic sul modulo e digitare il commento "Rimuovi righe valori mancanti".

	![Missing Values Scrubber properties][screen4a]

4. Eseguire l'esperimento facendo clic su **ESEGUI** sotto l'area di disegno dell'esperimento.

Al termine dell'esperimento, tutti i moduli saranno contraddistinti da un segno di spunta verde per indicarne il corretto completamento. Si noti anche lo stato **Esecuzione terminata** nell'angolo in alto a destra.

![First experiment run][screen5]

Fino a questo momento, nell'esperimento sono stati soltanto puliti i dati. Per visualizzare il set di dati pulito, fare clic sulla porta di output del modulo **Strumento di pulitura valori mancanti** e selezionare **Visualizza**. Si noti che la colonna **normalized-losses** non è più inclusa e che non sono presenti valori mancanti.

A questo punto, una volta puliti i dati è possibile specificare le funzionalità da usare nel modello predittivo.

### Passaggio 3: Definire le funzionalità

Nella Machine Learning, le  *features* sono singole proprietà misurabili di un elemento a cui si è interessati. Nel set di dati corrente ogni riga rappresenta un'automobile e ogni colonna è una funzionalità di tale automobile. Per cercare un set di funzionalità adeguato per creare un modello predittivo, è necessario sperimentare e conoscere approfonditamente il problema che si desidera risolvere. Alcune funzionalità sono infatti migliori di altre per le previsioni. Inoltre, alcune funzionalità sono strettamente correlate ad altre funzionalità (ad esempio city-mpg e highway-mpg), di conseguenza non aggiungono nuove informazioni al modello e possono essere rimosse.

Verrà ora creato un modello che usa un sottoinsieme delle funzionalità del set di dati. Se si vuole, si può tornare indietro e selezionare funzionalità diverse, eseguire di nuovo l'esperimento e verificare se i risultati ottenuti sono migliori. Come prima ipotesi, verranno selezionate le seguenti funzionalità (colonne) con il modulo **Colonne progetto**. Si noti che, per il training del modello, è necessario includere il valore *price* relativo al prezzo che si intende prevedere.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Trascinare un altro modulo **Colonne progetto** nell'area di disegno dell'esperimento e connetterlo al modulo **Strumento di pulitura valori mancanti**. Fare doppio clic sul modulo e digitare "Selezionare le funzionalità per la previsione".

2. Fare clic su **Avvia selettore colonne** nel riquadro **Proprietà**. 

3. Nel selettore della colonna, selezionare **Nessuna colonna** per **Inizia con**, quindi scegliere **Includi** e **nomi colonne** nella riga del filtro. Immettere l'elenco di nomi di colonna. In tal modo il modulo passerà solo nelle colonne specificate.

	> [AZURE.TIP] Siccome l'esperimento è stato eseguito, le definizioni della colonna per i dati sono state passate dal set di dati originale al modulo **Strumento di pulitura valori mancanti**. Quando si connette **Colonne progetto** a **Strumento di pulitura valori mancanti**, il modulo **Colonne progetto** riconosce le definizioni delle colonne nei dati. Quando si fa clic sulla casella **Nomi colonne**, viene visualizzato un elenco di colonne ed è possibile selezionare quelle da aggiungere all'elenco. 

4. Fare clic sul pulsante di segno di spunta (OK).

![Select columns][screen6]

Si ottiene in tal modo il set di dati che verrà usato nell'algoritmo di apprendimento nei passaggi successivi. In seguito, è possibile tornare indietro e provare a selezionare funzionalità diverse. 

### Passaggio 4: Scegliere e applicare un algoritmo di apprendimento

Ora che i dati sono pronti, la costruzione di un modello predittivo comprende training e test. *Classification* e *regression* sono due tipi di tecniche di Machine Learning supervisionate. La classificazione viene usata per effettuare una previsione in base a un insieme di valori definito, ad esempio un colore (rosso, blu o verde). La regressione viene invece usata per effettuare una previsione in base a un insieme continuo di valori, ad esempio l'età di una persona.

Volendo prevedere il prezzo di un'automobile, che può essere un valore qualsiasi, si userà un modello basato su regressione. In questo esempio, viene eseguito il training di un semplice modello di *linear regression*, che verrà testato nel passaggio successivo. 

1. Dividere i dati in set di traning e di test. Selezionare e trascinare il modulo **Suddivisione** nell'area di disegno della prova e connetterlo all'output dell'ultimo modulo **Colonne progetto**. Impostare **Frazione di righe nel primo set di dati di output** su 0,75. In questo modo per il training del modello verrà usato il 75% dei dati, mentre il restante 25% verrà usato per il testing.

	> [AZURE.TIP] Modificando il parametro **Valore di inizializzazione casuale**, è possibile ottenere esempi casuali diversi per il training e il test. Questo parametro controlla il seeding del generatore di numeri pseudocasuali.
	
2. Eseguire l'esperimento. In questo modo, nei moduli **Colonne progetto** e **Suddivisione** le definizioni di colonna vengono passate ai moduli che verranno aggiunte successivamente.  

3. Per selezionare l'algoritmo di apprendimento, espandere la categoria **Machine Learning** nella tavolozza dei moduli a sinistra dell'area di disegno e quindi espandere **Inizializza modello**. Verranno visualizzate diverse categorie di moduli che possono essere usate per inizializzare un algoritmo di apprendimento. 

	Per questo esperimento, selezionare il modulo **Regressione lineare** nella categoria **Regressione** (il modulo può essere anche trovato digitando "regressione lineare" nella casella Cerca della tavolozza) e trascinarla nell'area di disegno dell'esperimento.

4. Individuare e trascinare il modulo **Esegui training modello** nell'esperimento. Collegare la porta di input a sinistra all'output del modulo **Regressione lineare**. Collegare la porta di input a destra all'output di dati di training (porta sinistra) del modulo **Suddivisione**.

5. Eseguire l'esperimento per passare le definizioni di colonna per il modulo **Modello di training**.
 
6. Selezionare il modulo **Modello di training**, fare clic su **Avvia selettore di colonna** nel riquadro **Proprietà** e selezionare la colonna **Prezzo**. Questo è il valore che si intende prevedere con il modello.

	![Select "price" column][screen7]

7. Eseguire l'esperimento. 

Il risultato è un modello di regressione basato su training che può essere usato per assegnare un punteggio a nuovi campioni ai fini delle previsioni. 

![Applying the learning algorithm][screen8]

### Passaggio 5: Stimare i nuovi dati 

Dopo aver eseguito il training del modello, è possibile usarlo per calcolare il punteggio del 25% residuo dei dati per verificarne il funzionamento. 

1. Trovare e trascinare il modulo **Modello di punteggio** sulle aree di disegno dell'esperimento e collegare la porta di input a sinistra all'output del modulo **Modello di training**. Collegare la porta di input a destra all'output dei dati di test (porta destra) del modulo **Suddivisione**.  

	![Score Model module][screen8a]

2. Per eseguire l'esperimento e visualizzare l'output del modulo **Modello di classificazione**, fare clic sulla porta di output e selezionare **Visualizza**. L'output mostra i valori previsti per il prezzo e i valori noti dai dati di test.  

3. Infine, per verificare la qualità dei risultati, selezionare e trascinare il modulo **Valuta modello** sulle aree di disegno dell'esperimento e collegare la porta di input a sinistra all'output del modulo **Modello di classificazione**. Sono disponibili due porte di input perché il modulo **Valuta modello** può essere utilizzato per confrontare due modelli.
 
4. Per eseguire l'esperimento e visualizzare l'output del modulo **Valuta modello**, fare clic sulla porta di output e selezionare **Visualizza**. Per il modello vengono visualizzate le seguenti statistiche:

	- **Errore assoluto medio** (MAE): la media degli errori assoluti (si definisce *error* la differenza tra il valore stimato e il valore effettivo).
	- **Errore radice quadrata media** (RMSE): radice quadrata della media degli errori quadratici delle stime effettuate sul set di dati del test.
	- **Errore assoluto relativo**: media degli errori assoluti relativi alla differenza assoluta tra i valori effettivi e la media di tutti i valori effettivi.
	- **Errore quadratico relativo**: media degli errori quadratici relativi alla differenza quadratica tra i valori effettivi e la media di tutti i valori effettivi.
	- **Coefficiente di determinazione**: noto anche come **valore R quadrato**. Si tratta di una metrica statistica che indica la corrispondenza di un modello ai dati.
	
	Per ogni statistica di errore, sono preferibili i valori più piccoli. Un valore più piccolo indica che le stime sono più vicine ai valori effettivi. Per **Coefficiente di determinazione**, le previsioni saranno migliori se il valore si avvicina a uno (1,0).

	![Evaluation results][screen9]

L'esperimento finale dovrebbe risultare simile al seguente:

![Complete experiment][screen10]

### Passaggi successivi

A questo punto, dopo aver configurato l'esperimento, è possibile ripeterlo e provare a migliorare il modello. Ad esempio, è possibile modificare le funzionalità usate per la previsione. Oppure è possibile modificare le proprietà dell'algoritmo **Regressione lineare** o provare un algoritmo differente. È anche possibile aggiungere contemporaneamente più algoritmi all'esperimento e confrontarne due utilizzando il modulo **Valuta modello**. 

> [AZURE.TIP] Utilizzare il pulsante **SALVA CON NOME** nell'area di disegno dell'esperimento per copiare le iterazioni dell'esperimento. È possibile visualizzare tutte le iterazioni dell'esperimento selezionando **VISUALIZZA CRONOLOGIA ESECUZIONI** nell'area di disegno. Per maggiori dettagli, visualizzare [Gestione iterazioni dell'esperimento in Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

Una volta ottenuto il modello desiderato, è possibile pubblicarlo come servizio Web da usare per prevedere i prezzi delle automobili utilizzando dati nuovi. Per ulteriori dettagli, vedere [Pubblicazione di un servizio Web di Azure Machine Learning][pubblicazione].

[pubblicazione]: machine-learning-publish-a-machine-learning-web-service.md

Per una procedura più ampia e dettagliata per la creazione, il training, la valutazione e la pubblicazione di un modello predittivo, vedere [Sviluppare una soluzione predittiva utilizzando Azure Machine Learning ][procedura dettagliata]. 

[procedura dettagliata]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/screen10.png

<!--HONumber=49--> 