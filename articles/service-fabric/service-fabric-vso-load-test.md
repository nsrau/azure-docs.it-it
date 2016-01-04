<properties
    pageTitle="Eseguire test di carico dell'applicazione usando Visual Studio Team Services | Microsoft Azure"
    description="Informazioni su come eseguire test di stress delle applicazioni Azure Service Fabric usando Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Eseguire test di carico dell'applicazione usando Visual Studio Team Services

Questo articolo illustra come usare le funzionalità di test di carico di Visual Studio per eseguire test di stress di un'applicazione. Usa un back-end del servizio con stato dell'infrastruttura di servizi e un front-end Web del servizio senza stato. L'applicazione di esempio usata in questo argomento è un simulatore dei percorsi di volo degli aeroplani. È necessario fornire l'ID dell'aereo, la posizione di partenza e quella di arrivo. Il back-end dell'applicazione elabora le richieste e il front-end consente di visualizzare su una mappa l'aereo che corrisponde ai criteri inseriti.

Il diagramma seguente illustra l'applicazione dell'infrastruttura di servizi che verrà testata.

![][0]

## Prerequisiti
Prima di iniziare, è necessario seguire questa procedura.

- Ottenere un account di Visual Studio Team Services (VSTS). È possibile ottenerne uno gratuitamente visitando [Visual Studio Team Services](https://www.visualstudio.com).
- Ottenere e installare Visual Studio 2013 o Visual Studio 2015. Questo articolo usa Visual Studio 2015 Enterprise Edition, ma Visual Studio 2013 e altre edizioni dovrebbero funzionare in modo simile.
- Distribuire l'applicazione in un ambiente di gestione temporanea. Per altre informazioni, vedere l'articolo relativo alla [distribuzione di applicazioni in un cluster remoto usando Visual Studio](service-fabric-publish-app-remote-cluster.md).
- Comprendere il modello d'uso dell'applicazione. Queste informazioni vengono usate per simulare il modello di carico.
- Comprendere l'obiettivo del test di carico. Queste informazioni consentono di interpretare e analizzare i risultati del test di carico.

## Creare ed eseguire il progetto Test di carico e prestazioni Web

### Creare il progetto Test di carico e prestazioni Web

1. Aprire Visual Studio 2015. Scegliere **File** > **Nuovo** > **Progetto** nella barra dei menu per aprire la finestra di dialogo **Nuovo progetto**.

1. Espandere il nodo **Visual C#** e scegliere **Test** > **Progetto Test di carico e prestazioni Web**. Assegnare un nome al progetto e quindi fare clic sul pulsante **OK**.

    ![][1]

    Verrà visualizzato il nuovo progetto Test di carico e prestazioni Web.

    ![][2]

### Registrare un test di prestazioni Web

1. Aprire il progetto con estensione webtest.

1. Scegliere l'icona **Aggiungi registrazione** per avviare una sessione di registrazione nel browser.

    ![][3]

    ![][4]

1. Passare all'applicazione dell'infrastruttura di servizi. Il pannello di registrazione dovrebbe visualizzare le richieste Web.

    ![][5]

1. Eseguire una sequenza di azioni che si prevede verranno eseguite dagli utenti. Queste azioni vengono usate come modello per generare il carico.

1. Al termine, fare clic sul pulsante **Arresta** per interrompere la registrazione.

    ![][6]

    Il progetto con estensione webtest in Visual Studio dovrebbe avere acquisito in una serie di richieste. I parametri dinamici vengono sostituiti automaticamente. A questo punto, è possibile eliminare qualsiasi richiesta aggiuntiva ripetuta di dipendenza che non fa parte dello scenario di test.

1. Salvare il progetto e quindi scegliere il comando **Esegui test** per eseguire il test delle prestazioni Web in locale e assicurarsi che tutto funzioni correttamente.

    ![][7]

### Impostare i parametri per il test delle prestazioni Web

È possibile impostare i parametri per il test delle prestazioni Web convertendolo in un test delle prestazioni Web codificato e quindi modificando il codice. In alternativa, è possibile associare il test delle prestazioni Web a un elenco di dati in modo che il test esegua l'iterazione tra i dati. Per informazioni dettagliate sulla conversione del test delle prestazioni Web in un test codificato, vedere [Generare ed eseguire un test delle prestazioni Web codificato](https://msdn.microsoft.com/library/ms182552.aspx), mentre per informazioni su come associare dati a un test delle prestazioni Web, vedere [Aggiungere un'origine dati a un test delle prestazioni Web](https://msdn.microsoft.com/library/ms243142.aspx).

Per questo esempio si convertirà il test delle prestazioni Web in un test codificato in modo da poter sostituire l'ID dell'aereo con un GUID generato e aggiungere altre richieste per inviare i voli a destinazioni diverse.

### Creare un progetto di test di carico

Un progetto di test di carico è costituito da uno o più scenari descritti dal test delle prestazioni Web e dallo unit test, insieme ad altre impostazioni aggiuntive specifiche del test di carico. I passaggi seguenti illustrano come creare un progetto di test di carico.

1. Nel menu di scelta rapida del progetto Test di carico e prestazioni Web scegliere **Aggiungi** > **Test di carico**. Nella procedura guidata **Test di carico** fare clic sul pulsante **Avanti** per configurare le impostazioni del test.

1. Nella sezione **Modello di carico** scegliere se si desidera un carico di lavoro dell'utente costante o un carico per passaggio, che inizia con un numero limitato di utenti che aumenta nel tempo.

    Se si dispone di una buona stima della quantità di carico di lavoro dell'utente e si desidera visualizzare le prestazioni del sistema corrente, scegliere **Carico costante**. Se l'obiettivo è comprendere se il sistema offre prestazioni costanti con diverse quantità di carico, scegliere **Carico per passaggio**.

1. Nella sezione **Combinazione di test** fare clic sul pulsante **Aggiungi** e quindi selezionare il test da includere nel test di carico. È possibile usare la colonna **Distribuzione** per specificare la percentuale di test totali da eseguire per ogni test.

1. Nella sezione **Impostazioni di esecuzione** specificare la durata del test di carico.

    >[AZURE.NOTE]L'opzione **Iterazioni test** è disponibile solo quando si esegue il test di carico in locale usando Visual Studio.


1. Nella sezione **Percorso** di **Impostazioni di esecuzione** specificare il percorso in cui vengono generate richieste di test di carico. La procedura guidata potrebbe richiedere di eseguire l'accesso all'account di Team Services. Accedere usando l'account di Team Services, quindi scegliere una posizione geografica. Al termine, fare clic sul pulsante **Fine**.

1. Dopo aver creato il test di carico, aprire il progetto con estensione loadtest e scegliere l'impostazione di esecuzione corrente, ad esempio **Impostazioni di esecuzione** > **Impostazioni di esecuzione1 [Attivo]**. Verranno visualizzate le impostazioni di esecuzione nella finestra **Proprietà**.

1. Nella sezione **Risultati** della finestra delle proprietà **Impostazioni di esecuzione** il valore predefinito per l'impostazione **Intervallo archiviazione dettagli** dovrebbe essere **Nessuno**. Per ottenere altre informazioni sul risultato del test di carico, modificare questo valore impostando **Tutti i singoli dettagli**. Per altre informazioni su come connettersi a Visual Studio Team Services ed eseguire un test di carico, vedere la pagina relativa ai [test di carico](https://www.visualstudio.com/load-testing.aspx).

### Eseguire test di carico usando Visual Studio Team Services

Scegliere il comando **Esegui test di carico** per avviare l'esecuzione del test.

![][8]

## Visualizzare e analizzare i risultati del test di carico

Nel corso del test di carico vengono rappresentate graficamente le informazioni sulle prestazioni. Si dovrebbe visualizzare una schermata simile al grafico seguente.

![][9]

1. Scegliere il collegamento **Scarica report** nella parte superiore della pagina. Dopo il download del report, fare clic sul pulsante **Visualizza report**.

    Nella scheda **Grafico** è possibile visualizzare grafici per diversi contatori di prestazioni. Nella scheda **Riepilogo** vengono visualizzati i risultati complessivi del test. Nella scheda **Tabelle** viene visualizzato il numero totale di test di carico riusciti e non riusciti.

1. Scegliere i collegamenti ai numeri nelle colonne **Test** > **Non riuscito** e **Errori** > **Conteggio** per visualizzare i dettagli degli errori.

    La scheda **Dettaglio** mostra informazioni sullo scenario di test e sull'utente virtuale per le richieste non riuscite. Questi dati possono essere utili se il test di carico include più scenari.

Per altre informazioni sulla visualizzazione dei risultati del test di carico, vedere l'articolo relativo all'[analisi dei risultati del test di carico nella visualizzazione grafici dell'analizzatore del test di carico](https://www.visualstudio.com/load-testing.aspx).

## Automatizzare il test di carico

Il test di carico di Visual Studio Team Services fornisce le API che consentono di gestire i test di carico e analizzare i risultati in un account di Visual Studio Team Services. Per altre informazioni, vedere l'articolo relativo alle [API REST dei test di carico nel cloud](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx).

## Passaggi successivi
- [Monitoraggio e diagnosi dei servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

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

<!---HONumber=AcomDC_1217_2015-->
