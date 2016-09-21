<properties
	pageTitle="Esperimento semplice in Machine Learning Studio | Microsoft Azure"
	description="Questa esercitazione di Machine Learning illustra un esperimento semplice di analisi scientifica dei dati. Verrà stimato il prezzo di un'automobile usando un algoritmo di regressione."
	keywords="esperimento,regressione lineare,algoritmi di machine learning,esercitazione su machine learning,tecniche di modellazione predittiva,esperimento di analisi scientifica dei dati"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="garye"/>

# Esercitazione di Machine Learning: Creare il primo esperimento di analisi scientifica dei dati in Azure Machine Learning Studio

Questa esercitazione di Machine Learning illustra un esperimento semplice di analisi scientifica dei dati. Verrà creato un modello di regressione lineare che stima il prezzo di un'automobile in base a diverse variabili, come la marca e le specifiche tecniche. A tale scopo, si userà Azure Machine Learning Studio per sviluppare un semplice esperimento di analisi predittiva ed eseguirne l'iterazione.

L'*analisi predittiva* è un tipo di analisi scientifica dei dati che usa dati correnti per prevedere risultati futuri. Per un esempio molto semplice di analisi predittiva, vedere il video 4 della serie sull'analisi scientifica dei dati per principianti: [Predict an answer with a simple model](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (Prevedere una risposta con un modello semplice), durata 7:42.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Vantaggi di Machine Learning Studio

Machine Learning Studio semplifica la configurazione di un esperimento grazie a moduli a trascinamento preprogrammati con tecniche di modellazione predittiva. Per eseguire l'esperimento e prevedere una risposta, si userà Machine Learning Studio per *creare un modello*, *eseguire il training del modello* e infine *assegnare un punteggio al modello e provarlo*.

Accedere a Machine Learning Studio: [https://studio.azureml.net](https://studio.azureml.net). Se è già stato effettuato l'accesso a Machine Learning Studio in precedenza, fare clic su **Sign in here** (Accedere qui). In caso contrario fare clic su **Sign Up** (Iscrizione) e scegliere tra opzioni gratuite e a pagamento.

Per informazioni più generali su Machine Learning Studio, vedere [Informazioni su Azure Machine Learning Studio](machine-learning-what-is-ml-studio.md)

## Cinque passaggi per la creazione di un esperimento

In questa esercitazione di Machine Learning, si seguiranno cinque passaggi di base per compilare un esperimento in Machine Learning Studio per creare, eseguire il training e classificare il modello:

- Creare il modello
	- [Passaggio 1: Ottenere i dati]
	- [Passaggio 2: Pre-elaborare i dati]
	- [Passaggio 3: Definire le caratteristiche]
- Eseguire il training del modello
	- [Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]
- Classificare e testare il modello
	- [Passaggio 5: Stimare i prezzi delle nuove automobili]

[Passaggio 1: Ottenere i dati]: #step-1-get-data
[Passaggio 2: Pre-elaborare i dati]: #step-2-preprocess-data
[Passaggio 3: Definire le caratteristiche]: #step-3-define-features
[Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]: #step-4-choose-and-apply-a-learning-algorithm
[Passaggio 5: Stimare i prezzi delle nuove automobili]: #step-5-predict-new-automobile-prices


## Passaggio 1: Ottenere i dati

In Machine Learning Studio sono disponibili numerosi set di dati di esempio ed è possibile importare dati da diverse origini. Per questo esempio verrà usato il set di dati di esempio incluso, **Automobile price data (Raw)**. Questo set di dati include voci per diverse singole automobili, tra cui informazioni come la marca, il modello, le specifiche tecniche e il prezzo.

1. Avviare un nuovo esperimento facendo clic su **+NEW** nella parte inferiore della finestra di Machine Learning Studio, selezionare **EXPERIMENT** e quindi **Blank Experiment**. Selezionare il nome dell'esperimento predefinito nella parte superiore dell'area di disegno e denominarlo in modo significativo, ad esempio **Stima prezzi automobili**.

2. A sinistra dell'area di disegno dell'esperimento è presente una tavolozza di set di dati e moduli. Digitare **automobile** nella casella di ricerca nella parte superiore della tavolozza per trovare il set di dati denominato **Automobile price data (Raw)**.

	![Ricerca nella tavolozza][screen1a]

3. Trascinare il set di dati nell'area di disegno dell'esperimento.

	![Set di dati][screen1]

Per visualizzare l'aspetto dei dati, fare clic sulla porta di output nella parte inferiore del set di dati relativo alle automobili e selezionare **Visualize**.

![Porta di output modulo][screen1c]

Le variabili del set di dati vengono visualizzate sotto forma di colonne e ogni istanza di un'automobile viene visualizzata sotto forma di riga. La colonna all'estrema destra (colonna 26 e intitolata "price") è la variabile di destinazione che verrà stimata.

![Visualizzazione set di dati][screen1b]

Chiudere la finestra di visualizzazione facendo clic sulla "**x**" nell'angolo superiore destro.

## Passaggio 2: Pre-elaborare i dati

Prima di poter analizzare un set di dati è in genere necessario pre-elaborarlo. È possibile che si sia notata l'assenza di valori nelle colonne di diverse righe. Per consentire al modello di analizzare correttamente i dati, è necessario eseguire la pulizia di questi valori mancanti. In questo caso verranno rimosse le righe con i valori mancanti. Inoltre, la colonna **normalized-losses** ha molti valori mancanti, pertanto verrà esclusa completamente dal modello.

> [AZURE.TIP] La pulizia dei valori mancanti dai dati di input è un prerequisito all'uso della maggior parte dei moduli.

Verrà innanzitutto rimossa la colonna **normalized-losses** e quindi verranno rimosse tutte le righe con dati mancanti.

1. Digitare **select columns** (seleziona colonne) nella casella di ricerca nella parte superiore del pannello dei moduli per trovare il modulo [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati), quindi trascinarlo nell'area di disegno dell'esperimento e connetterlo alla porta di output del set di dati **Automobile price data (Raw)**. Questo modulo consente di selezionare le colonne di dati da includere o escludere nel modello.

2. Selezionare il modulo [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati) e fare clic su **Launch column selector** (Avvia selettore di colonna) nel riquadro **Properties** (Proprietà).

	- A sinistra, fare clic su **With rules** (Con regole)
	- In **Begin With** (Inizia con), fare clic su **All columns** (Tutte le colonne). In questo modo, [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati) analizzerà tutte le colonne, ad eccezione di quelle che verranno escluse.
	- Negli elenchi a discesa selezionare **Exclude** (Escludi) e **column names** (nomi colonna), quindi fare clic all'interno della casella di testo. Verrà visualizzato un elenco di colonne. Selezionare **normalized-losses** per aggiungere la colonna alla casella di testo.
	- Fare clic sul pulsante del segno di spunta (OK) per chiudere il selettore di colonne.

    ![Selezione colonne][screen3]

	Il riquadro delle proprietà per **Select Columns in Dataset** (Seleziona colonne in set di dati) indica che verranno analizzate tutte le colonne del set di dati ad eccezione di **normalized-losses**.

    ![Proprietà di Select Columns in Dataset (Seleziona colonne in set di dati)][screen4]

    > [AZURE.TIP] È possibile aggiungere un commento a un modulo facendo doppio clic sul modulo e immettendo del testo. In tal modo sarà possibile individuare subito l'operazione eseguita dal modulo nell'esperimento. In questo caso fare doppio clic sul modulo [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati) e immettere il commento "Escludi normalized-losses".

3. Trascinare il modulo [Clean Missing Data][clean-missing-data] \(Pulisci dati mancanti) nell'area di disegno dell'esperimento e connetterlo al modulo [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati). Nel riquadro **Properties** selezionare **Remove entire row** in **Cleaning mode** per pulire i dati rimuovendo le righe con valori mancanti. Fare doppio clic sul modulo e digitare il commento "Rimuovi righe valori mancanti".

	![Proprietà Clean Missing Data][screen4a]

4. Eseguire l'esperimento facendo clic su **RUN** sotto l'area di disegno dell'esperimento.

Al termine dell'esperimento, tutti i moduli saranno contraddistinti da un segno di spunta verde per indicarne il corretto completamento. Si noti anche lo stato **Finished running** nell'angolo in alto a destra.

![Prima esecuzione esperimento][screen5]

Fino a questo momento, nell'esperimento sono stati soltanto puliti i dati. Per visualizzare il set di dati pulito, fare clic sulla porta di output sinistra del modulo [Clean Missing Data][clean-missing-data] \("Cleaned dataset") e selezionare **Visualize**. Si noti che la colonna **normalized-losses** non è più inclusa e che non ci sono valori mancanti.

A questo punto, una volta puliti i dati è possibile specificare le caratteristiche da usare nel modello predittivo.

## Passaggio 3: Definire le caratteristiche

In Machine Learning le *caratteristiche* sono singole proprietà misurabili di un elemento a cui si è interessati. Nel set di dati corrente ogni riga rappresenta un'automobile e ogni colonna è una caratteristica di tale automobile.

Per cercare un set di caratteristiche adeguato per creare un modello predittivo, è necessario sperimentare e conoscere approfonditamente il problema che si desidera risolvere. Alcune caratteristiche sono infatti migliori di altre per le stime. Inoltre, alcune caratteristiche sono strettamente correlate ad altre (ad esempio city-mpg e highway-mpg), di conseguenza non aggiungono nuove informazioni al modello e possono essere rimosse.

Verrà ora creato un modello che usa un sottoinsieme delle caratteristiche del set di dati. Se si vuole, si può tornare indietro e selezionare caratteristiche diverse, eseguire di nuovo l'esperimento e verificare se i risultati ottenuti sono migliori. Per iniziare verranno tuttavia provate le funzionalità seguenti:

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Trascinare un altro modulo [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati) nell'area di disegno dell'esperimento e connetterlo alla porta di output sinistra del modulo [Clean Missing Data][clean-missing-data] \(Pulisci dati mancanti). Fare doppio clic sul modulo e digitare "Selezionare le caratteristiche per la stima".

2. Fare clic su **Launch column selector** nel riquadro **Properties**.

3. Fare clic su **With rules** (Con regole).

4. In **Begin With** (Inizia con) fare clic su **No columns** (Nessuna colonna) e quindi selezionare **Include** (Includi) e **column names** (nomi colonna) nella riga del filtro. Immettere l'elenco di nomi di colonna. In tal modo il modulo passerà solo nelle colonne specificate.

	> [AZURE.TIP] Con l'esecuzione dell'esperimento, le definizioni di colonna vengono passate dal set di dati attraverso il modulo [Clean Missing Data][clean-missing-data] \(Pulisci dati mancanti). Ciò significa che anche altri moduli connessi riceveranno le informazioni dal set di dati.

5. Fare clic sul pulsante di segno di spunta (OK).

![Selezione colonne][screen6]

Si ottiene in tal modo il set di dati che verrà usato nell'algoritmo di apprendimento nei passaggi successivi. In seguito, è possibile tornare indietro e provare a selezionare caratteristiche diverse.

## Passaggio 4: Scegliere e applicare un algoritmo di apprendimento

I dati sono pronti, di conseguenza la creazione del modello predittivo implica la fase di training e test. I dati verranno usati per il training del modello e quindi il modello verrà testato per determinare la precisione di stima dei prezzi. Per il momento non è importante trattare i motivi per i quali è necessario eseguire il training e quindi il test di un modello.

La *classificazione* e la *regressione* sono due tipi di tecniche di Machine Learning controllato. La classificazione stima una risposta da un set di categorie definito, ad esempio un colore (rosso, blu o verde). La regressione viene usata per stimare un numero.

Poiché si vuole stimare il prezzo, ovvero un numero, si userà un modello di regressione. Per questo esempio, verrà eseguito il training di un semplice modello di *regressione lineare*, che verrà testato nel passaggio successivo.

1. I dati vengono usati sia per il training che per il test, suddividendoli in set di training e di test distinti. Selezionare e trascinare il modulo [Split Data][split] \(Dividi dati) nell'area di disegno dell'esperimento e connetterlo all'output dell'ultimo modulo [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati). Impostare **Fraction of rows in the first output dataset** su 0,75. In questo modo per il training del modello verrà usato il 75% dei dati, mentre il restante 25% verrà usato per il testing.

	> [AZURE.TIP] Modificando il parametro **Random seed**, è possibile ottenere esempi casuali diversi per training e test. Questo parametro controlla il seeding del generatore di numeri pseudocasuali.

2. Eseguire l'esperimento. In questo modo, i moduli [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati) e [Split Data][split] \(Dividi dati) potranno passare le definizioni di colonna ai moduli che saranno aggiunti successivamente.

3. Per selezionare l'algoritmo di apprendimento, espandere la categoria **Machine Learning** nella tavolozza dei moduli a sinistra dell'area di disegno e quindi espandere **Initialize Model**. Verranno visualizzate diverse categorie di moduli che possono essere usate per inizializzare gli algoritmi di Machine Learning.

	Per questo esperimento, selezionare il modulo [Linear Regression][linear-regression] nella categoria **Regression** (è anche possibile trovare il modulo digitando "linear regression" nella casella Search della tavolozza) e trascinarlo nell'area di disegno dell'esperimento.

4. Trovare e trascinare il modulo [Train Model][train-model] nell'area di disegno dell'esperimento. Connettere la porta di input sinistra all'output del modulo [Linear Regression][linear-regression]. Connettere la porta di input destra all'output dei dati di training (porta sinistra) del modulo [Split Data][split].

5. Selezionare il modulo [Train Model][train-model], fare clic su **Launch column selector** nel riquadro **Properties** e quindi selezionare la colonna **price**. Questo è il valore che si intende stimare con il modello.

	![Selezione colonna "price"][screen7]

6. Eseguire l'esperimento.

Il risultato è un modello di regressione basato su training che può essere usato per assegnare un punteggio a nuovi campioni ai fini delle stime.

![Applicazione dell'algoritmo di Machine Learning][screen8]

## Passaggio 5: Stimare i prezzi delle nuove automobili

Dopo aver eseguito il training del modello usando il 75% dei dati, è possibile usarlo per classificare il restante 25% e verificarne il funzionamento.

1. Trovare il modulo [Score Model][score-model], trascinarlo sull'area di disegno dell'esperimento e connettere la porta di input sinistra all'output del modulo [Train Model][train-model]. Connettere la porta di input destra all'output dei dati di test (porta destra) del modulo [Split Data][split].

	![Modulo Score Model][screen8a]

2. Per eseguire l'esperimento e visualizzare l'output del modulo [Score Model][score-model], fare clic sulla porta di output e quindi selezionare **Visualize**. L'output mostra i valori stimati per il prezzo e i valori noti dai dati di test.

3. Infine, per verificare la qualità dei risultati, selezionare il modulo [Evaluate Model][evaluate-model], trascinarlo sull'area di disegno dell'esperimento e connettere la porta di input sinistra all'output del modulo [Score Model][score-model]. Sono disponibili due porte di input perché il modulo [Evaluate Model][evaluate-model] può essere usato per confrontare due modelli.

4. Eseguire l'esperimento.

Per visualizzare l'output del modulo [Evaluate Model][evaluate-model], fare clic sulla porta di output e selezionare **Visualize**. Per il modello vengono visualizzate le seguenti statistiche:

- **Mean Absolute Error** (MAE, errore assoluto medio): media degli errori assoluti (un *errore* è la differenza tra il valore stimato e quello effettivo).
- **Root Mean Squared Error** (RMSE, radice errore quadratico medio): radice quadrata della media degli errori quadratici delle stime effettuate sul set di dati di test.
- **Relative Absolute Error** (errore assoluto relativo): media degli errori assoluti relativamente alla differenza assoluta tra i valori effettivi e la media di tutti i valori effettivi.
- **Relative Squared Error** (errore quadratico relativo): media degli errori quadratici relativamente alla differenza quadratica tra i valori effettivi e la media di tutti i valori effettivi.
- **Coefficient of Determination** (coefficiente di determinazione): noto anche come **valore quadratico R**, è una metrica statistica che indica l'esattezza del modello rispetto ai dati.

Per ogni statistica di errore, sono preferibili i valori più piccoli. Un valore più piccolo indica che le stime sono più vicine ai valori effettivi. Per **Coefficient of Determination** più il valore si avvicina a uno (1,0) più le stime sono migliori.

![Valutazione dei risultati][screen9]

L'esperimento finale dovrebbe risultare simile al seguente:

![Esercitazione di Machine Learning: Completare esperimento di regressione lineare che usa tecniche di modellazione predittiva.][screen10]

## Passaggi successivi

Dopo aver completato la prima esercitazione di Machine Learning e aver configurato l'esperimento, è possibile ripeterlo e provare a migliorare il modello. Ad esempio, è possibile modificare le caratteristiche usate per la stima o le proprietà dell'algoritmo di [regressione lineare][linear-regression] oppure provare un algoritmo diverso. È anche possibile aggiungere contemporaneamente più algoritmi di Machine Learning all'esperimento e confrontarne due usando il modulo [Valuta modello][evaluate-model].

> [AZURE.TIP] Usare il pulsante **SAVE AS** nell'area di disegno dell'esperimento per copiare le iterazioni dell'esperimento. È possibile visualizzare tutte le iterazioni dell'esperimento facendo clic su **VIEW RUN HISTORY** sotto l'area di disegno. Per altre informazioni, vedere [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

Una volta ottenuto il modello desiderato, è possibile distribuirlo come servizio Web da usare per stimare i prezzi delle automobili utilizzando dati nuovi. Per altre informazioni, vedere [Distribuire un servizio Web di Azure Machine Learning][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Per una procedura più completa e dettagliata delle tecniche di modellazione predittiva per la creazione, il training, la valutazione e la distribuzione di un modello, vedere [Sviluppare una soluzione predittiva con Azure Machine Learning][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->