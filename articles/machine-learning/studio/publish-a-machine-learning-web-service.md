---
title: Distribuire un servizio Web di Machine Learning | Documentazione Microsoft
description: Come convertire un esperimento di training in un esperimento predittivo, prepararlo per la distribuzione e distribuirlo come servizio Web di Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: garye
ms.openlocfilehash: bdf0bd54130521a7178af3a28731f4c0e21e3e0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Distribuire un servizio Web di Azure Machine Learning
Azure Machine Learning consente di compilare, testare e distribuire soluzioni di analisi predittiva.

In generale, questo avviene in tre passaggi:

* **[Creare un esperimento di training]** - Azure Machine Learning Studio è un ambiente di sviluppo visivo di collaborazione che consente di eseguire il training e il test di un modello di analisi predittiva usando i dati di training forniti.
* **[Eseguire la conversione in un esperimento predittivo]** - Dopo avere eseguito il training del modello con i dati esistenti, preparare e ottimizzare l'esperimento per la stima quando si è pronti a usarlo per l'assegnazione dei punteggi ai nuovi dati.
* **[Distribuire come servizio Web]** - È possibile distribuire l'esperimento predittivo come servizio Web di Azure [nuovo] o [classico]. Gli utenti possono inviare dati al modello e ricevere le relative stime.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Creare un esperimento di training
Per il training di un modello di analisi predittiva, usare Azure Machine Learning Studio per creare un esperimento di training, in cui vengono inclusi diversi moduli per caricare i dati di training, prepararli, applicare gli algoritmi di apprendimento e valutare i risultati. È possibile eseguire l'iterazione dell'esperimento e provare diversi algoritmi di apprendimento automatico per confrontare e valutare i risultati.

Il processo di creazione e gestione degli esperimenti di training è trattato in modo più approfondito altrove. Per altre informazioni, vedere questi articoli:

* [Creare un semplice esperimento in Azure Machine Learning Studio](create-experiment.md)
* [Sviluppare una soluzione predittiva con Azure Machine Learning](walkthrough-develop-predictive-solution.md)
* [Importare dati di training in Azure Machine Learning Studio](import-data.md)
* [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire l'esperimento di training in un esperimento predittivo
Dopo avere eseguito il training del modello, si è pronti per convertire l'esperimento di training in un esperimento predittivo per assegnare i punteggi ai nuovi dati.

Effettuando la conversione in un esperimento predittivo, si prepara il modello sottoposto a training per la distribuzione come servizio Web di classificazione. Gli utenti del servizio Web possono inviare i dati di input al modello, che restituirà i risultati della previsione. Quando si esegue la conversione in un esperimento predittivo, tenere presente il modo in cui si prevede che il modello verrà usato da altri utenti.

Per convertire l'esperimento di training in un esperimento predittivo, fare clic su **Run** (Esegui) nella parte inferiore dell'area di disegno dell'esperimento, fare clic su **Set Up Web Service** (Configura servizio Web) e quindi scegliere **Predictive Web Service** (Servizio Web Predittivo).

![Eseguire la conversione a un esperimento di assegnazione dei punteggi](./media/publish-a-machine-learning-web-service/figure-1.png)

Per altre informazioni su come eseguire questa conversione, vedere l'articolo relativo alla [preparazione del modello per la distribuzione in Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Nei passaggi seguenti è descritta la distribuzione di un esperimento predittivo come nuovo servizio Web. È anche possibile distribuire l'esperimento come servizio Web classico.

## <a name="deploy-it-as-a-web-service"></a>Distribuire l'esperimento predittivo come servizio Web

È possibile distribuire l'esperimento predittivo come un servizio Web nuovo o classico.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Distribuire l'esperimento predittivo come nuovo servizio Web
Ora che l'esperimento predittivo è stato preparato, è possibile distribuirlo come nuovo servizio Web di Azure. Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per distribuire l'esperimento predittivo, fare clic su **Esegui** nella parte inferiore dell'area di disegno dell'esperimento. Al termine dell'esecuzione dell'esperimento, fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]).  Verrà aperta la pagina di distribuzione del portale dei servizi Web di Machine Learning.

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Pagina di distribuzione dell'esperimento del portale dei servizi Web di Machine Learning
Nella pagina Deploy Experiment (Sperimentazione distribuzione) immettere un nome per il servizio Web.
Selezionare un piano tariffario. Se è disponibile un piano tariffario, è possibile selezionarlo; in caso contrario è necessario creare un nuovo piano tariffario per il servizio.

1. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano esistente o l'opzione **Select new plan** (Seleziona nuovo piano).
2. In **Plan Name**(Nome piano) digitare un nome che identifica il piano di fatturazione.
3. Selezionare uno dei **livelli del piano mensile**. Per impostazione predefinita vengono usati i livelli di piano per l'area predefinita e il servizio Web viene distribuito in questa area.

Fare clic su **Distribuisci** e verrà visualizzata la pagina **Avvio rapido** per il servizio Web.

La pagina Avvio rapido del servizio Web offre indicazioni e accesso alle attività più comuni che verranno eseguite dopo la creazione di un servizio Web. Da qui è possibile accedere facilmente alle pagine relative a test e utilizzo.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Testare il nuovo servizio Web
Per testare il nuovo servizio Web, fare clic su **Test web service** (Test servizio Web) nelle attività comuni. Nella pagina di test è possibile testare il servizio Web come servizio Richiesta-risposta (RRS) o servizio Esecuzione batch (BES).

La pagina di test del servizio di richiesta-risposta (RRS) visualizza gli input, gli output ed eventuali parametri globali definiti per l'esperimento. Per testare il servizio Web, è possibile immettere manualmente i valori appropriati per gli input o fornire un file formattato con valori delimitati da virgole (CSV) contenente i valori di test.

Per eseguire il test usando il servizio di richiesta-risposta (RRS), nella modalità di visualizzazione elenco immettere i valori appropriati per gli input e fare clic su **Test Request-Response**(Test Richiesta-risposta). I risultati della stima verranno visualizzati nella colonna di output a sinistra.

![Distribuire il servizio web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Per testare il servizio BES, fare clic su **Batch**. Nella pagina di test Batch, fare clic su Sfoglia sotto l'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriati. Se non si ha un file CSV e l'esperimento predittivo è stato creato con Machine Learning Studio, è possibile scaricare e usare il set di dati per l'esperimento predittivo.

Per scaricare il set di dati, aprire Machine Learning Studio. Aprire l'esperimento predittivo e fare clic con il pulsante destro del mouse sull'input per l'esperimento. Dal menu di scelta rapida scegliere **dataset** (set di dati) e quindi selezionare **Download**(Scarica).

![Distribuire il servizio web](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Fare clic su **Test**. Lo stato del processo di esecuzione del batch verrà visualizzato a destra sotto **Test Batch Jobs** (Test Processi batch).

![Distribuire il servizio web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Nella pagina **CONFIGURAZIONE** è possibile modificare la descrizione e il titolo, aggiornare la chiave dell'account di archiviazione e abilitare i dati di esempio per il servizio Web.

![Configurare il servizio Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Dopo aver distribuito il servizio Web, è possibile:

* **Accedervi** tramite l'API del servizio Web.
* **Gestirlo** tramite il portale dei servizi Web di Azure Machine Learning o il portale di Azure classico.
* **Aggiornarlo** se il modello viene modificato.

#### <a name="access-your-new-web-service"></a>Accedere al nuovo servizio Web
Dopo aver distribuito il servizio Web da Machine Learning Studio, è possibile inviare dati al servizio e ricevere risposte in modo programmatico.

La pagina **Consume** (Uso) offre tutte le informazioni necessarie per accedere al servizio Web. Ad esempio, viene fornita la chiave API per consentire l'accesso autorizzato al servizio.

Per altre informazioni sull'accesso a un servizio Web di Machine Learning, vedere [Come usare un servizio Web di Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Gestire il nuovo servizio Web
È possibile gestire il portale dei servizi Web di Machine Learning per i nuovi servizi Web. Dalla [pagina principale del portale](https://services.azureml-test.net/) fare clic su **Web Services** (Servizi Web). Nella pagina dei servizi Web è possibile eliminare o copiare un servizio. Per monitorare un servizio specifico, fare clic sul servizio e quindi su **Dashboard**. Per monitorare i processi batch associati al servizio Web, fare clic su **Batch Request Log**(Log richieste batch).

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Distribuire l'esperimento predittivo come servizio Web classico

Ora che l'esperimento predittivo è stato sufficientemente preparato, è possibile distribuirlo come servizio Web di Azure classico. Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per distribuire l'esperimento predittivo, fare clic su **Run** (Esegui) nella parte inferiore dell'area di disegno dell'esperimento e quindi fare clic su **Deploy Web Service** (Distribuisci servizio Web). Il servizio Web viene configurato e viene visualizzato il dashboard del servizio Web.

![Distribuire il servizio web](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Testare il servizio Web classico

È possibile testare il servizio Web nel portale dei servizi Web di Azure Machine Learning o in Machine Learning Studio.

Per testare il servizio Web Richiesta-risposta, fare clic sul collegamento **Test** nel dashboard del servizio Web. Viene visualizzata una finestra di dialogo che richiede i dati di input per il servizio. Si tratta delle colonne necessarie per l'esperimento di classificazione. Immettere un set di dati e quindi fare clic su **OK**. Il risultato generato dal servizio Web viene visualizzato in fondo al dashboard.

È possibile fare clic sul collegamento di anteprima **Test** per testare il servizio nel portale dei servizi Web di Azure Machine Learning, come illustrato in precedenza nella sezione relativa al nuovo servizio Web.

Per testare il servizio Esecuzione batch, fare clic sul collegamento di anteprima **Test**. Nella pagina di test Batch, fare clic su Sfoglia sotto l'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriati. Se non si ha un file CSV e l'esperimento predittivo è stato creato con Machine Learning Studio, è possibile scaricare e usare il set di dati per l'esperimento predittivo.

![Testare il servizio Web](./media/publish-a-machine-learning-web-service/figure-3.png)

Nella pagina **CONFIGURATION** (CONFIGURAZIONE) è possibile modificare il nome visualizzato per il servizio e assegnare una descrizione. Il nome e la descrizione vengono visualizzati nel [portale di Azure classico](http://manage.windowsazure.com/) , in cui è possibile gestire i propri servizi Web.

È anche possibile fornire una descrizione per i dati di input e output, nonché parametri del servizio Web immettendo una stringa per ogni colonna in **INPUT SCHEMA** (SCHEMA DI INPUT), **OUTPUT SCHEMA** (SCHEMA DI OUTPUT) e **Web SERVICE PARAMETER** (PARAMETRO SERVIZIO WEB). Queste descrizioni vengono usate nella documentazione del codice di esempio fornita per il servizio Web.

È possibile abilitare la registrazione per diagnosticare eventuali errori che si verificano quando si accede al servizio Web. Per altre informazioni, vedere [Abilitare la registrazione per i servizi Web di Machine Learning](web-services-logging.md).

![Configurare il servizio Web](./media/publish-a-machine-learning-web-service/figure-4.png)

È anche possibile configurare gli endpoint per il servizio Web nel portale dei servizi Web di Azure Machine Learning, con una procedura simile a quella illustrata in precedenza nella sezione relativa al nuovo servizio Web. Le opzioni sono diverse: è possibile aggiungere o modificare la descrizione del servizio, abilitare la registrazione e abilitare i dati di esempio per il test.

#### <a name="access-your-classic-web-service"></a>Accedere al servizio Web classico
Dopo aver distribuito il servizio Web da Machine Learning Studio, è possibile inviare dati al servizio e ricevere risposte in modo programmatico.

Il dashboard offre tutte le informazioni necessarie per accedere al servizio Web. Ad esempio, la chiave API viene offerta per consentire l'accesso autorizzato al servizio, mentre le pagine della guida API hanno lo scopo di aiutare nella scrittura di codice.

Per altre informazioni sull'accesso a un servizio Web di Machine Learning, vedere [Come usare un servizio Web di Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Gestire il servizio Web classico
Per monitorare un servizio Web, sono disponibili diverse operazioni. È possibile aggiornare il servizio ed eliminarlo. È anche possibile aggiungere altri endpoint a un servizio Web classico oltre all'endpoint predefinito creato al momento della distribuzione.

Per altre informazioni, vedere [Gestire un'area di lavoro di Azure Machine Learning](manage-workspace.md) e [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Aggiornare il servizio Web
È possibile apportare modifiche al servizio Web, ad esempio aggiornare il modello con ulteriori dati di training e ridistribuirlo, sovrascrivendo il servizio Web originale.

Per aggiornare il servizio Web, aprire l'esperimento predittivo originale usato per distribuire il servizio Web e creare una copia modificabile facendo clic su **SALVA CON NOME**. Eseguire le modifiche e fare clic su **Distribuisci Servizio Web**.

Poiché questo esperimento è stato distribuito prima, viene chiesto se si desidera sovrascrivere (servizio Web classico) o aggiornare il servizio esistente (nuovo servizio Web). Facendo clic su **SÌ** o su **Aggiorna**, il servizio Web esistente viene arrestato e al suo posto viene distribuito il nuovo esperimento predittivo.

> [!NOTE]
> Se sono state apportate modifiche di configurazione al servizio Web originale, ad esempio immettendo un nuovo nome o descrizione, è necessario immettere nuovamente questi valori.
> 
> 

Un'opzione per l'aggiornamento del servizio Web consiste nel ripetere il training del modello a livello di codice. Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](retrain-models-programmatically.md).

<!-- internal links -->
[Creare un esperimento di training]: #create-a-training-experiment
[Eseguire la conversione in un esperimento predittivo]: #convert-the-training-experiment-to-a-predictive-experiment
[Distribuire come servizio Web]: #deploy-it-as-a-web-service
[nuovo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[classico]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
