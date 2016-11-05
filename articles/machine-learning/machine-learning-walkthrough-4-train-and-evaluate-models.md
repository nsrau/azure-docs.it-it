---
title: 'Passaggio 4: Eseguire il training e valutare i modelli analitici predittivi | Microsoft Docs'
description: 'Passaggio 4 della procedura dettagliata Sviluppare una soluzione predittiva: Eseguire il training, classificare e valutare più modelli in Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: garye

---
# <a name="walkthrough-step-4:-train-and-evaluate-the-predictive-analytic-models"></a>Passaggio 4 della procedura dettagliata: Eseguire il training e valutare i modelli analitici predittivi
Questo argomento contiene il quarto passaggio della procedura dettagliata [Sviluppare una soluzione di analisi predittiva in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3. [Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4. **Eseguire il training e valutare i modelli**
5. [Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

- - -
Uno dei vantaggi che offre Azure Machine Learning Studio per la creazione di modelli di Machine Learning è la possibilità di valutare più tipi di modelli contemporaneamente in un esperimento e confrontare i risultati. Questo tipo di esperimento consente di trovare la soluzione migliore per il problema che si desidera risolvere.

Nell'esperimento sviluppato in questa procedura guidata verranno creati due tipi diversi di modello, quindi ne verranno confrontati i punteggi risultanti per decidere quale algoritmo usare nell'esperimento finale.  

Sono disponibili diversi modelli tra cui scegliere. Per visualizzare i modelli disponibili, espandere il nodo **Machine Learning** nella tavolozza dei moduli, espandere **Initialize Model** (Inizializza modello) e quindi i nodi al suo interno. Ai fini di questo esperimento, verranno selezionati i moduli Support Vector Machine (SVM) e Two-Class Boosted Decision Tree.    

> [!TIP]
> Per ottenere informazioni circa quale algoritmo di Machine Learning sia più adatto per un problema specifico che si sta tentando di risolvere, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Eseguire il training dei modelli
Prima di tutto verrà configurato il modello di albero delle decisioni con boosting:  

1. Trovare il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni incrementato a due classi) nella tavolozza dei moduli e trascinarlo nell'area di disegno.
2. Trovare il modulo [Train Model][train-model] (Modello di training), trascinarlo nell'area di disegno e quindi connettere l'output del modulo di albero delle decisioni incrementato alla porta di input sinistra "Untrained model" (Modello senza training) del modulo [Train Model][train-model] (Modello di training).
   
   Il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni incrementato a due classi) inizializza il modello generico e [Train Model][train-model] (Modello di training) usa i dati di training per eseguire il training del modello. 
3. Connettere l'output sinistro "Result Dataset" (Set di dati risultati) del modulo [Execute R Script][execute-r-script] (Esegui script R) sinistro alla porta di input destra "Dataset" (Set di dati) del modulo [Train Model][train-model] (Modello di training).
   
   > [!TIP]
   > Poiché due degli input e uno degli output del modulo [Execute R Script][execute-r-script] (Esegui script R) non sono necessari per questo esperimento, verranno lasciati scollegati. 
   > 
   > 
4. Selezionare il modulo [Train Model][train-model] (Modello di training). Nel riquadro **Properties** (Proprietà) fare clic su **Launch column selector** (Avvia selettore di colonna), selezionare **All Types** (Tutti i tipi) nell'elenco a discesa in **Available Columns** (Colonne disponibili) e immettere "Rischio di credito" nel campo di testo. Selezionare **All Types** (Tutti i tipi) nell'elenco a discesa in **Selected Columns** (Colonne selezionate). Selezionare "Rischio di credito" e fare clic sul pulsante di freccia evidenziato per spostarlo in **Selected Columns** (Colonne selezionate). 
5. Fare clic su **Save**.

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:  

![Training a model][1]

È quindi possibile configurare il modello di macchina a vettori di supporto (SVM).  

Innanzitutto, una breve spiegazione di SVM. Gli alberi delle decisioni con boosting funzionano bene con qualsiasi tipo di funzionalità. Poiché tuttavia il modulo di macchina a vettori di supporto genera un classificatore lineare, il modello che genera ha l'errore di test migliore quando gli elementi numerici hanno la stessa scala. Per convertire tutte le funzioni numeriche nella stessa scala, viene usata una trasformazione "Tanh", con il modulo [Normalize Data][normalize-data] (Normalizza dati). In questo modo, vengono trasformati i numeri nell'intervallo [0,1]. Poiché gli elementi stringa vengono convertiti dal modulo SVM in elementi di categoria e quindi in elementi 0/1 binari, non è necessario trasformare manualmente gli elementi stringa. Non si deve inoltre trasformare la colonna Credit Risk (colonna 21): si tratta di un valore numerico, ma è anche il valore per il quale il modulo deve eseguire la previsione dopo il training e non dovrà pertanto essere alterato.  

Per configurare il modello SVM, procedere come segue:

1. Trovare il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) nella tavolozza dei moduli e trascinarlo nell'area di disegno.
2. Fare clic con il pulsante destro del mouse sul modulo [Train Model][train-model] (Modello di training), scegliere **Copy** (Copia), fare clic con il pulsante destro del mouse sull'area di disegno e quindi scegliere **Paste** (Incolla). La copia del modulo [Train Model][train-model] (Modello di training) ha la stessa selezione di colonne dell'originale.
3. Connettere l'output del modulo SVM alla porta di input sinistra "Untrained Model" (Modello senza training) del secondo modulo [Train Model][train-model] (Modello di training).
4. Trovare il modulo [Normalize Data][normalize-data] (Normalizza dati) e trascinarlo nell'area di disegno.
5. Connettere l'input di questo modulo all'output sinistro del modulo [Execute R Script][execute-r-script] (Esegui script R) sinistro. Notare che la porta di output di un modulo può essere connessa a più di un altro modulo.
6. Connettere la porta di output sinistra "Transformed Dataset" (Set di dati trasformato) del modulo [Normalize Data][normalize-data] (Normalizza dati) alla porta di input destra "Dataset" (Set di dati) del secondo modulo [Train Model][train-model] (Modello di training).
7. Nel riquadro **Properties** (Proprietà) per il modulo [Normalize Data][normalize-data] (Normalizza dati) selezionare **Tanh** come parametro di **Transformation method** (Metodo di trasformazione).
8. Fare clic su **Launch column selector** (Avvia selettore di colonna), selezionare "No columns" (Nessuna colonna) per **Begin With** (Inizia con), selezionare **Include** (Includi) nel primo elenco a discesa, selezionare **column type** (tipo di colonna) nel secondo elenco a discesa e infine selezionare **Numeric** (Numerico) nel terzo elenco a discesa. Questo specifica che verranno trasformate tutte le colonne numeriche, ma solo quelle di questo tipo.
9. Fare clic sul segno più (+) a destra di questa riga per creare una nuova riga di elenchi a discesa. Selezionare **Exclude** (Escludi) nel primo elenco a discesa, selezionare **column names** (nomi colonne) nel secondo elenco a discesa e fare clic nel campo di testo e selezionare "Rischio di credito" nell'elenco di colonne. Questa impostazione specifica che la colonna Credit Risk deve essere ignorata (è necessario farlo perché la colonna è numerica e pertanto verrebbe trasformata).
10. Fare clic su **OK**.  

Il modulo [Normalize Data][normalize-data] (Normalizza dati) è ora impostato per l'esecuzione di una trasformazione Tanh per tutte le colonne numeriche, ad eccezione della colonna Rischio di credito.  

Questa parte dell'esperimento avrà ora un aspetto simile al seguente:  

![Training the second model][2]  

## <a name="score-and-evaluate-the-models"></a>Classificare e valutare i modelli
Vengono usati i dati di test separati dal modulo [Split Data][split] (Dividi dati) per assegnare il punteggio ai moduli di cui è stato eseguito il training. Sarà quindi possibile confrontare i risultati dei due modelli per stabilire quale ha generato i risultati migliori.  

1. Trovare il modulo [Score Model][score-model] (Modello di punteggio) e trascinarlo nell'area di disegno.
2. Connettere il modulo [Train Model][train-model] (Modello di training) connesso al modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni incrementato a due classi) alla porta di input sinistra del modulo [Score Model][score-model] (Modello di punteggio).
3. Connettere la porta di input destra del modulo [Score Model][score-model] (Modello di punteggio) all'output sinistro del modulo [Execute R Script][execute-r-script] (Esegui script R) destro.
   
   Il modulo [Score Model][score-model] (Modello di punteggio) può ora prelevare le informazioni sul credito dai dati di test, eseguirle nel modello e confrontare le previsioni generate dal modello con l'effettiva colonna relativa al rischio di credito dei dati di test.
4. Copiare e incollare il modulo [Score Model][score-model] (Modello di punteggio) per creare una seconda copia oppure trascinare un nuovo modulo nell'area di disegno.
5. Connettere la porta di input sinistra di questo modulo al modello SVM, ovvero connetterla alla porta di output del modulo [Train Model][train-model] (Modello di training) connesso al modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi).
6. Per il modello SVM, è necessario eseguire la stessa trasformazione sui dati di test eseguita in precedenza sui dati di training. Copiare e incollare il modulo [Normalize Data][normalize-data] (Normalizza dati) per creare una seconda copia e connetterla all'output sinistro del modulo [Execute R Script][execute-r-script] (Esegui script R).
7. Connettere la porta di input destra del modulo [Score Model][score-model] (Modello di punteggio) all'output sinistro del modulo [Normalize Data][normalize-data] (Normalizza dati).  

Per valutare i due risultati di punteggio, viene usato il modulo [Evaluate Model][evaluate-model] (Modello di valutazione).  

1. Trovare il modulo [Evaluate Model][evaluate-model] (Modello di valutazione) e trascinarlo nell'area di disegno.
2. Connettere la porta di input sinistra alla porta di output del modulo [Score Model][score-model] (Modello di punteggio) associato al modello di albero delle decisioni incrementato.
3. Connettere la porta di input destra all'altro modulo [Score Model][score-model] (Modello di punteggio).  

Per eseguire l'esperimento, fare clic sul pulsante **RUN** (ESEGUI) sotto l'area di disegno. L'operazione potrebbe richiedere alcuni minuti. Su ogni modulo verrà visualizzato un indicatore rotante per indicare che il modulo è in esecuzione, quindi apparirà un segno di spunta verde al termine dell'esecuzione. Quando tutti i moduli presentano il segno di spunta, l'esecuzione dell'esperimento sarà completa.

L'esperimento avrà ora un aspetto analogo al seguente:  

![Valutazione di entrambi i modelli][3]

Per verificare i risultati, fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] (Modello di valutazione) e selezionare **Visualize** (Visualizza).  

Il modulo [Evaluate Model][evaluate-model] (Modello di valutazione) produce un paio di curve e alcune metriche che permettono di confrontare i risultati dei due modelli classificati. È possibile visualizzare i risultati come curve ROC (Receiver Operator Characteristic), curve precisione/recupero o curve di accuratezza. Altri dati visualizzati includono una matrice di confusione, valori cumulativi per l'area nella curva (AUC) e altra metrica. È possibile modificare il valore soglia spostando il dispositivo di scorrimento a sinistra o a destra e vedere come ciò influisce sul set della metrica.  

A destra del grafico fare clic su **Scored dataset** (Set di dati con punteggio) o **Scored dataset to compare** (Set di dati con punteggio da confrontare) per evidenziare la curva associata e visualizzare le metriche associate più sotto. Nella legenda delle curve "Scored dataset" (Set di dati con punteggio) corrisponde alla porta di input sinistra del modulo [Evaluate Model][evaluate-model] (Modello di valutazione). Nel nostro caso, si tratta del modello di albero delle decisioni incrementato. "Scored dataset to compare" corrisponde alla porta di input destra, in questo caso il modello di macchina a vettori di supporto. Quando si fa clic su una di queste etichette, viene evidenziata la curva per il modello e viene visualizzata la metrica corrispondente più sotto, come mostrato dalla grafica seguente.  

![ROC curves for models][4]

Esaminando questi valori, è possibile decidere quale sia il modello che più si avvicina ai risultati previsti. È possibile tornare indietro ed eseguire l'iterazione dell'esperimento modificando i valori dei vari modelli. 

> [!TIP]
> Ogni volta che si esegue l'esperimento, viene conservato un record dell'iterazione nella cronologia di esecuzione. È possibile visualizzare le iterazioni e tornare a una qualsiasi di esse facendo clic su **VISUALIZZA CRONOLOGIA ESECUZIONI** sotto l'area di disegno. È anche possibile fare clic su **Prior Run** (Esecuzione precedente) nel riquadro **Properties** (Proprietà) per tornare all'iterazione immediatamente precedente a quella aperta.
> 
> È possibile eseguire una copia di qualsiasi interazione dell'esperimento facendo clic su **SALVA CON NOME** sotto l'area di disegno. Usare le proprietà **Summary** (Riepilogo) e **Description** (Descrizione) dell'esperimento per tenere traccia dei tentativi eseguiti nelle iterazioni dell'esperimento.
> 
> Per altre informazioni, vedere [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).  
> 
> 

- - -
**Passaggio successivo: [Distribuire il servizio Web](machine-learning-walkthrough-5-publish-web-service.md)**

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



<!--HONumber=Oct16_HO2-->


