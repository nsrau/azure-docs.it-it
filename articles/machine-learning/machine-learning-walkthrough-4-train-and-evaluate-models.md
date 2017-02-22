---
title: 'Passaggio 4: Eseguire il training e valutare i modelli analitici predittivi | Microsoft Docs'
description: "Passaggio 4 della procedura dettagliata Sviluppare una soluzione predittiva: Eseguire il training, classificare e valutare più modelli in Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: a9ebbbdc431a34553de04e920efbbc8c2496ce5f
ms.openlocfilehash: 1ef11386d9040c3929546ea05cd15237defd8a28


---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Passaggio 4 della procedura dettagliata: Eseguire il training e valutare i modelli analitici predittivi
Questo argomento contiene il quarto passaggio della procedura dettagliata [Sviluppare una soluzione di analisi predittiva in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3. [Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4. **Eseguire il training e valutare i modelli**
5. [Distribuire il servizio Web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

- - -
Uno dei vantaggi che offre Azure Machine Learning Studio per la creazione di modelli di Machine Learning è la possibilità di valutare più tipi di modelli contemporaneamente in un singolo esperimento e confrontare i risultati. Questo tipo di esperimento consente di trovare la soluzione migliore per il problema che si desidera risolvere.

Nell'esperimento sviluppato in questa procedura guidata verranno creati due tipi diversi di modello, quindi ne verranno confrontati i punteggi risultanti per decidere quale algoritmo usare nell'esperimento finale.  

Sono disponibili diversi modelli tra cui scegliere. Per visualizzare i modelli disponibili, espandere il nodo **Machine Learning** nella tavolozza dei moduli, espandere **Initialize Model** (Inizializza modello) e quindi i nodi al suo interno. Ai fini di questo esperimento, verranno selezionati i moduli [Two-Class Support Vector Machine (SVM)][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) e [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi).    

> [!TIP]
> Per ottenere informazioni circa quale algoritmo di Machine Learning sia più adatto per un problema specifico che si sta tentando di risolvere, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Eseguire il training dei modelli

In questo esperimento, saranno aggiunti il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) e il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi).

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree

Prima di tutto verrà configurato il modello di albero delle decisioni con boosting.

1. Trovare il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) nella tavolozza dei moduli e trascinarlo nell'area di disegno.

2. Trovare il modulo [Train Model][train-model] (Training modello), trascinarlo nell'area di disegno, quindi connettere l'output del modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) alla porta di input sinistra del modulo [Train Model][train-model] (Training modello).
   
   Il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) inizializza il modello generico e [Train Model][train-model] (Training modello) usa i dati di training per il training del modello. 

3. Connettere l'output del modulo [Execute R Script][execute-r-script] (Esegui script R) a sinistra alla porta di input destra del modulo [Train Model][train-model] (Training modello). Nel [Passo 3](machine-learning-walkthrough-3-create-new-experiment.md) di questa procedura dettagliata è stato deciso di utilizzare i dati riportati a sinistra del modulo Split Data (Divisione dati) per il training.
   
   > [!TIP]
   > Due degli input e uno degli output del modulo [Execute R Script][execute-r-script] (Esegui script R) non sono necessari per questo esperimento, pertanto potranno rimanere scollegati. 
   > 
   > 

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:  

![Training a model][1]

A questo punto è necessario indicare il modulo [Train Model] [ train-model] (Training modello) per cui il modello deve stimare il valore di rischio di credito.

1. Selezionare il modulo [Train Model][train-model] (Training modello). Nel riquadro **Proprietà**, fare clic su **Launch column selector** (Avvia selettore colonne).

2. Nella finestra di dialogo **Select a single column** (Selezionare una singola colonna), digitare "rischio di credito" nel campo di ricerca in **Colonne disponibili**, selezionare "Rischio di credito" di seguito e fare clic sul pulsante freccia destra (**>**) per spostare "Rischio di credito" su **Colonne selezionate**. 

    ![Selezionare la colonna Rischio di credito per il modulo "Train Model" (Training modello)][0]

3. Fare clic sul segno di spunta accanto a **OK**.

### <a name="two-class-support-vector-machine"></a>Two-Class Support Vector Machine

È quindi possibile configurare il modello di macchina a vettori di supporto (SVM).  

Innanzitutto, una breve spiegazione di SVM. Gli alberi delle decisioni con boosting funzionano bene con qualsiasi tipo di funzionalità. Poiché tuttavia il modulo di macchina a vettori di supporto genera un classificatore lineare, il modello che genera ha l'errore di test migliore quando gli elementi numerici hanno la stessa scala. Per convertire tutte le funzioni numeriche nella stessa scala, viene usata una trasformazione "Tanh", con il modulo [Normalize Data][normalize-data] (Normalizza dati). In questo modo, vengono trasformati i numeri nell'intervallo [0,1]. I modulo SVM converte gli elementi stringa in elementi di categoria e quindi in elementi 0/1 binari, pertanto non è necessario trasformare manualmente gli elementi stringa. Non si deve inoltre trasformare la colonna Credit Risk (colonna 21): si tratta di un valore numerico, ma è anche il valore per il quale il modulo deve eseguire la previsione dopo il training e non dovrà pertanto essere alterato.  

Per configurare il modello SVM, procedere come segue:

1. Trovare il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) nella tavolozza dei moduli e trascinarlo nell'area di disegno.

2. Fare clic con il pulsante destro del mouse sul modulo [Train Model][train-model] (Training modello), scegliere **Copia**, quindi fare clic con il pulsante destro del mouse sull'area di disegno e scegliere **Incolla**. La copia del modulo [Train Model][train-model] (Training modello) presenta la stessa selezione di colonne dell'originale.

3. Connettere l'output del modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) alla porta di input sinistra del secondo modulo [Train Model][train-model] (Training modello).

4. Trovare il modulo [Normalize Data][normalize-data] (Normalizza dati) e trascinarlo nell'area di disegno.

5. Connettere l'output sinistro del modulo [Execute R Script][execute-r-script] (Esegui script R) sinistro all'input di questo modulo (si noti che la porta di output di un modulo potrebbe essere connessa a più di un modulo).

6. Connettere la porta di output sinistra del modulo [Normalize Data][normalize-data] (Normalizza dati) alla porta di input destra del secondo modulo [Train Model][train-model] (Training modello).

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:  

![Training the second model][2]  

Ora configurare il modulo [Normalize Data][normalize-data] (Normalizza dati):

1. Fare clic per selezionare il modulo [Normalize Data][normalize-data] (Normalizza dati). Nel riquadro **Proprietà** relativo al modulo di trasformazione scegliere **Tanh** per il parametro **Transformation method** (Metodo di trasformazione).

2. Fare clic su **Launch column selector** (Avvia selettore di colonna), selezionare "No columns" (Nessuna colonna) per **Begin With** (Inizia con), selezionare **Include** (Includi) nel primo elenco a discesa, selezionare **column type** (tipo di colonna) nel secondo elenco a discesa e infine selezionare **Numeric** (Numerico) nel terzo elenco a discesa. Questo specifica che verranno trasformate tutte le colonne numeriche, ma solo quelle di questo tipo.

3. Fare clic sul segno più (+) a destra di questa riga per creare una nuova riga di elenchi a discesa. Selezionare **Escludi** nel primo elenco a discesa, selezionare **column names** (nomi colonna) nel secondo elenco a discesa e immettere "Rischio di credito" nel campo di testo. Questa impostazione specifica che la colonna Rischi di credito deve essere ignorata (è necessario farlo perché la colonna è numerica e pertanto, se non esclusa, verrebbe trasformata).

4. Fare clic sul segno di spunta accanto a **OK**.  

    ![Selezionare le colonne per il modulo Normalize Data (Normalizza dati)][5]

Il modulo [Normalize Data][normalize-data] (Normalizza dati) è ora impostato per eseguire una trasformazione tanh su tutte le colonne numeriche, a eccezione della colonna Rischi di credito.  

## <a name="score-and-evaluate-the-models"></a>Classificare e valutare i modelli

Verranno usati i dati di test separati dal modulo [Split Data][split] (Divisione dati) per assegnare il punteggio ai moduli oggetto del training. Sarà quindi possibile confrontare i risultati dei due modelli per stabilire quale ha generato i risultati migliori.  

### <a name="add-the-score-model-modules"></a>Aggiungere i moduli Score Model (Punteggio modello)

1. Trovare il modulo [Score Model][score-model] (Punteggio modello) e trascinarlo nell'area di disegno.

2. Connettere il modulo [Train Model][train-model] (Training modello) connesso al modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) alla porta di input sinistra del modulo [Score Model][score-model] (Punteggio modello).

3. Connettere il modulo [Execute R Script][execute-r-script] (Esegui script R) alla porta di input destra del modulo [Score Model][score-model] (Punteggio modello).

    ![Modulo Score Model (Punteggio modello) connesso][6]
   
   Il modulo [Score Model][score-model] (Punteggio modello) può ora prelevare le informazioni sul credito dai dati di test, eseguirle tramite il modello e confrontare le previsioni generate dal modello con la colonna relativa al rischio di credito dei dati di test.

4. Copiare e incollare il modulo [Score Model][score-model] (Punteggio modello) per creare una seconda copia.

5. Connettere l'output del modello SVM (vale a dire la porta di output del modulo [Train Model][train-model] (Training modello) connessa al modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) alla porta di input del secondo modulo [Score Model][score-model] (Punteggio modello).

6. Per il modello SVM, è necessario eseguire la stessa trasformazione sui dati di test eseguita in precedenza sui dati di training. Pertanto, copiare e incollare il modulo [Normalize Data][normalize-data] (Normalizza dati) per creare una seconda copia e connetterla al modulo [Execute R Script][execute-r-script] (Esegui script R).

7. Connettere l'output sinistra del secondo modulo [Normalize Data][normalize-data] (Normalizza dati) alla porta di input destra del secondo modulo [Score Model][score-model] (Punteggio modello).

    ![Entrambi i moduli Score Model (Punteggio modello) connessi][7]

### <a name="add-the-evaluate-model-module"></a>Aggiungere il modulo Evaluate Model (Modello di valutazione)

Per valutare i due risultati di punteggio e confrontarli, viene usato il modulo [Evaluate Model][evaluate-model] (Modello di valutazione).  

1. Trovare il modulo [Evaluate Model][evaluate-model] (Modello di valutazione) e trascinarlo nell'area di disegno.

2. Connettere la porta di output del modulo [Score Model][score-model] (Punteggio modello) associata al modello di albero delle decisioni con boosting alla porta di input sinistra del modulo [Evaluate Model][evaluate-model] (Modello di valutazione).

3. Connettere l'altro modulo [Score Model][score-model] (Punteggio modello) alla porta di input destra.  

    ![Modulo Evaluate Model (Modello di valutazione) connesso][8]

### <a name="run-the-experiment-and-check-the-results"></a>Eseguire l'esperimento e controllare i risultati

Per eseguire l'esperimento, fare clic sul pulsante **RUN** (ESEGUI) sotto l'area di disegno. L'operazione potrebbe richiedere alcuni minuti. Su ogni modulo verrà visualizzato un indicatore rotante per indicare che il modulo è in esecuzione, quindi apparirà un segno di spunta verde al termine dell'esecuzione. Quando tutti i moduli presentano il segno di spunta, l'esecuzione dell'esperimento sarà completa.

L'esperimento avrà ora un aspetto analogo al seguente:  

![Valutazione di entrambi i modelli][3]

Per verificare i risultati, fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] (Modello di valutazione) e selezionare **Visualize** (Visualizza).  

Il modulo [Evaluate Model][evaluate-model] (Modello di valutazione) produce un paio di curve e metriche che consentono di confrontare i risultati dei due modelli classificati. È possibile visualizzare i risultati come curve ROC (Receiver Operator Characteristic), curve precisione/recupero o curve di accuratezza. Altri dati visualizzati includono una matrice di confusione, valori cumulativi per l'area nella curva (AUC) e altra metrica. È possibile modificare il valore soglia spostando il dispositivo di scorrimento a sinistra o a destra e vedere come ciò influisce sul set della metrica.  

A destra del grafico fare clic su **Scored dataset** (Set di dati con punteggio) o **Scored dataset to compare** (Set di dati con punteggio da confrontare) per evidenziare la curva associata e visualizzare le metriche associate più sotto. Nella legenda per le curve, "Scored dataset" (Set di dati con punteggio) corrisponde alla porta di input sinistra del modulo [Evaluate Model][evaluate-model] (Modello di valutazione). In questo caso si tratta del modello di albero delle decisioni con boosting. "Scored dataset to compare" corrisponde alla porta di input destra, in questo caso il modello di macchina a vettori di supporto. Quando si fa clic su una di queste etichette, viene evidenziata la curva per il modello e viene visualizzata la metrica corrispondente, come mostrato dalla grafica seguente.  

![ROC curves for models][4]

Esaminando questi valori, è possibile decidere quale sia il modello che più si avvicina ai risultati previsti. È possibile tornare indietro ed eseguire l'iterazione dell'esperimento modificando i valori di parametro dei vari modelli. 

La scienza e l'arte di interpretare questi risultati e di ottimizzare le prestazioni del modello non rientrano nell'ambito di questa procedura dettagliata. Per ulteriori informazioni, è possibile leggere gli articoli seguenti:
- [Come valutare le prestazioni del modello in Azure Machine Learning](machine-learning-evaluate-model-performance.md)
- [Scegliere i parametri per ottimizzare gli algoritmi in Azure Machine Learning](machine-learning-algorithm-parameters-optimize.md)
- [Interpretare i risultati dei modelli in Azure Machine Learning](machine-learning-interpret-model-results.md)

> [!TIP]
> Ogni volta che si esegue l'esperimento, viene conservato un record dell'iterazione nella cronologia di esecuzione. È possibile visualizzare le iterazioni e tornare a una qualsiasi di esse facendo clic su **VISUALIZZA CRONOLOGIA ESECUZIONI** sotto l'area di disegno. È anche possibile fare clic su **Prior Run** (Esecuzione precedente) nel riquadro **Properties** (Proprietà) per tornare all'iterazione immediatamente precedente a quella aperta.
> 
> È possibile eseguire una copia di qualsiasi interazione dell'esperimento facendo clic su **SALVA CON NOME** sotto l'area di disegno. 
> Usare le proprietà **Summary** (Riepilogo) e **Description** (Descrizione) dell'esperimento per tenere traccia dei tentativi eseguiti nelle iterazioni dell'esperimento.
> 
> Per altre informazioni, vedere [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).  
> 
> 

- - -
**Passaggio successivo: [Distribuire il servizio Web](machine-learning-walkthrough-5-publish-web-service.md)**

[0]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/



<!--HONumber=Feb17_HO3-->


