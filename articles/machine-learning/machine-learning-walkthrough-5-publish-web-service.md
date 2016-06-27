<properties
	pageTitle="Passaggio 5: Distribuire il servizio Web di Machine Learning | Microsoft Azure"
	description="Passaggio 5 della procedura dettagliata Sviluppare una soluzione predittiva: Distribuire un esperimento predittivo in Machine Learning Studio come servizio Web."
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
	ms.date="06/10/2016"
	ms.author="garye"/>


# Passaggio 5 della procedura dettagliata: Distribuzione del servizio Web di Azure Machine Learning

Questo è il quinto passaggio della procedura dettagliata [Sviluppare una soluzione predittiva in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.	[Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Distribuire il servizio web**
6.	[Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Per concedere ad altri utenti la possibilità di usare il modello di analisi predittiva sviluppato in questa procedura, questo verrà distribuito come servizio Web in Azure.

Fino a questo punto è stato sperimentato il training da parte del modello, ma il servizio distribuito non dovrà più eseguire il training perché genererà le stime assegnando un punteggio all'input dell'utente in base al modello. È necessario eseguire alcune operazioni per convertire questo esperimento da esperimento di ***training*** a esperimento ***predittivo***.

Il processo si articola in due passaggi:

1. Convertire l'*esperimento di training* creato in un *esperimento predittivo*
2. Distribuire l’esperimento predittivo come servizio web

Prima però è necessario ridurre un po' questo esperimento. Attualmente nell'esperimento sono presenti due modelli diversi, ma è necessario avere un solo modello da distribuire come servizio Web.

Si supponga che il modello di albero con boosting sia il modello più indicato. La prima cosa da fare, quindi, è rimuovere il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] e i moduli usati per eseguirne il training. È possibile creare prima una copia dell'esperimento facendo clic su **Save As** nella parte inferiore dell'area di disegno dell'esperimento.

È necessario eliminare i seguenti moduli:

- [Two-Class Support Vector Machine][two-class-support-vector-machine]
- I moduli [Train Model][train-model] e [Score Model][score-model] a esso connessi
- [Normalize Data][normalize-data] \(entrambi)
- [Evaluate Model][evaluate-model]

Selezionare il modulo e premere il tasto CANC oppure fare clic con il pulsante destro del mouse sul modulo e selezionare **Elimina**.

Ora il modello è pronto per essere distribuito mediante il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree].

## Convertire l'esperimento di training in un esperimento predittivo

La conversione in un esperimento predittivo prevede tre passaggi:

1. Salvare il modello di cui è stato eseguito il training e usarlo per sostituire i moduli di training
2. Ridurre l'esperimento per rimuovere i moduli che sono serviti solo per il training
3. Definire dove il servizio Web accetterà input e dove genererà output

Fortunatamente, per eseguire i tre passaggi, è sufficiente fare clic su **Distribuisci servizio web** nella parte inferiore dell'area di disegno dell'esperimento (selezionare l’opzione **Servizio web predittivo**).

Quando si fa clic su **Distribuisci servizio Web**, vengono eseguite diverse operazioni:

- Il modello di cui è stato eseguito il training viene salvato come singolo modulo **Trained Model** nella tavolozza dei moduli a sinistra dell'area di disegno dell'esperimento (sarà disponibile in **Trained Models**).
- Vengono rimossi i moduli usati per il training. In particolare:
  - [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree]
  - [Train Model][train-model]
  - [Split Data][split]
  - Il secondo modulo [Execute R Script][execute-r-script] usato per i dati di test
- Il modello con training salvato viene aggiunto nuovamente all'esperimento.
- Vengono aggiunti i moduli **Web service input** e **Web service output**.

> [AZURE.NOTE] L'esperimento è stato salvato in due parti in schede che sono state aggiunte in cima all'area di disegno dell'esperimento: l'esperimento di training originale si trova nella scheda **Training experiment** e l'esperimento di analisi predittiva appena creato si trova nella scheda **Predictive experiment**.

Con questo particolare esperimento è necessario effettuare un'operazione aggiuntiva. Sono stati aggiunti due moduli [Execute R Script][execute-r-script] per fornire una funzione di ponderazione per i dati di training e di test, ma nel modello finale questo diventa superfluo. Machine Learning Studio ha rimosso un modulo [Execute R Script][execute-r-script] quando ha rimosso il modulo [Split][split], pertanto ora è possibile rimuovere l’altro e connettere [Edit Metadata][edit-metadata] direttamente a [Score Model][score-model].

L'esperimento dovrebbe risultare simile al seguente:

![Valutazione del modello sottoposto a training][4]


> [AZURE.NOTE] Ci si chiederà perché il set di dati relativo alle carte di credito tedesche UCI sia stato lasciato nell'esperimento predittivo. Il servizio userà i dati dell'utente, non il set di dati originale, quindi perché lasciare il set di dati originale nel modello?
>
>Il servizio non necessita dei dati della carta di credito originali. Necessita però dello schema per tali dati, incluse informazioni come il numero di colonne presenti e quali colone sono numeriche. Queste informazioni sullo schema sono necessarie per interpretare i dati dell'utente. È necessario lasciare questi componenti collegati in modo che il modulo di punteggio abbia lo schema del set di dati quando il servizio è in esecuzione. I dati non vengono usati, solo lo schema.

Eseguire l'esperimento ancora una volta (facendo clic su **Esegui**). Se si vuole verificare che il modello funzioni ancora, fare clic sull'output del modulo [Score Model][score-model] e selezionare **Visualizza risultati**. Si vedranno i dati originali, insieme al valore di rischio di credito ("Etichette punteggio") e al valore di probabilità del punteggio ("Probabilità punteggio").

##
Distribuire il servizio web

Per distribuire un servizio Web derivato dall'esperimento, fare clic su **Distribuisci servizio web** sotto l'area di disegno. Machine Learning Studio distribuisce l'esperimento come servizio Web e apre il dashboard del servizio. Da qui è possibile ritornare all'esperimento (**View snapshot** o **View latest**) ed eseguire un test semplice del servizio Web (pulsante **Test**: vedere **Testare il servizio Web** di seguito). Qui sono inoltre disponibili informazioni per la creazione di applicazioni in grado di accedere al servizio Web (altre informazioni nella sezione successiva di questa procedura dettagliata).

![Dashboard del servizio Web][6]

> [AZURE.TIP] È possibile aggiornare il servizio Web dopo averlo distribuito. Se ad esempio si vuole cambiare il modello, è sufficiente modificare l'esperimento di training, modificare i parametri del modello e fare clic su **Distribuisci servizio web**. Quando si distribuisce di nuovo l'esperimento, il servizio Web verrà sostituito con il modello aggiornato.

È possibile configurare il servizio facendo clic sulla scheda **CONFIGURAZIONE**, dove è possibile modificare il nome del servizio (per impostazione predefinita ha il nome dell'esperimento) e aggiungere una descrizione. È anche possibile inserire etichette più descrittive per le colonne di input e output.

![Configurare il servizio Web][5]

## Testare il servizio Web
Nella pagina **DASHBOARD** fare clic sul pulsante **Test** in **Default Endpoint**.

![Testare il servizio Web][7]

Verrà visualizzata una finestra di dialogo e verranno richiesti i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito tedesco.

Immettere un set di dati e quindi fare clic su **OK**.

Nel servizio Web i dati entrano dal modulo **Web service input** (Input del servizio Web), passano attraverso il modulo [Edit Metadata][edit-metadata] \(Modifica metadati) e arrivano al modulo [Score Model][score-model] \(Modello di punteggio) in cui viene loro assegnato un punteggio. I risultati vengono quindi restituiti dal servizio Web tramite il modulo **Web service output** (Output del servizio Web).

> [AZURE.TIP] In considerazione del modo in cui l'esperimento predittivo è configurato, vengono restituiti tutti i risultati del modulo [Score Model][score-model] (Modello di punteggio). Ciò include tutti i dati di input, il valore del rischio di credito e il valore di probabilità del punteggio. Se si desidera che vengano restituiti valori diversi, ad esempio solo il valore del rischio di credito, è possibile inserire un modulo [Select Columns in Dataset][select-columns] \(Seleziona colonne in set di dati) tra [Score Model][score-model] \(Modello di punteggio) e **Web service output** (Output del servizio Web) per eliminare le colonne che non devono essere restituite.

## Gestire il servizio Web
Dopo aver distribuito il servizio Web, è possibile gestirlo dal [portale di Azure classico](https://manage.windowsazure.com).

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel riquadro dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**.
3. Fare clic sull'area di lavoro.
4. Fare clic sulla scheda **WEB SERVICES**.
5. Fare clic sul servizio Web appena creato.
6. Fare clic sull'endpoint "default".

Da qui è possibile eseguire varie operazioni, ad esempio monitorare come opera il servizio Web e apportare piccole modifiche alle prestazioni cambiando il numero di chiamate simultanee che il servizio può gestire. È anche possibile pubblicare il servizio Web in Azure Marketplace.

Per informazioni dettagliate, vedere:

- [Creazione di endpoint](machine-learning-create-endpoint.md)
- [Ridimensionamento di un servizio Web](machine-learning-scaling-webservice.md)
- [Pubblicare il servizio Web di Azure Machine Learning in Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

----------

**Passaggio successivo: [Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png
[7]: ./media/machine-learning-walkthrough-5-publish-web-service/publish7.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/it-IT/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

<!---HONumber=AcomDC_0615_2016-->