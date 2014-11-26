<properties title="Step 4: Train and evaluate the predictive analytic models" pageTitle="Step 4: Train and evaluate the predictive analytic models | Azure" description="Step 4: Train, score, and evaluate multiple models in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

Questo è il quarto passaggio della procedura dettagliata [Sviluppare una soluzione predittiva con Azure Machine Learning][Sviluppare una soluzione predittiva con Azure Machine Learning]:

1.  [Creare un'area di lavoro ML][Creare un'area di lavoro ML]
2.  [Caricare i dati esistenti][Caricare i dati esistenti]
3.  [Creare un nuovo esperimento][Creare un nuovo esperimento]
4.  **Addestrare e valutare i modelli**
5.  [Pubblicare il servizio Web][Pubblicare il servizio Web]
6.  [Accedere al servizio Web][Accedere al servizio Web]

------------------------------------------------------------------------

# Passaggio 4: Addestrare e valutare i modelli analitici predittivi

In questo esperimento si proveranno algoritmi diversi per il modello predittivo. Verranno creati due tipi diversi di modello, quindi ne verranno confrontati i punteggi risultanti per decidere quale algoritmo usare nell'esperimento finale.

È disponibile un numero di modelli tra cui scegliere. Per vedere i modelli disponibili, espandere il nodo **Machine Learning** nella tavolozza dei moduli, espandere **Inizializza modello** e quindi i nodi sotto di esso. Ai fini di questo esperimento, verranno selezionati Macchina a vettori di supporto e Albero delle decisioni con boosting a due classi. Verranno usati i moduli appropriati per inizializzare gli algoritmi di apprendimento e i moduli di **training modello** per il training dei modelli.

## Eseguire il training dei modelli

Prima di tutto verrà configurato il modello di albero delle decisioni con boosting:

1.  Trovare il modulo **Albero delle decisioni con boosting a due classi** nella tavolozza dei moduli e trascinarlo nell'area di disegno.
2.  Trovare il modulo **Esegui training modello**, trascinarlo nell'area di disegno, quindi connettere l'output del modulo dell'albero delle decisioni con boosting alla porta di input sinistra ("Modello senza training") del modulo **Training modello**.
3.  Connettere l'output del modulo **Esecuzione script R** sinistro alla porta di input destra ("Set di dati") del modulo **Esegui training modello**.

    > Suggerimento - Due degli input e uno degli output del modulo **Esecuzione script R** non sono necessari per questo esperimento, pertanto verranno lasciati scollegati. Questa situazione è alquanto frequente per alcuni moduli.

4.  Selezionare il modulo **Esegui training modello**. Nel riquadro **proprietà** fare clic su **Avvia selettore colonne**, selezionare "Indici con colonne" nel primo elenco a discesa, quindi immettere "21" nel campo successivo. È anche possibile selezionare "Nome colonna" e immettere "Rischio di credito". Questo identifica la colonna 21 come valore di rischio di credito, come la colonna in cui il modello calcolerà la previsione.

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:

![Training a model][Training a model]

Successivamente, si procederà alla configurazione del modello di macchina a vettori di supporto

Gli alberi delle decisioni con boosting funzionano bene con qualsiasi tipo di funzionalità. Poiché tuttavia il modulo di macchina a vettori di supporto genera un classificatore lineare, il modello che genera ha l'errore di test migliore quando gli elementi numerici hanno la stessa scala. Per convertire tutte le funzioni numeriche nella stessa scala si usa il modulo **Trasforma dati scalando** con una trasformazione tanh, che trasforma gli elementi nell'intervallo [0,1]. Notare che gli elementi numerici vengono convertiti dal modulo SVM in elementi di categoria e quindi in elementi 0/1 binari, quindi non è necessario trasformare manualmente gli elementi stringa. Non si deve inoltre trasformare la colonna Rischio di credito (colonna 21): si tratta di un valore numerico, ma è anche il valore per il quale il modulo deve eseguire la previsione dopo il training e non dovrà pertanto essere alterato.

1.  Trovare il modulo **Macchina a vettori di supporto a due classi** nella tavolozza dei moduli e trascinarlo nell'area di disegno.
2.  Fare clic con il pulsante destro del mouse sul modulo **Esegui training modello**, scegliere **Copia**, quindi fare clic con il pulsante destro del mouse sull'area di disegno e scegliere **Incolla**. Si noti che la copia del modulo **Esegui training modello** presenta la stessa selezione di colonne dell'originale.
3.  Connettere l'output del modulo Macchina a vettori di supporto alla porta di input sinistra ("Modello senza training") del modulo **Esegui training modello**.
4.  Trovare il modulo **Trasforma dati scalando** e trascinarlo nell'area di disegno.
5.  Connettere l'input di questo modulo di trasformazione all'output del modulo **Esecuzione script R** sinistro.
6.  Connettere la porta di output sinistra ("Set di dati trasformato") del modulo di trasformazione alla porta di input destra ("Set di dati") del modulo **Esegui training modello**.
7.  Nel riquadro **Proprietà** relativo al modulo di trasformazione, scegliere "Tanh" per il parametro **Metodo di trasformazione**.
8.  Fare clic su **Avvia selettore colonne**. Nel primo elenco a discesa selezionare "Tipi di colonna", quindi nel secondo elenco a discesa scegliere "Numerica" (lasciare il terzo elenco a discesa sulla selezione "Tutto"). Questo specifica che tutte le colonne numeriche (ma solo quelle di questo tipo) verranno trasformate.
9.  Fare clic sul segno più (+). Viene creata una nuova riga di elenchi a discesa. Selezionare "Escludi indici colonne" nel primo elenco a discesa, quindi immettere "21" nel campo di testo. Questo specifica che la colonna 21 (la colonna "Rischio di credito") verrà ignorata.
10. Fare clic su **OK**.

Il modulo **Trasforma dati scalando** è ora impostato per eseguire una trasformazione tanh su tutte le colonne numeriche, ad eccezione della colonna "Rischio di credito".

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:

![Training the second model][Training the second model]

## Classificare e valutare i modelli

Verranno usati i dati di assegnazione del punteggio separati dal modulo **Dividi** per assegnare il punteggio ai moduli di cui è stato eseguito il training. Sarà quindi possibile confrontare i risultati dei due modelli per stabilire quale ha generato i risultati migliori.

1.  Trovare il modulo **Calcola punteggio modello** e trascinarlo nell'area di disegno.
2.  Connettere la porta di input sinistra di questo modulo al modello di albero delle decisioni con boosting (connetterlo alla porta di output del modulo **Esegui training modello** connesso al modulo **Albero delle decisioni con boosting a due classi**).
3.  Connettere la porta di input destra del modulo **Calcola punteggio modello** all'output del modulo **Esecuzione script R** destro. Si noti che è accettabile che l'output di un modulo sia diretto a più destinazioni.
4.  Copiare e incollare il modulo **Calcola punteggio modello** per creare una seconda copia, oppure trascinare un nuovo modulo nell'area di disegno.
5.  Connettere la porta di input sinistra di questo modulo al modello di macchina a vettori di supporto (connetterla alla porta di output del modulo **Esegui training modello** connesso al modulo **Macchina a vettori di supporto a due classi**).
6.  Per il modello di macchina a vettori di supporto, è necessario eseguire la stessa trasformazione sui dati di test eseguita in precedenza sui dati di training. Copiare e incollare il modulo **Trasforma dati scalando** per creare una seconda copia e connetterla all'output del modulo **Esecuzione script R** destro.
7.  Connettere la porta di input destra del modulo **Calcola punteggio modello** all'output del modulo **Trasforma dati scalando**.

Per valutare i due risultati di punteggio verrà usato il modulo **Valuta modello**.

1.  Trovare il modulo **Valuta modello** e trascinarlo nell'area di disegno.
2.  Connettere la porta di input sinistra alla porta di output del modulo **Calcola punteggio modello** associato al modello di albero delle decisioni con boosting.
3.  Connettere la porta di input destra all'altro modulo **Calcola punteggio modello**.

L'esperimento avrà ora un aspetto analogo al seguente:

![Evaluating both models][Evaluating both models]

Fare clic sul pulsante **ESEGUI** sotto l'area di disegno per eseguire l'esperimento. L'operazione potrebbe richiedere alcuni minuti. Verrà visualizzato un indicatore rotante su ogni modulo per indicare che il modulo è in esecuzione, quindi un segno di spunta verde quando l'esecuzione di un modulo è terminata.

Quando tutti i moduli presentano il segno di spunta, l'esecuzione dell'esperimento sarà completa. Per verificare i risultati, fare clic con il pulsante destro del mouse sulla porta di output del modulo **Valuta modello** e selezionare **Visualizza**.

Il modulo **Valuta modello** produce un paio di curve e metriche che consentono di confrontare i risultati dei due modelli classificati. È possibile visualizzare i risultati come curve ROC (Receiver Operator Characteristic), curve precisione/recupero o curve di accuratezza. Altri dati visualizzati includono una matrice di confusione, valori cumulativi AUC e altre metriche. È possibile modificare il valore soglia spostando il dispositivo di scorrimento a sinistra o a destra e vedere come ciò influisce sul set di metriche.

Fare clic su "Set di dati con punteggio" o "Set di dati con punteggio per confronto" per evidenziare la curva associata e visualizzare le metriche associate in basso. Nella legenda per le curve, "Set di dati con punteggio" corrisponde alla porta di input sinistra del modulo **Valuta modello**. In questo caso si tratta del modello di albero delle decisioni con boosting. "Set di dati con punteggio per confronto" corrisponde alla porta di input destra, in questo caso il modello di macchina a vettori di supporto. Quando si fa clic su una di queste etichette, verrà evidenziata la curva per quel modello e verranno visualizzate in basso le metriche corrispondenti.

![ROC curves for models][ROC curves for models]

Esaminando i valori è possibile decidere quale sia il modello che più si avvicina ai risultati attesi. È possibile tornare indietro ed eseguire l'iterazione dell'esperimento modificando i valori dei vari modelli.

> Suggerimento - Ogni volta che si esegue l'esperimento, viene conservato un record dell'iterazione nella cronologia di esecuzione. È possibile visualizzare le iterazioni e tornare a una qualsiasi di esse facendo clic su **VISUALIZZA CRONOLOGIA ESECUZIONI** sotto l'area di disegno. È anche possibile fare clic su **Esecuzione precedente** nel riquadro **Proprietà** per tornare all'iterazione immediatamente precedente a quella aperta.

È anche possibile eseguire una copia di qualsiasi interazione dell'esperimento facendo clic su **SALVA CON NOME** sotto l'area di disegno. Questo consente di ottenere un duplicato dell'esperimento e di creare una nuova cronologia di esecuzione per tenere traccia delle interazioni che hanno condotto alla versione attuale. La nuova copia viene visualizzata nell'elenco **ESPERIMENTI** vicino all'originale. Ciò può risultare utile se si vuole creare un nuovo ramo di iterazioni dell'esperimento.

Come ulteriore accorgimento per tenere traccia delle modifiche apportate ai parametri dei moduli, è possibile aggiungere commenti a qualsiasi modulo nell'area di disegno dell'esperimento. Fare doppio clic sul modulo oppure fare clic su di esso con il pulsante destro del mouse e selezionare **Modifica commento**. I commenti vengono salvati con le iterazioni dell'esperimento e possono essere utili come annotazioni di lavoro.

------------------------------------------------------------------------

**Passaggi successivi: [Pubblicare il servizio Web][Pubblicare il servizio Web]**

  [Sviluppare una soluzione predittiva con Azure Machine Learning]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Creare un'area di lavoro ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Caricare i dati esistenti]: ../machine-learning-walkthrough-2-upload-data/
  [Creare un nuovo esperimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Pubblicare il servizio Web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Accedere al servizio Web]: ../machine-learning-walkthrough-6-access-web-service/
  [Training a model]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
  [Training the second model]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
  [Evaluating both models]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
  [ROC curves for models]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png
