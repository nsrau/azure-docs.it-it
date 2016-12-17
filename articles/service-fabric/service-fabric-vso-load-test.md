---
title: Eseguire test di carico dell&quot;applicazione tramite Visual Studio Team Services | Documentazione Microsoft
description: Informazioni su come eseguire test di stress delle applicazioni  Azure Service Fabric usando Visual Studio Team Services.
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: fc743585-0d1b-483f-981d-493f4552ac07
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b7d5280fd6dccdf325920ea3f924f965d533b210


---
# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Eseguire test di carico dell'applicazione usando Visual Studio Team Services
Questo articolo illustra come usare le funzionalità di test di carico di Microsoft Visual Studio per eseguire test di stress di un'applicazione. Viene usato un back-end del servizio con stato di Service Fabric di Azure e un front-end Web di un servizio senza stato. L'applicazione di esempio usata in questo argomento è un simulatore dei percorsi di volo degli aeroplani. È necessario fornire l'ID dell'aereo, l'orario di partenza e la destinazione. Il back-end dell'applicazione elabora le richieste e il front-end consente di visualizzare su una mappa l'aereo che corrisponde ai criteri.

Il diagramma seguente illustra l'applicazione di Service Fabric che verrà testata.

![Diagramma dell'applicazione della posizione dell'aereo di esempio][0]

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario seguire questa procedura:

* Ottenere un account di Visual Studio Team Services. È possibile ottenerne uno gratuitamente visitando [Visual Studio Team Services](https://www.visualstudio.com).
* Ottenere e installare Visual Studio 2013 o Visual Studio 2015. Questo articolo usa Visual Studio 2015 Enterprise Edition, ma Visual Studio 2013 e altre edizioni dovrebbero funzionare in modo simile.
* Distribuire l'applicazione in un ambiente di gestione temporanea. Per altre informazioni, vedere l'articolo relativo alla [distribuzione di applicazioni in un cluster remoto usando Visual Studio](service-fabric-publish-app-remote-cluster.md) .
* Comprendere il modello d'uso dell'applicazione. Queste informazioni vengono usate per simulare il modello di carico.
* Comprendere l'obiettivo del test di carico. Queste informazioni consentono di interpretare e analizzare i risultati del test di carico.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Creare ed eseguire il progetto Test di carico e prestazioni Web
### <a name="create-a-web-performance-and-load-test-project"></a>Creare il progetto Test di carico e prestazioni Web
1. Aprire Visual Studio 2015. Scegliere **File** > **Nuovo** > **Progetti** nella barra dei menu per aprire la finestra di dialogo **Nuovo progetto**.
2. Espandere il nodo **Visual C#** e scegliere **Test** > **Progetto di test di carico e prestazioni Web**. Assegnare un nome al progetto e quindi fare clic sul pulsante **OK** .

    ![Schermata della finestra di dialogo Nuovo progetto][1]

    Verrà visualizzato il nuovo progetto di test di carico e prestazioni Web in Esplora soluzioni.

    ![Schermata di Esplora soluzioni con il nuovo progetto][2]

### <a name="record-a-web-performance-test"></a>Registrare un test di prestazioni Web
1. Aprire il progetto con estensione webtest.
2. Scegliere l'icona **Aggiungi registrazione** per avviare una sessione di registrazione nel browser.

    ![Schermata dell'icona Aggiungi registrazione in un browser][3]

    ![Schermata del pulsante Record in un browser][4]
3. Passare all'applicazione dell'infrastruttura di servizi. Il pannello di registrazione dovrebbe visualizzare le richieste Web.

    ![Schermata delle richieste Web nel pannello di registrazione][5]
4. Eseguire una sequenza di azioni che si prevede verranno eseguite dagli utenti. Queste azioni vengono usate come modello per generare il carico.
5. Al termine, fare clic sul pulsante **Arresta** per interrompere la registrazione.

    ![Schermata del pulsante Interrompi][6]

    Il progetto con estensione webtest in Visual Studio dovrebbe avere acquisito in una serie di richieste. I parametri dinamici vengono sostituiti automaticamente. A questo punto, è possibile eliminare qualsiasi richiesta aggiuntiva ripetuta di dipendenza che non fa parte dello scenario di test.
6. Salvare il progetto e quindi scegliere il comando **Esegui test** per eseguire il test delle prestazioni Web in locale e assicurarsi che tutto funzioni correttamente.

    ![Schermata del comando Esegui test][7]

### <a name="parameterize-the-web-performance-test"></a>Impostare i parametri per il test delle prestazioni Web
È possibile impostare i parametri per il test delle prestazioni Web convertendolo in un test delle prestazioni Web codificato e quindi modificando il codice. In alternativa, è possibile associare il test delle prestazioni Web a un elenco di dati in modo che il test esegua l'iterazione tra i dati. Vedere [Generare ed eseguire un test delle prestazioni Web codificato](https://msdn.microsoft.com/library/ms182552.aspx) per informazioni dettagliate su come convertire il test delle prestazioni Web in un test codificato. Vedere [Aggiungere un'origine dati a un test delle prestazioni Web](https://msdn.microsoft.com/library/ms243142.aspx) per informazioni su come associare dati a un test delle prestazioni Web.

Per questo esempio, si convertirà il test delle prestazioni Web in un test codificato in modo da poter sostituire l'ID dell'aereo con un GUID generato e aggiungere altre richieste per inviare i voli in destinazioni diverse.

### <a name="create-a-load-test-project"></a>Creare un progetto di test di carico
Un progetto di test di carico è costituito da uno o più scenari descritti dal test delle prestazioni Web e dallo unit test, insieme a impostazioni aggiuntive specifiche del test di carico. I passaggi seguenti illustrano come creare un progetto di test di carico:

1. Nel menu di scelta rapida del progetto di test di carico e prestazioni Web, scegliere **Aggiungi** > **Test di carico**. Nella procedura guidata **Test di carico** fare clic sul pulsante **Avanti** per configurare le impostazioni del test.
2. Nella sezione **Modello di carico** scegliere se si desidera un carico di lavoro dell'utente costante o un carico per passaggio, che inizia con un numero limitato di utenti che aumenta nel tempo.

    Se si dispone di una buona stima della quantità di carico utente e si desidera visualizzare le prestazioni del sistema corrente, scegliere **Carico costante**. Se l'obiettivo è scoprire se il sistema offre prestazioni coerenti con diverse quantità di carico, scegliere **Carico per passaggio**.
3. Nella sezione **Combinazione di test** fare clic sul pulsante **Aggiungi** e quindi selezionare il test da includere nel test di carico. È possibile usare la colonna **Distribuzione** per specificare la percentuale di test totali da eseguire per ogni test.
4. Nella sezione **Impostazioni di esecuzione** specificare la durata del test di carico.

   > [!NOTE]
   > L'opzione **Iterazioni test** è disponibile solo quando si esegue il test di carico in locale usando Visual Studio.
   >
   >
5. Nella sezione **Percorso** di **Impostazioni di esecuzione** specificare il percorso in cui vengono generate richieste di test di carico. La procedura guidata potrebbe richiedere di eseguire l'accesso all'account di Team Services. Accedere e scegliere una posizione geografica. Al termine, fare clic sul pulsante **Fine** .
6. Dopo aver creato il test di carico, aprire il progetto con estensione loadtest e scegliere l'impostazione di esecuzione corrente, ad esempio **Impostazioni di esecuzione** > **Impostazioni di esecuzione1 [Active]**. Verranno visualizzate le impostazioni di esecuzione nella finestra **Proprietà** .
7. Nella sezione **Risultati** della finestra delle proprietà **Impostazioni di esecuzione** il valore predefinito per l'impostazione **Intervallo archiviazione dettagli** dovrebbe essere **Nessuno**. Per ottenere altre informazioni sui risultati del test di carico, modificare questo valore impostandolo su **Tutti i singoli dettagli** . Per altre informazioni su come connettersi a Visual Studio Team Services ed eseguire un test di carico, vedere la pagina relativa ai [test di carico](https://www.visualstudio.com/load-testing.aspx) .

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Eseguire il test di carico usando Visual Studio Team Services
Scegliere il comando **Esegui test di carico** per avviare l'esecuzione del test.

![Schermata del comando Esegui test di carico][8]

## <a name="view-and-analyze-the-load-test-results"></a>Visualizzare e analizzare i risultati del test di carico
Nel corso del test di carico vengono rappresentate graficamente le informazioni sulle prestazioni. Si dovrebbe visualizzare una schermata simile al grafico seguente.

![Schermata del grafico delle prestazioni per i risultati di test di carico][9]

1. Scegliere il collegamento **Scarica report** nella parte superiore della pagina. Dopo il download del report, fare clic sul pulsante **Visualizza report** .

    Nella scheda **Grafico** è possibile visualizzare grafici per diversi contatori di prestazioni. Nella scheda **Riepilogo** vengono visualizzati i risultati complessivi del test. Nella scheda **Tabelle** viene visualizzato il numero totale di test di carico riusciti e non riusciti.
2. Scegliere i collegamenti ai numeri nelle colonne **Test** > **Non riuscito** e **Errori** > **Conteggio** per visualizzare i dettagli degli errori.

    La scheda **Dettaglio** mostra informazioni sullo scenario di test e sull'utente virtuale per le richieste non riuscite. Questi dati possono essere utili se il test di carico include più scenari.

Per ulteriori informazioni sulla visualizzazione dei risultati del test di carico, vedere [Analisi dei risultati dei test di carico nella visualizzazione Grafici dell'Analizzatore test di carico](https://www.visualstudio.com/load-testing.aspx) .

## <a name="automate-your-load-test"></a>Automatizzare il test di carico
Il test di carico di Visual Studio Team Services fornisce API che consentono di gestire i test di carico e analizzare i risultati in un account Team Services. Per altre informazioni, vedere l'articolo relativo alle [API REST dei test di carico nel cloud](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) .

## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio e diagnosi dei servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png



<!--HONumber=Nov16_HO3-->


