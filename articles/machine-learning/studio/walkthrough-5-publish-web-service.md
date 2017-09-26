---
title: 'Passaggio 5: Distribuire il servizio Web di Machine Learning | Microsoft Docs'
description: 'Passaggio 5 della procedura dettagliata Sviluppare una soluzione predittiva: Distribuire un esperimento predittivo in Machine Learning Studio come servizio Web.'
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1bbc8ce31fc8e5ffb048a1fb9553a82975680a3c
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Passaggio 5 della procedura dettagliata: Distribuzione del servizio Web di Azure Machine Learning
Questo è il quinto passaggio della procedura dettagliata [Sviluppare una soluzione predittiva in Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Creare un'area di lavoro di Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Caricare i dati esistenti](walkthrough-2-upload-data.md)
3. [Creare un nuovo esperimento](walkthrough-3-create-new-experiment.md)
4. [Eseguire il training e valutare i modelli](walkthrough-4-train-and-evaluate-models.md)
5. **Distribuire il servizio web**
6. [Accedere al servizio Web](walkthrough-6-access-web-service.md)

- - -
Per permettere ad altri utenti di usare il modello predittivo sviluppato in questa procedura dettagliata, il modello può ora essere distribuito come servizio Web in Azure.

Fino a questo punto è stato sperimentato il training da parte del modello, ma il servizio distribuito non dovrà più eseguire il training, perché genererà le stime assegnando un punteggio all'input dell'utente in base al modello. Di conseguenza, è necessario eseguire alcuni preparativi per convertire questo esperimento da esperimento di ***training*** a esperimento ***predittivo***. 

Il processo si articola in tre passaggi:  

1. Rimuovere uno dei modelli
2. Convertire l'*esperimento di training* creato in *esperimento predittivo*
3. Distribuire l'esperimento predittivo come servizio Web

## <a name="remove-one-of-the-models"></a>Rimuovere uno dei modelli

Prima è necessario ridurre un po' questo esperimento. Nell'esperimento sono attualmente presenti due modelli diversi, ma si desidera usare un solo modello da distribuire come servizio Web.  

Si supponga che il modello di albero con boosting sia il modello eseguito meglio con il modello SVM. La prima cosa da fare, quindi, è rimuovere il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] e i moduli usati per eseguirne il training. È possibile creare prima una copia dell'esperimento facendo clic su **Save As** nella parte inferiore dell'area di disegno dell'esperimento.

È necessario eliminare i seguenti moduli:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine]
* I moduli [Train Model][train-model] e [Score Model][score-model] che sono stati connessi
* [Normalize Data][normalize-data] (entrambi)
* [Evaluate Model][evaluate-model] (poiché è terminata la valutazione dei modelli)

Selezionare ogni modulo e premere CANC oppure fare clic con il pulsante destro del mouse sul modulo e scegliere **Delete** (Elimina). 

![Rimuovere il modello SVM][3a]

Il modello avrà ora un aspetto analogo al seguente:

![Rimuovere il modello SVM][3]

Ora il modello è pronto per essere distribuito tramite il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire l'esperimento di training in un esperimento predittivo

Per preparare questo modello per la distribuzione, è necessario convertire l'esperimento di training in un esperimento predittivo. Questa operazione comporta tre passaggi:

1. Salvare il modello di cui è stato eseguito il training e usarlo per sostituire i moduli di training
2. Ridurre l'esperimento per rimuovere i moduli che sono serviti solo per il training
3. Definire il punto in cui il servizio Web accetterà l'input e in cui genererà l'output

È possibile eseguire questa procedura manualmente, ma i tre passaggi possono essere effettuati facendo clic su **Configura servizio Web** nella parte inferiore dell'area di disegno dell'esperimento e selezionare l'opzione **Predictive Web Service** (Servizio Web predittivo) per eseguire tutti e tre i passaggi.

> [!TIP]
> Per informazioni dettagliate cosa accade quando si converte un esperimento di training in un esperimento predittivo, vedere l'articolo relativo alla [preparazione del modello per la distribuzione in Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Quando si fa clic su **Set Up Web Service**(Configura servizio Web), vengono eseguite diverse operazioni:

* Il modello di cui è stato eseguito il training viene convertito in un singolo modulo **Trained Model** e archiviato nella tavolozza dei moduli a sinistra dell'area di disegno dell'esperimento e sarà disponibile in **Trained Models**.
* Vengono rimossi i moduli usati per il training, in particolare:
  * [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree]
  * [Train Model][train-model]
  * [Split Data][split]
  * Il secondo modulo [Execute R Script][execute-r-script] usato per i dati di test
* Il modello con training salvato viene aggiunto nuovamente all'esperimento
* Vengono aggiunti i moduli **Web service input** (Input servizio Web) e **Output servizio Web**; queste informazioni identificano dove verranno immessi i dati dell'utente nel modello e i dati che verranno restituiti quando si accede al servizio Web

> [!NOTE]
> Si può notare che l'esperimento viene salvato in due parti sotto le schede che sono state aggiunte all'inizio dell'area di disegno dell'esperimento. L'esperimento di training originale si trova sotto la scheda **Esperimento di training** e l'esperimento predittivo appena creato si trova sotto **Esperimento predittivo**. L'esperimento predittivo è quello che verrà distribuito come servizio Web.

Con questo particolare esperimento è necessario effettuare un'operazione aggiuntiva.
Sono stati aggiungi due moduli [Execute R Script][execute-r-script] per fornire una funzione di ponderazione per i dati. Si tratta semplicemente di un espediente necessario per il training e il test ed è quindi possibile estrarre i moduli nel modello finale.
Machine Learning Studio ha rimosso un modulo [Execute R Script][execute-r-script] durante la rimozione del modulo [Split Data][split]. È possibile rimuovere l'altro modulo e connettere [Metadata Editor][metadata-editor] direttamente a [Score Model][score-model].    

L'esperimento dovrebbe risultare simile al seguente:  

![Valutazione del modello sottoposto a training][4]  

> [!NOTE]
> Ci si chiederà perché il set di dati relativo alle carte di credito tedesche UCI sia stato lasciato nell'esperimento predittivo. Il servizio assegnerà un punteggio ai dati dell'utente, non al set di dati originale, quindi perché lasciare il set di dati originale nel modello?
> 
> Il servizio non necessita dei dati della carta di credito originali. Necessita però dello schema per tali dati, incluse informazioni come il numero di colonne presenti e quali colone sono numeriche. Queste informazioni sullo schema sono necessarie per interpretare i dati dell'utente. È necessario lasciare questi componenti connessi in modo che il modulo di punteggio abbia lo schema del set di dati quando il servizio è in esecuzione. I dati non vengono usati, solo lo schema.  
> 
> 

Eseguire l'esperimento un'ultima volta, facendo clic su **Run** (Esegui). Se si vuole verificare che il modello funzioni ancora, fare clic sull'output del modulo [Score Model][score-model] e selezionare **Visualizza risultati**. Verranno visualizzati i dati originali, insieme al valore di rischio di credito "Scored Labels" (Etichette punteggio) e al valore di probabilità del punteggio "Scored Probabilities" (Probabilità punteggio). 

## <a name="deploy-the-web-service"></a>Distribuire il servizio web
È possibile distribuire l'esperimento come servizio Web classico o come nuovo servizio Web basato su Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Distribuire l'esperimento come servizio Web classico
Per distribuire un servizio Web classico derivato dall'esperimento, fare clic su **Distribuisci servizio Web** sotto l'area di disegno e selezionare **Distribuisci servizio Web - Nuovo**. Machine Learning Studio distribuisce l'esperimento come servizio Web e apre il dashboard del servizio. Da qui è possibile tornare all'esperimento (**View snapshot** (Visualizza snapshot) o **View latest** (Visualizza più recente)) ed eseguire un semplice test del servizio Web. Vedere **Testare il servizio Web** di seguito. Qui sono inoltre disponibili informazioni per la creazione di applicazioni in grado di accedere al servizio Web (altre informazioni nella sezione successiva di questa procedura dettagliata).

![Dashboard del servizio Web][6]

È possibile configurare il servizio facendo clic sulla scheda **CONFIGURATION** (CONFIGURAZIONE), dove è possibile modificare il nome del servizio (per impostazione predefinita ha il nome dell'esperimento) e aggiungere una descrizione. È anche possibile inserire etichette più descrittive per i dati di input e output.  

![Configurare il servizio Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Distribuire l'esperimento come nuovo servizio Web

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md). 

Per distribuire un nuovo servizio Web derivato dall'esperimento:

1. Fare clic su **Deploy Web Service** (Distribuisci servizio Web) sotto l'area di disegno e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Machine Learning Studio visualizza la pagina **Deploy Experiment** (Distribuisci esperimento) dei servizi Web di Azure Machine Learning.

2. Immettere un nome per il servizio Web. 

3. Per **Piano tariffario** è possibile selezionare un piano tariffario esistente o selezionare "Nuovo" e denominare il nuovo piano, quindi selezionare l'opzione del piano mensile. Per impostazione predefinita vengono usati i livelli di piano per l'area predefinita e il servizio Web viene distribuito in questa area.

4. Fare clic su **Distribuisci**.

Dopo alcuni minuti verrà visualizzata la pagina **Avvio rapido** per il servizio Web.

È possibile configurare il servizio facendo clic sulla scheda **Configura**. Qui è possibile modificare il titolo del servizio e assegnare una descrizione. 

Per testare il servizio Web, selezionare la scheda **Test**. Vedere **Testare il servizio Web** di seguito. Per informazioni sulla creazione di applicazioni in grado di accedere al servizio Web, fare clic sull'opzione di menu **Consumo**. Altre informazioni sono disponibili nella sezione successiva di questa procedura dettagliata.

> [!TIP]
> È possibile aggiornare il servizio Web dopo averlo distribuito. Se, ad esempio, si vuole cambiare il modello, è possibile modificare l'esperimento di training, modificare i parametri del modello e fare clic su **Deploy Web Service** (Distribuisci servizio Web), selezionando **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Quando si distribuisce di nuovo l'esperimento, il servizio Web viene sostituito con il modello aggiornato.  
> 
> 

## <a name="test-the-web-service"></a>Testare il servizio Web

Quando viene eseguito l'accesso al servizio Web, i dati dell'utente vengono inseriti mediante il modulo **Web service input** (Input servizio Web)e trasferiti nel modulo [Score Model][score-model] (Modello punteggio), in cui viene assegnato un punteggio. In considerazione del modo in cui l'esperimento predittivo è configurato, il modello presuppone che i dati abbiano lo stesso formato del set di dati del rischio di credito originale.
I risultati vengono quindi restituiti all'utente dal servizio Web tramite il modulo **Output servizio Web**.

> [!TIP]
> In considerazione del modo in cui l'esperimento predittivo è configurato, vengono restituiti tutti i risultati del modulo [Score Model][score-model]. Ciò include tutti i dati di input, il valore del rischio di credito e il valore di probabilità del punteggio. Ma è possibile restituire un elemento diverso se si desidera, ad esempio, si potrebbe restituire solo il valore di rischio di credito. A tale scopo, inserire un modulo [Project Columns][project-columns] (Colonne progetto) tra [Score Model][score-model] (Modello punteggio) e **Output servizio Web** per eliminare le colonne che il servizio Web non deve restituire. 
> 
> 

È possibile testare il servizio Web in **Machine Learning Studio** o nel portale dei **servizi Web di Azure Machine Learning**.
È possibile testare un nuovo servizio Web solo dal portale dei **servizi Web di Azure Machine Learning**.

> [!TIP]
> Quando il test viene eseguito nel portale dei servizi Web di Azure Machine Learning, è possibile fare in modo che il portali crei dati di esempio da usare per testare il servizio di richiesta-risposta. Nella pagina **Configura** selezionare "Sì" per la richiesta di **dati di esempio abilitati?**. Quando si apre la scheda Richiesta-risposta della pagina **Test**, il portale compila i dati di esempio prelevati dal set di dati del rischio di credito originale.

### <a name="test-a-classic-web-service"></a>Testare un servizio Web classico

È possibile testare un servizio Web classico in Machine Learning Studio o nel portale dei servizi Web di Azure Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testare in Machine Learning Studio

1. Nella pagina **DASHBOARD** del servizio Web fare clic sul pulsante **Test** in **Default Endpoint** (Endpoint predefinito). Viene visualizzata una finestra di dialogo che richiede i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito originale.  

2. Immettere un set di dati e quindi fare clic su **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testare nel portale dei servizi Web di Azure Machine Learning

1. Nella pagina **DASHBOARD** del servizio Web fare clic sul pulsante **Test preview** (Anteprima test) in **Default Endpoint** (Endpoint predefinito). Viene visualizzata la pagina di test nel portale del servizio Web di Azure Machine Learning per l'endpoint di servizio Web e vengono chiesti i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito originale.

2. Fare clic su **Test Request-Response** (Test Richiesta-risposta). 

### <a name="test-a-new-web-service"></a>Testare un nuovo servizio Web

È possibile testare un nuovo servizio Web solo dal portale dei servizi Web di Azure Machine Learning.

1. Nel portale dei [servizi Web di Azure Machine Learning](https://services.azureml.net/quickstart) fare clic su **Test** nella parte superiore della pagina. Viene visualizzata la pagina **Test**, in cui è possibile immettere i dati per il servizio. I campi di input visualizzati corrispondono alle colonne visualizzate nel set di dati del rischio di credito originale. 

2. Immettere un set di dati e quindi fare clic su **Test Request-Response**(Test Richiesta-risposta).

I risultati del test vengono visualizzati sul lato destro della pagina nella colonna di output. 


## <a name="manage-the-web-service"></a>Gestire il servizio Web

### <a name="manage-a-classic-web-service-in-the-azure-classic-portal"></a>Gestire un servizio Web classico nel portale di Azure classico

Dopo aver distribuito il servizio Web classico, è possibile gestirlo dal [portale di Azure classico](https://manage.windowsazure.com).

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com)
2. Nel riquadro dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**
3. Fare clic sull'area di lavoro.
4. Fare clic sulla scheda **Servizi Web**.
5. Fare clic sul servizio Web creato
6. Fare clic sull'endpoint "predefinito".

Da qui è possibile eseguire varie operazioni, ad esempio monitorare come opera il servizio Web e apportare piccole modifiche alle prestazioni cambiando il numero di chiamate simultanee gestite dal servizio.

Per informazioni dettagliate, vedere:

* [Creazione di endpoint](create-endpoint.md)
* [Ridimensionamento di un servizio Web](scaling-webservice.md)

### <a name="manage-a-classic-or-new-web-service-in-the-azure-machine-learning-web-services-portal"></a>Gestire un servizio Web nuovo o classico usando il portale dei servizi Web di Azure Machine Learning

Dopo avere distribuito il servizio Web, classico o nuovo, è possibile gestirlo dal [portale dei servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/quickstart).

Per monitorare le prestazioni del servizio Web:

1. Accedere al portale dei [servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/quickstart)
2. Fare clic su **Servizi Web**
3. Fare clic sul servizio Web
4. Fare clic su **Dashboard**

- - -
**Passaggio successivo:[Accedere al servizio Web](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


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

