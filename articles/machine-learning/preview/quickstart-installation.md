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
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 91d2f47a528050f644973044f96c0354b91dba25
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Creare account di anteprima e installare Azure Machine Learning Workbench
Azure Machine Learning è una soluzione integrata di analisi scientifica dei dati e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa guida introduttiva illustra come creare account di sperimentazione e gestione dei modelli nell'anteprima dei servizi di Azure Machine Learning. Descrive anche come installare l'applicazione desktop di Azure Machine Learning Workbench e gli strumenti dell'interfaccia della riga di comando. Verrà anche proposta una presentazione rapida delle funzionalità di anteprima di Azure Machine Learning con il [dataset Iris](https://en.wikipedia.org/wiki/iris_flower_data_set) per compilare un modello per la previsione del tipo di iris in base ad alcune delle relative caratteristiche fisiche.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Azure Machine Learning Workbench può essere attualmente installato solo nei sistemi operativi Windows 10, Windows Server 2016 e macOS Sierra.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.
Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-azure-machine-learning-accounts"></a>Creare account di Azure Machine Learning
Usare il portale di Azure per eseguire il provisioning degli account di Azure Machine Learning. 
1. Selezionare il pulsante **Nuovo** (+) nell'angolo superiore sinistro del portale.

2. Digitare "Machine Learning" nella barra di ricerca. Selezionare il risultato di ricerca denominato **Machine Learning Experimentation (anteprima)**.  Fare clic sull'icona a stella per aggiungere la selezione agli elementi Preferiti nel portale di Azure.

   ![Ricerca di Azure Machine Learning](media/quickstart-installation/portal-more-services.png)

3. Fare clic su **+ Aggiungi** per configurare un nuovo account di Sperimentazione di Machine Learning. Verrà aperto il modulo dettagliato.

   ![Account di Sperimentazione di Machine Learning](media/quickstart-installation/portal-create-experimentation.png)

4. Compilare il modulo di Sperimentazione di Machine Learning con le informazioni seguenti:

   Impostazione|Valore consigliato|Descrizione
   ---|---|---
   Nome account di Experimentation | _Nome univoco_ |Scegliere un nome univoco per identificare l'account. È possibile usare il proprio nome o il nome di un reparto o progetto che identifichi l'esperimento in modo ottimale. Il nome processo può contenere solo caratteri alfanumerici e il carattere '-' e deve avere una lunghezza compresa tra 2 e 32 caratteri. 
   Sottoscrizione | _Sottoscrizione in uso_ |Sottoscrizione di Azure da usare per l'esperimento. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui viene fatturata la risorsa.
   Gruppo di risorse | _Il gruppo di risorse_ | È possibile creare un nuovo nome di gruppo di risorse o usarne uno esistente nella sottoscrizione.
   Percorso | _Area più vicina ai propri utenti_ | Scegliere la località più vicina agli utenti e alle risorse di dati.
   Number of seats (Numero di postazioni) | 2 | Digitare il numero di postazioni. Questa selezione influisce sui [prezzi](https://azure.microsoft.com/pricing/details/machine-learning/). Le prime due postazioni sono gratuite. Usare due postazioni ai fini di questa guida rapida. È possibile aggiornare il numero di postazioni in seguito nel portale di Azure.
   Account di archiviazione | _Nome univoco_ | Scegliere **Crea nuovo** e specificare un nome per creare un nuovo account di archiviazione di Azure oppure scegliere **Usa esistente** e selezionare l'account di archiviazione esistente dall'elenco a discesa. L'account di archiviazione è obbligatorio e viene usato per contenere gli elementi del progetto e i dati della cronologia di esecuzione. 
   Workspace for Experimentation account (Area di lavoro per account di Sperimentazione) | _Nome univoco_ | Specificare un nome per la nuova area di lavoro. Il nome processo può contenere solo caratteri alfanumerici e il carattere '-' e deve avere una lunghezza compresa tra 2 e 32 caratteri.
   Assign owner for the workspace (Proprietario area di lavoro) | _L'account_ | Selezionare il proprio account come proprietario dell'area di lavoro.
   Create Model Management Account (Crea account di Gestione modelli) | *check* | Nell'ambito dell'esperienza di creazione dell'account di Sperimentazione, è anche possibile creare l'account di Gestione modelli di Machine Learning. La risorsa viene usata quando si è pronti per distribuire e gestire i modelli come servizi Web in tempo reale. È consigliabile creare l'account di Gestione modelli al momento della creazione dell'account di Sperimentazione.
   Nome account | _Nome univoco_ | Scegliere un nome univoco per identificare l'account di Gestione modelli. È possibile usare il proprio nome o il nome di un reparto o progetto che identifichi l'esperimento in modo ottimale. Il nome processo può contenere solo caratteri alfanumerici e il carattere '-' e deve avere una lunghezza compresa tra 2 e 32 caratteri. 
   Piano tariffario di Gestione modelli | **DEVTEST** | Fare clic su **Nessun piano tariffario selezionato** per specificare il piano tariffario per il nuovo account di Gestione modelli. Per ridurre i costi selezionare il piano tariffario **DEVTEST** se è disponibile per la sottoscrizione (disponibilità limitata), in caso contrario selezionare il piano tariffario S1 per risparmiare sui costi. Fare clic su **Seleziona** per salvare la selezione del piano tariffario. 
   Aggiungi al dashboard | _check_ | Selezionare l'opzione **Aggiungi al dashboard** per tenere facilmente traccia dell'account di Sperimentazione di Machine Learning nella pagina iniziale del dashboard del portale di Azure.

5. Fare clic su **Crea** per avviare il processo di creazione.

6. Nella parte superiore destra della barra degli strumenti del portale di Azure, fare clic su **Notifiche** (icona a forma di campana) per monitorare il processo di distribuzione. 

   La notifica visualizza "Distribuzione in corso". Al termine, lo stato visualizzato sarà "Distribuzione completata". Si aprirà la pagina dell'account di Sperimentazione di Machine Learning.
   
   ![Notifiche del portale di Azure](media/quickstart-installation/portal-notification.png)

A seconda del sistema operativo usato nel computer locale, seguire ora una delle due sezioni successive per installare Azure Machine Learning Workbench nel computer. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Installare Azure Machine Learning Workbench in Windows
Installare Azure Machine Learning Workbench in computer che eseguono Windows 10, Windows Server 2016 o versioni successive.

1. Scaricare il programma di installazione di Azure Machine Learning Workbench più recente, **[AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)**.

2. In Esplora file fare doppio clic sul programma di installazione _AmlWorkbenchSetup.msi_ scaricato.

   >[!IMPORTANT]
   >Scaricare l'intero programma di installazione e avviarlo dal disco. Non avviarlo direttamente dal widget di download del browser.

3. Completare l'installazione seguendo le istruzioni visualizzate.

   Il programma di installazione scarica tutti i componenti dipendenti necessari, ad esempio Python, Miniconda e altre librerie correlate. L'installazione di tutti i componenti potrebbe richiedere circa mezz'ora. 

4. A questo punto, Azure Machine Learning Workbench sarà installato nella directory seguente:
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Installare Azure Machine Learning Workbench in macOS
Installare Azure Machine Learning Workbench in computer che eseguono macOS Sierra.

1. Installare la libreria openssl usando [Homebrew](http://brew.sh). Vedere [Prerequisiti per .NET Core in Mac](https://docs.microsoft.com/dotnet/core/macos-prerequisites) per altri dettagli.
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. Scaricare il programma di installazione di Azure Machine Learning Workbench più recente, **[AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg)**.

   >[!IMPORTANT]
   >Scaricare l'intero programma di installazione e avviarlo dal disco. Non avviarlo direttamente dal widget di download del browser.

3. In Finder fare doppio clic sul programma di installazione _AmlWorkbench.dmg_ scaricato.

4. Completare l'installazione seguendo le istruzioni visualizzate.

   Il programma di installazione scarica tutti i componenti dipendenti necessari, ad esempio Python, Miniconda e altre librerie correlate. L'installazione di tutti i componenti potrebbe richiedere circa mezz'ora. 

5. A questo punto, Azure Machine Learning Workbench sarà installato nella directory seguente: 

   _/Applications/AmlWorkbench.app_

## <a name="run-azure-machine-learning-workbench-to-log-in-the-first-time"></a>Eseguire Azure Machine Learning Workbench per accedere la prima volta
1. Fare clic sul pulsante **Launch Workbench** (Avvia Workbench) nell'ultima schermata del programma di installazione al termine del processo. Se il programma di installazione è stato chiuso, usare il collegamento **Azure Machine Learning Workbench** sul desktop o nel menu Start per avviare l'app.

2. Accedere all'area di lavoro con lo stesso account usato in precedenza per eseguire il provisioning delle risorse di Azure. 

3. Al termine della procedura di accesso, Workbench tenterà di trovare gli account di Sperimentazione di Machine Learning creati in precedenza cercando tutte le sottoscrizioni di Azure a cui le credenziali dell'utente hanno accesso. Quando viene trovato almeno un account di Sperimentazione, Workbench si apre con quell'account ed elenca le aree di lavoro e i progetti disponibili al suo interno. 

   >[!TIP]
   > Se si ha accesso a più di un account di Sperimentazione, è possibile passare a un altro facendo clic sull'icona dell'avatar nell'angolo inferiore sinistro dell'app Workbench.

Vedere la [configurazione dell'ambiente di distribuzione](deployment-setup-configuration.md) per la creazione di un ambiente per la distribuzione di servizi Web.

## <a name="create-a-new-project"></a>Creare un nuovo progetto
1. Avviare l'app Azure Machine Learning Workbench ed eseguire l'accesso. 

2. Fare clic su **File** --> **Nuovo progetto** oppure sul segno **+** nel riquadro **PROGETTI**. 

3. Compilare i campi **Project name** (Nome progetto) e **Project directory** (Directory progetto). Il campo **Project description** (Descrizione progetto) è facoltativo ma utile. Lasciare il campo **Visualstudio.com GIT Repository URL** (URL repository GIT Visualstudio.com) vuoto per il momento. Scegliere un'area di lavoro e selezionare **Classifying Iris** (Classificazione Iris) come modello di progetto.

   >[!TIP]
   >È anche possibile compilare il campo di testo del repository Git con l'URL di un repository Git ospitato in un progetto [VSTS (Visual Studio Team Service)](https://www.visualstudio.com). Questo repository Git deve già esistere ed essere vuoto, senza alcun ramo principale. È anche necessario avere l'accesso in scrittura al repository. L'aggiunta di un repository Git consente di abilitare in seguito scenari di roaming e condivisione. [Altre informazioni](using-git-ml-project.md).

4. Fare clic sul pulsante **Crea** per creare il progetto. Il nuovo progetto viene creato e viene aperto automaticamente. A questo punto è possibile esplorare la home page, le origini dati, i blocchi appunti e i file di codice sorgente del progetto. 

    >[!TIP]
    >È anche possibile aprire il progetto in Visual Studio Code o altri editor semplicemente configurando un collegamento IDE (Integrated Development Environment) e quindi aprendo la directory del progetto al suo interno. [Altre informazioni](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Eseguire uno script Python
Si eseguirà ora uno script nel computer locale. 

1. Ogni progetto si apre nella propria pagina del **dashboard del progetto**. Selezionare `local` come destinazione dell'esecuzione dalla barra dei comandi nella parte superiore dell'applicazione a sinistra del pulsante di esecuzione e `iris_sklearn.py` come script da eseguire.  L'esempio mette a disposizione altri file che è possibile esaminare in un secondo momento. 

   ![Immagine](media/quickstart-installation/run_control.png)

2. Nel campo di testo **Arguments** (Argomenti) immettere `0.01`. Questo numero viene usato nel codice per impostare la frequenza di regolarizzazione, un valore che consente di configurare il training del modello di regressione lineare. 

3. Fare clic sul pulsante **Run** (Esegui) per avviare l'esecuzione di `iris_sklearn.py` nel computer. 

   Questo codice usa l'algoritmo di [regressione logistica](https://en.wikipedia.org/wiki/logistic_regression) della nota libreria [scikit-learn](http://scikit-learn.org/stable/index.html) di Python per compilare il modello.

4. Il pannello **Jobs** (Processi) compare a destra se non è già visibile e al pannello viene aggiunto un processo `iris_sklearn`. Lo stato passa da **Submitting** (Invio in corso) a **Running** (In esecuzione) quando inizia l'esecuzione del processo, quindi passa a **Completed** (Completato) dopo pochi secondi. 

5. A questo punto È stato eseguito correttamente uno script Python in Azure Machine Learning Workbench.

6. Ripetere i passaggi 2-4 volte. Usare ogni volta valori di argomento diversi compresi tra `10` e `0.001`.

## <a name="view-run-history"></a>Visualizzare la cronologia di esecuzione
1. Passare alla vista **Runs** (Esecuzioni) e fare clic su **iris_sklearn.py** nell'elenco di esecuzione. Si aprirà il dashboard della cronologia di esecuzione per `iris_sklearn.py`, con ogni esecuzione in `iris_sklearn.py`. 

   ![Immagine](media/quickstart-installation/run_view.png)

2. Il dashboard della cronologia di esecuzione visualizza anche le metriche principali, un set di grafici predefiniti e un elenco delle metriche per ogni esecuzione. È possibile personalizzare questa visualizzazione ordinando, filtrando e modificando le configurazioni facendo clic sull'icona di configurazione o sull'icona del filtro.

   ![Immagine](media/quickstart-installation/run_dashboard.png)

3. Fare clic su un'esecuzione completata per visualizzarne i dettagli, tra cui altre metriche, i file prodotti e altri log potenzialmente utili.

## <a name="next-steps"></a>Passaggi successivi
È stato creato un account di Sperimentazione di Azure Machine Learning e un account di Gestione modelli di Azure Machine Learning. Sono state installate l'app desktop Azure Machine Learning Workbench e l'interfaccia della riga di comando. È stato creato un nuovo progetto, è stato creato un modello eseguendo uno script ed è stata esaminata la cronologia di esecuzione dello script.

Per un'esperienza più approfondita di questo flusso di lavoro, inclusa la distribuzione del modello Iris come servizio Web, seguire l'esercitazione Classificazione iris completa che contiene i passaggi dettagliati per la [preparazione dei dati](tutorial-classifying-iris-part-1.md), la [ sperimentazione](tutorial-classifying-iris-part-2.md) e la [gestione dei modelli](tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Esercitazione Classificazione iris](tutorial-classifying-iris-part-1.md)

