---
title: Flask e archiviazione tabelle di Azure con Python Tools 2.2 per Visual Studio
description: Informazioni su come usare Python Tools per Visual Studio per creare un&quot;app Web Flask che archivia i dati nel servizio di archiviazione tabelle di Azure e per distribuirla in App Web del servizio app di Azure.
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: d8e70a29-aca1-4010-95f5-cfe769e3be06
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 96b27bdd528b0f57b9e0a9959ead23b8f820a82d


---
# <a name="flask-and-azure-table-storage-on-azure-with-python-tools-22-for-visual-studio"></a>Flask e archiviazione tabelle di Azure con Python Tools 2.2 per Visual Studio
In questa esercitazione si userà [Python Tools per Visual Studio] al fine di creare una semplice app Web per sondaggi con uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile in formato [video](https://www.youtube.com/watch?v=qUtZWtPwbTk).

L'app Web per sondaggi definisce un'astrazione per il proprio repository; in questo modo, è possibile passare facilmente tra diversi tipi di repository (in memoria, archiviazione tabelle di Azure, MongoDB).

Si apprenderà come creare un account di archiviazione di Azure, come configurare l'app Web per l'uso dell'archiviazione tabelle di Azure e come pubblicare l'app Web in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Vedere il [Centro per sviluppatori Python] per consultare altri articoli che trattano lo sviluppo di app Web del servizio app di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL. Sebbene questo articolo sia incentrato sul servizio app, i passaggi sono simili a quelli previsti per lo sviluppo dei [servizi cloud di Azure].

## <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2015
* [Python Tools 2.2 per Visual Studio]
* [VSIX degli esempi di Python Tools 2.2 per Visual Studio]
* [Strumenti di Azure SDK per Visual Studio 2015]
* [Python 2.7 a 32 bit] o [Python 3.4 a 32 bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="create-the-project"></a>Creare il progetto
In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari. L'applicazione verrà quindi eseguita in locale tramite il repository in memoria predefinito.

1. In Visual Studio selezionare **File**, **Nuovo progetto**.
2. I modelli di progetto di [VSIX degli esempi di Python Tools 2.2 per Visual Studio] sono disponibili in **Python**, **Esempi**. Selezionare **Polls Flask Web Project** e fare clic su OK per creare il progetto.
   
     ![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)
3. Verrà richiesto di installare pacchetti esterni. Selezionare **Installa in un ambiente virtuale**.
   
     ![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)
4. Selezionare **Python 2.7** o **Python 3.4** come interprete di base.
   
     ![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)
5. Verificare che l'applicazione funzioni premendo `F5`. Per impostazione predefinita, l'applicazione usa un repository in memoria che non richiede alcuna configurazione. Tutti i dati vengono persi quando il server Web viene arrestato.
6. Fare clic su **Crea sondaggio di esempio**, quindi fare clic su un sondaggio e su un voto.
   
     ![Web browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure
Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del portale, quindi fare clic su **Dati e archiviazione** > ** Account di archiviazione**. Fare clic su **Crea**quindi assegnare un nome univoco all'account di archiviazione e creare un nuovo [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) correlato.
   
      ![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)
   
    Quando l'account di archiviazione viene creato, nel pulsante **Notifiche** lampeggia in verde il testo **OPERAZIONE RIUSCITA** e il pannello dell'account di archiviazione si apre per visualizzare che appartiene al nuovo gruppo di risorse creato.
3. Fare clic sulla sezione **Chiavi di accesso** del pannello dell'account di archiviazione. Prendere nota del nome dell'account e della chiave denominata key1.
   
      ![Chiavi](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageKeys.png)
   
    Queste informazioni sono necessarie per configurare il progetto nella sezione successiva.

## <a name="configure-the-project"></a>Configurare il progetto
In questa sezione verrà configurata l'applicazione per usare l'account di archiviazione appena creato. Verranno fornite informazioni su come ottenere le impostazioni di connessione dal portale di Azure. quindi verrà eseguita l'applicazione in locale.

1. In Visual Studio fare clic con il pulsante destro del mouse sul nodo del progetto in Esplora soluzioni e scegliere **Proprietà**. Fare clic sulla scheda **Debug** .
   
     ![Impostazioni di debug del progetto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)
2. Impostare i valori delle variabili di ambiente richieste dall'applicazione in **Debug Server Command**, **Environment**.
   
       REPOSITORY_NAME=azuretablestorage
       STORAGE_NAME=<storage account name>
       STORAGE_KEY=<primary access key>
   
   In questo modo verranno impostate le variabili di ambiente quando si sceglie **Avvia debug**. Se si vuole che le variabili vengano impostate quando si **avvia senza eseguire il debug**, impostare gli stessi valori anche in **Run Server Command**.
   
   In alternativa, è possibile definire variabili di ambiente usando il Pannello di controllo di Windows. Si tratta di un'opzione migliore se si vuole evitare di archiviare le credenziali nel codice sorgente/nel file del progetto. Si noti che è necessario riavviare Visual Studio affinché i nuovi valori di ambiente siano disponibili per l'applicazione.
3. Il codice che implementa il repository di archiviazione tabelle di Azure si trova in **models/azuretablestorage.py**. Per altre informazioni su come usare il servizio tabelle da Python, vedere la [documentazione] .
4. Eseguire l'applicazione con `F5`. I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nell'archiviazione tabelle di Azure.
   
   > [!NOTE]
   > L'ambiente virtuale Python 2.7 può causare un'interruzione di eccezioni in Visual Studio.  Premere `F5` per continuare il caricamento del progetto web.
   > 
   > 
5. Passare alla pagina **About** per verificare che l'applicazione usi il repository di **archiviazione tabelle di Azure**.
   
     ![Web browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## <a name="explore-the-azure-table-storage"></a>Esplorare l'archiviazione tabelle di Azure
È facile visualizzare e modificare le tabelle di archiviazione tramite Cloud Explorer in Visual Studio. In questa sezione si userà Esplora server per visualizzare il contenuto delle tabelle dell'applicazione di sondaggio.

> [!NOTE]
> A tale scopo, è necessario che siano installati gli strumenti di Microsoft Azure, disponibili come parte di [Azure SDK per .NET].
> 
> 

1. Aprire **Cloud Explorer**. Espandere **Account di archiviazione**, l'account di archiviazione di riferimento e quindi **Tabelle**.
   
     ![Cloud Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)
2. Fare doppio clic su **sondaggi** o **opzioni** per visualizzarne i contenuti in una finestra di documento, nonché aggiungere/rimuovere/modificare entità.
   
     ![Risultati della query relativa alla tabella](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Pubblicare l'app Web nel servizio app di Azure
L'SDK .NET di Azure offre un modo semplice di distribuire l'app Web nel servizio app di Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo di progetto e scegliere **Pubblica**.
   
     ![Finestra di dialogo Pubblica sito Web](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)
2. Fare clic su **App Web di Microsoft Azure**.
3. Fare clic su **Nuovo** per creare una nuova app Web.
4. Compilare i campi seguenti, quindi fare clic su **Crea**.
   
   * **Nome dell'app Web**
   * **Piano di servizio app**
   * **Gruppo di risorse**
   * **Area**
   * Lasciare **Server database** impostato su **Nessun database**.
5. Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.
6. L'app Web pubblicata verrà aperto automaticamente nel Web browser. Se si passa alla pagina relativa alle informazioni, si noterà che viene usato il repository **in memoria**, non il repository di **archiviazione tabelle di Azure**.
   
   Ciò avviene perché le variabili di ambiente non sono impostate per l'istanza app Web in Azure App Service; pertanto vengono usati i valori predefiniti specificati in **settings.py**.

## <a name="configure-the-web-apps-instance"></a>Configurare l'istanza di app Web
In questa sezione verranno configurate le variabili dell'istanza di App Web.

1. Nel [portale di Azure](https://portal.azure.com) aprire il pannello dell'app Web facendo clic su **Sfoglia** > **Servizi app** > nome dell'app Web.
2. Nel pannello dell'app Web fare clic su **Tutte le impostazioni** e quindi su **Impostazioni applicazione**.
3. Scorrere verso il basso fino alla sezione **Impostazioni app** e impostare i valori per **REPOSITORY\_NAME**, **STORAGE\_NAME** e **STORAGE\_KEY**, come descritto nella sezione precedente **Configurare il progetto**.
   
     ![Impostazioni app](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)
4. Fare clic su **Save**. Dopo aver ricevuto le notifiche relative all'applicazione delle modifiche, fare clic su **Sfoglia** dal pannello principale dell'app Web.
5. L'app Web dovrebbe funzionare come previsto, usando il repository di **archiviazione tabelle di Azure** .
   
   Congratulazioni.
   
     ![Web browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## <a name="next-steps"></a>Passaggi successivi
Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Flask e archiviazione tabelle di Azure.

* [Documentazione di Python Tools per Visual Studio]
  * [Progetti Web]
  * [Progetti servizio cloud]
  * [Debug remoto in Microsoft Azure]
* [Documentazione di Flask]
* [Archiviazione di Azure]
* [Azure SDK per Python]
* [Come usare il servizio di archiviazione tabelle di Python]

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Centro per sviluppatori Python]: /develop/python/
[servizi cloud di Azure]: ../cloud-services/cloud-services-python-ptvs.md
[documentazione]: ../storage/storage-python-how-to-use-table-storage.md
[Come usare il servizio di archiviazione tabelle di Python]: ../storage/storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[Azure SDK per .NET]: http://azure.microsoft.com/downloads/
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[VSIX degli esempi di Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Strumenti di Azure SDK per Visual Studio 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentazione di Python Tools per Visual Studio]: http://aka.ms/ptvsdocs
[Documentazione di Flask]: http://flask.pocoo.org/
[Debug remoto in Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Progetti Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Progetti servizio cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Archiviazione di Azure]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK per Python]: https://github.com/Azure/azure-sdk-for-python



<!--HONumber=Feb17_HO2-->


