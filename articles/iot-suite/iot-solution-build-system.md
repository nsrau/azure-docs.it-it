<properties 
	pageTitle="Esempio di IoT di Azure MyDriving - Compilazione | Microsoft Azure" 
	description="Compilazione di un'app che rappresenta una dimostrazione completa di come progettare un sistema IoT con Microsoft Azure che include l'analisi di flusso, Machine Learning e gli hub eventi." 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>


# Compilazione e distribuzione della soluzione MyDriving nell'ambiente

MyDriving è una soluzione di Internet delle cose (IoT) che esegue la raccolta di dati dall'automobile, li elabora con la Machine Learning e li fornisce al telefono cellulare. Il back-end è costituito da una varietà di servizi forniti da Microsoft Azure e i client possono essere telefoni Android, iOS o Windows 10.

La soluzione MyDriving è stata creata per offrire agli utenti la possibilità di iniziare a creare rapidamente il proprio sistema IoT. Dal repository è possibile ottenere gli script di Azure Resource Manager per distribuire l'architettura di back-end nel proprio account Azure, dove è possibile riconfigurare i vari servizi, modificare le query in base ai dati disponibili e così via. È possibile trovare questi script insieme al codice per l'app per dispositivi mobili, il progetto API del servizio app di Azure e altro ancora su <https://github.com/Azure-Samples/MyDriving>.

Agli utenti che non abbiano ancora provato l'app si consiglia di leggere la [Guida introduttiva](iot-solution-get-started.md).

## Cosa è necessario creare?

Una descrizione dettagliata dell'architettura è fornita nella [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs). In sintesi, sono disponibili numerose risorse utili per creare un progetto simile:

* **App client**. Viene eseguita su telefoni Android, iOS e Windows 10. Viene usata la piattaforma Xamarin per condividere la maggior parte del codice, che viene archiviato su GitHub in `src/MobileApp`. L'app esegue due funzioni distinte:
 * **Inoltro dei dati di telemetria** dal dispositivo OBD e dal proprio servizio di individuazione della posizione al back-end cloud del sistema.
 * **Interfaccia utente**, che consente agli utenti di eseguire query sui propri viaggi registrati.
* **Servizio cloud**, che inserisce i dati di viaggio in tempo reale e li elabora. L'attività principale della creazione di questo servizio consiste nella scelta, nella definizione dei parametri e nel collegamento a un'ampia gamma di servizi di Azure. Alcune parti richiedono script per filtrare ed elaborare i dati in ingresso. Viene usato un modello di **Gestione delle risorse di Azure (ARM)** per configurare tutte le parti.
* **App dei servizi mobili**. Si tratta del servizio Web usato dall'interfaccia utente dell'app per dispositivi. La sua funzione principale è l'esecuzione di query nel database di dati elaborati archiviati. Il relativo codice è su GitHub in `src/MobileAppService`.
* **Visual Studio con Xamarin** è l'ambiente di sviluppo. Xamarin, che esiste sia come componente di Visual Studio che come un IDE autonomo, viene usato per compilare il codice di dispositivi multipiattaforma. Per compilare il codice iOS, è necessario che un'istanza di Xamarin sia in esecuzione su un computer OSX; tuttavia, se necessario, può essere eseguito come un agente, gestito da Visual Studio.
* **Testing unità** delle app per dispositivi vengono eseguiti in Xamarin Test Cloud.
* **GitHub** è il repository in cui vengono archiviati il codice, gli script e i modelli.
* **Visual Studio Team Services** è un servizio cloud usato per gestire una compilazione continua ed eseguire il test del servizio Web e delle app per dispositivi.
* **HockeyApp** viene usato per distribuire le versioni del codice di dispositivo. Oltre a gestire la distribuzione, esegue la raccolta dei dati relativi agli arresti anomali del sistema e all'utilizzo e i commenti degli utenti.
* **Application Insights** monitora il servizio Web per dispositivi mobili.


Ora verrà esaminata la procedura. Si noti che molti dei passaggi sono facoltativi.

## Iscrizione per la creazione di account

-   [**Visual Studio Dev Essentials**](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Questo programma gratuito consente di accedere facilmente a molti strumenti di sviluppo e servizi tra cui Visual Studio, Visual Studio Team Services e Azure e fornisce un credito di 25 dollari al mese in Azure per dodici mesi. Include le sottoscrizioni al training Pluralsight e a Xamarin University. È anche possibile iscriversi separatamente per i livelli gratuiti di [Azure](https://azure.com) e [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), che tuttavia non forniscono crediti Azure.

-   [**HockeyApp**](https://rink.hockeyapp.net/) (facoltativo), per la gestione della distribuzione di test di app per dispositivi mobili e la raccolta di dati di telemetria.

-   [**Xamarin**](https://xamarin.com/) (obbligatorio), per la compilazione dell'app per dispositivi mobili e l'esecuzione di test e debug su [Xamarin Test Cloud](https://xamarin.com/test-cloud).

-   [**GitHub**](https://github.com/Azure-Samples/MyDriving/) (facoltativo), per creare repository pubblici per il proprio codice (i repository privati sono a pagamento). In alternativa, è possibile usare il piano Basic nei repository privati di Visual Studio Team Services.

-   [**Power BI**](https://powerbi.microsoft.com/) (facoltativo), per creare visualizzazioni dettagliate dei dati nell'intero sistema.

> [AZURE.NOTE] Non è necessario un account GitHub per accedere al codice MyDriving su [https://github.com/Azure-Samples/MyDriving](https://github.com/Azure-Samples/MyDriving).

## Installare gli strumenti di sviluppo

La configurazione seguente è destinata allo sviluppo della soluzione completa: app multipiattaforma iOS, Android e Windows Phone 10, con un back-end di Azure.

In alternativa, è possibile usare Xamarin Studio in Windows o Mac per sviluppare app per dispositivi mobili se non si sta usando il back-end di Azure.

[In questa pagina](https://msdn.microsoft.com/library/mt613162.aspx) è disponibile una descrizione più dettagliata di questa configurazione.

### Computer di sviluppo Windows

Lo strumento centrale in Windows è Visual Studio, per l'uso dell'app MyDriving per Android e Windows, del progetto di API del servizio app e delle estensioni del microservizio.

Xamarin, Git, emulatori e altri componenti utili sono tutti integrati in Visual Studio.

Installare:

-   [**Visual Studio 2015 con Xamarin**](https://www.visualstudio.com/products/visual-studio-community-vs) (qualsiasi edizione; l'edizione Community è gratuita)

-   [**SQLite per la piattaforma UWP (Universal Windows Platform)**](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Necessario per compilare il codice di Windows Phone 10.

-   [Azure SDK per VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409): offre l'SDK per le app in esecuzione in Azure, strumenti da riga di comando per la gestione di Azure.

-   [Azure Service Fabric SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Necessario per compilare l'estensione del [microservizio](../service-fabric/service-fabric-get-started.md).

Estensioni di Visual Studio: verificare che in Strumenti siano visualizzati Android, iOS, Xamarin e così via. In caso contrario, aprire **Pannello di controllo**, **Programmi e funzionalità**, **Microsoft** **Visual Studio 2015**, **Modifica**. In **Sviluppo di app per dispositivi mobili multipiattaforma** scegliere **C#/.Net (Xamarin).** Controllare anche che **GitHub** sia installato.

### Computer di sviluppo Mac

Il computer Mac (Yosemite o versione successiva) è necessario se si vuole sviluppare iOS. Anche se usiamo Visual Studio con Xamarin in Windows per sviluppare e gestire tutto il codice, Xamarin usa un agente installato in un computer Mac per compilare e firmare il codice iOS

![](./media/iot-solution-build-system/image1.png)

(in alternativa, è possibile usare Xamarin Studio direttamente nel computer Mac per sviluppare app multipiattaforma).

Il computer Mac non è necessario se non si vuole includere iOS come piattaforma di destinazione.

Installare:

-   [**Xamarin Studio** per iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). È possibile anche configurare Visual Studio e Xamarin in un computer Mac che esegue una macchina virtuale Windows. Vedere [Configurazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx) su MSDN.

-   (Facoltativo) [Strumenti di sviluppo Azure](https://azure.microsoft.com/downloads/).

Abilitare l'accesso remoto sul computer Mac. Aprire Preferenze di sistema, Condivisione e selezionare Login remoto.

Quando si apre un progetto iOS in Visual Studio su Windows, il plug-in Xamarin richiederà l'ID del computer Mac.

## Recuperare il repository GitHub

Recuperare una copia locale di <https://github.com/Azure-Samples/MyDriving> usando il pulsante **Download ZIP** in GitHub, Visual Studio o in un altro client Git.

Decomprimere il file in una cartella con un nome di percorso breve, ad esempio C:\\codice.

In alternativa, per rimanere aggiornati sul codice o fornire il proprio contributo, clonare il repository come segue:

> git clone <https://github.com/Azure-Samples/MyDriving.git>

## Codice dello sviluppatore di Bing Mappe

[Registrare una chiave API di Bing Mappe](https://msdn.microsoft.com/library/ff428642.aspx).

È necessario sostituire questo codice nella riga 22 in `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`



## Compilare l'app demo

In Visual Studio aprire le soluzioni seguenti:

-   src\\MobileApps\\MyDriving.sln
-   src\\MobileAppService\\MyDrivingService.sln
-   src\\Extensions\\ServiceFabric\\VINLookUpApplication\\VINLookUpApplication.sln

Verranno visualizzati prompt per:

-   Considerare attendibili alcuni progetti potenzialmente non attendibili. Per procedere, scegliere di aprirli.

-   Impostare la modalità sviluppatore se si usa un nuovo computer Windows 10.

-   Fornire le credenziali Xamarin.

-   Connettersi al computer Mac Xamarin. Se non è disponibile un computer Mac, fare clic sul progetto iOS in Visual Studio e scegliere **Scarica progetto**.

Ricompilare la soluzione.

### Problemi di compilazione

Ecco alcuni comportamenti anomali che sono stati a volte riscontrati:

-   Il progetto VINLookupApplication non viene caricato: assicurarsi che [Azure SDK per VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) sia installato.

-   Il progetto di Service Fabric non viene compilato: compilare prima di tutto i progetti di interfaccia e assicurarsi che Service Fabric SDK sia installato.

-   L'app Android non viene compilata:

    -   Aprire Strumenti, Android, Android SDK Manager e verificare che la piattaforma Android 6 (API 23)/SDK sia installata.

    -   Eliminare questa directory e quindi ricompilare:<br/> `%LocalAppData%\Xamarin\zips`

## Conoscere il codice

In questa soluzione sono disponibili:

-   Estensioni Azure

    -   Service Fabric

-   HDInsight: script per l'elaborazione dei dati di viaggio in Azure

-   App per dispositivi mobili: le app per dispositivi

-   MobileAppsService/MyDrivingService: il back-end Web

-   Power BI: definizione di report di Power BI

-   Script

    -   ARM: modelli per compilare le risorse di Azure

    -   PowerShell: script per eseguire i modelli ARM

    -   SQLDatabase: debug dei database

-   SQLDatabase

    -   CreateTables: definizioni dello schema

-   StreamingAnalytics: le query che consentono di trasformare il flusso di dati in ingresso

## Eseguire le app in modalità di sviluppo

### Back-end

Impostare MyDrivingService come progetto di avvio, quindi premere F5 per eseguire il servizio Web back-end. Verrà aperta una visualizzazione browser dell'elenco delle API.

### Client mobili

Le [app per dispositivi mobili vengono sviluppate in Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).

-   [Debug di Android in Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)

### iOS

-   [Debug in iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)

### Windows Phone

-   [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)


## HockeyApp

HockeyApp gestisce la distribuzione dell'app Android, iOS o Windows nel dispositivo agli utenti di test, inviando loro notifiche relative alla disponibilità di nuove versioni. Raccoglie anche utili segnalazioni di arresti anomali del sistema, commenti degli utenti con schermate, nonché metriche di utilizzo.

[Iniziare con il caricamento](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) dell'app di compilazione. Quindi, accedere a HockeyApp (<https://rink.hockeyapp.net>) dal computer di sviluppo. Nel dashboard sviluppatore fare clic su **Nuova app** e trascinare i file compilati nella finestra (in un secondo momento è possibile automatizzare il servizio di compilazione per eseguire questa operazione).

Viene visualizzato il dashboard dell'app.

![](./media/iot-solution-build-system/image2.png)

Ripetere il processo per ogni piattaforma in cui viene eseguita l'app.

- Usare l'[ID app](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) del dashboard per inviare dati di arresto anomalo del sistema e commenti dall'app. In MyDriving, aggiornare gli ID in src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs 
-   [**Invitare utenti di test**](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Si riceverà un URL per selezionare gli utenti di test, che potranno registrarsi al team, scaricare l'app e inviare i propri commenti.

-   Se invece si preferisce una versione beta più aperta, impostare la distribuzione su Public: fare clic su **Manage App, Distribution, Download = Public.** In questo modo tutti gli utenti possono scaricare l'app e inviare commenti, oltre a visualizzare una notifica quando viene pubblicata una nuova versione, e si potrebbe anche ricevere da loro alcune segnalazioni di arresti anomali del sistema. ![](./media/iot-solution-build-system/image3.png)

-   [**Per collegare le segnalazioni di arresti anomali del sistema a Visual Studio Team Services**](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs): fare clic su **Manage App, Visual Studio Team Services**. HockeyApp riesce a creare automaticamente gli elementi di lavoro in Team Services quando sono presenti segnalazioni di arresti anomali del sistema o si ricevono commenti.

> Altre informazioni sono disponibili all'indirizzo <https://hockeyapp.net>.

## Xamarin Test Cloud

[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatizza i test dell'interfaccia utente in dispositivi reali nel cloud. Con il framework NUnit è possibile scrivere test che eseguono l'app con l'interfaccia utente.

Per usare Xamarin, incorporare l'SDK [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) nell'app, che viene fornito come pacchetto NuGet. È disponibile nell'app demo ed è incluso quando si creano nuovi progetti di test con i modelli Xamarin.

![](./media/iot-solution-build-system/image4.png)

Un progetto di test di esempio è incluso con l'app nel repository; in [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService) cercare in [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/

Se si usa il servizio di compilazione di Visual Studio Team Services, è facile scrivere unit test dell'interfaccia utente di Xamarin ed eseguirli come parte della compilazione.

## Distribuire servizi di Azure

Consultare le istruzioni dettagliate in **scripts/README.md** per eseguire una distribuzione automatica dei servizi di Azure e dei servizi di compilazione di Team Build.

### Quali attività vengono effettuate dallo script di distribuzione?

Microsoft Azure offre un'ampia gamma di servizi diversi che è possibile usare per compilare applicazioni cloud. Anche se molti di essi possono essere usati singolarmente (il servizio app/app Web), producono i risultati migliori quando vengono interconnessi per formare un sistema integrato che viene usato in MyDriving.

È possibile creare e connettere manualmente i servizi di Azure, ma una procedura molto più veloce e affidabile prevede l'uso dei modelli Azure Resource Manager (ARM). [ARM](../resource-group-overview.md) automatizza la distribuzione delle risorse di una soluzione e la creazione di interconnessioni tra di esse.

Il modello per il sistema MyDriving è disponibile nel repository GitHub in [Scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Fornisce una vista completa e concisa della modalità di interconnessione dei vari servizi nell'architettura. Tali servizi vengono descritti in dettaglio nella [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs), ma è possibile acquisire molte informazioni anche leggendo il modello stesso.

> [AZURE.NOTE] La maggior parte dei servizi di Azure comporta l'applicazione di un costo associato che dipende dal piano tariffario. Se non si conosce Azure, è possibile [provarlo gratuitamente](https://azure.microsoft.com/free/). Se, invece, non si intende usare alcuni componenti nel sistema MyDriving, assicurarsi di rimuoverli in modo da evitare di incorrere in costi. Vedere *Costi operativi stimati* di seguito per un riepilogo delle spese di servizio standard.

#### Modificare il modello

Per personalizzare la distribuzione, ad esempio per rimuovere componenti non necessari o aggiungerne altri, prima di tutto creare copie di scenario\_complete.params.json e scenario\_complete.json in cui apportare le modifiche.

Il file scenario\_complete.params.json consente di eseguire l'override di diversi valori predefiniti, ad esempio lo SKU del servizio o il tipo di replica di archiviazione come descritto nella tabella seguente. I valori predefiniti corrispondono alle opzioni di costo più basse.

| **Parametro** | **Descrizione** | **Valore predefinito** |
|--------|---------|-------|
| SKU per hub IoT | Livello per il servizio hub IoT di Azure | F1 |
| Tipo di account di archiviazione | Tipo di replica di archiviazione | Archiviazione con ridondanza locale Standard |
| Obiettivo del servizio SQL | Utilizzo di slot di concorrenza | DW100 |
| SKU del piano di hosting | Piano di servizio per il servizio app | F1 |

In scenario\_complete.json:

-   Cercare "baseName" e modificarne il nome usandone uno di propria scelta.

-   Cercare "Create": ognuna di queste sezioni crea una risorsa.

-   Impostare sqlServerAdminLogin e sqlServerAdminPassword su valori appropriati.

-   Prima di eliminare una sezione che crea una risorsa, controllare l'eventuale presenza di elementi dipendenti effettuando la ricerca del nome della sezione in altre parti del file. Si noti che ogni sezione che crea un servizio include una sezione *dependsOn* che elenca le relative dipendenze.

Ecco gli elementi configurati dal modello; i dettagli sono forniti nella [guida di riferimento](http://aka.ms/mydrivingdocs):

| **Servizio** | **Descrizione e dettagli**  
|---|----
| Account di archiviazione | Il modello crea tre account:                                                                                                                                                                       
||- Un database SQL che riceve i dati di telemetria aggregati da Analisi di flusso e funge da archivio di backup per le tabelle del servizio app di Azure che espongono tali dati usando gli endpoint dell'API.                      
| | - L'archivio BLOB che accumula i dati cronologici di un altro processo di Analisi di flusso, che verrà elaborato da HDInisight.                                                                                         
| | - Un database SQL che riceve i risultati elaborati da HDInsight per l'uso con Power BI.                                                                                                                 
| Hub IoT | Stabilisce una connessione bidirezionale per ogni dispositivo connesso. Nella soluzione MyDriving l'app mobile funge da gateway campo per inviare dati all'hub IoT. L'hub IoT funge quindi da input in Analisi di flusso. |
| Hub eventi | Un output per un processo di Analisi di flusso che accoda l'output nelle estensioni create con Azure Service Fabric.                                                                                               
| SQL Data Warehouse |                                                                                                                                                                                                            
| Processi di Analisi di flusso di Azure | Connettere l'input e output con una query, che viene usata per aggregare i dati in tempo reale e i dati cronologici per le API del servizio App, Machine Learning, le estensioni e Power BI.                               
| Area di lavoro di Machine Learning | Include esperimenti, codice R e servizio API.                                                                                                                                                              
| Data factory | Ripetizione del training di Machine Learning pianificata.                                                                                                                                                                     
| Piano di hosting di Service Fabric | Per le estensioni.                                                                                                                                                                                            
| Servizio app ("App per dispositivi mobili") | Ospita il progetto API per dispositivi mobili che fornisce gli endpoint per l'app per dispositivi mobili. Il codice dell'API deve essere distribuito nel servizio app da Visual Studio.                                                         
| Regole di avviso | Invia un messaggio di posta elettronica se le risposte delle app indicano errori.                                                                                                                                            
| Application Insights | Per il monitoraggio delle prestazioni delle API nel servizio app. È necessario configurare la connessione in Visual Studio.                                                                                          
| Insieme di credenziali di chiave | Per salvare il certificato del cluster del servizio Web.                                                                                                                                                                

#### Eseguire il modello

Sono disponibili **istruzioni dettagliate in scripts/README.md**

Per eseguire il provisioning di tutti questi servizi nel proprio account Azure usando lo script, effettuare una delle operazioni seguenti:

-   Usando PowerShell:

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *location* è la [località di Azure](https://azure.microsoft.com/regions/) ad esempio "Europa settentrionale" o "Stati Uniti occidentali". Usare `Get-AzureLocation` per trovare un elenco delle località disponibili.

 -   *resourceGroupName* è il nome che verrà assegnato al gruppo al quale apparterranno tutte le risorse. Dopo aver usato le risorse, sarà possibile eliminarle tutte contemporaneamente eliminando questo gruppo.

-   Eseguire DeploymentScripts/Bash/deploy.sh con Bash.

-   Aprire e compilare la soluzione di Visual Studio DeploymentScripts/VS/DeployARM.sln

Si noti che ogni volta che viene eseguito il modello, viene creato un nuovo set di risorse con nuovi nomi. Per eliminare le risorse, passare al portale ed eliminare il gruppo di risorse.

Se per qualsiasi motivo lo script non riesce, è possibile eseguirlo nuovamente.

Lo script consente di configurare l'integrazione continua in Visual Studio Team Services. Se è stato configurato un progetto Team Services, sarà disponibile un URL https://yourAccountName.visualstudio.com. Immettere l'URL completo quando viene richiesto. È possibile assegnare all'URL un nome nuovo o esistente per un progetto Team Services.



## Visual Studio Team Services

In questo progetto viene usato Team Services principalmente per le relative funzionalità di compilazione e test. Fornisce anche un supporto eccellente per la collaborazione, ad esempio gestione delle attività con le bacheche Kanban, revisione del codice integrata con attività e controllo del codice sorgente, compilazioni gestite e altro ancora. Si integra bene con altri strumenti, ad esempio GitHub, Xamarin, HockeyApp e, naturalmente, Visual Studio. È possibile accedervi con l'interfaccia Web o Visual Studio, a seconda di quale sia la scelta più comoda in un dato momento.

I passaggi contenuti nelle definizioni di compilazione e di versione usano un'ampia gamma di servizi plug-in che sono disponibili nel [Marketplace](https://marketplace.visualstudio.com/VSTS) di Team Services. Oltre alle utilità di base per eseguire le righe di comando o copiare i file, esistono servizi che richiamano le compilazioni di Xamarin, Android e di altri fornitori e che si interfacciano con HockeyApp.

![](./media/iot-solution-build-system/image5.png)

### Definizioni di compilazione

Sono disponibili definizioni di compilazione per ognuno degli obiettivi principali e variazioni per i test delle funzionalità e di regressione. Le definizioni sono le seguenti:

-   MyDriving.Services: l'app Web back-end per l'app per dispositivi mobili

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android: funzionalità

    -   MyDriving.Xamarin.Android: regressione

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS: funzionalità

    -   MyDriving.Xamarin.iOS: regressione

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP: funzionalità

    -   MyDriving.Xamarin.UWP: regressione

Per visualizzare i dettagli completi della configurazione, vedere la sezione 4.7 relativa alla *configurazione della build e del rilascio* della [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs). Seguono lo stesso modello generale:

1.  Ripristino dei pacchetti NuGet. Il codice compilato non viene mantenuto nel repository, quindi i primi passaggi di ogni compilazione prevedono il ripristino dei pacchetti NuGet necessari.

2.  Attivazione della licenza. La compilazione viene eseguita nel cloud; quindi, nei casi in cui sia necessaria una licenza, in particolare per il servizio di compilazione Xamarin, è necessario attivare la licenza nel computer di compilazione corrente. Subito dopo è necessario disattivarla per consentirne l'uso in un altro computer.

3.  Compilazione con il servizio appropriato. Per le app per dispositivi mobili vengono usate le compilazioni di Xamarin mentre per il servizio Web back-end viene usata la compilazione di Visual Studio.

4.  Test della compilazione

5.  Esecuzione dei test. I test delle app per dispositivi mobili vengono eseguiti in Cloud Test Xamarin.

6.  Pubblicazione dei risultati della compilazione nel percorso di rilascio.

Il trigger per le compilazioni principali è impostato su Integrazione continua; ciò significa che la compilazione viene eseguita ogni volta che il codice viene archiviato nel ramo master.

![](./media/iot-solution-build-system/image6.png)

### Definizioni di versione

Le definizioni di versione vengono configurate nello stesso modo.

Per il servizio Web, la distribuzione viene configurata come un'app Web di Azure:

![](./media/iot-solution-build-system/image7.png)

Impostare il trigger di rilascio sulla distribuzione continua; ciò significa che ogni archiviazione viene seguita dai risultati di una compilazione riuscita in un aggiornamento dell'app Web:

![](./media/iot-solution-build-system/image8.png)

Per le app per dispositivi mobili la distribuzione viene effettuata in HockeyApp: ![](./media/iot-solution-build-system/image9.png)

## Application Insights

[Application Insights](../application-insights/app-insights-overview.md) raccoglie dati di telemetria sulle prestazioni e utilizzo dei servizi Web. Application Insights SDK invia dati di telemetria dal servizio alla risorsa di Application Insights in Azure.

Passare alla risorsa di Application Insights configurata dal modello, dove è possibile esplorare i grafici delle prestazioni del [progetto del servizio app per dispositivi mobili](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Nei grafici sono mostrate le richieste di server e i tempi di risposta, gli errori e i conteggi delle eccezioni. Sono anche disponibili i grafici dei tempi di risposta delle dipendenze, ovvero chiamate al database e alle API REST, ad esempio Machine Learning. Se sono presenti problemi di prestazioni, sarà possibile vedere quale parte del sistema ne è la causa.

![Esempio di grafico delle prestazioni](./media/iot-solution-build-system/image11.png)

Se è presente un servizio Web configurato manualmente, è possibile ottenere facilmente gli stessi grafici:

![Nel pannello del servizio Web, scegliere Strumenti, Estensioni, Aggiungi. Selezionare Application Insights.](./media/iot-solution-build-system/image12.png)

La funzionalità diventa operativa instrumentando l'applicazione con Application Insights SDK.

È possibile aggiungere dati di telemetria personalizzati (o instrumentare un'applicazione in esecuzione all'esterno di Azure) [aggiungendo Application Insights SDK](../application-insights/app-insights-asp-net.md) in fase di sviluppo. Si tratta di una funzionalità utile per registrare le metriche che dipendono dall'applicazione, ad esempio la lunghezza media del viaggio o il chilometraggio totale degli utenti.

![In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere Aggiungi Application Insights.](./media/iot-solution-build-system/image10.png)

Application Insights invia avvisi di posta elettronica se rileva numeri insoliti di risposte di errore ed è anche possibile impostare propri avvisi su varie metriche, ad esempio i tempi di risposta.

Per verificare che il servizio Web sia sempre attivo e in esecuzione, è possibile impostare [test di disponibilità](../application-insights/app-insights-monitor-web-app-availability.md), che eseguono il ping del sito da diverse posizioni in tutto il mondo ogni 15 minuti. Anche in questo caso si riceverà un messaggio di posta elettronica in caso di problemi.



## Costi operativi stimati

L'esecuzione di un'app come questa su scala ridotta è molto conveniente. Per molti dei servizi sono disponibili livelli di base gratuiti, in modo da ridurre al minimo i costi di sviluppo e operazione su scala ridotta. E, naturalmente, non è necessario che le app usino tutte le funzionalità illustrate in MyDriving.

Ecco una stima approssimativa dei costi di impostazione relativi alla configurazione dello sviluppo per MyDriving.

Si notino anche alcune alternative che *non* sono state usate. #

Si presuppone quanto segue:

-   Un team di non più di cinque membri (ai quali si aggiungono le parti interessate che hanno un ruolo di osservatori).

-   Esecuzione per circa un mese.

-   100 utenti che effettuano 4 viaggi al giorno.

>[AZURE.NOTE] Se non si ha familiarità con Azure, è disponibile un [account di valutazione gratuita](https://azure.microsoft.com/free/).

| **Servizio/Componente** | **Note** | **Costo/mese** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) con [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>Ambiente di sviluppo multipiattaforma.| Visual Studio Community. <br/>(richiede [VS Pro](https://www.visualstudio.com/vs-2015-product-editions) per [Xamarin.Forms](https://xamarin.com/forms), per progettare la multipiattaforma da una singola codebase). | $ 0 |
| [Hub IoT Azure](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Connessione bidirezionale ai dispositivi. | 8\.000 messaggi + 0,5 KB/messaggio gratuito | $ 0 |
| [Analisi di flusso](https://azure.microsoft.com/pricing/details/stream-analytics/) <br/> Elaborazione di volumi elevati di dati del flusso. | Costo di $ 0,031 per unità di streaming all'ora, quando è abilitata. Scegliere il numero di unità di streaming desiderato; aumentarlo per aumentare le prestazioni. | $ 23 |
| [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Risposte adattive. | $ 10/postazione/mese<br/> + 3 ore esperimento * $ 1/ora esperimento <br/> + 3,5 ore CPU API * $ 2/ora CPU produzione <br/> Il tempo della CPU API presuppone 5 min/giorno di ripetizione del training, anche se questo valore potrebbe aumentare con l'incremento dei dati di input; <br/> + assegnazione dei punteggi di 2 min/giorno per elaborare 400 viaggi/giorno. | $ 20 |
| [Servizio app di Azure](https://azure.microsoft.com/pricing/details/app-service/) <br/> Back-end mobile host | Livello B1: app Web di produzione | $ 56 |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/) <br/> Gestione della compilazione, delle unit test e del rilascio. Gestione delle attività. | Agenti privati, 5 utenti| $ 0 |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Monitoraggio delle prestazioni e utilizzo di servizi Web e siti.| Livello gratuito | $ 0 |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Distribuzione delle app beta, raccolta di commenti, dati sull'utilizzo e l'arresto anomalo del sistema. | Due app gratuite per i nuovi utenti.<br/> In seguito, $ 30 dollari/al mese. | $ 0 |
| [Xamarin](https://store.xamarin.com/)<br/> Codice su una piattaforma uniforme per più dispositivi. | Versione di valutazione gratuita. <br/>In seguito, 25 dollari/mese.| $ 0 |
| [Istanza SQL](https://azure.microsoft.com/pricing/details/sql-database/) per il servizio app Di Azure| Piano Basic: modello di database singolo | $ 5 |
| [Service Fabric](../service-fabric/services/service-fabric.md) (facoltativo) | Eseguire un cluster locale | $ 0 |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Visualizzazioni versatili e analisi dei dati di flusso e statici.| Piano Gratuito: 1 GB, 10.000 righe/ora, aggiornamento giornaliero <br/> # $ 10/utente/mese per [limiti più elevati](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), altre opzioni di connessione, collaborazione | $ 0 |
| [Archiviazione](https://azure.microsoft.com/pricing/details/storage/) | L (localmente ridondante) &lt; 100 G $ 0,024/GB | $ 3 |
| [Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) | $ 0,60 per attività * (8 - 5 FOC)| $ 2 |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) (cluster on demand) <br/> Per la ripetizione giornaliera del training | 3 nodi A3 a $ 0,32/ora per 1 ora ogni giorno X 31 giorni | $ 30 |
| [Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/) | Basic con unità elaborate di $ 11/mese + ingresso $ 0,028 | $ 11 |
| Adattatore OBD || $ 12 |
| **Totale**| | **$ 157** |

Vedere anche la pagina relativa alla

-   Riepilogo di [limiti e quote dei servizi di Azure](../azure-subscription-service-limits/#iot-hub-limits).

-   [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure



## Guida di riferimento

Si consiglia di leggere la [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs), che è una descrizione completa della progettazione del sistema e dei relativi componenti.


## Commenti e suggerimenti 

MyDriving è stato creato per aiutare gli utenti a usare rapidamente i propri sistemi IoT. Per questo motivo sarà molto apprezzato qualsiasi commento sul funzionamento. Inviare commenti in caso di problemi o difficoltà, se un punto di estensione renderebbe la soluzione più adatta a uno specifico scenario, se si trova un modo più efficace per soddisfare particolari esigenze o se si hanno altri suggerimenti per migliorare MyDriving o questa documentazione.

È anche possibile inviare un [problema su GitHub] o lasciare un commento qui sotto (versione it-IT).

Tutti i commenti e suggerimenti saranno apprezzati.

<!---HONumber=AcomDC_0406_2016-->