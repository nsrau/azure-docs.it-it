<properties
	pageTitle="Note sulla versione per l'estensione di Visual Studio per Developer Analytics"
	description="Aggiornamenti più recenti per Strumenti di Visual Studio per Developer Analytics"
	services="application-insights"
    documentationCenter=""
	authors="aruna"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="acearun"/>

# Note sulla versione: Strumenti di analisi per sviluppatori
##### Analisi di Application Insights e di HockeyApp in Visual Studio
## Versione 5.2
Microsoft è lieta di annunciare l'introduzione di scenari di HockeyApp in Visual Studio. La prima integrazione abilitata è la distribuzione beta delle app Windows universale e Windows Form in VS.

La distribuzione beta consente di caricare le versioni non definitive delle app in HockeyApp per distribuirle a un subset scelto di clienti o tester. La distribuzione beta, combinata con le funzionalità di feedback degli utenti e di raccolta degli arresti anomali di HockeyApp, può fornire utili informazioni sull'app prima di un rilascio su vasta scala. È possibile usare queste informazioni per risolvere i problemi relativi all'app prima che diventino gravi (valutazioni basse, feedback insufficiente e così via).

Caricare build per la distribuzione beta da VS è davvero semplice.
### App Windows universali
Il menu contestuale per il nodo di un progetto per la piattaforma UWP ora include un'opzione per caricare la build in HockeyApp.

![Menu contestuale del progetto per le app universali](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Scegliere la voce per visualizzare la finestra di dialogo di caricamento di HockeyApp. Per caricare la build, sarà necessario un account HockeyApp. I nuovi utenti potranno creare un account con un semplice processo.

Una volta connessi, verrà visualizzato il form per il caricamento nella finestra di dialogo.

![Finestra di dialogo di caricamento per le app universali](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Selezionare il contenuto da caricare (APPXBUNDLE o APPX) e scegliere le opzioni per la versione nella procedura guidata. Nella pagina successiva è possibile aggiungere le note sulla versione. Scegliere "Fine" per iniziare il caricamento.

Al termine del caricamento, verrà visualizzato un avviso popup di HockeyApp con la conferma e un collegamento all'app nel portale di HockeyApp.

![Avviso popup di caricamento completato](./media/app-insights-release-notes-vsix/UploadComplete.png)

Sono bastati pochi clic per caricare una build per la distribuzione beta.

Il portale di HockeyApp consente di gestire l'applicazione in diversi modi (invitare gli utenti, visualizzare i report sugli arresti anomali e il feedback, modificare i dettagli e così via).

![Portale di HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Per altri dettagli sulla gestione dell'app, vedere la [Knowledge Base di Hockey App](http://support.hockeyapp.net/kb/app-management-2).

### App Windows Form
Il menu contestuale per il nodo di un progetto per Windows Form include un'opzione per caricare la build in HockeyApp.

![Menu contestuale del progetto per le app Windows Form](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

In questo modo viene visualizzata la finestra di dialogo di caricamento di HockeyApp, simile a quella per le app universali.

![Finestra di dialogo di caricamento per le app Windows Form](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

In questa procedura guidata è presente un campo in più per specificare la versione dell'app. Per le app universali, le informazioni vengono inserite dal manifesto. Purtroppo non esiste un equivalente per Windows Form e quindi è necessario specificare manualmente le informazioni.

Il resto del flusso è simile a quello delle app universali: selezione della build e delle opzioni per la versione, aggiunta delle note sulla versione, caricamento e gestione nel portale di HockeyApp.

È davvero semplice. È possibile provare e inviare commenti e suggerimenti.
## Versione 4.3
### Ricercare i dati di telemetria da sessioni di debug locali
In questa versione è possibile ricercare i dati di telemetria di Application Insights generati nella sessione di debug di Visual Studio. Nelle versioni precedenti la ricerca è possibile soltanto se l'app è stata registrata in Application Insights. In questa versione, per ricercare i dati di telemetria locali è sufficiente che nell'app sia stato installato Application Insights SDK.

#### Se si dispone di un'applicazione ASP.NET con Application Insights SDK

- Eseguire il debug dell'applicazione.
- Aprire Ricerca di Application Insights in uno dei modi seguenti
	- Menu Visualizza -> Altre finestre -> Ricerca di Application Insights
	- Fare clic sul pulsante della barra degli strumenti di Application Insights
	- In Esplora soluzioni, espandere i dati di telemetria della sessione di debug di ricerca ApplicationInsights.config ->
- Se non è stata eseguita l'iscrizione ad Application Insights, la finestra di ricerca viene aperta in modalità 'Telemetria della sessione di debug'.
- Fare clic sull'icona di ricerca per visualizzare i dati di telemetria locali.

![Caricamento completato](./media/app-insights-release-notes-vsix/LocalSearch.png)



##Versione 4.2
In questa versione sono state aggiunte funzionalità per semplificare la ricerca di dati nel contesto degli eventi, passare al codice da altri eventi di dati e inviare i dati di registrazione in Application Insights. Questa estensione viene aggiornata ogni mese. Inviare eventuali commenti o suggerimenti a aidevtools@microsoft.com
###- Accesso a zero clic
Se si usa il sistema di traccia NLog, Log4Net o System.Diagnostics non è necessario spostare le tracce in AI. Gli adattatori di accesso di Application Insights sono integrati nel normale processo di configurazione. Se si dispone già di uno di questi framework di registrazione configurato:
####Se Application Insights è già stato aggiunto
- Fare clic con il pulsante destro del mouse su Nodo progetto->Application Insights->Configura Application Insights. Assicurarsi che venga visualizzata l'opzione che consente di aggiungere l'adattatore appropriato nella finestra di configurazione.
- In alternativa, durante la compilazione della soluzione, nella finestra popup visualizzata nella parte superiore destra della schermata fare clic su Configura. ![Avviso popup](./media/app-insights-release-notes-vsix/LoggingToast.png)

Dopo aver installato l'adattatore di registrazione, è possibile eseguire l'applicazione e verificare di visualizzare i dati nella scheda degli strumenti di diagnostica come indicato di seguito: ![Tracce](./media/app-insights-release-notes-vsix/Traces.png)
###- L'utente può trovare/passare al codice in cui viene generata la proprietà dell'evento di telemetria
Con la nuova versione l'utente può fare clic su qualsiasi valore nei dettagli dell'evento per ricercare una stringa corrispondente nella soluzione aperta. I risultati verranno visualizzati nell'elenco "Risultati ricerca" di Visual Studio come illustrato di seguito: ![Ricerca della corrispondenza](./media/app-insights-release-notes-vsix/FindMatch.png)
###- Nuova schermata per l'utente che non ha eseguito l'accesso nella finestra di ricerca
La finestra di ricerca è stata modificata per consentire agli utenti di ricercare i dati nell'ambiente di produzione. ![Finestra di ricerca](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- L'utente può visualizzare tutti gli eventi di telemetria associati all'evento
È stata aggiunta una nuova scheda accanto ai dettagli dell'evento contenente query predefinite per la visualizzazione di tutti i dati correlati all'evento di telemetria visualizzato dall'utente. Ad esempio: la richiesta include un campo denominato ID operazione e ogni evento associato alla richiesta avrà lo stesso ID operazione. In questo modo, se si è verificata un'eccezione durante l'elaborazione della richiesta, l'eccezione avrà lo stesso ID operazione della richiesta per facilitare la ricerca, ecc. L'utente che visualizza la richiesta potrà fare clic su "Tutti i dati di telemetria per questa operazione" per aprire una nuova scheda con i nuovi risultati della ricerca. ![Elementi correlati](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - Aggiunta dell'opzione di passaggio alla schermata successiva/ritorno all'ultima schermata nella finestra di ricerca
L'utente può ora passare alla schermata successiva o tornare a quella precedente dei risultati di ricerca. ![Tornare alla schermata precedente](./media/app-insights-release-notes-vsix/GoBAck.png)

##Versione 4.1
Questa versione include numerose nuove funzionalità e miglioramenti per quelle esistenti. Per ottenere questa versione è necessario avere installato Update 1 nel computer.

### Passare da un'eccezione al metodo nel codice sorgente
Ora gli utenti che visualizzano le eccezioni dalle app di produzione nella finestra di ricerca di Application Insights possono passare al metodo nel punto del codice in cui si è verificata l'eccezione. È sufficiente caricare il progetto corretto e tutte le operazioni saranno eseguite automaticamente. Per altre informazioni sulla finestra di ricerca, vedere le note sulla versione 4.0 di seguito.

#### Come funziona?

Quando una soluzione non è aperta, è possibile usare la funzionalità di ricerca di Application Insights senza aprire la soluzione. In tal caso, nell'area di analisi dello stack verrà visualizzato un messaggio informativo e molti elementi non saranno disponibili.


Se sono disponibili informazioni sui file, alcuni elementi potrebbero essere collegamenti, ma l'elemento con informazioni sulla soluzione sarà comunque visibile.

Facendo clic sul collegamento ipertestuale si passerà al punto in cui si trova il metodo selezionato nel codice. Potrebbe esserci una differenza nel numero di versione, ma quella funzionalità sarà presente nelle versioni successive. Passare alla versione corretta del codice.

![Clic su un'eccezione](./media/app-insights-release-notes-vsix/jumptocode.png)

###Nuovi punti di ingresso per l'esperienza di ricerca in Esplora soluzioni

![Punto di ingresso in Esplora soluzioni](./media/app-insights-release-notes-vsix/searchentry.png)


###Visualizzare un avviso popup quando la pubblicazione è completata
Dopo la pubblicazione del progetto online, verrà visualizzata una finestra popup che consente di vedere i dati di Application Insights nell'ambiente di produzione.

![Popup](./media/app-insights-release-notes-vsix/publishtoast.png)

## Versione 4.0

###Cercare i dati di Application Insights in Visual Studio
Come per la ricerca nel portale di Application Insights, è possibile filtrare e cercare tipi di eventi, valori delle proprietà e testo ed esaminare singoli eventi.

![Finestra di ricerca](./media/app-insights-release-notes-vsix/search.png)

###Visualizzare i dati della casella locale nella finestra degli strumenti di diagnostica

I dati di telemetria vengono visualizzati anche con altri dati di debug nell'Hub diagnostica di Visual Studio. Supporta solo ASP.NET 4.5. Il supporto per ASP.NET 5 sarà incluso nelle versioni future.

![Finestra Hub diagnostica](./media/app-insights-release-notes-vsix/diagtools.png)

###Aggiungere l'SDK al progetto senza avere essere connessi ad Azure

Non è più necessario accedere ad Azure per aggiungere pacchetti di Application Insights al progetto tramite la finestra di dialogo Nuovo progetto o il menu di scelta rapida progetto. Se si esegue l'accesso, l'SDK viene installato e configurato per l'invio di dati di telemetria al portale, come in precedenza. Se non esegue l'accesso, l'SDK viene aggiunto al progetto e genera i dati di telemetria per l'hub di diagnostica. Sarà possibile configurarlo in un secondo momento, se necessario.

![Finestra di dialogo Nuovo progetto](./media/app-insights-release-notes-vsix/newproject.png)

###Supporto dei dispositivi

Al *Connect();* 2015 [è stato annunciato](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) che l'esperienza DevOps per dispositivi mobili è HockeyApp. HockeyApp consente di distribuire le build beta ai tester, raccogliere e analizzare tutti gli arresti anomali dell'app e raccogliere commenti e suggerimenti direttamente dai clienti. HockeyApp supporta l'utente su qualsiasi piattaforma usata per la compilazione dell'applicazione per dispositivi mobili, sia essa iOS, Android, Windows o una soluzione multipiattaforma come Xamarin, Cordova o Unity.

Nelle versioni future dell'estensione Application Insights saranno introdotte nuove funzionalità per consentire un'esperienza maggiormente integrata tra HockeyApp e Visual Studio. Per ora è possibile iniziare a usare HockeyApp semplicemente aggiungendo il riferimento NuGet. Per altre informazioni, vedere la [documentazione](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone).

<!---HONumber=AcomDC_0330_2016-->