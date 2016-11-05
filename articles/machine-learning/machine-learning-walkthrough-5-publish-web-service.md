---
title: 'Passaggio 5: Distribuire il servizio Web di Machine Learning | Microsoft Docs'
description: Passaggio 5 della procedura dettagliata Sviluppare una soluzione predittiva relativo alla distribuzione di un esperimento in Machine Learning Studio come servizio Web.
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
ms.date: 10/05/2016
ms.author: garye

---
# <a name="walkthrough-step-5:-deploy-the-azure-machine-learning-web-service"></a>Passaggio 5 della procedura dettagliata: Distribuire il servizio Web di Azure Machine Learning
Questo è il quinto passaggio della procedura dettagliata [Sviluppare una soluzione predittiva in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3. [Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. **Distribuire il servizio Web**
6. [Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

- - -
Per permettere ad altri utenti di usare il modello predittivo sviluppato in questa procedura dettagliata, il modello predittivo verrà ora distribuito come servizio Web in Azure.

Fino a questo punto è stato sperimentato il training da parte del modello, ma il servizio distribuito non dovrà più eseguire il training, perché genererà le stime assegnando un punteggio all'input dell'utente in base al modello. Di conseguenza, è necessario eseguire alcuni preparativi per convertire questo esperimento da esperimento di ***training*** a esperimento ***predittivo***. 

Il processo si articola in due passaggi:  

1. Convertire l'*esperimento di training* creato in *esperimento predittivo*
2. Distribuire l'esperimento predittivo come servizio Web

Prima però è necessario ridurre un po' questo esperimento. Nell'esperimento sono attualmente presenti due modelli diversi, ma è necessario avere un solo modello da distribuire come servizio Web.  

Si supponga che il modello di albero con boosting sia il modello più indicato. La prima cosa da fare, quindi, è rimuovere il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) e i moduli usati per eseguirne il training. È possibile creare prima una copia dell'esperimento facendo clic su **Save As** nella parte inferiore dell'area di disegno dell'esperimento.

È necessario eliminare i seguenti moduli:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi)
* I moduli [Train Model][train-model] (Modello di training) e [Score Model][score-model] (Modello di punteggio) connessi
* [Normalize Data][normalize-data] (Normalizza dati) (entrambi)
* [Evaluate Model][evaluate-model] (Modello di valutazione)

Selezionare il modulo e premere CANC oppure fare clic con il pulsante destro del mouse sul modulo e scegliere **Delete** (Elimina).

Ora il modello è pronto per essere distribuito tramite il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni incrementato a due classi).

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire l'esperimento di training in un esperimento predittivo
La conversione in un esperimento predittivo prevede tre passaggi:

1. Salvare il modello di cui è stato eseguito il training e usarlo per sostituire i moduli di training
2. Ridurre l'esperimento per rimuovere i moduli che sono serviti solo per il training
3. Definire il punto in cui il servizio Web accetterà l'input e in cui genererà l'output

Per eseguire tutti e tre i passaggi, è sufficiente fare clic su **Set Up Web Service** (Configura servizio Web) nella parte inferiore dell'area di disegno dell'esperimento e selezionare l'opzione **Predictive Web Service** (Servizio Web predittivo).

Quando si fa clic su **Set Up Web Service** (Configura servizio Web), vengono eseguite diverse operazioni:

* Il modello di cui è stato eseguito il training viene salvato come singolo modulo **Trained Model** (Modello con training) nella tavolozza dei moduli a sinistra dell'area di disegno dell'esperimento e sarà disponibile in **Trained Models** (Modelli con training).
* Vengono rimossi i moduli usati per il training. In particolare:
  * [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni incrementato a due classi).
  * [Train Model][train-model] (Modello di training)
  * [Split Data][split] (Dividi dati)
  * Secondo modulo [Execute R Script][execute-r-script] (Esegui script R) usato per testare i dati
* Il modello con training salvato viene aggiunto nuovamente all'esperimento.
* Vengono aggiunti i moduli **Web service input** (Input servizio Web) e **Web service output** (Output servizio Web).

> [!NOTE]
> L'esperimento è stato salvato in due parti in schede che sono state aggiunte nella parte superiore dell'area di disegno dell'esperimento: l'esperimento di training originale si trova nella scheda **Training experiment** (Esperimento di training), mentre l'esperimento di analisi predittiva appena creato si trova nella scheda **Predictive experiment** (Esperimento predittivo).
> 
> 

Con questo particolare esperimento è necessario effettuare un'operazione aggiuntiva.
Sono stati aggiungi due moduli [Execute R Script][execute-r-script] (Esegui script R) per fornire una funzione di ponderazione per i dati per il training e il testing. ma nel modello finale questo diventa superfluo.

Machine Learning Studio ha rimosso un modulo [Execute R Script][execute-r-script] (Esegui script R) durante la rimozione del modulo [Split Data][split] (Dividi dati). È ora possibile rimuovere l'altro e connettere [Metadata Editor][metadata-editor] (Editor metadati) direttamente a [Score Model][score-model] (Modello di punteggio).    

L'esperimento dovrebbe risultare simile al seguente:  

![Valutazione del modello sottoposto a training][4]  

> [!NOTE]
> Ci si chiederà perché il set di dati relativo alle carte di credito tedesche UCI sia stato lasciato nell'esperimento predittivo. Il servizio userà i dati dell'utente, non il set di dati originale, quindi perché lasciare il set di dati originale nel modello?
> 
> Il servizio non necessita dei dati della carta di credito originali. Necessita però dello schema per tali dati, incluse informazioni come il numero di colonne presenti e quali colone sono numeriche. Queste informazioni sullo schema sono necessarie per interpretare i dati dell'utente. È necessario lasciare questi componenti connessi in modo che il modulo di punteggio abbia lo schema del set di dati quando il servizio è in esecuzione. I dati non vengono usati, solo lo schema.  
> 
> 

Eseguire l'esperimento un'ultima volta, facendo clic su **Run** (Esegui). Se si vuole verificare se il modello funziona ancora, fare clic sull'output del modulo [Score Model][score-model] (Modello di punteggio) e selezionare **View Results** (Visualizza risultati). Verranno visualizzati i dati originali, insieme al valore di rischio di credito "Scored Labels" (Etichette punteggio) e al valore di probabilità del punteggio "Scored Probabilities" (Probabilità punteggio). 

## <a name="deploy-the-web-service"></a>Distribuire il servizio Web
È possibile distribuire l'esperimento come servizio Web classico o come nuovo servizio Web basato su Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Distribuire l'esperimento come servizio Web classico
Per distribuire un servizio Web classico derivato dall'esperimento, fare clic su **Deploy Web Service** (Distribuisci servizio Web) sotto l'area di disegno e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Machine Learning Studio distribuisce l'esperimento come servizio Web e apre il dashboard di questo servizio Web. Da qui è possibile tornare all'esperimento (**View snapshot** (Visualizza snapshot) o **View latest** (Visualizza più recente)) ed eseguire un semplice test del servizio Web. Vedere **Testare il servizio Web** di seguito. Qui sono inoltre disponibili informazioni per la creazione di applicazioni in grado di accedere al servizio Web. Altre informazioni sono disponibili nella sezione successiva di questa procedura dettagliata.

![Dashboard del servizio Web][6]

È possibile configurare il servizio facendo clic sulla scheda **CONFIGURATION** (CONFIGURAZIONE), dove è possibile modificare il nome del servizio (per impostazione predefinita ha il nome dell'esperimento) e aggiungere una descrizione. È anche possibile inserire etichette più descrittive per le colonne di input e output.  

![Configurare il servizio Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Distribuire l'esperimento come nuovo servizio Web
Per distribuire un nuovo servizio Web derivato dall'esperimento, fare clic su **Deploy Web Service** (Distribuisci servizio Web) sotto l'area di disegno e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Machine Learning Studio visualizza la pagina Deploy Experiment (Distribuisci esperimento) dei servizi Web di Azure Machine Learning.

Immettere un nome per il servizio Web e selezionare un piano tariffario. Se è disponibile un piano tariffario, è possibile selezionarlo; in caso contrario è necessario creare un nuovo piano tariffario per il servizio. 

1. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano esistente oppure fare clic sull'opzione **Select new plan** (Seleziona nuovo piano).
2. In **Plan Name**(Nome piano) digitare un nome che identifichi il piano nella fattura.
3. Selezionare uno dei **livelli del piano mensile**. Si noti che per impostazione predefinita vengono usati i livelli di piano per l'area predefinita e il servizio Web viene distribuito in questa area.

Fare clic su **Deploy** (Distribuisci). Verrà visualizzata la pagina **Quickstart** (Avvio rapido) per il servizio Web.

È possibile configurare il servizio facendo clic sull'opzione di menu **Configura** . dove è possibile modificare il nome del servizio (per impostazione predefinita ha il nome dell'esperimento) e aggiungere una descrizione. 

Per testare il servizio Web, selezionare l'opzione di menu **Test**. Vedere **Testare il servizio Web** di seguito. Per informazioni sulla creazione di applicazioni in grado di accedere al servizio Web, fare clic sull'opzione di menu **Consume** (Uso). Altre informazioni sono disponibili nella sezione successiva di questa procedura dettagliata.

> [!TIP]
> È possibile aggiornare il servizio Web dopo averlo distribuito. Se, ad esempio, si vuole cambiare il modello, è sufficiente modificare l'esperimento di training, modificare i parametri del modello e fare clic su **Deploy Web Service** (Distribuisci servizio Web). Selezionare quindi **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Quando si distribuisce di nuovo l'esperimento, il servizio Web viene sostituito con il modello aggiornato.  
> 
> 

## <a name="test-the-web-service"></a>Testare il servizio Web
### <a name="test-a-classic-web-service"></a>Testare un servizio Web classico
È possibile testare il servizio in Machine Learning Studio o nel portale dei servizi Web di Azure Machine Learning. Il testing nel portale dei servizi Web di Azure Machine Learning offre il vantaggio dell'abilitazione. 

**Test in Machine Learning Studio**

Nella pagina **DASHBOARD** fare clic sul pulsante **Test** in **Default Endpoint** (Endpoint predefinito). Verrà visualizzata una finestra di dialogo e verranno richiesti i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito tedesco.  

Immettere un set di dati e quindi fare clic su **OK**. 

**Test nel portale dei servizi Web di Azure Machine Learning**

Nella pagina **DASHBOARD** fare clic sul collegamento di anteprima **Test** in **Default Endpoint** (Endpoint predefinito). Viene visualizzata la pagina di test nel portale per l'endpoint di servizio Web e vengono chiesti i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito tedesco.

Fare clic su **Test Request-Response** (Test Richiesta-risposta). 

Nel servizio Web i dati vengono inseriti nel modulo **Web service input** (Input servizio Web), nel modulo [Metadata Editor][metadata-editor] (Editor metadati) e nel modulo [Score Model][score-model] (Modello di punteggio), in cui ricevono un punteggio. I risultati vengono quindi restituiti dal servizio Web tramite il modulo **Web service output** (Output servizio Web).

**Testare un nuovo servizio Web**

Nel portale dei servizi Web di Azure Machine Learning fare clic su **Test** nella parte superiore della pagina. Viene visualizzata la pagina **Test**, in cui è possibile immettere i dati per il servizio. I campi di input corrispondono alle colonne visualizzate nel set di dati del rischio di credito tedesco. 

Immettere un set di dati e quindi fare clic su **Test Request-Response**(Test Richiesta-risposta).

I risultati del test verranno visualizzati sul lato destro della pagina nella colonna di output. 

Quando il test viene eseguito nel portale dei servizi Web di Azure Machine Learning, è possibile abilitare dati di esempio da usare per testare il servizio di richiesta-risposta. Se il servizio Web è stato creato in Machine Learning Studio, i dati di esempio vengono recuperati dai dati usati per il training del modello.

> [!TIP]
> Tutti i risultati del modulo [Score Model][score-model] (Modello di punteggio) vengono restituiti in base al modo in cui è configurato l'esperimento predittivo. Ciò include tutti i dati di input, il valore del rischio di credito e il valore di probabilità del punteggio. Se si vuole che vengano restituiti valori diversi, ad esempio solo il valore del rischio di credito, è possibile inserire un modulo [Project Columns][project-columns] (Colonne progetto) tra [Score Model][score-model] (Modello di punteggio) e **Web service output** (Output servizio Web) per eliminare le colonne che non devono essere restituite dal servizio Web. 
> 
> 

## <a name="manage-the-web-service"></a>Gestire il servizio Web
**Gestire un servizio Web classico**

Dopo aver distribuito il servizio Web classico, è possibile gestirlo dal [portale di Azure classico](https://manage.windowsazure.com).

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel riquadro dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**.
3. Fare clic sull'area di lavoro.
4. Fare clic sulla scheda **Servizi Web**.
5. Fare clic sul servizio Web creato.
6. Fare clic sull'endpoint "default".

Da qui è possibile eseguire varie operazioni, ad esempio monitorare come opera il servizio Web e apportare piccole modifiche alle prestazioni cambiando il numero di chiamate simultanee gestite dal servizio.
È anche possibile pubblicare il servizio Web in Azure Marketplace.

Per informazioni dettagliate, vedere:

* [Creazione di endpoint](machine-learning-create-endpoint.md)
* [Ridimensionamento di un servizio Web](machine-learning-scaling-webservice.md)
* [Pubblicare il servizio Web di Azure Machine Learning in Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning**

Dopo aver distribuito il nuovo servizio Web, classico o nuovo, è possibile gestirlo dal [portale dei servizi Web di Azure Machine Learning](https://servics.azureml.net).

Per monitorare le prestazioni del servizio Web:

1. Accedere al [portale dei servizi Web di Azure Machine Learning](https://servics.azureml.net).
2. Fare clic su **Servizi Web**.
3. Fare clic sul servizio Web.
4. Fare clic su **Dashboard**.

- - -
**Passaggio successivo:[](machine-learning-walkthrough-6-access-web-service.md)Accedere al servizio Web**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


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
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/


<!--HONumber=Oct16_HO2-->


