---
title: Guida introduttiva all'installazione dei servizi di Azure Machine Learning | Microsoft Docs
description: Questa guida introduttiva illustra come creare le risorse di Azure Machine Learning e come installare Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 10/13/2017
ms.openlocfilehash: 7c74a9ebaae0b027277fe282b958a653ab498fc5
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Creare account della versione di anteprima di Azure Machine Learning e installare Azure Machine Learning Workbench
Azure Machine Learning è una soluzione integrata per data science end-to-end e analisi avanzate. Consente ai data scientist professionisti di preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa guida introduttiva illustra come creare account di Sperimentazione e Gestione modelli nella versione di anteprima di Azure Machine Learning. Descrive anche come installare l'applicazione desktop di Azure Machine Learning Workbench e gli strumenti dell'interfaccia della riga di comando. Viene anche fornita una breve presentazione delle funzionalità della versione di anteprima di Azure Machine Learning usando il [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/iris_flower_data_set) per creare un modello che possa prevedere il tipo di iris in base ad alcune delle relative caratteristiche fisiche.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Attualmente è possibile installare l'app desktop Azure Machine Learning Workbench solo nei sistemi operativi seguenti: 
- Windows 10
- Windows Server 2016
- macOS Sierra
- macOS High Sierra

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-azure-machine-learning-accounts"></a>Creare account di Azure Machine Learning
Usare il portale di Azure per effettuare il provisioning degli account di Azure Machine Learning: 
1. Selezionare il pulsante **Nuovo** (+) nell'angolo superiore sinistro del portale.

2. Immettere **Machine Learning** nella barra di ricerca. Selezionare il risultato di ricerca denominato **Machine Learning Experimentation (anteprima)**.  Fare clic sull'icona a forma di stella per aggiungere la selezione agli elementi preferiti nel portale di Azure.

   ![Ricerca di Azure Machine Learning](media/quickstart-installation/portal-more-services.png)

3. Selezionare **+ Aggiungi** per configurare un nuovo account di Sperimentazione di Machine Learning. Verrà aperto il modulo dettagliato.

   ![Account di Sperimentazione di Machine Learning](media/quickstart-installation/portal-create-experimentation.png)

4. Compilare il modulo di Sperimentazione di Machine Learning con le informazioni seguenti:

   Impostazione|Valore consigliato|Descrizione
   ---|---|---
   Nome account di Experimentation | _Nome univoco_ |Scegliere un nome univoco per identificare l'account. È possibile usare il proprio nome o il nome di un reparto o un progetto che identifichi l'esperimento. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-). 
   Sottoscrizione | _Sottoscrizione in uso_ |Scegliere la sottoscrizione di Azure da usare per l'esperimento. Se si hanno più sottoscrizioni, scegliere quella appropriata per la fatturazione della risorsa.
   Gruppo di risorse | _Il gruppo di risorse_ | È possibile creare un nuovo nome di gruppo di risorse o usarne uno esistente nella sottoscrizione.
   Percorso | _Area più vicina ai propri utenti_ | Scegliere la località più vicina agli utenti e alle risorse di dati.
   Number of seats (Numero di postazioni) | 2 | Immettere il numero di postazioni. Questa impostazione influisce sui [prezzi](https://azure.microsoft.com/pricing/details/machine-learning/). Le prime due postazioni sono gratuite. Usare due postazioni ai fini di questa guida rapida. È possibile aggiornare il numero di postazioni in seguito nel portale di Azure.
   Account di archiviazione | _Nome univoco_ | Selezionare **Crea nuovo** e specificare un nome per creare un account di archiviazione di Azure. In alternativa, selezionare **Usa esistente** e selezionare un account di archiviazione esistente nell'elenco a discesa. L'account di archiviazione è obbligatorio e viene usato per contenere gli elementi del progetto e i dati della cronologia di esecuzione. 
   Workspace for Experimentation account (Area di lavoro per account di Sperimentazione) | _Nome univoco_ | Specificare un nome per la nuova area di lavoro. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-).
   Assign owner for the workspace (Proprietario area di lavoro) | _L'account_ | Selezionare il proprio account come proprietario dell'area di lavoro.
   Creare un account di Gestione modelli | *check* | Nell'ambito dell'esperienza di creazione dell'account di Sperimentazione, è anche possibile creare l'account di Gestione modelli di Machine Learning. Questa risorsa viene usata quando si è pronti per distribuire e gestire i modelli come servizi Web in tempo reale. È consigliabile creare l'account di Gestione modelli al momento della creazione dell'account di Sperimentazione.
   Nome account | _Nome univoco_ | Scegliere un nome univoco per identificare l'account di Gestione modelli. È possibile usare il proprio nome o il nome di un reparto o un progetto che identifichi l'esperimento. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-). 
   Piano tariffario di Gestione modelli | **DEVTEST** | Selezionare **Nessun piano tariffario selezionato** per specificare il piano tariffario per il nuovo account di Gestione modelli. Per risparmiare sui costi selezionare il piano tariffario **DEVTEST** se è disponibile per la sottoscrizione (disponibilità limitata). In caso contrario, per risparmiare sui costi selezionare il piano tariffario S1. Fare clic su **Seleziona** per salvare la selezione del piano tariffario. 
   Aggiungi al dashboard | _check_ | Selezionare l'opzione **Aggiungi al dashboard** per tenere facilmente traccia dell'account di Sperimentazione di Machine Learning nella pagina iniziale del dashboard del portale di Azure.

5. Selezionare **Crea** per avviare il processo di creazione.

6. Sulla barra degli strumenti del portale di Azure fare clic su **Notifiche** (icona a forma di campana) per monitorare il processo di distribuzione. 

   La notifica indica **Distribuzione in corso**. Al termine, lo stato cambia in **La distribuzione è riuscita**. Si aprirà la pagina dell'account di Sperimentazione di Machine Learning.
   
   ![Notifiche del portale di Azure](media/quickstart-installation/portal-notification.png)

A seconda del sistema operativo usato nel computer locale, seguire una delle due sezioni successive per installare Azure Machine Learning Workbench. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Installare Azure Machine Learning Workbench in Windows
Installare Azure Machine Learning Workbench in computer che eseguono Windows 10, Windows Server 2016 o versioni successive.

1. Scaricare la versione più recente del programma di installazione di Azure Machine Learning Workbench, [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).

2. In Esplora file fare doppio clic sul programma di installazione **AmlWorkbenchSetup.msi** scaricato.

   >[!IMPORTANT]
   >Scaricare l'intero programma di installazione sul disco e avviarlo. Non eseguirlo direttamente dal widget di download del browser.

3. Completare l'installazione seguendo le istruzioni visualizzate.

   Il programma di installazione scarica tutti i componenti dipendenti necessari, ad esempio Python, Miniconda e altre librerie correlate. L'installazione di tutti i componenti potrebbe richiedere circa mezz'ora. 

4. A questo punto, Azure Machine Learning Workbench sarà installato nella directory seguente:
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Installare Azure Machine Learning Workbench in macOS
Installare Azure Machine Learning Workbench in computer che eseguono macOS Sierra o versione successiva.

1. Scaricare la versione più recente del programma di installazione di Azure Machine Learning Workbench, [AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg).

   >[!IMPORTANT]
   >Scaricare l'intero programma di installazione sul disco e avviarlo. Non eseguirlo direttamente dal widget di download del browser.

2. In Finder fare doppio clic sul programma di installazione **AmlWorkbench.dmg** scaricato.

3. Completare l'installazione seguendo le istruzioni visualizzate.

   Il programma di installazione scarica tutti i componenti dipendenti necessari, ad esempio Python, Miniconda e altre librerie correlate. L'installazione di tutti i componenti potrebbe richiedere circa mezz'ora. 

4. A questo punto, Azure Machine Learning Workbench sarà installato nella directory seguente: 

   `/Applications/AmlWorkbench.app`

## <a name="run-azure-machine-learning-workbench-to-sign-in-for-the-first-time"></a>Eseguire Azure Machine Learning Workbench per accedere la prima volta
1. Al termine del processo di installazione, fare clic sul pulsante **Launch Workbench** (Avvia Workbench) nell'ultima schermata del programma di installazione. Se il programma di installazione è stato chiuso, usare il collegamento ad Azure Machine Learning Workbench sul desktop e la voce **Azure Machine Learning Workbench** del menu **Start** per avviare l'app.

2. Accedere a Workbench con lo stesso account usato in precedenza per effettuare il provisioning delle risorse di Azure. 

3. Al termine della procedura di accesso, Workbench tenterà di trovare gli account di Sperimentazione di Machine Learning creati in precedenza. cercando tutte le sottoscrizioni di Azure a cui le credenziali dell'utente hanno accesso. Quando viene trovato almeno un account di Sperimentazione, Workbench si apre con quell'account. Elenca quindi le aree di lavoro e i progetti presenti nell'account. 

   >[!TIP]
   > Se si ha accesso a più di un account di Sperimentazione, è possibile passare a un account diverso selezionando l'icona dell'avatar nell'angolo inferiore sinistro dell'app Workbench.

Per informazioni sulla creazione di un ambiente per la distribuzione di servizi Web, vedere [Configurazione dell'ambiente di distribuzione](deployment-setup-configuration.md).

## <a name="create-a-new-project"></a>Creare un nuovo progetto
1. Avviare l'app Azure Machine Learning Workbench ed eseguire l'accesso. 

2. Selezionare **File** > **Nuovo progetto** (oppure selezionare il segno **+** nel riquadro **PROGETTI**). 

3. Compilare le caselle **Nome progetto** e **Directory del progetto**. Il campo **Descrizione del progetto** è facoltativo ma utile. Per il momento, lasciare vuota la casella **URL repository Git Visualstudio.com**. Scegliere un'area di lavoro e selezionare **Classifying Iris** (Classificazione Iris) come modello di progetto.

   >[!TIP]
   >Facoltativamente, è possibile compilare la casella di testo relativa al repository Git con l'URL di un repository Git ospitato in un progetto [Visual Studio Team Services](https://www.visualstudio.com). Questo repository Git deve già esistere ed essere vuoto, senza alcun ramo principale. È anche necessario avere l'accesso in scrittura al repository. L'aggiunta di un repository Git consente di abilitare in seguito scenari di roaming e condivisione. [Altre informazioni](using-git-ml-project.md).

4. Fare clic sul pulsante **Crea** per creare il progetto. Il nuovo progetto viene creato e viene aperto automaticamente. A questo punto, è possibile esplorare la home page, le origini dati, i blocchi appunti e i file di codice sorgente del progetto. 

    >[!TIP]
    >È anche possibile aprire il progetto in Visual Studio Code o altri editor semplicemente configurando un collegamento IDE (Integrated Development Environment, ambiente di sviluppo integrato) e quindi aprendo la directory del progetto al suo interno. [Altre informazioni](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Eseguire uno script Python
Si eseguirà ora uno script nel computer locale. 

1. Ogni progetto si apre nella propria pagina del **dashboard del progetto**. Selezionare **locale** come destinazione dell'esecuzione sulla barra dei comandi nella parte superiore dell'applicazione e **iris_sklearn.py** come script da eseguire. L'esempio include altri file che è possibile esaminare in un secondo momento. 

   ![Barra dei comandi](media/quickstart-installation/run_control.png)

2. Nella casella di testo **Argomenti** immettere **0.01**. Questo numero viene usato nel codice per impostare il tasso di regolarizzazione. È un valore usato per configurare il training del modello di regressione lineare. 

3. Fare clic sul pulsante **Esegui** per avviare l'esecuzione di **iris_sklearn.py** nel computer. 

   Questo codice usa l'algoritmo di [regressione logistica](https://en.wikipedia.org/wiki/logistic_regression) della nota libreria [scikit-learn](http://scikit-learn.org/stable/index.html) di Python per compilare il modello.

4. Il pannello **Processi** compare a destra, se non è già visibile, con il processo **iris_sklearn** aggiunto. Lo stato passa da **Submitting** (Invio in corso) a **Running** (In esecuzione) quando inizia l'esecuzione del processo, quindi passa a **Completed** (Completato) dopo pochi secondi. 

   A questo punto È stato eseguito correttamente uno script Python in Azure Machine Learning Workbench.

6. Ripetere i passaggi da 2 a 4 alcune volte. Usare ogni volta valori di argomento diversi compresi tra **10** e **0.001**.

## <a name="view-run-history"></a>Visualizzare la cronologia di esecuzione
1. Passare alla vista **Esecuzioni** e selezionare **iris_sklearn.py** nell'elenco di esecuzione. Si aprirà il dashboard della cronologia di esecuzione per **iris_sklearn.py**. Viene visualizzata ogni esecuzione avvenuta in **iris_sklearn.py**. 

   ![Dashboard della cronologia di esecuzione](media/quickstart-installation/run_view.png)

2. Il dashboard della cronologia di esecuzione visualizza anche le metriche principali, un set di grafici predefiniti e un elenco delle metriche per ogni esecuzione. È possibile personalizzare questa visualizzazione ordinando, filtrando e modificando le configurazioni. Selezionare l'icona di configurazione o quella di filtro.

   ![Metriche e grafici](media/quickstart-installation/run_dashboard.png)

3. Selezionare un'esecuzione completata per aprire una visualizzazione dettagliata per l'esecuzione specifica. I dettagli includono metriche aggiuntive, i file prodotti e altri log potenzialmente utili.

## <a name="next-steps"></a>Passaggi successivi
È stato creato un account di Sperimentazione di Azure Machine Learning e un account di Gestione modelli di Azure Machine Learning. Sono state installate l'app desktop Azure Machine Learning Workbench e l'interfaccia della riga di comando. È stato creato un nuovo progetto, è stato creato un modello eseguendo uno script ed è stata esaminata la cronologia di esecuzione dello script.

Per un'analisi più approfondita di questo flusso di lavoro, incluse informazioni su come distribuire il modello Iris come servizio Web, seguire l'esercitazione *Classificazione di dati Iris* completa. L'esercitazione include i passaggi di [preparazione dei dati](tutorial-classifying-iris-part-1.md), [sperimentazione](tutorial-classifying-iris-part-2.md) e [gestione dei modelli](tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Esercitazione Classificazione iris](tutorial-classifying-iris-part-1.md)
