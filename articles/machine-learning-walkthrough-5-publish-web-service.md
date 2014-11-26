<properties title="Step 5: Publish the Azure Machine Learning web service" pageTitle="Step 5: Publish the Machine Learning web service | Azure" description="Step 5: Publish a scoring experiment in Azure Machine Learning Studio as an ML API web service" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

Questo è il quinto passaggio della procedura dettagliata [Sviluppare una soluzione predittiva con Azure Machine Learning][Sviluppare una soluzione predittiva con Azure Machine Learning]:

1.  [Creare un'area di lavoro ML][Creare un'area di lavoro ML]
2.  [Caricare i dati esistenti][Caricare i dati esistenti]
3.  [Creare un nuovo esperimento][Creare un nuovo esperimento]
4.  [Addestrare e valutare i modelli][Addestrare e valutare i modelli]
5.  **Pubblicare il servizio Web**
6.  [Accedere al servizio Web][Accedere al servizio Web]

------------------------------------------------------------------------

# Passaggio 5: Pubblicare il servizio Web di Azure Machine Learning

Per rendere il modello predittivo disponibile per altri utenti, occorre pubblicarlo come servizio Web in Azure. L'utente potrà inviare al servizio un set di dati di una richiesta di credito e il servizio restituirà la stima del rischio di credito.

A questo scopo, è necessario:

-   Preparare l'esperimento in modo che sia pronto per la pubblicazione
-   Pubblicarlo in un server di gestione temporanea in cui sia possibile testarlo
-   Alzarlo di livello sul server attivo quando si pensa che sia pronto

Prima di proseguire, occorre creare una copia di questo esperimento da modificare. In questo modo sarà possibile tornare all'esperimento creato ogni volta che sarà necessario lavorare di nuovo sui modelli.

1.  Fare clic su **Salva con nome** sotto l'area di disegno.
2.  Specificare un nome descrittivo per la copia dell'esperimento. In genere si aggiunge "- Copia" al nome originale dell'esperimento. In questo caso, è possibile digitare "Stima del rischio di credito - Esperimento di assegnazione dei punteggi".
3.  Fare clic su **OK**.

L'esperimento originale e la copia saranno presenti nell'elenco ESPERIMENTI in ML Studio.

![Elenco degli esperimenti][Elenco degli esperimenti]

## Preparare l'esperimento di assegnazione dei punteggi

Per preparare il modello per la pubblicazione come servizio Web, sono necessarie due operazioni.

Occorre innanzitutto convertire l'esperimento da *esperimento di training* a *esperimento di assegnazione dei punteggi*. Fino a questo punto è stato sperimentato il training da parte del modello, tuttavia il servizio pubblicato non servirà più per il training, ma per assegnare un punteggio all'input dell'utente. Si salverà quindi una copia del modello sottoposto a training e quindi si elimineranno tutti i componenti dell'esperimento relativi al training.

In secondo luogo, il servizio Web di Azure Machine Learning accetterà l'input dell'utente e restituirà un risultato, quindi è necessario identificare i punti di input e output dell'esperimento.

### Convertire un esperimento di training in un esperimento di assegnazione dei punteggi

Si supponga che il modello di albero con boosting sia il modello più indicato. La prima cosa da fare è rimuovere i moduli di training SVM.

1.  Eliminare la **macchina a vettori di supporto a due classi**
2.  Eliminare i moduli **Modello di training** e **Modello di punteggio** a essa collegati
3.  Eliminare il modulo **Trasforma dati scalando**

Salvare quindi il modello di albero con boosting sottoposto a training. A questo punto è possibile rimuovere i moduli rimanenti dell'esperimento usato per il training e sostituirli con il modello sottoposto a training.

1.  Fare clic con il pulsante destro del mouse sulla porta di output del modulo **Modello di training** rimanente e selezionare **Salva come modello sottoposto a training**.
2.  Inserire un nome e una descrizione per il modello sottoposto a training. Per questo esempio si userà il nome "Stima del rischio di credito".

    > **Nota**: una volta salvato, il modello sottoposto ad addestramento viene visualizzato nella tavolozza dei moduli ed è disponibile per essere usato in altri esperimenti.

3.  Trovare il modello nella tavolozza dei moduli digitando "rischio di credito" nella casella **Cerca**, quindi trascinare il modello sottoposto ad addestramento **Stima del rischio di credito** nell'area di disegno dell'esperimento.
4.  Eliminare i moduli **Albero delle decisioni con boosting a due classi** e **Modello di training**.
5.  Collegare l'output del modello **Stima del rischio di credito** all'input a sinistra del modulo **Modello di punteggio**.

È ora disponibile la versione salvata e sottoposta a training del modello dell'esperimento al posto dei moduli di training originali.

Ci sono altri componenti dell'esperimento aggiunti solo per il training e per valutare gli algoritmi a due modelli. Si possono rimuovere anche questi:

-   **Dividi**
-   Entrambi i moduli **Esecuzione script R**
-   **Valuta modello**

Un'ultima cosa: i dati originali della carta di credito inclusi nella colonna Rischio di credito. Questa colonna è stata passata al modulo **Modello di training** in modo che fosse possibile eseguire il training del modello per stimare tali valori. Ma ora che il modello è stato sottoposto a training non è consigliabile continuare a passare tale colonna. Il modello sottoposto a training stimerà il valore automaticamente. Per rimuovere questa colonna dal flusso di dati, si usa il modulo **Colonne progetto**.

1.  Trovare e trascinare il modulo **Colonne progetto** nell'area di disegno.
2.  Collegare il modulo all'output del modulo **Editor metadati** (ora che i moduli **Dividi** e **Esecuzione script R** sono stati rimossi)
3.  Selezionare il modulo **Colonne progetto** e fare clic su **Avvia selettore colonne**.
4.  Lasciare "Tutte le colonne" nell'elenco a discesa.
5.  Fare clic sul segno più (+) per creare una nuova riga nell'elenco a discesa.
6.  Nella nuova riga, selezionare "Escludi nomi colonne" e inserire "Rischio di credito" nel campo di testo. È anche possibile specificare la colonna tramite il relativo numero, ovvero 21.
7.  Fare clic su **OK**.

    > Suggerimento: il selettore colonne segue la logica degli elenchi a discesa nella sequenza in cui le voci sono elencate. In questo caso, il modulo **Colonne progetto** è stato impostato per eseguire il pass-through di tutte le colonne ad eccezione della colonna "Rischio di credito". Se si fosse tralasciato il primo elenco a discesa, il modulo non avrebbe eseguito il pass-through di alcuna colonna.

8.  Collegare l'output del modulo **Colonne progetto** all'input appropriato del modulo **Modello di punteggio**.

L'esperimento dovrebbe risultare simile al seguente:

![Valutazione del modello sottoposto a training][Valutazione del modello sottoposto a training]

### Selezionare l'input e l'output del servizio

Nel modello originale, i dati da valutare venivano passati nella porta di input corretta ("Set di dati") del modulo **Modello di punteggio** e il risultato del punteggio veniva inviato alla porta di output ("Set di dati con punteggio"). Quando il servizio è in esecuzione, è consigliabile che i dati dell'utente e i risultati usino la stesse porte.

1.  Fare clic con il pulsante destro del mouse sulla porta di input corretta del modulo **Modello di punteggio** e selezionare **Imposta come input pubblicazione**. I dati dell'utente dovranno includere tutti i dati del vettore delle funzionalità.

    > **Suggerimento**: se occorre modificare i dati dell'utente prima di passarli al modulo di punteggio (come è stato usato un modulo **Trasforma dati scalando** per preparare i dati per il modello SVM), lasciare il modulo nel servizio Web e impostare l'input del servizio sulla porta di input del modulo.

2.  Fare clic con il pulsante destro del mouse sulla porta di output e selezionare **Imposta come input pubblicazione**. L'output del modulo Modello di punteggio verrà restituito dal servizio. È incluso il vettore delle funzionalità, più la stima del rischio di credito e il valore di probabilità del punteggio.

    > **Suggerimento**: se si desidera che il servizio Web restituisca solo parte dei dati, ad esempio se non si vuole restituire l'intero vettore delle funzionalità, è possibile aggiungere un modulo **Colonne progetto** dopo il modulo **Modello di punteggio**, configurarlo in modo da escludere le colonne non desiderate e quindi impostando l'output del modulo **Colonne progetto** in modo che corrisponda a quello del servizio Web.

> **Nota**: ci si chiederà perché il set di dati relativo alle carte di credito tedesche UCI e i moduli a esso associati collegati al modulo **Modello di punteggio** siano stati lasciati. Il servizio userà i dati dell'utente, non il set di dati originale, quindi serve mantenerli collegati?

Il servizio non necessita dei dati della carta di credito originali. Necessita però dello schema per tali dati, incluse informazioni come il numero di colonne presenti e quali colone sono numeriche. Queste informazioni sullo schema sono necessarie per interpretare i dati dell'utente. È necessario lasciare questi componenti collegati in modo che il modulo di punteggio abbia lo schema del set di dati quando il servizio è in esecuzione. I dati non vengono usati, solo lo schema.

Eseguire l'esperimento ancora una volta, facendo clic su **ESEGUI**. Se si vuole verificare che il modello funzioni ancora, fare clic con il pulsante destro del mouse sull'output del modulo **Modello di punteggio** e selezionare **Visualizza**. Si vedranno i dati originali, insieme al valore di rischio di credito ("Etichette punteggio") e al valore di probabilità del punteggio ("Probabilità punteggio").

## Pubblicare il servizio Web

Per pubblicare un servizio Web derivato dall'esperimento, fare clic su **PUBBLICA SERVIZIO WEB** sotto l'area di disegno e quindi su **SÌ** quando richiesto. ML Studio pubblica l'esperimento come servizio Web sul server di gestione temporanea ML e apre il dashboard del servizio.

> **Suggerimento**: è possibile aggiornare il servizio Web dopo averlo pubblicato. Se ad esempio si vuole cambiare il modello, è sufficiente modificare l'esperimento di training salvato in precedenza, modificare i parametri del modello e salvare il modello sottoposto a training (sovrascrivendo quello salvato). Quando si apre di nuovo l'esperimento di assegnazione del punteggio, sarà presente una nota che indica che qualcosa è cambiato (il modello sottoposto a training) e che è possibile aggiornare l'esperimento. Quando si pubblica di nuovo l'esperimento, il servizio Web verrà sostituito con il modello aggiornato.

È possibile configurare il servizio facendo clic sulla scheda **CONFIGURAZIONE**, dove è possibile modificare il nome del servizio (per impostazione predefinita ha il nome dell'esperimento) e aggiungere una descrizione. È anche possibile inserire etichette più descrittive per le colonne di input e output.

L'opzione **PASSARE ALL'AMBIENTE DI PRODUZIONE?** verrà descritta più avanti.

## Testare il servizio Web

Nella pagina **DASHBOARD** fare clic sul collegamento **Test** in **Servizi di gestione temporanea**. Verrà visualizzata una finestra di dialogo che richiede i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito tedesco.

Immettere un set di dati e quindi fare clic su **OK**.

Il risultato generato dal servizio Web viene visualizzato in fondo al dashboard. Nel modo in cui è configurato il servizio, i risultati visualizzati sono generati dal modulo di punteggio.

## Alzare di livello il servizio Web sul server attivo

Fino a questo momento il servizio è stato eseguito sul server di gestione temporanea ML. Quando si è pronti per attivarlo, è possibile richiedere di alzarlo di livello sul server attivo.

Nella scheda **CONFIGURAZIONE** fare clic su "SÌ" accanto a **PASSARE ALL'AMBIENTE DI PRODUZIONE?** Verrà inviato un messaggio all'amministratore IT per informarlo che il servizio Web è pronto per essere pubblicato. L'amministratore può quindi alzarlo di livello sul server attivo.

![Innalzamento di livello del servizio all'ambiente attivo][Innalzamento di livello del servizio all'ambiente attivo]

------------------------------------------------------------------------

**Passaggi successivi: [Accedere al servizio Web][Accedere al servizio Web]**

  [Sviluppare una soluzione predittiva con Azure Machine Learning]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Creare un'area di lavoro ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Caricare i dati esistenti]: ../machine-learning-walkthrough-2-upload-data/
  [Creare un nuovo esperimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Addestrare e valutare i modelli]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Accedere al servizio Web]: ../machine-learning-walkthrough-6-access-web-service/
  [Elenco degli esperimenti]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
  [Valutazione del modello sottoposto a training]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
  [Innalzamento di livello del servizio all'ambiente attivo]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
