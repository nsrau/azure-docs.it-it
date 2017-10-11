---
title: Esperimento semplice in Machine Learning Studio | Documentazione di Microsoft
description: "Questa esercitazione di Machine Learning illustra un esperimento semplice di analisi scientifica dei dati. Verrà stimato il prezzo di un'automobile usando un algoritmo di regressione."
keywords: esperimento,regressione lineare,algoritmi di machine learning,esercitazione su machine learning,tecniche di modellazione predittiva,esperimento di analisi scientifica dei dati
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.author: garye
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4cc8e78e3ce22d70546d8a25da17b56f4b7cc166
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Esercitazione di Machine Learning: Creare il primo esperimento di analisi scientifica dei dati in Azure Machine Learning Studio

Questa esercitazione è destinata agli utenti che non hanno mai usato **Azure Machine Learning Studio**.

In questa esercitazione verrà illustrato come usare per la prima volta Studio per creare un esperimento di Machine Learning. L'esperimento testerà un modello analitico che stima il prezzo di un'automobile in base a diverse variabili, ad esempio la marca e le specifiche tecniche.

> [!NOTE]
> Questa esercitazione offre le nozioni di base su come trascinare moduli nell'esperimento, connetterli, eseguire l'esperimento ed esaminare i risultati. Non verrà invece illustrato l'argomento generale di Machine Learning o come selezionare e usare i numerosi algoritmi predefiniti (più di 100) e i moduli di gestione dei dati inclusi in Studio.
>
>Se non si ha familiarità con Machine Learning, è consigliabile vedere la serie di video [Analisi scientifica dei dati per principianti](data-science-for-beginners-the-5-questions-data-science-answers.md). Questa serie di video rappresenta un'introduzione completa a Machine Learning e usa linguaggio e concetti semplici.
>
>Se si ha familiarità con Machine Learning, ma si vuole approfondire la conoscenza su Machine Learning Studio e sugli algoritmi inclusi, vedere:
>
- [Cos'è Machine Learning Studio?](what-is-ml-studio.md) : questa è una panoramica generale di Studio.
- [Nozioni fondamentali di Machine Learning con esempi di algoritmi](basics-infographic-with-algorithm-examples.md): questa infografica è molto utile se si vuole acquisire una maggiore conoscenza sui diversi tipi di algoritmi di Machine Learning inclusi in Machine Learning Studio.
- [Machine Learning Guide](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) (Guida di Machine Learning): questa guida offre informazioni simili all'infografica, ma in formato interattivo.
- [Foglio informativo sugli algoritmi di Machine Learning](algorithm-cheat-sheet.md) e [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](algorithm-choice.md): questo poster scaricabile e l'articolo relativo illustrano in modo più approfondito gli algoritmi di Studio.
- [Machine Learning Studio: Algorithm and Module Help](https://msdn.microsoft.com/library/azure/dn905974.aspx) (Machine Learning Studio: Guida agli algoritmi e ai moduli): questa è una guida di riferimento completa sui moduli di Studio e include gli algoritmi di Machine Learning.

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Vantaggi di Machine Learning Studio

Machine Learning Studio semplifica la configurazione di un esperimento grazie a moduli a trascinamento preprogrammati con tecniche di modellazione predittiva.

L'uso di un'area di lavoro interattiva grafica consente di selezionare e trascinare ***set di dati*** e ***moduli*** di analisi in un'area di disegno interattiva. Questi vengono successivamente connessi per creare un ***esperimento*** da eseguire in Machine Learning Studio.
È possibile ***creare un modello***, ***eseguire il training del modello***, ***assegnare un punteggio e testare il modello***.

È possibile anche scorrere la struttura del modello, modificare l'esperimento ed eseguirlo di nuovo fino a quando non restituisce i risultati appropriati. Quando il modello è pronto, è possibile pubblicarlo come ***servizio Web*** in modo che altri utenti possano inviare nuovi dati e ottenere stime in cambio.

## <a name="open-machine-learning-studio"></a>Aprire Machine Learning Studio

Per avviare Studio, andare a [https://studio.azureml.net](https://studio.azureml.net). Se è già stato effettuato l'accesso a Machine Learning Studio in precedenza, fare clic su **Sign In** (Accedi). In caso contrario fare clic su **Sign up here** (Effettuare l'iscrizione qui) e scegliere un'opzione gratuita o a pagamento.

![Accedere a Machine Learning Studio][sign-in-to-studio]
<br/>
***Accedere a Machine Learning Studio***

## <a name="five-steps-to-create-an-experiment"></a>Cinque passaggi per la creazione di un esperimento

In questa esercitazione di Machine Learning, si seguiranno cinque passaggi di base per compilare un esperimento in Machine Learning Studio per creare un modello, eseguire il training e assegnare un punteggio:

- **Creare un modello**
    - [Passaggio 1: Ottenere i dati]
    - [Passaggio 2: Preparare i dati]
    - [Passaggio 3: Definire le caratteristiche]
- **Eseguire il training del modello**
    - [Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]
- **Assegnare un punteggio e testare il modello**
    - [Passaggio 5: Stimare i prezzi delle nuove automobili]

[Passaggio 1: Ottenere i dati]: #step-1-get-data
[Passaggio 2: Preparare i dati]: #step-2-prepare-the-data
[Passaggio 3: Definire le caratteristiche]: #step-3-define-features
[Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]: #step-4-choose-and-apply-a-learning-algorithm
[Passaggio 5: Stimare i prezzi delle nuove automobili]: #step-5-predict-new-automobile-prices

> [!TIP] 
> Per una copia di lavoro dell'esperimento seguente, visitare il sito Web [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com). Passare a **[Your first data science experiment - Automobile price prediction](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** (Primo esperimento scientifico sui dati - stima dei prezzi delle automobili) e fare clic su **Open in Studio** (Apri in Studio) per scaricare una copia dell'esperimento nell'area di lavoro di Machine Learning Studio.


## <a name="step-1-get-data"></a>Passaggio 1: Ottenere i dati

La prima operazione per eseguire un esperimento in Machine Learning è ottenere i dati.
In Machine Learning Studio sono disponibili numerosi set di dati di esempio da usare oppure è possibile importare dati da molte altre origini. Per questo esempio, verrà usato il set di dati di esempio **Automobile price data (Raw)** (Dati non elaborati sui prezzi delle automobili) incluso nell'area di lavoro.
Questo set di dati include voci per diverse automobili e include informazioni su marca, modello, specifiche tecniche e prezzo.

Di seguito viene illustrato come ottenere il set di dati nell'esperimento.

1. Creare un nuovo esperimento facendo clic su **+NEW** (NUOVO) nella parte inferiore della finestra di Machine Learning Studio, selezionare **EXPERIMENT** (ESPERIMENTO) e scegliere **Blank Experiment** (Esperimento vuoto).

2. All'esperimento viene assegnato un nome predefinito visualizzato nella parte superiore dell'area di disegno. Selezionare il testo e rinominarlo in modo significativo, ad esempio **Stima prezzi automobili**. Il nome non deve essere univoco.

    ![Rinominare l'esperimento][rename-experiment]

2. A sinistra dell'area di disegno dell'esperimento è presente una tavolozza di set di dati e moduli. Digitare **automobile** nella casella di ricerca nella parte superiore della tavolozza per trovare il set di dati denominato **Automobile price data (Raw)**. Trascinare il set di dati nell'area di disegno dell'esperimento.

    ![Individuare il set di dati delle automobili e trascinarlo nell'area di disegno dell'esperimento][type-automobile]
    <br/>
    ***Individuare il set di dati delle automobili e trascinarlo nel canvas dell'esperimento***

Per visualizzare l'aspetto dei dati, fare clic sulla porta di output nella parte inferiore del set di dati relativo alle automobili e selezionare **Visualize**.

![Fare clic sulla porta di output e selezionare "Visualize"][select-visualize]
 (Visualizza)<br/>
***Fare clic sulla porta di output e selezionare "Visualize"*** (Visualizza)

> [!TIP]
> I set di dati e i moduli hanno porte di input e output rappresentate da piccoli cerchi: le porte di input nella parte superiore e le porte di output nella parte inferiore.
Per creare un flusso di dati nell'esperimento, connettere una porta di output di un modulo a una porta di input di un altro.
In qualsiasi momento, è possibile fare clic sulla porta di output di un set di dati o di un modulo per visualizzare l'aspetto dei dati in tale punto specifico del flusso di dati.

In questo set di dati di esempio, ogni istanza di un'automobile viene rappresentata da una riga e le variabili associate a ogni automobile vengono rappresentate da colonne. In base alle variabili per un'automobile specifica, si proverà a stimare il prezzo nella colonna che si trova all'estrema destra, ovvero la colonna 26 denominata "price" (prezzo).

![Visualizzare i dati delle automobili nella finestra di visualizzazione dei dati][visualize-auto-data]
<br/>
***Visualizzare i dati delle automobili nella finestra di visualizzazione dei dati***

Chiudere la finestra di visualizzazione facendo clic sulla "**x**" nell'angolo superiore destro.

## <a name="step-2-prepare-the-data"></a>Passaggio 2: Preparare i dati

Prima di poter analizzare un set di dati è in genere necessario pre-elaborarlo. Ad esempio, si potrebbe notare l'assenza di valori nelle colonne di diverse righe. Per consentire al modello di analizzare correttamente i dati, è necessario eseguire la pulizia di questi valori mancanti. In questo caso verranno rimosse le righe con i valori mancanti. Inoltre, la colonna **normalized-losses** ha molti valori mancanti, pertanto verrà esclusa completamente dal modello.

> [!TIP]
> La pulizia dei valori mancanti dai dati di input è un prerequisito all'uso della maggior parte dei moduli.

Si aggiunge prima un modulo che rimuove completamente la colonna **normalized-losses** (perdite normalizzate) e si aggiunge un altro modulo che rimuove tutte le righe con dati mancanti.

1. Digitare **select columns** (seleziona colonne) nella casella di ricerca nella parte superiore del pannello dei moduli per trovare il modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) e trascinarlo nell'area di disegno. Questo modulo consente di selezionare le colonne di dati da includere o escludere nel modello.

2. Connettere la porta di output del set di dati **Automobile price data (Raw)** (Dati non elaborati sui prezzi delle automobili) alla porta di input del modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati).

    ![Aggiungere il modulo "Select Columns in Dataset" all'area di disegno dell'esperimento e connetterlo][type-select-columns]
    <br/>
    ***Aggiungere il modulo "Select Columns in Dataset" al canvas dell'esperimento e connetterlo***

3. Fare clic su [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) e fare clic su **Launch column selector** (Avvia selettore di colonna) nel riquadro **Properties** (Proprietà).

    - A sinistra, fare clic su **With rules**
    - In **Begin With** (Inizia con), fare clic su **All columns** (Tutte le colonne). In questo modo, [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) analizzerà tutte le colonne, ad eccezione di quelle che verranno escluse.
    - Negli elenchi a discesa selezionare **Escludi** e **nomi colonna**, quindi fare clic all'interno della casella di testo. Verrà visualizzato un elenco di colonne. Selezionare **normalized-losses** per aggiungere la colonna alla casella di testo.
    - Fare clic sul pulsante del segno di spunta (OK) per chiudere il selettore di colonne (nella parte inferiore destra).

    ![Avviare il selettore di colonna ed escludere la colonna "normalized-losses"][launch-column-selector]
    <br/>
    ***Avviare il selettore di colonna ed escludere la colonna "normalized-losses"***

    Il riquadro delle proprietà del modulo **Select Columns in Dataset** indica ora che verranno analizzate tutte le colonne del set di dati, a eccezione di **normalized-losses**.

    ![Il riquadro delle proprietà visualizza che la colonna "normalized-losses" è stata esclusa][showing-excluded-column]
    <br/>
    ***Il riquadro delle proprietà visualizza che la colonna "normalized-losses" è stata esclusa***

    > [!TIP]
    È possibile aggiungere un commento a un modulo facendo doppio clic sul modulo e immettendo del testo. In tal modo sarà possibile individuare subito l'operazione eseguita dal modulo nell'esperimento. In questo caso, fare doppio clic sul modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) e immettere il commento "Escludi perdite normalizzate".
    >
    >


    ![Fare doppio clic su un modulo per aggiungere un commento][add-comment]
    <br/>
    ***Fare doppio clic su un modulo per aggiungere un commento***

3. Trascinare il modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) nell'area di disegno dell'esperimento e connetterlo al modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati). Nel riquadro delle **proprietà** selezionare **Remove entire row** (Rimuovi riga intera) in **modalità Cleaning** (Pulizia). Questa operazione indica al modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) di pulire i dati rimuovendo le righe con valori mancanti. Fare doppio clic sul modulo e digitare il commento "Rimuovi righe valori mancanti".

    ![Impostare la modalità di pulizia a "Remove entire row" (Rimuovi riga intera) per il modulo "Clean Missing Data"][set-remove-entire-row]
     (Pulisci dati mancanti)<br/>
    ***Impostare la modalità di pulizia a "Remove entire row" (Rimuovi riga intera) per il modulo "Clean Missing Data"***

4. Eseguire l'esperimento facendo clic su **RUN** (ESEGUI) nella parte inferiore della pagina.

    Al termine dell'esecuzione dell'esperimento, tutti i moduli saranno contraddistinti da un segno di spunta verde per indicarne il corretto completamento. Si noti anche lo stato **Finished running** nell'angolo in alto a destra.

![Dopo l'esecuzione, l'esperimento dovrebbe avere l'aspetto seguente][early-experiment-run]
<br/>
***Dopo l'esecuzione, l'esperimento dovrebbe avere l'aspetto seguente***

> [!TIP]
> Perché è stato eseguito l'esperimento ora? Con l'esecuzione dell'esperimento, le definizioni delle colonne per i dati passano dal set di dati, attraverso i moduli [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) e [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti). Ciò significa che tutti i moduli connessi a [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) avranno anche queste stesse informazioni.

Fino a questo punto dell'esperimento sono stati solo puliti i dati. Per visualizzare il set di dati pulito, fare clic sulla porta di output sinistra del modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) e selezionare **Visualize** (Visualizza). Si noti che la colonna **normalized-losses** non è più inclusa e che non ci sono valori mancanti.

A questo punto, una volta puliti i dati è possibile specificare le caratteristiche da usare nel modello predittivo.

## <a name="step-3-define-features"></a>Passaggio 3: Definire le caratteristiche

In Machine Learning le *caratteristiche* sono singole proprietà misurabili di un elemento a cui si è interessati. Nel set di dati corrente ogni riga rappresenta un'automobile e ogni colonna è una caratteristica di tale automobile.

Per cercare un set di caratteristiche adeguato per creare un modello predittivo, è necessario sperimentare e conoscere approfonditamente il problema che si desidera risolvere. Alcune caratteristiche sono infatti migliori di altre per le stime. Alcune caratteristiche sono strettamente correlate ad altre e possono essere rimosse. Ad esempio, city-mpg e highway-mpg sono strettamente correlate ed è possibile rimuoverne una senza influire in modo significativo sulla stima.

Verrà ora creato un modello che usa un sottoinsieme delle caratteristiche del set di dati. È possibile tornare più tardi e selezionare caratteristiche diverse, eseguire di nuovo l'esperimento e verificare se i risultati ottenuti sono migliori. Per iniziare verranno tuttavia provate le funzionalità seguenti:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Trascinare un altro modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) nell'area di disegno dell'esperimento. Connettere la porta di output sinistra del modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) alla porta di input del modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati).

    ![Connettere il modulo "Select Columns in Dataset" al modulo "Clean Missing Data"][connect-clean-to-select]
    <br/>
    ***Connettere il modulo "Select Columns in Dataset" al modulo "Clean Missing Data"***

2. Fare doppio clic sul modulo e digitare "Selezionare le caratteristiche per la stima".

2. Fare clic su **Launch column selector** nel riquadro **Proprietà**.

3. Fare clic su **With rules**(Con regole).

4. In **Begin With** (Inizia con), fare clic su **No columns** (Nessuna colonna). Nella riga del filtro, scegliere **Include** (Includi) e **i nomi delle colonne** e selezionare l'elenco dei nomi delle colonne nella casella di testo. Ciò indica al modulo di non analizzare le colonne (caratteristiche), a eccezione di quelle specificate.

5. Fare clic sul pulsante di segno di spunta (OK).

    ![Selezionare le colonne (caratteristiche) da includere nella stima][select-columns-to-include]
    <br/>
    ***Selezionare le colonne (caratteristiche) da includere nella stima***

Questa operazione produce un set di dati filtrato contenente solo le caratteristiche che si vuole passare all'algoritmo di apprendimento che si userà nel passaggio successivo. In seguito, è possibile tornare indietro e provare a selezionare caratteristiche diverse.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Passaggio 4: Scegliere e applicare un algoritmo di apprendimento

I dati sono pronti, di conseguenza la creazione del modello predittivo implica la fase di training e test. Questi dati verranno usati per il training del modello e verrà testata la sua capacità di precisione per stimare i prezzi.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Classificazione* e *regressione* sono due tipi di algoritmi di Machine Learning sottoposti a supervisione. La classificazione stima una risposta da un set di categorie definito, ad esempio un colore (rosso, blu o verde). La regressione viene usata per stimare un numero.

Poiché si vuole stimare il prezzo, ovvero un numero, si userà un algoritmo di regressione. Per questo esempio, verrà usato un modello *a regressione lineare* semplice.

> [!TIP]
> Per altre informazioni sui diversi tipi di algoritmi di Machine Learning e su quando usarli, è consigliabile vedere il primo video [Le cinque domande a cui risponde l'analisi scientifica dei dati](data-science-for-beginners-the-5-questions-data-science-answers.md) della serie Analisi scientifica dei dati per principianti. È anche possibile esaminare l'infografica [Nozioni fondamentali di Machine Learning con esempi di algoritmi](basics-infographic-with-algorithm-examples.md) oppure scaricare il [Foglio informativo sugli algoritmi di Machine Learning](algorithm-cheat-sheet.md).

Il training del modello viene eseguito tramite l'assegnazione di un set di dati che include i prezzi. Il modello analizza i dati e cerca le correlazioni tra le caratteristiche di un'automobile e il prezzo. Il modello viene quindi testato: viene assegnato un set di caratteristiche per le automobili di cui si ha familiarità e viene visualizzata la precisione con la quale il modello stima il prezzo noto.

Il training e il test del modello verranno eseguiti con dati separati in un set di dati per il training e in un set di dati per il test.

1. Selezionare e trascinare il modulo [Split Data][split] (Dividi dati) nell'area di disegno dell'esperimento e connetterlo all'output dell'ultimo modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati).

2. Fare clic sul modulo [Split Data][split] (Dividi dati) per selezionarlo. Trovare l'opzione **Fraction of rows in the first output dataset** (Frazione di righe nel primo set di dati di output) nel riquadro delle **proprietà** a destra dell'area di disegno e impostarlo a 0,75. In questo modo verrà usato il 75% dei dati per eseguire il training del modello e il 25% per il test. Successivamente è possibile sperimentare percentuali diverse.

    ![Impostare la frazione di divisione del modulo "Split Data" a 0,75][set-split-data-percentage]
    <br/>
    ***Impostare la frazione di divisione del modulo "Split Data" a 0,75***

    > [!TIP]
    > Modificando il parametro **Random seed** , è possibile ottenere esempi casuali diversi per training e test. Questo parametro controlla il seeding del generatore di numeri pseudocasuali.

2. Eseguire l'esperimento. Durante l'esecuzione dell'esperimento, i moduli [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) e [Split Data][split] (Dividi dati) passano le definizioni di colonna ai moduli che saranno aggiunti successivamente.  

3. Per selezionare l'algoritmo di apprendimento, espandere la categoria **Machine Learning** nella tavolozza dei moduli a sinistra dell'area di disegno e quindi espandere **Initialize Model** (Inizializza modello). Verranno visualizzate diverse categorie di moduli che possono essere usate per inizializzare gli algoritmi di Machine Learning. Per questo esperimento, selezionare il modulo [Linear Regression][linear-regression] (Regressione lineare) della categoria **Regression** (Regressione) e trascinarlo nell'area di disegno dell'esperimento.
È anche possibile trovare il modulo digitando "linear regression" nella casella di ricerca della tavolozza.

4. Trovare e trascinare il modulo [Train Model][train-model] (Training modello) nell'area di disegno dell'esperimento. Connettere la porta di output del modulo [Linear Regression][linear-regression] (Regressione lineare) alla porta di input sinistra del modulo [Train Model][train-model] (Training modello) e connettere la porta di output sinistra dei dati per il training del modulo [Split Data][split] (Dividi dati) alla porta di input destra del modulo [Train Model][train-model] (Training modello).

    ![Connettere il modulo "Train Model" ai moduli "Linear Regression" e "Split Data"][connect-train-model]
    <br/>
    ***Connettere il modulo "Train Model" ai moduli "Linear Regression" e "Split Data"***

5. Fare clic sul modulo [Train Model][train-model] (Training modello), fare clic su **Launch column selector** (Avvia selettore di colonna) nel riquadro **Properties** (Proprietà) e quindi selezionare la colonna **price** (prezzo). Questo è il valore che si intende stimare con il modello.

    Per selezionare la colonna **price** nel selettore delle colonne, spostarla dall'elenco **Available columns** (Colonne disponibili) all'elenco **Selected columns** (Colonne selezionate).

    ![Selezionare la colonna price per il modulo "Train Model"][select-price-column]
    <br/>
    ***Selezionare la colonna price per il modulo "Train Model"***

6. Eseguire l'esperimento.

Il risultato è un modello di regressione, del quale è stato eseguito il training, che può essere usato per assegnare un punteggio ai nuovi dati delle automobili per effettuare stime sui prezzi.

![Dopo l'esecuzione, l'esperimento dovrebbe avere l'aspetto seguente][second-experiment-run]
<br/>
***Dopo l'esecuzione, l'esperimento dovrebbe avere l'aspetto seguente***

## <a name="step-5-predict-new-automobile-prices"></a>Passaggio 5: Stimare i prezzi delle nuove automobili

Dopo aver eseguito il training del modello usando il 75% dei dati, è possibile usarlo per classificare il restante 25% e verificarne il funzionamento.

1. Trovare e trascinare il modulo [Score Model][score-model] (Modello di punteggio) nell'area di disegno dell'esperimento. Connettere la porta di output del modulo [Train Model][train-model] (Training modello) alla porta di input sinistra del modulo [Score Model][score-model] (Modello di punteggio). Connettere la porta di output dei dati di test (porta destra) del modulo [Split Data][split] alla porta di input destra [Score Model][score-model] (Modello di punteggio).

    ![Connettere il modulo "Score Model" ai moduli "Train Model" e "Split Data"][connect-score-model]
    <br/>
    ***Connettere il modulo "Score Model" ai moduli "Train Model" e "Split Data"***

2. Eseguire l'esperimento e visualizzare l'output del modulo [Score Model][score-model] (Modello di punteggio), fare clic sulla porta di output di [Score Model][score-model] (Modello di punteggio) e selezionare **Visualize** (Visualizza). L'output mostra i valori stimati per il prezzo e i valori noti dai dati di test.  

    ![Output del modulo "Score Model"][score-model-output]
    <br/>
    ***Output del modulo "Score Model"***

3. Alla fine viene testata la qualità dei risultati. Selezionare e trascinare il modulo [Evaluate Model][evaluate-model] (Modello di valutazione) nell'area di disegno dell'esperimento e connettere la porta di output del modulo [Score Model][score-model] (Modello di punteggio) alla porta di input sinistra del modulo [Evaluate Model][evaluate-model] (Modello di valutazione).

    > [!TIP]
    > Ci sono due porte di input nel modulo [Evaluate Model][evaluate-model] (Modello di punteggio) perché può essere usato per confrontare due modelli affiancati. In un secondo momento, è possibile aggiungere un altro algoritmo all'esperimento e usare il modulo [Evaluate Model][evaluate-model] (Modello di punteggio) per vedere quale modello produce i risultati migliori.

4. Eseguire l'esperimento.

Per visualizzare l'output del modulo [Evaluate Model][evaluate-model], fare clic sulla porta di output e selezionare **Visualize** (Visualizza).

![Risultati di valutazione dell'esperimento][evaluation-results]
<br/>
***Risultati di valutazione dell'esperimento***

Per il modello vengono visualizzate le seguenti statistiche:

- **Mean Absolute Error** (MAE, errore assoluto medio): media degli errori assoluti (un *errore* è la differenza tra il valore stimato e quello effettivo).
- **Root Mean Squared Error** (RMSE, radice errore quadratico medio): radice quadrata della media degli errori quadratici delle stime effettuate sul set di dati di test.
- **Relative Absolute Error**(errore assoluto relativo): media degli errori assoluti relativamente alla differenza assoluta tra i valori effettivi e la media di tutti i valori effettivi.
- **Relative Squared Error**(errore quadratico relativo): media degli errori quadratici relativamente alla differenza quadratica tra i valori effettivi e la media di tutti i valori effettivi.
- **Coefficient of Determination** (coefficiente di determinazione): noto anche come **valore quadratico R**, è una metrica statistica che indica l'esattezza del modello rispetto ai dati.

Per ogni statistica di errore, sono preferibili i valori più piccoli. Un valore più piccolo indica che le stime sono più vicine ai valori effettivi. Per **Coefficient of Determination**più il valore si avvicina a uno (1,0) più le stime sono migliori.

## <a name="final-experiment"></a>Esperimento finale

L'esperimento dovrebbe avere un aspetto simile al seguente:

![Esperimento finale][complete-linear-regression-experiment]
<br/>
***Esperimento finale***

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la prima esercitazione di Machine Learning e impostato l'esperimento, è possibile migliorare il modello e successivamente distribuirlo come servizio Web predittivo.

- **Eseguire l'iterazione per migliorare il modello**: ad esempio, è possibile modificare le caratteristiche usate per la stima. In alternativa, è possibile modificare le proprietà dell'algoritmo [Linear Regression][linear-regression] (Regressione lineare) oppure provare un algoritmo diverso. È anche possibile aggiungere contemporaneamente più algoritmi di Machine Learning all'esperimento e confrontarne due usando il modulo [Evaluate Model][evaluate-model] (Modello di valutazione).
Per un esempio di come confrontare più modelli in un esperimento singolo, vedere [Compare Regressors](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) (Confrontare regressori) in [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com).

    > [!TIP]
    > Usare il pulsante **SAVE AS** (SALVA CON NOME) nella parte inferiore della pagina per copiare eventuali iterazioni dell'esperimento. È possibile visualizzare tutte le iterazioni dell'esperimento facendo clic su **VIEW RUN HISTORY** (VISUALIZZA LA CRONOLOGIA DI ESECUZIONE) nella parte inferiore della pagina. Per altre informazioni, vedere [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio][runhistory].

[runhistory]: manage-experiment-iterations.md

- **Distribuire il modello come servizio Web predittivo**: se si è soddisfatti con i risultati del modello, è possibile distribuirlo come servizio Web per stimare prezzi di automobili usando dati nuovi. Per altre informazioni dettagliate, vedere [Distribuire un servizio Web di Azure Machine Learning][publish].

[publish]: publish-a-machine-learning-web-service.md

Per altre informazioni: Per una procedura più completa e dettagliata del processo di creazione, training, assegnazione di punteggi e distribuzione di un modello, vedere [Sviluppare una soluzione predittiva con Azure Machine Learning][walkthrough].

[walkthrough]: walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/create-experiment/rename-experiment.png
[visualize-auto-data]:./media/create-experiment/visualize-auto-data.png
[select-visualize]: ./media/create-experiment/select-visualize.png
[showing-excluded-column]:./media/create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/create-experiment/second-experiment-run.png
[connect-score-model]:./media/create-experiment/connect-score-model.png
[evaluation-results]:./media/create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/create-experiment/type-automobile.png
[type-select-columns]:./media/create-experiment/type-select-columns.png
[launch-column-selector]:./media/create-experiment/launch-column-selector.png
[add-comment]:./media/create-experiment/add-comment.png
[connect-clean-to-select]:./media/create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/create-experiment/connect-train-model.png
[select-price-column]:./media/create-experiment/select-price-column.png

[score-model-output]:./media/create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

