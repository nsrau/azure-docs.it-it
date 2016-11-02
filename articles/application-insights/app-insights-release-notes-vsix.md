<properties
	pageTitle="Note sulla versione per l'estensione di Visual Studio per Developer Analytics"
	description="Aggiornamenti più recenti per Strumenti di Visual Studio per Developer Analytics"
	services="application-insights"
    documentationCenter=""
	authors="acearun"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2016"
	ms.author="acearun"/>

# Note sulla versione per Strumenti di analisi per sviluppatori
Novità: analisi di Application Insights e di HockeyApp in Visual Studio.
## Versione 7.0
### Tendenze di Visual Studio Application Insights
Visual Studio Application Insights è un nuovo strumento di Visual Studio che consente di analizzare il funzionamento dell'app nel corso del tempo. Per iniziare, usando il pulsante della barra degli strumenti di **Application Insights** o la finestra Ricerca di Application Insights, scegliere **Esplora tendenze di telemetria**. Oppure scegliere **Altre finestre** dal menu **Visualizza** e quindi fare clic su **Tendenze di Application Insights**. Scegliere una delle cinque query più comuni per iniziare. È possibile analizzare set di dati diversi in base ai tipi di dati di telemetria, agli intervalli di tempo e ad altre proprietà. Per trovare le anomalie nei dati, scegliere una delle opzioni relative alle anomalie nell'elenco a discesa **Tipo di visualizzazione**. Le opzioni di filtro nella parte inferiore della finestra permettono di trovare facilmente subset specifici dei dati di telemetria.

![Application Insights Trends](./media/app-insights-release-notes-vsix/Trends.PNG)

### Eccezioni in CodeLens
La telemetria delle eccezioni viene ora visualizzata in CodeLens. Se il progetto è già connesso al servizio Application Insights, verrà visualizzato il numero di eccezioni che si sono verificate in ogni metodo nell'ambiente di produzione nelle ultime 24 ore. Da CodeLens è possibile passare a Ricerca o Tendenze per ricercare le eccezioni in modo più dettagliato.

![Eccezioni in CodeLens](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### Supporto di ASP.NET Core
Application Insights ora supporta i progetti ASP.NET Core RC2 in Visual Studio. È possibile aggiungere Application Insights ai nuovi progetti ASP.NET Core RC2 dalla finestra di dialogo **Nuovo progetto**, come nello screenshot seguente. Oppure è possibile aggiungerlo a un progetto esistente, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e quindi scegliere **Aggiungi Application Insights Telemetry**.

![Supporto di ASP.NET Core](./media/app-insights-release-notes-vsix/NetCoreSupport.PNG)

Inoltre, i progetti ASP.NET 5 RC1 e ASP.NET Core RC2 presentano un nuovo supporto nella finestra degli strumenti di diagnostica. In fase di debug locale sul PC, dall'app ASP.NET sarà possibile visualizzare gli eventi di Application Insights, quali richieste ed eccezioni. Da ogni evento fare clic su **Cerca** per eseguire il drill-down per ottenere altre informazioni.

![Supporto degli strumenti di diagnostica](./media/app-insights-release-notes-vsix/DiagnosticTools.PNG)

### HockeyApp per App Windows universali
Oltre ai commenti e suggerimenti degli utenti e alla distribuzione beta, HockeyApp fornisce la segnalazione degli arresti anomali con simboli per le App universali di Windows. L'aggiunta di HockeyApp SDK è ora ancora più semplice: fare clic con il pulsante destro del mouse sul progetto Windows universale e quindi scegliere **HockeyApp - Abilita Analisi arresti anomali**. In questo modo si installa l'SDK, si configura la raccolta di arresti anomali del sistema e si effettua il provisioning di una risorsa HockeyApp nel cloud, senza caricare l'app nel servizio HockeyApp.

Altre nuove funzionalità:

* L'esperienza di Ricerca di Application Insights è più veloce e intuitiva, grazie all'applicazione automatica degli intervalli di tempo e dei filtri di dettagli nel momento stesso in cui vengono selezionati.
* In Ricerca di Application Insights ora è disponibile anche un'opzione per passare al codice direttamente dai dati di telemetria della richiesta.
* Sono stati apportati miglioramenti all'esperienza di accesso a HockeyApp.
* In Strumenti di diagnostica vengono ora visualizzate le informazioni di telemetria della produzione per le eccezioni.

## Versione 5.2
Microsoft è lieta di annunciare l'introduzione di scenari di HockeyApp in Visual Studio. La prima integrazione è nella distribuzione beta delle app di Windows universale e delle app di Windows Form da Visual Studio.

Con la distribuzione beta, si caricano le versioni non definitive delle app in HockeyApp per distribuirle a un subset selezionato di clienti o tester. La distribuzione beta, combinata con le funzionalità di feedback degli utenti e di raccolta degli arresti anomali di HockeyApp, può fornire utili informazioni sull'app prima di effettuare un rilascio su vasta scala. È possibile usare queste informazioni per risolvere i problemi relativi all'app e poter così evitare o ridurre al minimo futuri problemi, ad esempio classificazioni delle app basse, feedback negativo e così via.

Caricare build per la distribuzione beta da Visual Studio è davvero semplice.
### App Windows universali
Il menu contestuale per il nodo di un progetto per un'app di Windows universale ora include un'opzione per caricare la build in HockeyApp.

![Menu contestuale del progetto per le app di Windows universale](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Scegliere la voce per aprire la finestra di dialogo di caricamento di HockeyApp. Per caricare la build, sarà necessario un account HockeyApp. I nuovi utenti potranno creare un account con un semplice processo.

Quando si è connessi, verrà visualizzato il form per il caricamento nella finestra di dialogo.

![Finestra di dialogo di caricamento per le app di Windows universale](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Selezionare il contenuto da caricare (un file APPXBUNDLE o APPX) e quindi scegliere le opzioni per la versione nella procedura guidata. Nella pagina successiva è possibile aggiungere le note sulla versione. Scegliere **Fine** per iniziare il caricamento.

Al termine del caricamento, viene visualizzata una notifica di HockeyApp con la conferma e un collegamento all'app nel portale di HockeyApp.

![Notifica di caricamento completato](./media/app-insights-release-notes-vsix/UploadComplete.png)

La procedura è terminata. Sono bastati pochi clic per caricare una build per la distribuzione beta.

È possibile gestire l'applicazione in diversi modi nel portale di HockeyApp. Ad esempio, è possibile invitare gli utenti, visualizzare i report sugli arresti anomali e il feedback, modificare i dettagli e così via.

![Portale di HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Per altri dettagli sulla gestione dell'app, vedere [HockeyApp Knowledge Base](http://support.hockeyapp.net/kb/app-management-2) (Knowledge Base di HockeyApp).

### App Windows Form
Il menu contestuale per il nodo di un progetto per Windows Form ora include un'opzione per caricare la build in HockeyApp.

![Menu contestuale del progetto per le app Windows Form](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Viene aperta la finestra di dialogo di caricamento di HockeyApp, simile a quella di un'app di Windows universale.

![Finestra di dialogo di caricamento per le app di Windows Form](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

In questa procedura guidata è presente un nuovo campo per specificare la versione dell'app. Per le app di Windows universale, le informazioni vengono inserite dal manifesto. Le app di Windows Form, purtroppo, non hanno una funzionalità equivalente. Sarà necessario specificarle manualmente.

Il resto del flusso è simile a quello delle app di Windows universale: scelta della build e delle opzioni per la versione, aggiunta delle note sulla versione, caricamento e gestione nel portale di HockeyApp.

È davvero semplice. È possibile provare e inviare commenti e suggerimenti.
## Versione 4.3
### Ricercare i dati di telemetria da sessioni di debug locali
In questa versione ora è possibile cercare i dati di telemetria di Application Insights generati nella sessione di debug di Visual Studio. Prima la ricerca era possibile soltanto se l'app era stata registrata in Application Insights. Ora, per cercare i dati di telemetria locali, è sufficiente che nell'app sia installato Application Insights SDK.

Se si ha un'applicazione ASP.NET con Application Insights SDK, seguire questa procedura per usare la ricerca.

1. Eseguire il debug dell'applicazione.
2. Aprire Ricerca di Application Insights in uno dei modi seguenti:
	- Scegliere **Altre finestre** dal menu **Visualizza** e quindi fare clic su **Ricerca di Application Insights**.
	- Fare clic sul pulsante della barra degli strumenti **Application Insights**.
	- In Esplora soluzioni espandere **ApplicationInsights.config** e quindi fare clic su **Cerca nei dati di telemetria della sessione di debug**.
3. Se non è stata eseguita l'iscrizione ad Application Insights, la finestra di ricerca viene aperta in modalità telemetria della sessione di debug.
4. Fare clic sull'icona **Cerca** per visualizzare i dati di telemetria locali.

![Caricamento completato](./media/app-insights-release-notes-vsix/LocalSearch.png)

## Versione 4.2
In questa versione sono state aggiunte funzionalità per semplificare la ricerca di dati nel contesto degli eventi, con la possibilità di passare al codice da altri eventi di dati e inviare i dati di registrazione in Application Insights. Questa estensione viene aggiornata ogni mese. Inviare eventuali commenti o suggerimenti a aidevtools@microsoft.com.
### Registrazione senza clic
Se si usa NLog, log4net o System.Diagnostics.Tracing, non è necessario spostare le tracce in Application Insights. In questa versione gli adattatori di registrazione di Application Insights sono stati integrati nel normale processo di configurazione. Se uno di questi framework di registrazione è già stato configurato, la sezione seguente illustra come ottenerlo. **Se Application Insights è già stato aggiunto:**
1. Fare clic con il pulsante destro del mouse sul nodo del progetto, scegliere **Application Insights** e quindi fare clic su **Configura Application Insights**. Verificare che venga visualizzata l'opzione che consente di aggiungere l'adattatore corretto nella finestra di configurazione.
2. In alternativa, durante la compilazione della soluzione, nella finestra popup visualizzata nella parte superiore destra della schermata fare clic su **Configura**.

![Notifica di registrazione](./media/app-insights-release-notes-vsix/LoggingToast.png)

Dopo aver installato l'adattatore di registrazione, eseguire l'applicazione e verificare di visualizzare i dati nella scheda degli strumenti di diagnostica, come di seguito:

![Tracce](./media/app-insights-release-notes-vsix/Traces.png)

### Trovare o passare al codice in cui viene generata la proprietà dell'evento di telemetria
Con la nuova versione l'utente può fare clic su qualsiasi valore nei dettagli dell'evento per ricercare una stringa corrispondente nella soluzione aperta. I risultati verranno visualizzati nell'elenco "Risultati ricerca" di Visual Studio come illustrato di seguito:

![Ricerca della corrispondenza](./media/app-insights-release-notes-vsix/FindMatch.png)

### Nuova finestra di ricerca da usare quando non si è connessi
La finestra Ricerca di Application Insights è stata modificata per consentire di cercare i dati mentre l'app è in fase di produzione.

![Finestra di ricerca](./media/app-insights-release-notes-vsix/SearchWindow.png)

### Visualizzare tutti gli eventi di telemetria associati all'evento
È stata aggiunta una nuova scheda, con query predefinite per tutti i dati correlati all'evento di telemetria visualizzato dall'utente, accanto alla scheda dei dettagli dell'evento. Ad esempio, una richiesta ha un campo denominato **ID operazione**. Ogni evento associato alla richiesta avrà lo stesso valore per **ID operazione**. Se si verifica un'eccezione mentre l'operazione sta elaborando la richiesta, all'eccezione viene assegnato lo stesso ID operazione della richiesta per facilitarne la ricerca. Se si sta visualizzando una richiesta, fare clic su **Tutti i dati di telemetria per questa operazione** per aprire una nuova scheda che visualizza i nuovi risultati della ricerca.

![Elementi correlati](./media/app-insights-release-notes-vsix/RelatedItems.png)

### Passaggio alla schermata successiva e ritorno all'ultima schermata nella finestra di ricerca
Ora è possibile passare alla schermata successiva o tornare a quella precedente dei risultati di ricerca.

![Tornare alla schermata precedente](./media/app-insights-release-notes-vsix/GoBAck.png)

## Versione 4.1
Questa versione include numerose nuove funzionalità e aggiornamenti. Per installare questa versione, è necessario avere installato Update 1.

### Passare da un'eccezione al metodo nel codice sorgente
Ora, se si visualizzano eccezioni dall'app di produzione nella finestra Ricerca di Application Insights, è possibile passare al metodo nel punto del codice in cui si sta verificando l'eccezione. È sufficiente caricare il progetto corretto e tutte le operazioni saranno eseguite da Application Insights. Per altre informazioni sulla finestra Ricerca di Application Insights, vedere le note sulla versione per la versione 4.0 nelle sezioni seguenti.

Come funziona? È possibile usare Ricerca di Applications Insights anche quando una soluzione non è aperta. L'area di analisi dello stack visualizza un messaggio informativo e molti elementi non saranno disponibili.

Se sono disponibili informazioni sui file, alcuni elementi potrebbero essere collegamenti, ma l'elemento con informazioni sulla soluzione sarà comunque visibile.

Se si fa clic sul collegamento ipertestuale, si passerà alla posizione del metodo selezionato nel codice. Potrebbe esserci una differenza nel numero di versione, ma la funzionalità per passare alla versione corretta del codice sarà presente nelle versioni successive.

![Fare clic sui dettagli dell'eccezione](./media/app-insights-release-notes-vsix/jumptocode.png)

### Nuovi punti di ingresso per l'esperienza di ricerca in Esplora soluzioni
Ora è possibile accedere alla funzionalità di ricerca da Esplora soluzioni.

![Eseguire la ricerca in Esplora soluzioni](./media/app-insights-release-notes-vsix/searchentry.png)

### Visualizzare una notifica al termine della pubblicazione
Dopo la pubblicazione del progetto online, verrà visualizzata una finestra di dialogo popup che consente di vedere i dati di Application Insights nell'ambiente di produzione.

![Notifica di pubblicazione completata](./media/app-insights-release-notes-vsix/publishtoast.png)

## Versione 4.0

### Cercare i dati di Application Insights da Visual Studio
Come per la funzione di ricerca nel portale di Application Insights, ora in Visual Studio è possibile filtrare e cercare tipi di eventi, valori delle proprietà e testo e quindi esaminare singoli eventi.

![Finestra di ricerca](./media/app-insights-release-notes-vsix/search.png)

### Visualizzare i dati del computer locale in Strumenti di diagnostica

È possibile visualizzare la telemetria, oltre ad altri dati di debug, nella pagina Strumenti di diagnostica di Visual Studio. È supportato solo ASP.NET 4.5.

![Pagina Strumenti di diagnostica](./media/app-insights-release-notes-vsix/diagtools.png)

### Aggiungere l'SDK al progetto senza avere accedere ad Azure

Non è più necessario accedere ad Azure per aggiungere pacchetti di Application Insights al progetto tramite la finestra di dialogo **Nuovo progetto** o dal menu di scelta rapida del progetto. Se si esegue l'accesso, l'SDK viene installato e configurato per l'invio di dati di telemetria al portale, come in precedenza. Se non esegue l'accesso, l'SDK viene aggiunto al progetto e genera i dati di telemetria per l'hub di diagnostica. Sarà possibile configurarlo in un secondo momento, se necessario.

![Finestra di dialogo Nuovo progetto](./media/app-insights-release-notes-vsix/newproject.png)

### Supporto dei dispositivi

Al *Connect();* 2015 [è stato annunciato](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) che l'esperienza per sviluppatori di dispositivi mobili è HockeyApp. HockeyApp consente di distribuire le build beta ai tester, raccogliere e analizzare tutti gli arresti anomali dell'app e raccogliere commenti e suggerimenti direttamente dai clienti. HockeyApp supporta l'app su qualsiasi piattaforma scelta per la compilazione, sia essa iOS, Android, Windows o una soluzione multipiattaforma come Xamarin, Cordova o Unity.

Nelle versioni future dell'estensione Application Insights sarà introdotta un'esperienza maggiormente integrata tra HockeyApp e Visual Studio. Per ora è possibile iniziare a usare HockeyApp semplicemente aggiungendo il riferimento NuGet. Per altre informazioni, vedere la [documentazione](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone).

<!---HONumber=AcomDC_0727_2016-->