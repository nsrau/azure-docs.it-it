<properties
	pageTitle="Esempio di IoT di Azure MyDriving - Compilazione | Microsoft Azure"
	description="Compilare un'app che rappresenta una dimostrazione completa di come progettare un sistema IoT con Microsoft Azure, inclusi analisi di flusso, Machine Learning e hub eventi."
	services=""
    documentationCenter=".net"
    suite=""
	authors="harikmenon"
	manager="douge"/>

<tags
	ms.service="multiple"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="harikm"/>


# Compilare e distribuire la soluzione MyDriving nell'ambiente

MyDriving è una soluzione di Internet delle cose (IoT) che raccoglie dati dall'automobile, li elabora con Machine Learning e li presenta sul telefono cellulare. Il back-end è costituito da un'ampia gamma di servizi offerti da Microsoft Azure. I client possono essere telefoni Android, iOS o Windows 10.

La soluzione MyDriving è stata creata per offrire agli utenti la possibilità di iniziare a creare rapidamente il proprio sistema IoT. Dal [repository MyDriving in GitHub](https://github.com/Azure-Samples/MyDriving) è possibile ottenere gli script di Azure Resource Manager per distribuire l'architettura di back-end nel proprio account Azure. Sarà quindi possibile riconfigurare i diversi servizi, modificare le query in base ai propri dati e così via. Questi script, il codice per l'app per dispositivi mobili, il progetto API del servizio app di Azure e altro ancora sono disponibili nel repository MyDriving.

Agli utenti che non abbiano ancora provato l'app si consiglia di leggere la [guida introduttiva](iot-solution-get-started.md).

Una descrizione dettagliata dell'architettura è fornita nella [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs). Sono in sintesi disponibili numerose risorse utili per creare un progetto simile:

* Un'**app client** viene eseguita su telefoni Android, iOS e Windows 10. Viene usata la piattaforma Xamarin per condividere la maggior parte del codice, che viene archiviato su GitHub in `src/MobileApp`. L'app esegue due funzioni distinte:
 * Inoltra dati di telemetria dal dispositivo di diagnostica di bordo (OBD) e dal proprio servizio di posizionamento al back-end cloud del sistema.
 * Si tratta di un'interfaccia utente che consente di eseguire query sui dati dei viaggi su strada registrati.
* Un **servizio cloud** inserisce i dati dei viaggi su strada in tempo reale e li elabora. L'attività principale della creazione di questo servizio consiste nella scelta, nella definizione dei parametri e nel collegamento di un'ampia gamma di servizi di Azure. Alcune parti richiedono script per filtrare ed elaborare i dati in ingresso. Viene usato un modello di Azure Resource Manager per configurare tutte le parti.
* Un'**app per servizi mobili** rappresenta il servizio Web usato dall'interfaccia utente dell'app per dispositivi. La sua funzione principale è l'esecuzione di query sul database di dati elaborati archiviati. Il relativo codice è disponibile su GitHub in `src/MobileAppService`.
* L'ambiente di sviluppo è **Visual Studio con Xamarin**. Xamarin, disponibile sia come componente di Visual Studio, sia come ambiente di sviluppo integrato (IDE) autonomo, viene usato per compilare il codice di dispositivi multipiattaforma. Per compilare il codice iOS è necessario disporre di un'istanza di Xamarin in esecuzione in un computer OS X. Se necessario, Xamarin può essere eseguito come agente gestito da Visual Studio.
* Gli **unit test** delle app per dispositivi vengono eseguiti in Xamarin Test Cloud.
* **GitHub** è il repository in cui vengono archiviati il codice, gli script e i modelli.
* **Visual Studio Team Services** è un servizio cloud usato per gestire le continue attività di compilazione e test del servizio Web e delle app per dispositivi.
* **HockeyApp** viene usato per distribuire le versioni del codice di dispositivo. Raccoglie anche report di utilizzo e arresto anomalo e commenti degli utenti.
* **Visual Studio Application Insights** monitora il servizio Web per dispositivi mobili.

Ora verrà esaminata la procedura. Si noti che molti dei passaggi sono facoltativi.

## Iscrizione per la creazione di account

-   [Visual Studio Dev Essentials](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Questo programma gratuito consente di accedere facilmente a molti strumenti e servizi di sviluppo tra cui Visual Studio, Visual Studio Team Services e Azure. Offre un credito di $ 25 al mese per Azure per dodici mesi. Include le sottoscrizioni al training Pluralsight e a Xamarin University. È anche possibile iscriversi separatamente per accedere ai livelli gratuiti di [Azure](https://azure.com) e [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), che tuttavia non offrono crediti Azure.

-   [HockeyApp](https://rink.hockeyapp.net/) (facoltativo) per gestire la distribuzione di test di app per dispositivi mobili e raccogliere dati di telemetria.

-   [Xamarin](https://xamarin.com/) (obbligatorio) per compilare le app per dispositivi mobili ed eseguire test e debug su [Xamarin Test Cloud](https://xamarin.com/test-cloud).

-   [GitHub](https://github.com/Azure-Samples/MyDriving/) (facoltativo) per creare repository pubblici gratuiti per il proprio codice. I repository privati sono a pagamento. In alternativa, per i repository privati è possibile usare il piano Basic in Visual Studio Team Services.

-   [Power BI](https://powerbi.microsoft.com/) (facoltativo) per creare visualizzazioni avanzate dei dati nell'intero sistema.

> [AZURE.NOTE] Non è necessario un account GitHub per accedere al codice MyDriving nel [repository MyDriving in GitHub](https://github.com/Azure-Samples/MyDriving).

## Installare gli strumenti di sviluppo

La configurazione seguente è destinata allo sviluppo della soluzione completa: un'app multipiattaforma per iOS, Android e Windows 10 Mobile con un back-end di Azure.

In alternativa è possibile usare Xamarin Studio in Mac o Windows per sviluppare app per dispositivi mobili, se non si usa il back-end di Azure.

Vedere qui per una [descrizione più dettagliata di questa configurazione](https://msdn.microsoft.com/library/mt613162.aspx).

### Computer di sviluppo Windows

Lo strumento centrale in Windows è Visual Studio, per l'uso dell'app MyDriving per Android e Windows, del progetto di API del servizio app e delle estensioni del microservizio.

Xamarin, Git, emulatori e altri componenti utili sono tutti integrati in Visual Studio.

Installare:

-   [Visual Studio 2015 con Xamarin](https://www.visualstudio.com/products/visual-studio-community-vs) (qualsiasi edizione; l'edizione Community è gratuita).

-   [SQLite per la piattaforma UWP (Universal Windows Platform)](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Necessario per compilare il codice di Windows 10 Mobile.

-   [Azure SDK per Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409). Offre l'SDK per l'esecuzione di app in Azure e strumenti da riga di comando per la gestione di Azure.

-   [Azure Service Fabric SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Necessario per compilare l'estensione del [microservizio](../service-fabric/service-fabric-get-started.md).

Verificare anche che siano disponibili le estensioni di Visual Studio corrette. Verificare che in **Strumenti** siano visualizzati **Android, iOS, Xamarin e così via**. In caso contrario aprire il Pannello di controllo e quindi selezionare **Programmi e funzionalità** > **Microsoft** > **Visual Studio 2015** > **Modifica**. In **Sviluppo di app per dispositivi mobili multipiattaforma** scegliere **C#/.Net (Xamarin)**. Verificare anche che **GitHub per Windows** sia installato.

### Computer di sviluppo Mac

Il computer Mac (Yosemite o versione successiva) è necessario se si intende sviluppare per iOS. Anche se usiamo Visual Studio con Xamarin in Windows per sviluppare e gestire tutto il codice, Xamarin usa un agente installato in un computer Mac per compilare e firmare il codice iOS

![Sviluppare in Windows e compilare in Mac](./media/iot-solution-build-system/image1.png)

(in alternativa, è possibile usare Xamarin Studio direttamente nel computer Mac per sviluppare app multipiattaforma).

Il computer Mac non è necessario se non si vuole includere iOS come piattaforma di destinazione.

Installare:

-   [Xamarin Studio per iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). È anche possibile configurare Visual Studio e Xamarin in un computer Mac che esegue una macchina virtuale Windows. Vedere [Configurazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx) su MSDN.

-   [Strumenti di sviluppo Azure](https://azure.microsoft.com/downloads/) (facoltativo).

Abilitare l'accesso remoto sul computer Mac. Aprire **Preferenze di Sistema** > **Condivisione** e quindi selezionare **Login remoto**.

Quando si apre un progetto iOS in Visual Studio su Windows, il plug-in Xamarin richiederà l'ID del computer Mac.

## Recuperare il repository GitHub

Recuperare una copia locale del [repository MyDriving di GitHub](https://github.com/Azure-Samples/MyDriving) usando il pulsante **Download ZIP** in GitHub, Visual Studio o un altro client Git.

Decomprimere il file in una cartella con un nome di percorso breve, ad esempio C:\\codice.

In alternativa, per rimanere aggiornati sul codice o fornire il proprio contributo, clonare il repository come segue:

**git clone https://github.com/Azure-Samples/MyDriving.git**

## Ottenere una chiave API di Bing Mappe

[Eseguire la registrazione per ottenere una chiave API di Bing Mappe](https://msdn.microsoft.com/library/ff428642.aspx).

È necessario sostituire questo codice nella riga 22 in `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`.



## Compilare l'app demo

In Visual Studio aprire le soluzioni seguenti:

-   src\\MobileApps\\MyDriving.sln

-   src\\MobileAppService\\MyDrivingService.sln

-   src\\Extensions\\ServiceFabric\\VINLookUpApplication\\VINLookUpApplication.sln

Verranno visualizzati prompt per:

-   Considerare attendibili alcuni progetti potenzialmente non attendibili. Per procedere, scegliere di aprirli.

-   Impostare la modalità sviluppatore se si usa un nuovo computer Windows 10.

-   Fornire le credenziali Xamarin.

-   Connettersi al computer Mac Xamarin. Se non si usa un computer Mac, fare clic con il pulsante destro del mouse sul progetto iOS in Visual Studio e quindi scegliere **Scarica progetto**.

Ricompilare la soluzione.

In caso di problemi con la compilazione, provare queste soluzioni per comportamenti anomali:

-   *Il progetto VINLookupApplication non viene caricato*: verificare che sia installato [Azure SDK per Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   *Il progetto di Service Fabric non viene compilato*: compilare prima i progetti di interfaccia e verificare che Service Fabric SDK sia installato.

-   *L'app per Android non viene compilata*:

    -   Aprire **Strumenti** > **Android** > **Android SDK Manager** e quindi verificare che la piattaforma Android 6 (API 23)/SDK sia installata.

    -   Eliminare questa directory e quindi ricompilare:<br/> `%LocalAppData%\Xamarin\zips`

## Conoscere il codice

In questa soluzione sono disponibili:

-   Estensioni di Azure: Service Fabric.

-   Azure HDInsight: script per l'elaborazione dei dati di viaggio in Azure.

-   App per dispositivi mobili: app per dispositivi.

-   MobileAppsService/MyDrivingService: back-end Web.

-   Power BI: definizione dei report.

-   Scripts:

    -   Resource Manager: modelli per creare le risorse di Azure.

    -   PowerShell: script per eseguire i modelli di Resource Manager.

    -   Database SQL di Azure: debug dei database.

-   Database SQL: CreateTables: definizioni dello schema.

-   Analisi di flusso di Azure: query che consentono di trasformare il flusso di dati in ingresso.

## Eseguire le app in modalità di sviluppo

Predisporre l'esecuzione delle app in base al dispositivo in uso:

-  Back-end: impostare MyDrivingService come progetto di avvio, quindi premere F5 per eseguire il servizio Web back-end. Verrà aperta una visualizzazione browser dell'elenco delle API.

-  Client per dispositivi mobili: le [app per dispositivi mobili vengono sviluppate in Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).
 -  Android: per i dettagli, vedere l'articolo relativo al [debug di Android in Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/).

 -  iOS: per i dettagli, vedere l'articolo relativo al [debug in iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/).

 -  Windows Phone: per i dettagli, vedere l'articolo relativo a [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/).

## Caricare l'app per dispositivi mobili su HockeyApp

HockeyApp gestisce la distribuzione dell'app per Android, iOS o Windows agli utenti di test, inviando loro notifiche relative alla disponibilità di nuove versioni. Raccoglie anche utili segnalazioni di arresti anomali, commenti degli utenti con screenshot e metriche di utilizzo.

[Iniziare caricando](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) l'app compilata. Accedere quindi a [HockeyApp](https://rink.hockeyapp.net) dal computer di sviluppo. Nel dashboard di sviluppo fare clic su **New App** e trascinare i file compilati nella finestra (in un secondo momento è possibile automatizzare il servizio di compilazione per eseguire questa operazione).

Viene visualizzato il dashboard dell'app.

![Scheda Overview nel dashboard dell'app](./media/iot-solution-build-system/image2.png)

Ripetere il processo per ogni piattaforma in cui viene eseguita l'app. A questo punto è possibile eseguire queste operazioni:

-  Usare l'[ID app](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) nel dashboard per inviare dati di arresto anomalo e commenti dall'app. In MyDriving aggiornare gli ID in src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs.

-  [Invitare utenti di test](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Si riceverà un URL per selezionare gli utenti di test, che potranno registrarsi al team, scaricare l'app e inviare commenti.

-  Se si preferisce una versione beta più aperta, impostare la distribuzione come pubblica. Fare clic su **Manage App** > **Distribution** > **Download = Public**. In questo modo tutti gli utenti possono scaricare l'app e inviare commenti, oltre a visualizzare una notifica quando viene pubblicata una nuova versione, ed è anche possibile ricevere da loro alcune segnalazioni di arresti anomali.

    ![Team sul dashboard](./media/iot-solution-build-system/image3.png)

-  [Collegare le segnalazioni di arresto anomalo a Visual Studio Team Services](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). Fare clic su **Manage App** > **Visual Studio Team Services**. HockeyApp è in grado di creare automaticamente gli elementi di lavoro in Team Services quando sono presenti segnalazioni di arresti anomali o si ricevono commenti.

Altre informazioni sono disponibili sul [sito HockeyApp](https://hockeyapp.net).

## Eseguire i test dell'app per dispositivi mobili in Xamarin Test Cloud

[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatizza i test dell'interfaccia utente su dispositivi reali nel cloud. Con il framework NUnit è possibile scrivere test che eseguono l'app con l'interfaccia utente.

Per usare Xamarin incorporare nell'app l'SDK [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/), disponibile come pacchetto NuGet. È disponibile nell'app demo ed è incluso quando si creano nuovi progetti di test con i modelli Xamarin.

![Posizione dell'SDK multipiattaforma nell'interfaccia](./media/iot-solution-build-system/image4.png)

Un progetto di test di esempio è incluso con l'app nel repository. In [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService) cercare in [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/.

Se si usa una build di Visual Studio Team Services è facile scrivere unit test dell'interfaccia utente di Xamarin ed eseguirli nell'ambito della build.

## Distribuire servizi di Azure

Vedere le istruzioni dettagliate in **scripts/README.md** per eseguire la distribuzione automatica dei servizi di Azure e dei servizi di compilazione di Team Services.

Microsoft Azure offre un'ampia gamma di servizi diversi che è possibile usare per compilare applicazioni cloud. Anche se molti di essi possono essere usati singolarmente, ad esempio il servizio app/app Web, producono risultati ottimali quando vengono collegati per formare un sistema integrato come quello usato in MyDriving.

È possibile creare e collegare manualmente i servizi di Azure, ma l'uso dei modelli di Azure Resource Manager rappresenta una soluzione molto più rapida e affidabile. [Resource Manager](../resource-group-overview.md) automatizza la distribuzione delle risorse di una soluzione e la creazione di interconnessioni tra di esse.

Il modello per il sistema MyDriving è disponibile nel repository GitHub in [scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Offre una visualizzazione completa e concisa della modalità di interconnessione dei diversi servizi nell'architettura. Tali servizi vengono descritti in dettaglio nella [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs), ma è possibile acquisire molte informazioni anche leggendo il modello stesso.

> [AZURE.NOTE] La maggior parte dei servizi di Azure comporta un costo in funzione del piano tariffario. Se non si ha familiarità con Azure è possibile [provarlo gratuitamente](https://azure.microsoft.com/free/). Se tuttavia non si intende usare alcuni componenti nel sistema MyDriving, rimuoverli per evitare l'addebito di costi. La sezione "Stimare i costi operativi" più avanti in questo articolo offre un riepilogo delle spese di servizio standard.

### Modificare il modello

Per personalizzare la distribuzione, ad esempio per rimuovere componenti non necessari o aggiungerne altri, prima di tutto creare copie di scenario\_complete.params.json e scenario\_complete.json in cui apportare le modifiche.

È possibile usare il file scenario\_complete.params.json per eseguire l'override di diversi valori predefiniti, ad esempio lo SKU del servizio o il tipo di replica di archiviazione come descritto nella tabella seguente. I valori predefiniti corrispondono alle opzioni di costo più basse.

| **Parametro** | **Descrizione** | **Valore predefinito** |
|--------|---------|-------|
| SKU per hub IoT | Livello per il servizio hub IoT di Azure | F1 |
| Tipo di account di archiviazione | Tipo di replica di archiviazione | Archiviazione con ridondanza locale Standard |
| Obiettivo del servizio SQL | Utilizzo di slot di concorrenza | DW100 |
| SKU del piano di hosting | Piano di servizio per il servizio app | F1 |

In scenario\_complete.json:

-   Cercare "baseName" e modificarne il nome con uno di propria scelta.

-   Cercare "Create". Ognuna di queste sezioni crea una risorsa.

-   Impostare sqlServerAdminLogin e sqlServerAdminPassword su valori appropriati.

-   Prima di eliminare una sezione che crea una risorsa, verificare se esistono elementi dipendenti cercando il nome della sezione nelle altre parti del file. Si noti che ogni sezione che crea un servizio include una sezione *dependsOn* che elenca le relative dipendenze.

La tabella seguente descrive gli elementi configurati dal modello. I dettagli sono disponibili nella [guida di riferimento](http://aka.ms/mydrivingdocs).

| **Servizio** | **Descrizione e dettagli**  
|---|----
| Account di archiviazione | Il modello crea tre account:                                                                                                                                                                       
|| - Un database SQL che riceve i dati di telemetria aggregati da Analisi di flusso e funge da archivio di backup per le tabelle del servizio app di Azure che espongono tali dati usando gli endpoint dell'API.                      
|| - L'archivio BLOB che accumula i dati cronologici di un altro processo di analisi di flusso, per l'elaborazione in HDInsight.                                                                                         
|| - Un database SQL che riceve i risultati elaborati da HDInsight per l'uso con Power BI.                                                                                                                 
| Hub IoT Azure | Stabilisce una connessione bidirezionale per ogni dispositivo connesso. Nella soluzione MyDriving, l'app per dispositivi mobili viene usata come gateway sul campo per inviare dati all'hub IoT di Azure. L'hub IoT di Azure viene quindi usato come input per analisi di flusso. |
| Hub eventi di Azure | Un output per un processo di analisi di flusso che accoda l'output alle estensioni create con Azure Service Fabric.                                                                                               
| Azure SQL Data Warehouse |                                                                                                                                                                                                            
| Processi di analisi di flusso | Consentono di connettere input e output con una query, usata successivamente per aggregare i dati in tempo reale e i dati cronologici per le API del servizio app, Azure Machine Learning, le estensioni e Power BI.                               
| Area di lavoro di Machine Learning | Include esperimenti, codice R e servizio API.                                                                                                                                                              
| Data factory di Azure | Ripetizione del training di Machine Learning pianificata.                                                                                                                                                                     
| Piano di hosting di Service Fabric | Per le estensioni.                                                                                                                                                                                            
| Servizio app ("App per dispositivi mobili") | Ospita il progetto API per dispositivi mobili che fornisce gli endpoint per l'app per dispositivi mobili. Il codice API deve essere distribuito nel servizio app da Visual Studio.                                                         
| Regole di avviso | Invia un messaggio di posta elettronica se le risposte delle app indicano errori.                                                                                                                                            
| Application Insights | Per il monitoraggio delle prestazioni delle API nel servizio app. È necessario configurare la connessione in Visual Studio.                                                                                          
| Insieme di credenziali chiave Azure | Per salvare il certificato del cluster del servizio Web.                                                                                                                                                                

### Eseguire il modello

Istruzioni dettagliate per l'esecuzione del modello sono disponibili in **scripts/README.md**.

Per eseguire il provisioning di tutti questi servizi nel proprio account Azure usando lo script, eseguire una di queste operazioni:

-   Usare PowerShell:

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *location* è la [località di Azure](https://azure.microsoft.com/regions/), ad esempio `North Europe` o `West US`. Usare `Get-AzureLocation` per trovare un elenco delle località disponibili.

 -   *resourceGroupName* è il nome da assegnare al gruppo al quale apparterranno tutte le risorse. Dopo aver usato le risorse sarà possibile eliminarle tutte contemporaneamente eliminando questo gruppo.

-   Eseguire DeploymentScripts/Bash/deploy.sh con Bash.

-   Aprire e compilare la soluzione di Visual Studio DeploymentScripts/VS/DeployARM.sln.

Si noti che ogni volta che viene eseguito il modello, viene creato un nuovo set di risorse con nuovi nomi. Per eliminare le risorse, passare al portale ed eliminare il gruppo di risorse.

Se per qualsiasi motivo lo script non riesce, è possibile eseguirlo nuovamente.

Lo script consente di configurare l'integrazione continua in Visual Studio Team Services. Se è stato configurato un progetto Team Services, sarà disponibile un URL https://yourAccountName.visualstudio.com. Immettere l'URL completo quando viene richiesto. È possibile assegnare all'URL un nome nuovo o esistente per un progetto Team Services.

## Configurare definizioni di compilazione e test in Visual Studio Team Services

In questo progetto viene usato Team Services principalmente per le relative funzionalità di compilazione e test. Offre tuttavia anche un supporto eccellente per la collaborazione, ad esempio gestione delle attività con le lavagne Kanban, revisione del codice integrata con attività e controllo del codice sorgente e compilazioni gestite. Si integra bene con altri strumenti, ad esempio GitHub, Xamarin, HockeyApp e naturalmente Visual Studio. È possibile accedervi con l'interfaccia Web o Visual Studio, a seconda di quale sia la scelta più comoda in un dato momento.

I passaggi contenuti nelle definizioni di compilazione e versione usano un'ampia gamma di servizi plug-in disponibili nel [Marketplace](https://marketplace.visualstudio.com/VSTS) di Team Services. Oltre alle utilità di base per eseguire le righe di comando o copiare i file, esistono servizi che richiamano le build di Xamarin, Android e di altri fornitori e che si interfacciano con HockeyApp.

![Opzioni di compilazione in Team Services](./media/iot-solution-build-system/image5.png)

### Definizioni di compilazione

Sono disponibili definizioni di compilazione per ognuna delle destinazioni principali. Sono anche disponibili varianti per test di funzionalità e di regressione. Le definizioni sono le seguenti:

-   MyDriving.Services: app Web back-end per l'app per dispositivi mobili

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android: funzionalità

    -   MyDriving.Xamarin.Android: regressione

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS: funzionalità

    -   MyDriving.Xamarin.iOS: regressione

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP: funzionalità

    -   MyDriving.Xamarin.UWP: regressione

Per visualizzare i dettagli completi della configurazione, vedere la sezione 4.7 relativa alla configurazione della build e della versione della [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs). Seguono lo stesso modello generale. Lo script:

1.  Ripristina il pacchetto NuGet. Il codice compilato non viene mantenuto nel repository, quindi i primi passaggi di ogni compilazione prevedono il ripristino dei pacchetti NuGet necessari.

2.  Attiva la licenza. La compilazione viene eseguita nel cloud; nei casi in cui sia necessaria una licenza, in particolare per il servizio di compilazione Xamarin, è quindi necessario attivare la licenza nel computer di compilazione corrente. Subito dopo è necessario disattivarla per consentirne l'uso in un altro computer.

3.  Esegue la compilazione con il servizio appropriato. Per le app per dispositivi mobili vengono usate le build di Xamarin, mentre per il servizio Web back-end vengono usate le build di Visual Studio.

4.  Compila i test.

5.  Esegue i test. I test delle app per dispositivi mobili vengono eseguiti in Xamarin Test Cloud.

6.  Pubblica il risultato della compilazione nel percorso di rilascio.

Il trigger per le compilazioni principali è impostato per l'integrazione continua. In altre parole, la compilazione viene eseguita ogni volta che viene archiviato codice nel ramo master.

![Interfaccia nella quale il trigger è impostato per l'integrazione continua](./media/iot-solution-build-system/image6.png)

### Definizioni di versione

Le definizioni di versione vengono configurate nello stesso modo.

Per il servizio Web, la distribuzione viene configurata come un'app Web di Azure:

![Interfaccia per la configurazione della distribuzione come app Web di Azure](./media/iot-solution-build-system/image7.png)

Il trigger versione è impostato per la distribuzione continua. In altre parole, ogni archiviazione seguita da una compilazione riuscita produce un aggiornamento per l'app Web.

![Interfaccia per l'impostazione del trigger versione per la distribuzione continua](./media/iot-solution-build-system/image8.png)

Per le app per dispositivi mobili, la distribuzione viene effettuata in HockeyApp:

![Interfaccia per la distribuzione di un'app per dispositivi mobili in HockeyApp](./media/iot-solution-build-system/image9.png)

## Esplorare i dati di telemetria con Application Insights

[Application Insights](../application-insights/app-insights-overview.md) raccoglie dati di telemetria su prestazioni e utilizzo dei servizi Web. Application Insights SDK invia dati di telemetria dal servizio alla risorsa di Application Insights in Azure.

Passare alla risorsa di Application Insights configurata dal modello, dove è possibile esplorare i grafici delle prestazioni del [progetto del servizio app per dispositivi mobili](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Nei grafici sono mostrate le richieste di server e i tempi di risposta, gli errori e i conteggi delle eccezioni. Sono anche disponibili grafici dei tempi di risposta delle dipendenze, ovvero chiamate al database e alle API REST, ad esempio Machine Learning. Se sono presenti problemi di prestazioni, sarà possibile vedere quale parte del sistema ne è la causa.

![Esempio di grafico delle prestazioni](./media/iot-solution-build-system/image11.png)

Se è presente un servizio Web configurato manualmente è possibile ottenere facilmente gli stessi grafici. Nel pannello del servizio Web fare clic su **Strumenti** > **Estensioni** > **Aggiungi**. Selezionare **Application Insights**.

![Interfaccia per la selezione di Application Insights per ottenere i grafici](./media/iot-solution-build-system/image12.png)

La funzionalità diventa operativa instrumentando l'applicazione con Application Insights SDK.

È possibile aggiungere dati di telemetria personalizzati (o instrumentare un'applicazione in esecuzione all'esterno di Azure) [aggiungendo Application Insights SDK](../application-insights/app-insights-asp-net.md) in fase di sviluppo. Si tratta di una funzionalità utile per registrare le metriche che dipendono dall'applicazione, ad esempio la lunghezza media del viaggio o il chilometraggio totale degli utenti. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Aggiungi Application Insights**.

![Interfaccia per la selezione di Aggiungi Application Insights per aggiungere dati di telemetria personalizzati](./media/iot-solution-build-system/image10.png)

Application Insights invierà avvisi di posta elettronica se rileva un numero insolito di risposte di errore. È anche possibile impostare avvisi su diverse metriche, ad esempio i tempi di risposta.

Per assicurarsi che il servizio Web sia sempre operativo è possibile configurare [test di disponibilità](../application-insights/app-insights-monitor-web-app-availability.md). Questi test effettuano il ping del sito da diverse località in tutto il mondo ogni 15 minuti. Anche in questo caso si riceverà un messaggio di posta elettronica in caso di problemi.

## Stimare i costi operativi

L'esecuzione di un'app come questa su scala ridotta è molto conveniente. Per molti dei servizi sono disponibili livelli di base gratuiti, in modo da ridurre al minimo i costi di sviluppo e gestione su scala ridotta. E, naturalmente, non è necessario che le app usino tutte le funzionalità illustrate in MyDriving.

Di seguito è disponibile una stima approssimativa dei costi di impostazione della configurazione di sviluppo per MyDriving. Si notino anche alcune alternative che *non* sono state usate. Queste informazioni possono essere utili per la stima dei costi.

Si presuppone quanto segue:

-   Un team di non più di cinque membri, ai quali si aggiungono stakeholder che hanno un ruolo di osservatori.

-   Esecuzione per circa un mese.

-   100 utenti che effettuano 4 viaggi al giorno.

>[AZURE.NOTE] Se non si ha familiarità con Azure, è disponibile un [account gratuito](https://azure.microsoft.com/free/).

| **Servizio/Componente** | **Note** | **Costo/mese** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) con [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>Ambiente di sviluppo multipiattaforma| Visual Studio Community. Richiede [Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions) per [Xamarin.Forms](https://xamarin.com/forms) per progettare la multipiattaforma da una singola codebase. | $ 0 |
| [Hub IoT di Azure](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Connessione bidirezionale ai dispositivi | 8\.000 messaggi + 0,5 KB/messaggio gratuiti. | $ 0 |
| [Analisi di flusso](https://azure.microsoft.com/pricing/details/stream-analytics/) <br/> Elaborazione di volumi elevati di dati del flusso | Costo di $ 0,031 per unità di streaming all'ora, quando è abilitata. Scegliere il numero di unità di streaming; aumentarlo per aumentare le prestazioni. | $ 23 |
| [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Risposte adattive | $ 10/postazione/mese. <br/> + 3 ore esperimento * $ 1/ora esperimento. <br/> + 3,5 ore CPU API * $ 2/ora CPU produzione. <br/> Il tempo per CPU API presuppone 5 min/giorno di ripetizione del training, anche se questo valore potrebbe aumentare con l'incremento dei dati di input. <br/> + assegnazione dei punteggi di 2 min/giorno per elaborare 400 viaggi/giorno. | $ 20 |
| [Servizio app](https://azure.microsoft.com/pricing/details/app-service/) <br/> Host per back-end per dispositivi mobili | Livello B1: app Web di produzione. | $ 56 |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/) <br/> Gestione della compilazione, degli unit test e delle versioni; gestione delle attività | Agenti privati, cinque utenti.| $ 0 |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Monitoraggio delle prestazioni e dell'utilizzo di servizi Web e siti| Livello gratuito. | $ 0 |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Distribuzione di app beta e raccolta di commenti e dati su utilizzo e arresti anomali | Due app gratuite per i nuovi utenti.<br/> In seguito $ 30/mese. | $ 0 |
| [Xamarin](https://store.xamarin.com/)<br/> Codice su una piattaforma uniforme per più dispositivi | Versione di valutazione gratuita. <br/>In seguito $ 25/mese.| $ 0 |
| [Database SQL](https://azure.microsoft.com/pricing/details/sql-database/) per servizio app di Azure| Piano Basic, modello di database singolo. | $ 5 |
| [Service Fabric](../service-fabric/services/service-fabric.md) (facoltativo) | Consente di eseguire un cluster locale. | $ 0 |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Visualizzazioni versatili e analisi dei dati di flusso e statici| Livello gratuito: 1 GB, 10.000 righe/ora, aggiornamento giornaliero. <br/> $ 10/utente/mese per [limiti più elevati](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), altre opzioni di connessione, collaborazione. | $ 0 |
| [Archiviazione](https://azure.microsoft.com/pricing/details/storage/) | L (ridondanza locale) &lt; 100 G $ 0,024/GB. | $ 3 |
| [Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) | $ 0,60 per attività * (8 - 5 FOC).| $ 2 |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/> Cluster on demand per la ripetizione giornaliera del training | Tre nodi A3 a $ 0,32/ora per 1 ora al giorno * 31 giorni. | $ 30 |
| [Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/) | Basic con unità elaborate di $ 11/mese + ingresso $ 0,028. | $ 11 |
| Adattatore OBD || $ 12 |
| **Totale**| | **$ 157** |

Per altre informazioni, vedere:

-   Riepilogo di [limiti e quote dei servizi di Azure](../azure-subscription-service-limits.md#iot-hub-limits)

-   [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

## Invio di commenti

MyDriving è stato creato per aiutare gli utenti a usare rapidamente i propri sistemi IoT. Per questo motivo sarà molto apprezzato qualsiasi commento sul funzionamento. Indicare se:

-  Si sono riscontrate difficoltà o problematiche.

-  Esiste un punto di estensione che potrebbe renderlo più adatto al proprio scenario.

-  Si è trovato un modo più efficace per soddisfare determinate esigenze.

-  Si hanno altri suggerimenti per migliorare MyDriving o questa documentazione.

Per inviare commenti e suggerimenti, inviare un [problema su GitHub] o lasciare un commento qui sotto nella pagina in lingua inglese (codice lingua it-IT).

Tutti i commenti e suggerimenti saranno apprezzati.

## Passaggi successivi

È consigliabile leggere la [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs), una descrizione completa della progettazione del sistema e dei relativi componenti.

<!---HONumber=AcomDC_0928_2016-->