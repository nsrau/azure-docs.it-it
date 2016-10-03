<properties
	pageTitle="Passaggio 4: Eseguire il training e valutare i modelli analitici predittivi | Microsoft Azure"
	description="Passaggio 4 della procedura dettagliata Sviluppare una soluzione predittiva: Eseguire il training, classificare e valutare più modelli in Azure Machine Learning Studio."
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
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye"/>  


# Passaggio 4 della procedura dettagliata: Eseguire il training e valutare i modelli analitici predittivi

Questo è il quarto passaggio della procedura dettagliata [Sviluppare una soluzione di analisi predittiva in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.	[Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**Eseguire il training e valutare i modelli**
5.	[Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Uno dei vantaggi che offre Azure Machine Learning Studio per la creazione di modelli di Machine Learning è la possibilità di valutare più tipi di modelli contemporaneamente in un esperimento e confrontare i risultati. Questo tipo di esperimento consente di trovare la soluzione migliore per il problema che si desidera risolvere.

Nell'esperimento sviluppato in questa procedura guidata verranno creati due tipi diversi di modello, quindi ne verranno confrontati i punteggi risultanti per decidere quale algoritmo usare nell'esperimento finale.

È disponibile un numero di modelli tra cui scegliere. Per vedere i modelli disponibili, espandere il nodo **Machine Learning** nella tavolozza dei moduli, espandere **Inizializza modello** e quindi i nodi sotto di esso. Ai fini di questo esperimento, verranno selezionati i moduli Support Vector Machine (SVM) e Two-Class Boosted Decision Tree.

> [AZURE.TIP] Per ottenere informazioni circa quale algoritmo di Machine Learning sia più adatto per un problema specifico che si sta tentando di risolvere, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

## Eseguire il training dei modelli

Prima di tutto verrà configurato il modello di albero delle decisioni con boosting:

1.	Trovare il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] nella tavolozza dei moduli e trascinarlo nell'area di disegno.
2.	Trovare il modulo [Esegui training modello][train-model], trascinarlo nell'area di disegno, quindi connettere l'output del modulo dell'albero delle decisioni con boosting alla porta di input sinistra ("Modello senza training") del modulo [Training modello][train-model].
    
    Il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] inizializza il modello generico e [Train Model][train-model] usa i dati di training per il training del modello.
     
3.	Connettere l'output sinistro ("Result Dataset") del modulo [Execute R Script][execute-r-script] sinistro alla porta di input destra ("Dataset") del modulo [Train Model][train-model].

	> [AZURE.TIP] Due degli input e uno degli output del modulo [Execute R Script][execute-r-script] non sono necessari per questo esperimento, pertanto verranno lasciati scollegati. Questa situazione è alquanto frequente per alcuni moduli.

4.	Selezionare il modulo [Esegui training modello][train-model]. Nel riquadro **Properties** fare clic su **Launch column selector**, selezionare **Include** nel primo elenco a discesa, selezionare **column names** nel secondo elenco a discesa e immettere "Credit Risk" nel campo di testo. È anche possibile selezionare **column indices** e immettere "21". Questo identifica la colonna 21 come valore di rischio di credito, come la colonna in cui il modello calcolerà la previsione.


Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:

![Training a model][1]

Successivamente, si procederà alla configurazione del modello di macchina a vettori di supporto

Innanzitutto, una breve spiegazione di SVM. Gli alberi delle decisioni con boosting funzionano bene con qualsiasi tipo di funzionalità. Poiché tuttavia il modulo di macchina a vettori di supporto genera un classificatore lineare, il modello che genera ha l'errore di test migliore quando gli elementi numerici hanno la stessa scala. Per convertire tutti gli elementi numerici nella stessa scala, si usa il modulo [Normalize Data][normalize-data] con una trasformazione "tanh", che trasforma i numeri nell'intervallo [0,1]. Si noti che, poiché gli elementi stringa vengono convertiti dal modulo SVM in elementi categorici e quindi in elementi 0/1 binari, non è necessario trasformare manualmente gli elementi stringa. Non si deve inoltre trasformare la colonna Credit Risk (colonna 21): si tratta di un valore numerico, ma è anche il valore per il quale il modulo deve eseguire la previsione dopo il training e non dovrà pertanto essere alterato.

Per configurare il modello SVM, procedere come segue:

1.	Trovare il modulo [Macchina a vettori di supporto a due classi][two-class-support-vector-machine] nella tavolozza dei moduli e trascinarlo nell'area di disegno.
2.	Fare clic con il pulsante destro del mouse sul modulo [Train Model][train-model], scegliere **Copy** e quindi fare clic con il pulsante destro del mouse sull'area di disegno e scegliere **Paste**. Si noti che la copia del modulo [Esegui training modello][train-model] presenta la stessa selezione di colonne dell'originale.
3.	Connettere l'output del modulo SVM alla porta di input sinistra ("modello non sottoposto a training ") del secondo modulo [Train Model][train-model].
4.	Trovare il modulo [Normalize Data][normalize-data] e trascinarlo nell'area di disegno.
5.	Connettere l'input di questo modulo all'output sinistro del modulo [Execute R Script][execute-r-script] sinistro. Si noti che la porta di output di un modulo può essere connessa a più di un modulo.
6.	Connettere la porta di output sinistra ("Transformed Dataset") del modulo [Normalize Data][normalize-data] alla porta di input destra ("Dataset") del secondo modulo [Train Model][train-model].
7.	Nel riquadro **Properties** del modulo [Normalize Data][normalize-data] scegliere **Tanh** come parametro di **Transformation method**.
8.	Fare clic su **Launch column selector**, selezionare "No columns" for **Begin With**, selezionare **Include** nel primo elenco a discesa, selezionare **column type** nel secondo elenco a discesa e selezionare **Numeric** nel terzo elenco a discesa. Questo specifica che tutte le colonne numeriche (ma solo quelle di questo tipo) verranno trasformate.
9.	Fare clic sul segno più (+) a destra di questa riga per creare una nuova riga di elenchi a discesa. Selezionare **Exclude** (Escludi) nel primo elenco a discesa, selezionare **column names** (nomi colonne) nel secondo elenco a discesa e fare clic nel campo di testo e selezionare "Credit risk" (Rischio di credito) dall'elenco di colonne. Questa impostazione specifica che la colonna Credit Risk deve essere ignorata (è necessario farlo perché la colonna è numerica e pertanto verrebbe trasformata).
10.	Fare clic su **OK**.


Il modulo [Normalize Data][normalize-data] è ora impostato per eseguire una trasformazione tanh su tutte le colonne numeriche, a eccezione della colonna Credit Risk.

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:

![Training the second model][2]

## Classificare e valutare i modelli

Verranno usati i dati di test separati dal modulo [Split Data][split] per assegnare il punteggio ai moduli di cui è stato eseguito il training. Sarà quindi possibile confrontare i risultati dei due modelli per stabilire quale ha generato i risultati migliori.

1.	Trovare il modulo [Calcola punteggio modello][score-model] e trascinarlo nell'area di disegno.
2.	Connettere la porta di input sinistra di questo modulo al modello di albero delle decisioni con boosting (connetterlo alla porta di output del modulo [Esegui training modello][train-model] connesso al modulo [Albero delle decisioni con boosting a due classi][two-class-boosted-decision-tree]).
3.	Connettere la porta di input destra del modulo [Score Model][score-model] all'output sinistro del modulo [Execute R Script][execute-r-script] destro.

    Il modulo [Score Model][score-model] ora può prelevare le informazioni sul credito dai dati di test, eseguirle tramite il modello e confrontare le previsioni generate dal modello con la colonna relativa al rischio di credito dei dati di test.

4.	Copiare e incollare il modulo [Calcola punteggio modello][score-model] per creare una seconda copia, oppure trascinare un nuovo modulo nell'area di disegno.
5.	Connettere la porta di input sinistra di questo modulo al modello di macchina a vettori di supporto (connetterla alla porta di output del modulo [Esegui training modello][train-model] connesso al modulo [Macchina a vettori di supporto a due classi][two-class-support-vector-machine]).
6.	Per il modello SVM, è necessario eseguire la stessa trasformazione sui dati di test eseguita in precedenza sui dati di training. Copiare e incollare il modulo [Normalize Data][normalize-data] per creare una seconda copia e connetterla all'output sinistro del modulo [Execute R Script][execute-r-script] destro.
7.	Connettere la porta di input destra del modulo [Score Model][score-model] all'output sinistro del modulo [Normalize Data][normalize-data].

Per valutare i due risultati di punteggio verrà usato il modulo [Valuta modello][evaluate-model].

1.	Trovare il modulo [Valuta modello][evaluate-model] e trascinarlo nell'area di disegno.
2.	Connettere la porta di input sinistra alla porta di output del modulo [Calcola punteggio modello][score-model] associato al modello di albero delle decisioni con boosting.
3.	Connettere la porta di input destra all'altro modulo [Calcola punteggio modello][score-model].

Fare clic sul pulsante **ESEGUI** sotto l'area di disegno per eseguire l'esperimento. L'operazione potrebbe richiedere alcuni minuti. Verrà visualizzato un indicatore rotante su ogni modulo per indicare che il modulo è in esecuzione, quindi un segno di spunta verde quando l'esecuzione di un modulo è terminata. Quando tutti i moduli presentano il segno di spunta, l'esecuzione dell'esperimento sarà completa.

L'esperimento avrà ora un aspetto analogo al seguente:

![Evaluating both models][3]

Per verificare i risultati, fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] e selezionare **Visualize** (Visualizza).

Il modulo [Valuta modello][evaluate-model] produce un paio di curve e metriche che consentono di confrontare i risultati dei due modelli classificati. È possibile visualizzare i risultati come curve ROC (Receiver Operator Characteristic), curve precisione/recupero o curve di accuratezza. Altri dati visualizzati includono una matrice di confusione, valori cumulativi per l'area nella curva (AUC) e altra metrica. È possibile modificare il valore soglia spostando il dispositivo di scorrimento a sinistra o a destra e vedere come ciò influisce sul set della metrica.

A destra del grafico, fare clic su **Scored dataset** o su **Scored dataset to compare** per evidenziare la curva associata e visualizzare le metriche associate in basso. Nella legenda per le curve, "Set di dati con punteggio" corrisponde alla porta di input sinistra del modulo [Valuta modello][evaluate-model]. In questo caso si tratta del modello di albero delle decisioni con boosting. "Scored dataset to compare" corrisponde alla porta di input destra, in questo caso il modello di macchina a vettori di supporto. Quando si fa clic su una di queste etichette, verrà evidenziata la curva per quel modello e verrà visualizzata in basso la metrica corrispondente.

![ROC curves for models][4]

Esaminando i valori è possibile decidere quale sia il modello che più si avvicina ai risultati attesi. È possibile tornare indietro ed eseguire l'iterazione dell'esperimento modificando i valori dei vari modelli.

> [AZURE.TIP] Ogni volta che si esegue l'esperimento, viene conservato un record dell'iterazione nella cronologia di esecuzione. È possibile visualizzare le iterazioni e tornare a una qualsiasi di esse facendo clic su **VISUALIZZA CRONOLOGIA ESECUZIONI** sotto l'area di disegno. È anche possibile fare clic su **Esecuzione precedente** nel riquadro **Proprietà** per tornare all'iterazione immediatamente precedente a quella aperta. È possibile eseguire una copia di qualsiasi interazione dell'esperimento facendo clic su **SALVA CON NOME** sotto l'area di disegno. Usare le proprietà **Summary** e **Description** dell'esperimento per tenere traccia di cosa si è tentato nelle iterazioni dell'esperimento.

>  Per altre informazioni, vedere [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).


----------

**Successivo: [Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->  
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0921_2016-->