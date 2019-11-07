---
title: 'Esercitazione 3: Distribuire il modello di rischio di credito'
titleSuffix: Azure Machine Learning Studio (classic)
description: Esercitazione dettagliata che mostra come creare una soluzione di analisi predittiva per la valutazione del rischio di credito nella versione classica Azure Machine Learning Studio. Questa esercitazione è la terza di una serie in tre parti. Mostra come distribuire un modello come servizio Web.
keywords: rischio di credito, soluzione di analisi predittiva, valutazione del rischio, distribuire, servizio Web
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 34b0e783b3655aba52cc3d40957b63dd3b0e03b9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492530"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Esercitazione 3: Distribuire il modello di rischio di credito - Azure Machine Learning Studio (versione classica)

In questa esercitazione si esamina il processo di sviluppo di una soluzione di analisi predittiva. Si svilupperà un modello semplice in Machine Learning Studio (versione classica).  Il modello verrà quindi distribuito come servizio Web di Azure Machine Learning.  Questo modello distribuito può creare previsioni usando nuovi dati. Questa esercitazione è la **terza di una serie in tre parti**.

Si supponga di dover prevedere il rischio di credito di un soggetto in base alle informazioni fornite in una richiesta di credito.  

La valutazione del rischio di credito è un problema complesso che verrà tuttavia semplificato con questa esercitazione. Verrà usata come esempio di come è possibile creare una soluzione di analisi predittiva con Microsoft Azure Machine Learning Studio (versione classica). Per questa soluzione si userà la versione classica di Azure Machine Learning Studio e un servizio Web di Machine Learning. 

In questa esercitazione in tre parti si inizia con dati sul rischio di credito disponibili pubblicamente.  Verrà quindi sviluppato e sottoposto a training un modello predittivo.  Il modello verrà infine distribuito come servizio Web.

Nella [prima parte dell'esercitazione](tutorial-part1-credit-risk.md) è stata creata un'area di lavoro di Machine Learning Studio (versione classica), sono stati caricati i dati ed è stato creato un esperimento.

Nella [seconda parte dell'esercitazione](tutorial-part2-credit-risk-train.md) sono stati eseguiti il training e la valutazione dei modelli.

In questa parte dell'esercitazione verranno eseguite queste operazioni:

> [!div class="checklist"]
> * Preparare la distribuzione
> * Distribuire il servizio web
> * Testare il servizio Web
> * Gestire il servizio Web
> * Accedere al servizio Web

## <a name="prerequisites"></a>Prerequisiti

Completare la [seconda parte dell'esercitazione](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Preparare la distribuzione
Per consentire ad altri utenti di usare il modello predittivo sviluppato in questa esercitazione, il modello può essere distribuito come servizio Web in Azure.

Fino a questo punto è stato sperimentato il training del modello, ma il servizio distribuito non dovrà più eseguire il training, perché genererà le stime assegnando un punteggio all'input dell'utente in base al modello. Di conseguenza, è necessario eseguire alcuni preparativi per convertire questo esperimento da esperimento di ***training*** a esperimento ***predittivo***. 

La preparazione per la distribuzione è un processo che prevede tre passaggi:  

1. Rimuovere uno dei modelli
1. Convertire l'*esperimento di training* creato in un *esperimento predittivo*
1. Distribuire l'esperimento predittivo come servizio Web

### <a name="remove-one-of-the-models"></a>Rimuovere uno dei modelli

Prima è necessario ridurre leggermente questo esperimento. Nell'esperimento sono attualmente presenti due modelli diversi, ma si intende usare un solo modello da distribuire come servizio Web.  

Si supponga che il modello di albero con boosting funzioni meglio del modello SVM. La prima cosa da fare, quindi, è rimuovere il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] e i moduli usati per eseguirne il training. È possibile creare prima una copia dell'esperimento facendo clic su **Save As** nella parte inferiore dell'area di disegno dell'esperimento.

È necessario eliminare i moduli seguenti:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine]
* I moduli [Train Model][train-model] e [Score Model][score-model] che sono stati connessi
* [Normalize Data][normalize-data] (entrambi)
* [Evaluate Model][evaluate-model] (poiché è terminata la valutazione dei modelli)

Selezionare ogni modulo e premere CANC oppure fare clic con il pulsante destro del mouse sul modulo e scegliere **Delete** (Elimina). 

![Evidenzia i moduli da eliminare per rimuovere il modello Macchina a vettore di supporto](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Il modello avrà ora un aspetto analogo al seguente:

![Esperimento risultante dall'eliminazione del modello Macchina a vettore di supporto](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Ora il modello è pronto per essere distribuito tramite il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire l'esperimento di training in un esperimento predittivo

Per preparare questo modello per la distribuzione, è necessario convertire l'esperimento di training in un esperimento predittivo. Questa operazione comporta tre passaggi:

1. Salvare il modello di cui è stato eseguito il training e sostituire i moduli di training
1. Ridurre l'esperimento per rimuovere i moduli che sono serviti solo per il training
1. Definire il punto in cui il servizio Web accetterà l'input e in cui genererà l'output

È possibile eseguire questa procedura manualmente, ma i tre passaggi possono essere effettuati facendo clic su **Configura servizio Web** nella parte inferiore del canvas dell'esperimento e selezionando l'opzione **Predictive Web Service** (Servizio Web predittivo).

> [!TIP]
> Per informazioni dettagliate cosa accade quando si converte un esperimento di training in un esperimento predittivo, vedere l'articolo relativo alla [preparazione del modello per la distribuzione in Azure Machine Learning Studio (versione classica)](convert-training-experiment-to-scoring-experiment.md).

Quando si fa clic su **Set Up Web Service**(Configura servizio Web), vengono eseguite diverse operazioni:

* Il modello di cui è stato eseguito il training viene convertito in un singolo modulo **Trained Model** e archiviato nella tavolozza dei moduli a sinistra dell'area di disegno dell'esperimento e sarà disponibile in **Trained Models**.
* Vengono rimossi i moduli usati per il training, in particolare:
  * [Albero delle decisioni incrementato a due classi][two-class-boosted-decision-tree]
  * [Eseguire il training del modello][train-model]
  * [Dividere dati][split]
  * Il secondo modulo [Execute R Script][execute-r-script] usato per i dati di test
* Il modello con training salvato viene aggiunto nuovamente all'esperimento
* Vengono aggiunti i moduli **Web service input** (Input servizio Web) e **Output servizio Web**; queste informazioni identificano dove verranno immessi i dati dell'utente nel modello e i dati che verranno restituiti quando si accede al servizio Web

> [!NOTE]
> Si può notare che l'esperimento viene salvato in due parti sotto le schede che sono state aggiunte all'inizio dell'area di disegno dell'esperimento. L'esperimento di training originale si trova sotto la scheda **Esperimento di training** e l'esperimento predittivo appena creato si trova sotto **Esperimento predittivo**. L'esperimento predittivo è quello che verrà distribuito come servizio Web.

Con questo particolare esperimento è necessario effettuare un'operazione aggiuntiva.
Sono stati aggiungi due moduli [Execute R Script][execute-r-script] per fornire una funzione di ponderazione per i dati. Si tratta semplicemente di un espediente necessario per il training e il test ed è quindi possibile estrarre i moduli nel modello finale.
Machine Learning Studio (versione classica) ha rimosso un modulo [Execute R Script][execute-r-script] durante la rimozione del modulo [Split Data][split]. È possibile rimuovere l'altro modulo e connettere [Metadata Editor][metadata-editor] direttamente a [Score Model][score-model].    

L'esperimento dovrebbe risultare simile al seguente:  

![Valutazione del modello sottoposto a training](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Ci si chiederà perché il set di dati relativo alle carte di credito tedesche UCI sia stato lasciato nell'esperimento predittivo. Il servizio assegnerà un punteggio ai dati dell'utente, non al set di dati originale, quindi perché lasciare il set di dati originale nel modello?
> 
> Il servizio non necessita dei dati della carta di credito originali. Necessita però dello schema per tali dati, incluse informazioni come il numero di colonne presenti e quali colone sono numeriche. Queste informazioni sullo schema sono necessarie per interpretare i dati dell'utente. È necessario lasciare questi componenti connessi in modo che il modulo di punteggio abbia lo schema del set di dati quando il servizio è in esecuzione. I dati non vengono usati, solo lo schema.  
> 
>Una cosa importante da notare è che. se il set di dati originale contiene l'etichetta, lo schema previsto dall'input Web prevederà anch'esso una colonna con etichetta. Un modo di evitare questo problema consiste nel rimuovere l'etichetta e ogni altro dato del set di dati di training, ma non previsto negli input Web, prima di connettere l'input Web e il set di dati di training in un modulo comune. 
> 

Eseguire l'esperimento un'ultima volta, facendo clic su **Run** (Esegui). Se si vuole verificare che il modello funzioni ancora, fare clic sull'output del modulo [Score Model][score-model] e selezionare **Visualizza risultati**. Verranno visualizzati i dati originali, insieme al valore di rischio di credito "Scored Labels" (Etichette punteggio) e al valore di probabilità del punteggio "Scored Probabilities" (Probabilità punteggio). 

## <a name="deploy-the-web-service"></a>Distribuire il servizio web
È possibile distribuire l'esperimento come servizio Web classico o come nuovo servizio Web basato su Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Distribuire l'esperimento come servizio Web classico
Per distribuire un servizio Web classico derivato dall'esperimento, fare clic su **Distribuisci servizio Web** sotto l'area di disegno e selezionare **Distribuisci servizio Web - Nuovo**. Machine Learning Studio (versione classica) distribuisce l'esperimento come servizio Web e apre il dashboard del servizio. Da questa pagina è possibile tornare all'esperimento (**View snapshot** (Visualizza snapshot) o **View latest** (Visualizza più recente)) ed eseguire un semplice test del servizio Web. Vedere **Testare il servizio Web** di seguito. Qui sono anche disponibili informazioni per la creazione di applicazioni in grado di accedere al servizio Web. Altre informazioni sono disponibili nella sezione successiva di questa esercitazione.

![Dashboard del servizio Web](./media/tutorial-part3-credit-risk-deploy/publish6.png)


È possibile configurare il servizio facendo clic sulla scheda **CONFIGURATION** (CONFIGURAZIONE), dove è possibile modificare il nome del servizio (per impostazione predefinita ha il nome dell'esperimento) e aggiungere una descrizione. È anche possibile inserire etichette più descrittive per i dati di input e output.  

![Configurare il servizio Web](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Distribuire l'esperimento come nuovo servizio Web

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md). 

Per distribuire un nuovo servizio Web derivato dall'esperimento:

1. Fare clic su **Deploy Web Service** (Distribuisci servizio Web) sotto l'area di disegno e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Machine Learning Studio (versione classica) visualizza la pagina **Deploy Experiment** (Distribuisci esperimento) dei servizi Web di Azure Machine Learning.

1. Immettere un nome per il servizio Web. 

1. Per **Piano tariffario** è possibile selezionare un piano tariffario esistente o selezionare "Nuovo" e denominare il nuovo piano, quindi selezionare l'opzione del piano mensile. Per impostazione predefinita vengono usati i livelli di piano per l'area predefinita e il servizio Web viene distribuito in questa area.

1. Fare clic su **Distribuisci**.

Dopo alcuni minuti verrà visualizzata la pagina **Avvio rapido** per il servizio Web.

È possibile configurare il servizio facendo clic sulla scheda **Configura**. Qui è possibile modificare il titolo del servizio e assegnare una descrizione. 

Per testare il servizio Web, selezionare la scheda **Test**. Vedere **Testare il servizio Web** di seguito. Per informazioni sulla creazione di applicazioni in grado di accedere al servizio Web, fare clic sulla scheda **Consume** (Utilizzo). Altre informazioni sono disponibili nella sezione successiva di questa esercitazione.

> [!TIP]
> È possibile aggiornare il servizio Web dopo averlo distribuito. Se, ad esempio, si vuole cambiare il modello, è possibile modificare l'esperimento di training, modificare i parametri del modello e fare clic su **Deploy Web Service** (Distribuisci servizio Web), selezionando **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Quando si distribuisce di nuovo l'esperimento, il servizio Web viene sostituito con il modello aggiornato.  
> 
> 

## <a name="test-the-web-service"></a>Testare il servizio Web

Quando viene eseguito l'accesso al servizio Web, i dati dell'utente vengono inseriti mediante il modulo **Web service input** e trasferiti nel modulo [Score Model][score-model], in cui viene assegnato un punteggio. In considerazione del modo in cui l'esperimento predittivo è configurato, il modello presuppone che i dati abbiano lo stesso formato del set di dati del rischio di credito originale.
I risultati vengono quindi restituiti all'utente dal servizio Web tramite il modulo **Output servizio Web**.

> [!TIP]
> In considerazione del modo in cui l'esperimento predittivo è configurato, vengono restituiti tutti i risultati del modulo [Score Model][score-model]. Ciò include tutti i dati di input, il valore del rischio di credito e il valore di probabilità del punteggio. Ma è possibile restituire un elemento diverso se si desidera, ad esempio, si potrebbe restituire solo il valore di rischio di credito. A tale scopo, inserire un modulo [Select Columns][select-columns] tra [Score Model][score-model] e **Output servizio Web** per eliminare le colonne che il servizio Web non deve restituire. 
> 
> 

È possibile testare il servizio Web in **Machine Learning Studio (versione classica)** o nel portale dei **servizi Web di Azure Machine Learning**.
È possibile testare un nuovo servizio Web solo dal portale dei **servizi Web di Azure Machine Learning**.

> [!TIP]
> Quando il test viene eseguito nel portale dei servizi Web di Azure Machine Learning, è possibile fare in modo che il portali crei dati di esempio da usare per testare il servizio di richiesta-risposta. Nella pagina **Configura** selezionare "Sì" per la richiesta di **dati di esempio abilitati?** . Quando si apre la scheda Richiesta-risposta della pagina **Test**, il portale compila i dati di esempio prelevati dal set di dati del rischio di credito originale.

### <a name="test-a-classic-web-service"></a>Testare un servizio Web classico

È possibile testare il servizio Web in Machine Learning Studio (versione classica) o nel portale dei servizi Web di Azure Machine Learning. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testare in Machine Learning Studio (versione classica)

1. Nella pagina **DASHBOARD** del servizio Web fare clic sul pulsante **Test** in **Default Endpoint** (Endpoint predefinito). Viene visualizzata una finestra di dialogo che richiede i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito originale.  

1. Immettere un set di dati e quindi fare clic su **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testare nel portale dei servizi Web di Azure Machine Learning

1. Nella pagina **DASHBOARD** del servizio Web fare clic sul pulsante **Test preview** (Anteprima test) in **Default Endpoint** (Endpoint predefinito). Viene visualizzata la pagina di test nel portale del servizio Web di Azure Machine Learning per l'endpoint di servizio Web e vengono chiesti i dati di input per il servizio. Sono le stesse colonne visualizzate nel set di dati del rischio di credito originale.

2. Fare clic su **Test Request-Response** (Test Richiesta-risposta). 

### <a name="test-a-new-web-service"></a>Testare un nuovo servizio Web

È possibile testare un nuovo servizio Web solo dal portale dei servizi Web di Azure Machine Learning.

1. Nel portale dei [servizi Web di Azure Machine Learning](https://services.azureml.net/quickstart) fare clic su **Test** nella parte superiore della pagina. Viene visualizzata la pagina **Test**, in cui è possibile immettere i dati per il servizio. I campi di input visualizzati corrispondono alle colonne visualizzate nel set di dati del rischio di credito originale. 

1. Immettere un set di dati e quindi fare clic su **Test Request-Response**(Test Richiesta-risposta).

I risultati del test vengono visualizzati sul lato destro della pagina nella colonna di output. 


## <a name="manage-the-web-service"></a>Gestire il servizio Web

Dopo avere distribuito il servizio Web, classico o nuovo, è possibile gestirlo dal [portale dei servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/quickstart).

Per monitorare le prestazioni del servizio Web:

1. Accedere al portale dei [servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/quickstart)
1. Fare clic su **Servizi Web**
1. Fare clic sul servizio Web
1. Fare clic su **Dashboard**

## <a name="access-the-web-service"></a>Accedere al servizio Web

Nel passaggio precedente di questa esercitazione è stato distribuito un servizio Web che usa il modello di previsione del rischio di credito. Ora gli utenti sono in grado di inviare dati al servizio e ricevere risultati. 

Questo è un servizio Web di Azure che può ricevere e restituire dati tramite le API REST in due modi:  

* **Richiesta/risposta** : l'utente invia uno o più set di dati di credito al servizio usando un protocollo HTTP e il servizio risponde con uno o più set di risultati.
* **Esecuzione batch** : l'utente archivia una o più righe di dati di credito in un BLOB di Azure e invia il percorso del BLOB al servizio. Il servizio assegna un punteggio a tutte le righe di dati del BLOB di input, archivia i risultati in un altro BLOB e restituisce l'URL di quel contenitore.  

È il modo più rapido e semplice per accedere a un servizio Web classico tramite il [modello di app Web di richiesta-risposta di Azure Machine Learning ](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) o il [modello di app Web di esecuzione batch di Azure Machine Learning](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

I modelli di app Web consentono di compilare un'app Web personalizzata che riconosce i dati di input del servizio Web e i dati da restituire. È sufficiente concedere l'accesso al proprio servizio Web e ai dati e il modello farà il resto.

Per altre informazioni sull'utilizzo di modelli di app Web, vedere [Utilizzare un servizio Web di Azure Machine Learning con un modello di app Web](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare la distribuzione
> * Distribuire il servizio web
> * Testare il servizio Web
> * Gestire il servizio Web
> * Accedere al servizio Web

È possibile sviluppare anche un'applicazione personalizzata per accedere al servizio Web utilizzando il codice di avvio fornito automaticamente nei linguaggi di programmazione R, C# e Python.

> [!div class="nextstepaction"]
> [Usare un servizio Web di Azure Machine Learning](consume-web-services.md)

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
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
