---
title: Distribuire un servizio Web
titleSuffix: ML Studio (classic) - Azure
description: Come convertire un esperimento di training in un esperimento predittivo, prepararlo per la distribuzione e quindi distribuirlo come servizio Web Azure Machine Learning Studio (classico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: a2c1ba1d4cd2dfdbf2a94005c539e70705486ba4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851095"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Distribuire un servizio Web di Azure Machine Learning Studio (classico)

Azure Machine Learning Studio (classico) consente di compilare e testare una soluzione analitica predittiva. È quindi possibile distribuire la soluzione come servizio Web.

I servizi Web di Machine Learning Studio (classico) forniscono un'interfaccia tra un'applicazione e un modello di valutazione del flusso di lavoro Machine Learning Studio (classico). Un'applicazione esterna può comunicare con un modello di punteggio del flusso di lavoro Machine Learning Studio (classico) in tempo reale. Una chiamata a un servizio Web di Machine Learning Studio (classico) restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un servizio Web, passare una chiave API creata al momento della distribuzione del servizio Web. Un servizio Web di Machine Learning Studio (classico) è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

La versione classica di Azure Machine Learning Studio dispone di due tipi di servizi Web:

* Servizio di richiesta-risposta (RRS): servizio A bassa latenza e scalabilità elevata che assegna un punteggio a un singolo record di dati.
* Servizio esecuzione batch (BES): un servizio asincrono che assegna un punteggio a un batch di record di dati.

L'input per BES è simile all'input di dati usato da RRS. La differenza principale è che BES legge un blocco di record da diverse origini, ad esempio un archivio BLOB di Azure, un archivio tabelle di Azure, un database SQL di Azure, HDInsight (query Hive) e origini HTTP.

In generale, la distribuzione del modello avviene in tre passaggi:

* **[Creare un esperimento di training]** : nella versione classica di studio è possibile eseguire il training e il test di un modello di analisi predittiva usando i dati di training forniti, usando un ampio set di algoritmi di Machine Learning predefiniti.
* **[Eseguire la conversione in un esperimento predittivo]** - Dopo avere eseguito il training del modello con i dati esistenti, preparare e ottimizzare l'esperimento per la stima quando si è pronti a usarlo per l'assegnazione dei punteggi ai nuovi dati.
* **Eseguire la distribuzione** come **[nuovo servizio Web]** o come **[servizio Web classico]** : quando si distribuisce l'esperimento predittivo come servizio Web di Azure, gli utenti possono inviare dati al modello e ricevere le stime del modello.

## <a name="create-a-training-experiment"></a>Creare un esperimento di training

Per eseguire il training di un modello di analisi predittiva, è possibile usare la versione classica di Azure Machine Learning Studio per creare un esperimento di training in cui si includono diversi moduli per caricare i dati di training, preparare i dati in modo necessario, applicare algoritmi di machine learning e valutare i risultati. È possibile eseguire l'iterazione dell'esperimento e provare diversi algoritmi di apprendimento automatico per confrontare e valutare i risultati.

Il processo di creazione e gestione degli esperimenti di training è trattato in modo più approfondito altrove. Per altre informazioni, vedere questi articoli:

* [Creare un esperimento semplice in Azure Machine Learning Studio (classico)](create-experiment.md)
* [Sviluppare una soluzione predittiva con Azure Machine Learning Studio (versione classica)](tutorial-part1-credit-risk.md)
* [Importare i dati di training in Azure Machine Learning Studio (versione classica)](import-data.md)
* [Gestire le iterazioni dell'esperimento in Azure Machine Learning Studio (versione classica)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire l'esperimento di training in un esperimento predittivo

Dopo avere eseguito il training del modello, si è pronti per convertire l'esperimento di training in un esperimento predittivo per assegnare i punteggi ai nuovi dati.

Effettuando la conversione in un esperimento predittivo, si prepara il modello sottoposto a training per la distribuzione come servizio Web di classificazione. Gli utenti del servizio Web possono inviare i dati di input al modello, che restituirà i risultati della previsione. Quando si esegue la conversione in un esperimento predittivo, tenere presente il modo in cui si prevede che il modello verrà usato da altri utenti.

Per convertire l'esperimento di training in un esperimento predittivo, fare clic su **Run** (Esegui) nella parte inferiore dell'area di disegno dell'esperimento, fare clic su **Set Up Web Service** (Configura servizio Web) e quindi scegliere **Predictive Web Service** (Servizio Web Predittivo).

![Eseguire la conversione a un esperimento di assegnazione dei punteggi](./media/publish-a-machine-learning-web-service/figure-1.png)

Per ulteriori informazioni su come eseguire questa conversione, vedere [come preparare il modello per la distribuzione in Azure Machine Learning Studio (classico)](convert-training-experiment-to-scoring-experiment.md).

Nei passaggi seguenti è descritta la distribuzione di un esperimento predittivo come nuovo servizio Web. È anche possibile distribuire l'esperimento come servizio Web classico.

## <a name="deploy-it-as-a-new-web-service"></a>Eseguire la distribuzione come nuovo servizio Web

Ora che l'esperimento predittivo è stato preparato, è possibile distribuirlo come nuovo servizio Web di Azure (basato su Resource Manager). Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per distribuire l'esperimento predittivo, fare clic su **Esegui** nella parte inferiore dell'area di disegno dell'esperimento. Al termine dell'esecuzione dell'esperimento, fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service New** (Distribuisci servizio Web [Nuovo]).  Verrà visualizzata la pagina distribuzione del portale del servizio Web Machine Learning Studio (classico).

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Pagina dell'esperimento di distribuzione del portale servizi Web

Nella pagina Deploy Experiment (Sperimentazione distribuzione) immettere un nome per il servizio Web.
Selezionare un piano tariffario. Se è disponibile un piano tariffario, è possibile selezionarlo; in caso contrario è necessario creare un nuovo piano tariffario per il servizio.

1. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano esistente o l'opzione **Select new plan** (Seleziona nuovo piano).
2. In **Plan Name**(Nome piano) digitare un nome che identifica il piano di fatturazione.
3. Selezionare uno dei **livelli del piano mensile**. Per impostazione predefinita vengono usati i livelli di piano per l'area predefinita e il servizio Web viene distribuito in questa area.

Fare clic su **Distribuisci** e verrà visualizzata la pagina **Avvio rapido** per il servizio Web.

La pagina Avvio rapido del servizio Web offre indicazioni e accesso alle attività più comuni che verranno eseguite dopo la creazione di un servizio Web. Da qui è possibile accedere facilmente alle pagine relative a test e utilizzo.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testare il nuovo servizio Web

Per testare il nuovo servizio Web, fare clic su **Test web service** (Test servizio Web) nelle attività comuni. Nella pagina di test è possibile testare il servizio Web come servizio Richiesta-risposta (RRS) o servizio Esecuzione batch (BES).

La pagina di test del servizio di richiesta-risposta (RRS) visualizza gli input, gli output ed eventuali parametri globali definiti per l'esperimento. Per testare il servizio Web, è possibile immettere manualmente i valori appropriati per gli input o fornire un file formattato con valori delimitati da virgole (CSV) contenente i valori di test.

Per eseguire il test usando il servizio di richiesta-risposta (RRS), nella modalità di visualizzazione elenco immettere i valori appropriati per gli input e fare clic su **Test Request-Response**(Test Richiesta-risposta). I risultati della stima verranno visualizzati nella colonna di output a sinistra.

![Immettere i valori appropriati per testare il servizio Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Per testare il servizio BES, fare clic su **Batch**. Nella pagina di test Batch, fare clic su Sfoglia sotto l'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriati. Se non si ha un file CSV e l'esperimento predittivo è stato creato con la versione classica di Machine Learning Studio, è possibile scaricare il set di dati per l'esperimento predittivo e usarlo.

Per scaricare il set di dati, aprire la versione classica di Machine Learning Studio. Aprire l'esperimento predittivo e fare clic con il pulsante destro del mouse sull'input per l'esperimento. Dal menu di scelta rapida scegliere **dataset** (set di dati) e quindi selezionare **Download**(Scarica).

![Scaricare il set di dati dall'area di disegno di studio (classico)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Fare clic su **Test**. Lo stato del processo di esecuzione del batch verrà visualizzato a destra sotto **Test Batch Jobs** (Test Processi batch).

![Testare il processo di esecuzione batch con il portale dei servizi Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Nella pagina **CONFIGURAZIONE** è possibile modificare la descrizione e il titolo, aggiornare la chiave dell'account di archiviazione e abilitare i dati di esempio per il servizio Web.

![Configurare il servizio Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Accedere al nuovo servizio Web

Dopo aver distribuito il servizio Web dalla versione classica di Machine Learning Studio, è possibile inviare dati al servizio e ricevere risposte a livello di codice.

La pagina **Consume** (Uso) offre tutte le informazioni necessarie per accedere al servizio Web. Ad esempio, viene fornita la chiave API per consentire l'accesso autorizzato al servizio.

Per ulteriori informazioni sull'accesso a un servizio Web Machine Learning Studio (classico), vedere [come utilizzare un servizio Web di Azure Machine Learning Studio (classico)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gestire il nuovo servizio Web

È possibile gestire i nuovi servizi Web usando il portale dei servizi Web Machine Learning Studio (classico). Dalla [pagina principale del portale](https://services.azureml.net/) fare clic su **Web Services** (Servizi Web). Nella pagina dei servizi Web è possibile eliminare o copiare un servizio. Per monitorare un servizio specifico, fare clic sul servizio e quindi su **Dashboard**. Per monitorare i processi batch associati al servizio Web, fare clic su **Batch Request Log**(Log richieste batch).

### <a id="multi-region"></a> Distribuire il nuovo servizio Web in più aree

È possibile distribuire facilmente un nuovo servizio Web in più aree, senza la necessità di più sottoscrizioni o aree di lavoro.

I prezzi sono specifici per ogni area, quindi è necessario definire un piano di fatturazione per ogni area in cui verrà distribuito il servizio Web.

#### <a name="create-a-plan-in-another-region"></a>Creare un piano in un'altra area

1. Accedere a [Servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Fare clic sull'opzione del menu **Plans** (Piani).
3. Nella pagina della panoramica Plans (Piani), fare clic su **New**(Nuovo).
4. Nell'elenco a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione in cui risiederà il nuovo piano.
5. Nell'elenco a discesa **Regione** (Area) selezionare un'area per il nuovo piano. Le opzioni del piano per l'area selezionata verranno visualizzate nella sezione **Plan Options** (Opzioni del piano) della pagina.
6. Nell'elenco a discesa **Resource Group** (Gruppo di risorse) selezionare un gruppo di risorse per il piano. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. In **Plan Name** (Nome piano) digitare il nome del piano.
8. In **Plan Options**(Opzioni piano) selezionare il livello di fatturazione per il nuovo piano.
9. Fare clic su **Create**(Crea).

#### <a name="deploy-the-web-service-to-another-region"></a>Eseguire la distribuzione del servizio Web in un'altra area

1. Nella pagina de servizi Web di Microsoft Azure Machine Learning fare clic sull'opzione di menu **Servizi Web**.
2. Selezionare il servizio Web da distribuire in una nuova area.
3. Fare clic su **Copy**.
4. In **Web Service Name**(Nome servizio Web) digitare un nuovo nome per il servizio Web.
5. In **Web service description**(Descrizione servizio Web) immettere una descrizione per il servizio Web.
6. Nell'elenco a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione in cui risiederà il nuovo servizio Web.
7. Nell'elenco a discesa **Resource Group** (Gruppo di risorse) selezionare un gruppo di risorse per il servizio Web. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Nell'elenco a discesa **Region** (Area) selezionare l'area in cui si desidera distribuire il servizio Web.
9. Nell'elenco a discesa **Storage account** (Account di archiviazione) selezionare un account di archiviazione in cui archiviare il servizio Web.
10. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano nell'area scelta nel passaggio 8.
11. Fare clic su **Copy**.

## <a name="deploy-it-as-a-classic-web-service"></a>Eseguire la distribuzione come servizio Web classico

Ora che l'esperimento predittivo è stato sufficientemente preparato, è possibile distribuirlo come servizio Web di Azure classico. Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per distribuire l'esperimento predittivo, fare clic su **Run** (Esegui) nella parte inferiore dell'area di disegno dell'esperimento e quindi fare clic su **Deploy Web Service** (Distribuisci servizio Web). Il servizio Web viene configurato e viene visualizzato il dashboard del servizio Web.

![Distribuire il servizio Web da studio (classico)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testare il servizio Web classico

È possibile testare il servizio Web nel portale dei servizi Web Machine Learning Studio (classico) o Machine Learning Studio (classico).

Per testare il servizio Web Richiesta-risposta, fare clic sul collegamento **Test** nel dashboard del servizio Web. Viene visualizzata una finestra di dialogo che richiede i dati di input per il servizio. Si tratta delle colonne necessarie per l'esperimento di classificazione. Immettere un set di dati e quindi fare clic su **OK**. Il risultato generato dal servizio Web viene visualizzato in fondo al dashboard.

È possibile fare clic sul collegamento anteprima **test** per testare il servizio nella versione classica di Azure Machine Learning Studio portale dei servizi Web, come illustrato in precedenza nella sezione relativa al nuovo servizio Web.

Per testare il servizio Esecuzione batch, fare clic sul collegamento di anteprima **Test**. Nella pagina di test Batch, fare clic su Sfoglia sotto l'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriati. Se non si ha un file CSV e l'esperimento predittivo è stato creato con la versione classica di Machine Learning Studio, è possibile scaricare il set di dati per l'esperimento predittivo e usarlo.

![Testare il servizio Web](./media/publish-a-machine-learning-web-service/figure-3.png)

Nella pagina **CONFIGURATION** (CONFIGURAZIONE) è possibile modificare il nome visualizzato per il servizio e assegnare una descrizione. Il nome e la descrizione vengono visualizzati nel [portale di Azure](https://portal.azure.com/), in cui è possibile gestire i propri servizi Web.

È anche possibile fornire una descrizione per i dati di input e output, nonché parametri del servizio Web immettendo una stringa per ogni colonna in **INPUT SCHEMA** (SCHEMA DI INPUT), **OUTPUT SCHEMA** (SCHEMA DI OUTPUT) e **Web SERVICE PARAMETER** (PARAMETRO SERVIZIO WEB). Queste descrizioni vengono usate nella documentazione del codice di esempio fornita per il servizio Web.

È possibile abilitare la registrazione per diagnosticare eventuali errori che si verificano quando si accede al servizio Web. Per altre informazioni, vedere [abilitare la registrazione per i servizi Web di Machine Learning Studio (classico)](web-services-logging.md).

![Abilitare la registrazione nel portale dei servizi Web](./media/publish-a-machine-learning-web-service/figure-4.png)

È anche possibile configurare gli endpoint per il servizio Web nel portale dei servizi Web di Azure Machine Learning, con una procedura simile a quella illustrata in precedenza nella sezione relativa al nuovo servizio Web. Le opzioni sono diverse: è possibile aggiungere o modificare la descrizione del servizio, abilitare la registrazione e abilitare i dati di esempio per il test.

### <a name="access-your-classic-web-service"></a>Accedere al servizio Web classico

Dopo aver distribuito il servizio Web dalla versione classica di Machine Learning Studio, è possibile inviare dati al servizio e ricevere risposte a livello di codice.

Il dashboard offre tutte le informazioni necessarie per accedere al servizio Web. Ad esempio, la chiave API viene offerta per consentire l'accesso autorizzato al servizio, mentre le pagine della guida API hanno lo scopo di aiutare nella scrittura di codice.

Per ulteriori informazioni sull'accesso a un servizio Web Machine Learning Studio (classico), vedere [come utilizzare un servizio Web di Azure Machine Learning Studio (classico)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Gestire il servizio Web classico

Per monitorare un servizio Web, sono disponibili diverse operazioni. È possibile aggiornare il servizio ed eliminarlo. È anche possibile aggiungere altri endpoint a un servizio Web classico oltre all'endpoint predefinito creato al momento della distribuzione.

Per altre informazioni, vedere [gestire un'area di lavoro di Azure Machine Learning Studio (classica)](manage-workspace.md) e [gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning Studio (classico)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aggiornare il servizio Web
È possibile apportare modifiche al servizio Web, ad esempio aggiornare il modello con ulteriori dati di training e ridistribuirlo, sovrascrivendo il servizio Web originale.

Per aggiornare il servizio Web, aprire l'esperimento predittivo originale usato per distribuire il servizio Web e creare una copia modificabile facendo clic su **SALVA CON NOME**. Eseguire le modifiche e fare clic su **Distribuisci Servizio Web**.

Poiché questo esperimento è stato distribuito prima, viene chiesto se si desidera sovrascrivere (servizio Web classico) o aggiornare il servizio esistente (nuovo servizio Web). Facendo clic su **SÌ** o su **Aggiorna**, il servizio Web esistente viene arrestato e al suo posto viene distribuito il nuovo esperimento predittivo.

> [!NOTE]
> Se sono state apportate modifiche di configurazione al servizio Web originale, ad esempio immettendo un nuovo nome o descrizione, è necessario immettere nuovamente questi valori.

Un'opzione per l'aggiornamento del servizio Web consiste nel ripetere il training del modello a livello di codice. Per altre informazioni, vedere ripetere il [training dei modelli di Machine Learning Studio (classico) a livello di codice](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni più tecniche sul funzionamento della distribuzione, vedere [come un modello di Machine Learning Studio (classico) progredisce da un esperimento a un servizio Web operativo](model-progression-experiment-to-web-service.md).

* Per informazioni dettagliate su come preparare il modello per la distribuzione, vedere [come preparare il modello per la distribuzione in Azure Machine Learning Studio (classico)](convert-training-experiment-to-scoring-experiment.md).

* È possibile utilizzare l'API REST e accedere al servizio Web in diversi modi. Vedere [come utilizzare un servizio Web di Azure Machine Learning Studio (classico)](consume-web-services.md).

<!-- internal links -->
[Creare un esperimento di training]: #create-a-training-experiment
[Eseguire la conversione in un esperimento predittivo]: #convert-the-training-experiment-to-a-predictive-experiment
[Nuovo servizio Web]: #deploy-it-as-a-new-web-service
[Servizio Web classico]: #deploy-it-as-a-classic-web-service
[Nuovo]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
