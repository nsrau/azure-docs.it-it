<properties 
	pageTitle="Passaggio 5: Pubblicare il servizio Web di Machine Learning | Microsoft Azure" 
	description="Passaggio 5 della procedura dettagliata Sviluppare una soluzione predittiva: Pubblicare un esperimento di punteggio in Machine Learning Studio come servizio Web." 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Passaggio 5 della procedura dettagliata: Pubblicare il servizio Web di Azure Machine Learning

Questo è il quinto passaggio della procedura dettagliata, [Sviluppo di una soluzione predittiva con Azure ML](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.	[Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Pubblicare il servizio Web**
6.	[Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Per rendere il modello predittivo disponibile per altri utenti, occorre pubblicarlo come servizio Web in Azure.

Fino a questo punto è stato sperimentato il training da parte del modello, tuttavia il servizio pubblicato non servirà più per il training, ma per assegnare un punteggio all'input dell'utente. Si eseguiranno perciò alcune attività di preparazione e quindi si pubblicherà questo esperimento come servizio Web funzionante accessibile agli utenti. L'utente potrà inviare al servizio un set di dati di una richiesta di credito e il servizio restituirà la stima del rischio di credito.

A questo scopo, è necessario:

- Convertire l'*esperimento di training* creato in un *esperimento di classificazione*
- Pubblicazione dell'esperimento di classificazione come servizio Web

Prima però è necessario ridurre un po' questo esperimento. Nell'esperimento attualmente sono presenti due modelli diversi, ma ora è necessario selezionare un solo modello da pubblicare.

Si supponga che il modello di albero con boosting sia il modello più indicato. La prima cosa da fare, quindi, è rimuovere il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] e i moduli usati per eseguirne il training. È possibile creare prima una copia dell'esperimento facendo clic su **Save As** nella parte inferiore dell'area di disegno dell'esperimento.

È necessario eliminare i seguenti moduli:

1.	[Two-Class Support Vector Machine][two-class-support-vector-machine]
2.	I moduli [Train Model][train-model] e [Score Model][score-model] a esso connessi
3.	[Normalize Data][normalize-data] (entrambi)
4.	[Evaluate Model][evaluate-model]

A questo punto, è possibile pubblicare il modello.

##Convertire l'esperimento di training in un esperimento di classificazione

La conversione in un esperimento di punteggio prevede tre passaggi:

1. Salvare il modello di cui è stato eseguito il training e usarlo per sostituire i moduli di training
2. Ridurre l'esperimento per rimuovere i moduli che sono serviti solo per il training
3. Definire dove devono trovarsi i nodi di input e di output del servizio Web

Fortunatamente, per eseguire i tre passaggi, è sufficiente fare clic su **Create Scoring Experiment** nella parte inferiore dell'area di disegno dell'esperimento.

Quando si clic su **Create Scoring Experiment**, vengono eseguite alcune operazioni:

- Il modello di cui è stato eseguito il training viene salvato come modulo **Trained Model** nella tavolozza dei moduli a sinistra dell'area di disegno dell'esperimento e sarà disponibile nella tavolozza in **Trained Models**.
- Vengono rimossi i moduli usati per il training. In particolare:
  - [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree]
  - [Train Model][train-model] 
  - [Split][split]
  - Il secondo modulo [Execute R Script][execute-r-script] usato per i dati di test
- Il modello con training salvato viene aggiunto all'esperimento.
- Vengono aggiunti i moduli **Web service input** e **Web service output**.

> [AZURE.NOTE]L'esperimento è stato salvato in due parti: l'esperimento di training originale e il nuovo esperimento di punteggio . È possibile accedere a entrambi usando le schede nella parte superiore dell'area di disegno dell'esperimento.

È necessario eseguire un altro passaggio con l'esperimento. Machine Learning Studio ha rimosso un modulo [Execute R Script][execute-r-script] quando ha rimosso il modulo [Split][split], ma ha lasciato l'altro modulo [Execute R Script][execute-r-script]. Poiché tale modulo è stato usato solo per il training e il test (ha fornito una funzione di ponderazione per i dati di esempio), ora è possibile rimuoverlo e connettere [Metadata Editor][metadata-editor] a [Score Model][score-model].

L'esperimento dovrebbe risultare simile al seguente:

![Valutazione del modello sottoposto a training][4]


Ci si chiederà perché il set di dati relativo alle carte di credito tedesche UCI sia stato lasciato nell'esperimento di punteggio. Il servizio userà i dati dell'utente, non il set di dati originale, quindi serve mantenerli collegati?

Il servizio non necessita dei dati della carta di credito originali. Necessita però dello schema per tali dati, incluse informazioni come il numero di colonne presenti e quali colone sono numeriche. Queste informazioni sullo schema sono necessarie per interpretare i dati dell'utente. È necessario lasciare questi componenti collegati in modo che il modulo di punteggio abbia lo schema del set di dati quando il servizio è in esecuzione. I dati non vengono usati, solo lo schema.

Eseguire l'esperimento ancora una volta, facendo clic su **ESEGUI**. Se si vuole verificare che il modello funzioni ancora, fare clic sull'output del modulo [Score Model][score-model] e selezionare **View Results**. Si vedranno i dati originali, insieme al valore di rischio di credito ("Etichette punteggio") e al valore di probabilità del punteggio ("Probabilità punteggio").

##Pubblicare il servizio Web

Per pubblicare un servizio Web derivato dall'esperimento, fare clic su **PUBLISH WEB SERVICE** sotto l'area di disegno. Machine Learning Studio pubblica l'esperimento come servizio Web e apre il dashboard del servizio.

> [AZURE.TIP]È possibile aggiornare il servizio Web dopo averlo pubblicato. Se ad esempio si vuole cambiare il modello, è sufficiente modificare l'esperimento di training, modificare i parametri del modello e fare clic su **PUBLISH WEB SERVVICE**. Quando si pubblica di nuovo l'esperimento, il servizio Web verrà sostituito con il modello aggiornato.

È possibile configurare il servizio facendo clic sulla scheda **CONFIGURAZIONE**, dove è possibile modificare il nome del servizio (per impostazione predefinita ha il nome dell'esperimento) e aggiungere una descrizione. È anche possibile inserire etichette più descrittive per le colonne di input e output.

##Testare il servizio Web
Nella pagina **DASHBOARD** fare clic sul collegamento **Test** in **Default Endpoint**. Verrà visualizzata una finestra di dialogo e verranno richiesti i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito tedesco.

Immettere un set di dati e quindi fare clic su **OK**.

Il risultato generato dal servizio Web viene visualizzato in fondo al dashboard. Nel modo in cui è configurato il servizio, i risultati visualizzati sono generati dal modulo di punteggio.


----------

**Passaggio successivo: [Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
 

<!---HONumber=August15_HO6-->