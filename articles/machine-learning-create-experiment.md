<properties title="Create a simple experiment in Azure Machine Learning Studio" pageTitle="Create a simple experiment in Machine Learning Studio | Azure" description="How to create an experiment to train and test a simple model in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Creare un semplice esperimento in Azure Machine Learning Studio

Un esperimento di analisi predittiva è in sostanza costituito da componenti per *creare un modello*, *eseguire il training del modello* e infine *assegnare un punteggio al modello e testarlo*. È possibile combinare questi componenti per creare un esperimento che accetti i dati in cui a cui eseguire il training e applichi il modello ai nuovi dati. È anche possibile aggiungere dati per pre-elaborare i dati e selezionare funzionalità, dividere i dati in set di training e di test e quindi valutare o eseguire la convalida incrociata della qualità del modello.

In questo articolo verrà usato Microsoft Azure Machine Learning Studio per sviluppare e ripetere un semplice esperimento di analisi predittiva.

## Cinque passaggi per la creazione di un esperimento

I cinque passaggi di base per creare un esperimento in ML Studio consentono di creare, eseguire il training e assegnare un punteggio al modello:

-   Creare il modello

    -   [Passaggio 1: Ottenere i dati][Passaggio 1: Ottenere i dati]
    -   [Passaggio 2: Pre-elaborare i dati][Passaggio 2: Pre-elaborare i dati]
    -   [Passaggio 3: Definire le funzionalità][Passaggio 3: Definire le funzionalità]
-   Eseguire il training del modello

    -   [Passaggio 4: Scegliere e applicare un algoritmo di apprendimento][Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]
-   Assegnare un punteggio e testare il modello

    -   [Passaggio 5: Effettuare previsioni basate su nuovi dati][Passaggio 5: Effettuare previsioni basate su nuovi dati]

In questo esempio verranno esaminate tutte le fasi della creazione di un modello di regressione usando dati di esempio relativi alle automobili. L'obiettivo è prevedere il prezzo di un'automobile usando diverse variabili, come la marca e le specifiche tecniche.

### Passaggio 1: Ottenere i dati

In ML Studio sono disponibili numerosi set di dati di esempio ed è possibile importare dati da diverse origini. Per questo esempio verrà usato il set di dati di esempio **Automobile price data (Raw)**, che rappresenta i dati relativi ai prezzi delle automobili.

1.  Avviare un nuovo esperimento. Fare clic su **+NUOVO** nella parte inferiore della finestra di ML Studio e selezionare **ESPERIMENTO**. Rinominare l'esperimento "Senza titolo", specificando un nome più significativo, ad esempio "Previsione prezzi auto".

2.  A sinistra dell'area di disegno dell'esperimento è presente una tavolozza di set di dati e moduli. Digitare "automobile" nella casella di ricerca nella parte superiore della tavolozza per individuare il set di dati **Automobile price data (Raw)**.

    ![Palette search][Palette search]

3.  Trascinare il set di dati nell'area di disegno dell'esperimento.

    ![Dataset][Dataset]

Per visualizzare l'aspetto dei dati, fare doppio clic sulla porta di output nella parte inferiore del set di dati automobile e selezionare **Visualizza**. Le variabili del set di dati vengono visualizzate sotto forma di colonne e ogni istanza di un'automobile viene visualizzata sotto forma di riga. La colonna 26 all'estrema destra "price" corrisponde alla variabile di destinazione per la quale si proverà a effettuare la previsione.

![Dataset visualization][Dataset visualization]

Chiudere la finestra di visualizzazione facendo clic sulla "**x**" nell'angolo superiore destro.

### Passaggio 2: Pre-elaborare i dati

Prima di poter analizzare un set di dati è in genere necessario pre-elaborare i dati. Come illustrato nella figura, nelle colonne di diverse righe sono presenti valori mancanti. Per analizzare i dati, è necessario pulire i valori mancanti. In questo caso verranno rimosse solo le righe con valori mancanti. Anche la colonna "normalized-losses" contiene una notevole percentuale di valori mancanti, pertanto questa colonna verrà esclusa dal modello.

> **Suggerimento**: la pulizia dei valori mancanti nei dati di input è un'operazione da eseguire prima di usare la maggior parte dei moduli.

Innanzitutto verrà rimossa la colonna "normalized-losses" e quindi tutte le righe con dati mancanti.

1.  Trascinare il modulo **Colonne progetto** nell'area di disegno dell'esperimento e connetterlo alla porta di output del set di dati **Automobile price data (Raw)**. Questo modulo consente di selezionare le colonne di dati da includere o escludere nel modello.

2.  Selezionare il modulo **Colonne progetto** e fare clic su **Avvia selettore colonne** nel riquadro delle proprietà.

    -   Assicurarsi che nell'elenco a discesa dei filtri **Inizia con** sia selezionato **Tutte le colonne**. In tal modo **Colonne progetto** esaminerà tutte le colonne, ad eccezione di quelle che verranno escluse a breve.
    -   Nella riga successiva selezionare **Escludi** e **nomi colonne**, quindi fare clic all'interno della casella di testo. Viene visualizzato un elenco di colonne. Selezionare "normalized-losses" per aggiungerlo alla casella di testo.
    -   Fare clic sul pulsante del segno di spunta **OK** per chiudere il selettore di colonne.

    ![Select columns][Select columns]

    Il riquadro delle proprietà per **Colonne progetto** indica che verranno esaminate tutte le colonne del set di dati ad eccezione di "normalized-losses".

    ![Project Columns properties][Project Columns properties]

    > **Suggerimento**: per aggiungere un commento a un modulo, fare doppio clic sul modulo e immettere il testo. In tal modo sarà possibile individuare subito l'operazione eseguita dal modulo nell'esperimento. In questo caso fare doppio clic sul modulo **Colonne progetto** e immettere il commento "Exclude normalized-losses".

3.  Trascinare il modulo **Strumento di pulitura valori mancanti** nell'area di disegno dell'esperimento e connetterlo al modulo **Colonne progetto**. Nel riquadro delle proprietà selezionare **Rimuovi intera riga** in **Per valori mancanti** per pulire i dati rimuovendo le righe con valori mancanti. Fare doppio clic sul modulo e immettere il commento "Remove missing value rows".

    ![Missing Values Scrubber properties][Missing Values Scrubber properties]

4.  Eseguire l'esperimento facendo clic su **ESEGUI** sotto l'area di disegno dell'esperimento.

Al termine dell'esperimento, tutti i moduli saranno contraddistinti da un segno di spunta verde per indicarne il corretto completamento. Si noti anche lo stato "Esecuzione terminata" nell'angolo in alto a destra.

![First experiment run][First experiment run]

L'unica operazione eseguita finora per l'esperimento è stata la pulizia dei dati. Per visualizzare il set di dati pulito, fare doppio clic sulla porta di output del modulo **Strumento di pulitura valori mancanti** e selezionare **Visualizza**. Si noti che la colonna "normalized-losses" non è più inclusa e che non sono presenti valori mancanti.

A questo punto, una volta puliti i dati è possibile specificare le funzionalità da usare nel modello predittivo.

### Passaggio 3: Definire le funzionalità

In Machine Learning le *funzionalità* sono singole proprietà misurabili di un elemento a cui si è interessati. Nel set di dati corrente ogni riga rappresenta un'automobile e ogni colonna è una funzionalità di tale automobile. Per cercare un set adeguato di funzionalità per creare un modello predittivo, è necessario sperimentare e conoscere approfonditamente il problema. Alcune funzionalità sono infatti migliori di altre per le previsioni. Inoltre, alcune funzionalità sono strettamente correlate con altre funzionalità, ad esempio city-mpg e highway-mpg, di conseguenza non aggiungono nuove informazioni al modello e possono essere rimosse.

Verrà ora creato un modello che usa un sottoinsieme delle funzionalità del set di dati. Se si vuole, si può tornare indietro e selezionare funzionalità diverse, eseguire di nuovo l'esperimento e verificare se i risultati ottenuti sono migliori. Come prima ipotesi, verranno selezionate le seguenti funzionalità (colonne) con il modulo **Colonne progetto**. Tenere presente che per il training del modello è necessario includere il valore *price* relativo al prezzo che si intende prevedere.

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1.  Trascinare un altro modulo **Colonne progetto** nell'area di disegno dell'esperimento e connetterlo al modulo **Strumento di pulitura valori mancanti**. Fare doppio clic sul modulo e immettere "Select features for prediction".

2.  Fare clic su **Avvia selettore colonne** nel riquadro delle proprietà.

3.  Nel selettore colonne selezionare **Nessuna colonna** per **Inizia con**, quindi selezionare **Includi** e **nomi di colonna** nella riga del filtro. Immettere l'elenco di nomi di colonna. In tal modo il modulo passerà solo nelle colonne specificate.

    > **Suggerimento**: dal momento che l'esperimento è stato eseguito fino a questo punto, le definizioni delle colonne per i dati sono state passate dal set di dati originale tramite il modulo **Strumento di pulitura valori mancanti**. Quando si connette **Colonne progetto** a **Strumento di pulitura valori mancanti**, il modulo **Colonne progetto** riconosce le definizioni delle colonne nei dati. Quando si fa clic sulla casella dei nomi di colonna, viene visualizzato un elenco di colonne ed è possibile selezionare una alla volta le colonne da aggiungere all'elenco.

4.  Fare clic su **OK**.

![Select columns][1]

Si otterrà in tal modo il set di dati che verrà usato nell'algoritmo di apprendimento nei passaggi successivi. In seguito è possibile tornare indietro e provare a selezionare funzionalità diverse.

### Passaggio 4: Scegliere e applicare un algoritmo di apprendimento

I dati sono pronti, di conseguenza la creazione del modello predittivo implica la fase di training e test. La *classificazione* e la *regressione* sono due tipi di tecniche di Machine Learning controllato. La classificazione viene usata per effettuare una previsione in base a un insieme di valori definiti, ad esempio un colore (rosso, blu o verde). La regressione viene invece usata per effettuare una previsione in base a un insieme continuo di valori, ad esempio l'età di una persona.

Volendo prevedere il prezzo di un'automobile, che può essere un valore qualsiasi, si userà un modello basato su regressione. Per questo esempio, verrà eseguito il training di un modello basato su *regressione lineare*, che verrà poi testato nel passaggio successivo.

1.  Dividere i dati in set di traning e di test. Selezionare e trascinare il modulo **Suddivisione** nell'area di disegno dell'esperimento e connetterlo all'output dell'ultimo modulo **Colonne progetto**. Impostare **Frazione di righe nel primo set di dati di output** su 0,75. In questo modo per il training del modello verrà usato il 75% dei dati, mentre il restante 25% verrà usato per il testing.

    > **Suggerimento**: se si modifica il parametro **Valore di inizializzazione casuale**, è possibile ottenere esempi casuali diversi per il training e il testing. Questo parametro controlla il seeding del generatore di numeri pseudocasuali.

2.  Eseguire l'esperimento. In questo modo i moduli **Colonne progetto** e **Suddivisione** vengono passati insieme alle definizioni di colonna ai moduli che verranno aggiunti più avanti.

3.  Per selezionare l'algoritmo di apprendimento, espandere la categoria **Machine Learning** nella tavolozza dei moduli a sinistra dell'area di disegno e quindi espandere **Inizializza modello**. Verranno visualizzate diverse categorie di moduli che possono essere usate per inizializzare un algoritmo di apprendimento.

    Per questo esperimento di esempio, selezionare la categoria **Regressione lineare** nella categoria **Regressione** (per trovarla digitare "Regressione lineare" nella casella di ricerca della tavolozza) e trascinarla nell'area di disegno dell'esperimento.

4.  Individuare e trascinare il modulo **Esegui training modello** nell'esperimento. Fare clic su **Avvia selettore colonne** e selezionare la colonna *price*. Questo è il valore che si intende prevedere con il modello.

    ![Select "price" column][Select "price" column]

5.  Connettere la porta di input di sinistra all'output del modulo **Regressione lineare** e la porta di input di destra all'output dei dati di training (porta sinistra) del modulo **Suddivisione**.

6.  Eseguire l'esperimento.

Il risultato è un modello di regressione basato su training che può essere usato per assegnare un punteggio a nuovi campioni ai fini delle previsioni.

![Applying the learning algorithm][Applying the learning algorithm]

### Passaggio 5: Effettuare previsioni basate su nuovi dati

Dopo aver eseguito il training del modello è possibile usarlo per calcolare il punteggio del 25% residuo dei dati e verificarne il funzionamento.

1.  Individuare e trascinare il modulo **Calcola punteggio modello** nell'area di disegno dell'esperimento e connettere la porta di input di sinistra all'output del modulo **Esegui training modello** e la porta di input di destra all'output dei dati di test (porta destra) del modulo **Suddivisione**.

    ![Score Model module][Score Model module]

2.  Eseguire l'esperimento e visualizzare l'output del modulo **Calcola punteggio modello** (fare doppio clic sulla porta di output e selezionare **Visualizza**). L'output mostrerà i valori previsti per il prezzo unitamente a quelli noti inclusi nei dati di test.

3.  Infine, per testare la qualità dei risultati, selezionare e trascinare il modulo **Valuta modello** nell'area di disegno dell'esperimento, quindi connettere la porta di input di sinistra all'output del modulo **Calcola punteggio modello** (sono presenti due porte di input perché il modulo **Valuta modello** consente di confrontare due modelli).

4.  Eseguire l'esperimento e visualizzare l'output del modulo **Valuta modello** (fare doppio clic sulla porta di output e selezionare **Visualizza**). Per il modello vengono visualizzate le seguenti statistiche.

    -   **Errore assoluto medio**: media degli errori assoluti (un *errore* è dato dalla differenza tra il valore previsto e il valore effettivo).
    -   **Errore radice quadrata media**: radice quadrata della media degli errori quadratici delle previsioni effettuate sul set di dati di test.
    -   **Errore assoluto relativo**: media degli errori assoluti relativi alla differenza assoluta tra i valori effettivi e la media di tutti i valori effettivi.
    -   **Errore quadratico relativo**: media degli errori quadratici relativi alla differenza quadratica tra i valori effettivi e la media di tutti i valori effettivi.
    -   **Coefficiente di determinazione**: noto anche come "valore quadratico R", è una metrica statistica che indica la percentuale di idoneità di un modello rispetto ai dati.

    Per ogni statistica di errore, un valore inferiore indica che le previsioni si avvicinano maggiormente ai valori effettivi. Per **Coefficiente di determinazione** le previsioni saranno migliori se il valore si avvicina a uno (1,0).

    ![Evaluation results][Evaluation results]

L'esperimento finale dovrebbe risultare simile al seguente:

![Complete experiment][Complete experiment]

### Passaggi successivi

A questo punto, dopo aver configurato l'esperimento, è possibile ripeterlo e provare a migliorare il modello. Ad esempio, è possibile modificare le funzionalità usate per la previsione o le proprietà dell'algoritmo **Regressione lineare** oppure provare un algoritmo diverso. È persino possibile aggiungere più algoritmi all'esperimento e confrontarli (due alla volta) usando il modulo **Valuta modello**.

> **Suggerimento**: usare il pulsante **SALVA CON NOME** sotto l'area di disegno dell'esperimento per creare una copia di qualsiasi iterazione dell'esperimento. Per visualizzare tutte le iterazioni dell'esperimento, fare clic su **VISUALIZZA CRONOLOGIA ESECUZIONI** sotto l'area di disegno. Per altri dettagli, vedere l'argomento della Guida di ML Studio sulla **visualizzazione della cronologia delle esecuzioni**.

Una volta ottenuto il modello desiderato, è possibile pubblicarlo come servizio Web da usare per prevedere i prezzi delle automobili sulla base di dati nuovi. Per altri dettagli, vedere l'argomento della Guida di ML Studio sulla **pubblicazione di esperimenti**.

Per informazioni più complete e dettagliate sulla creazione, il training, l'assegnazione di un punteggio e la pubblicazione di un modello predittivo, vedere [Procedura dettagliata: Sviluppare una soluzione predittiva con Azure Machine Learning][Procedura dettagliata: Sviluppare una soluzione predittiva con Azure Machine Learning].

<!-- Images -->

  [Passaggio 1: Ottenere i dati]: #step-1-get-data
  [Passaggio 2: Pre-elaborare i dati]: #step-2-pre-process-data
  [Passaggio 3: Definire le funzionalità]: #step-3-define-features
  [Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]: #step-4-choose-and-apply-a-learning-algorithm
  [Passaggio 5: Effettuare previsioni basate su nuovi dati]: #step-5-predict-over-new-data
  [Palette search]: ./media/machine-learning-create-experiment/screen1a.png
  [Dataset]: ./media/machine-learning-create-experiment/screen1.png
  [Dataset visualization]: ./media/machine-learning-create-experiment/screen1b.png
  [Select columns]: ./media/machine-learning-create-experiment/screen3.png
  [Project Columns properties]: ./media/machine-learning-create-experiment/screen4.png
  [Missing Values Scrubber properties]: ./media/machine-learning-create-experiment/screen4a.png
  [First experiment run]: ./media/machine-learning-create-experiment/screen5.png
  [1]: ./media/machine-learning-create-experiment/screen6.png
  [Select "price" column]: ./media/machine-learning-create-experiment/screen7.png
  [Applying the learning algorithm]: ./media/machine-learning-create-experiment/screen8.png
  [Score Model module]: ./media/machine-learning-create-experiment/screen8a.png
  [Evaluation results]: ./media/machine-learning-create-experiment/screen9.png
  [Complete experiment]: ./media/machine-learning-create-experiment/screen10.png
  [Procedura dettagliata: Sviluppare una soluzione predittiva con Azure Machine Learning]: http://azure.microsoft.com/it-it/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/
