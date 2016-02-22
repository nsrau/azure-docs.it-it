<properties 
	pageTitle="Note sulla versione per l'estensione di Visual Studio per Application Insights" 
	description="Aggiornamenti più recenti per Strumenti di Visual Studio per Application Insights" 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Note sulla versione per Strumenti Application Insights per Visual Studio v 4.2
##Versione 4.2
In questa versione sono state aggiunte funzionalità per semplificare la ricerca di dati nel contesto degli eventi, passare al codice da altri eventi di dati e inviare i dati di registrazione in Application Insights. Questa estensione viene aggiornata ogni mese. Inviare eventuali commenti o suggerimenti a aidevtools@microsoft.com
###- Accesso a zero clic
Se si usa il sistema di traccia NLog, Log4Net o System.Diagnostics non è necessario spostare le tracce in AI. Gli adattatori di accesso di Application Insights sono integrati nel normale processo di configurazione. Se si dispone già di uno di questi framework di registrazione configurato:
####Se Application Insights è già stato aggiunto
- Fare clic con il pulsante destro del mouse su Nodo progetto->Application Insights->Configura Application Insights. Assicurarsi che venga visualizzata l'opzione che consente di aggiungere l'adattatore appropriato nella finestra di configurazione. 
- Oppure, durante la compilazione della soluzione, nella finestra popup visualizzata nella parte superiore destra della schermata fare clic su Configura. ![Avviso popup](./media/app-insights-release-notes-vsix/LoggingToast.png)

Dopo aver installato l'adattatore di registrazione, è possibile eseguire l'applicazione e assicurarsi di visualizzare i dati nella scheda degli strumenti di diagnostica come indicato di seguito: ![Tracce](./media/app-insights-release-notes-vsix/Traces.png)
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

 

<!---HONumber=AcomDC_0211_2016-->