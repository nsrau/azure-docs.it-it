<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage web sites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal web site management pages in Microsoft Azure. Details are provided for each web site management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Web Sites" authors="timamm" solutions="" writer="timamm" manager="" editor="" />

Come gestire i siti Web
=======================

Nel portale di Azure è disponibile un set di pagine o "schede" che consente di gestire i siti Web. Di seguito viene fornita una descrizione di ogni pagina di gestione del sito Web.

Quickstart
----------

Nella pagina di gestione **QuickStart** sono incluse le sezioni seguenti:

-   **Get the tools**: include i collegamenti a [Install WebMatrix](http://go.microsoft.com/fwlink/?LinkID=226244) e [Microsoft Azure SDK](http://go.microsoft.com/fwlink/?LinkId=246928).
-   **Publish your app**: include i collegamenti per scaricare il profilo di pubblicazione del sito Web, reimpostare le credenziali di distribuzione per il sito Web, aggiungere uno slot di pubblicazione (distribuzione) di gestione temporanea a un sito non di gestione temporanea e per acquisire ulteriori informazioni sulla pubblicazione di gestione temporanea.
-   **Integrate source control**: consente di configurare e gestire la distribuzione da strumenti di controllo del codice sorgente o siti Web quali TFS, CodePlex, GitHub, Dropbox, Bitbucket o Git locale.

Dashboard
---------

Nella pagina di gestione **Dashboard** sono inclusi gli elementi seguenti:

-   Un grafico in cui è riportato un riepilogo dell'utilizzo del sito Web come misure di determinate metriche.
-   **CPU Time**: misura dell'utilizzo della CPU del sito Web.
-   **Data In**: misura dei dati ricevuti dal sito Web dai client.
-   **Data Out**: misura dei dati inviati dal sito Web ai client.
-   **HTTP Server Errors**: numero di messaggi di errore del server HTTP 5xx inviati.
-   **Requests**: conteggio di tutte le richieste client al sito Web.
    **Nota:** per aggiungere ulteriori metriche delle prestazioni, passare alla pagina di gestione **Monitor** e scegliere **Add Metrics** nella parte inferiore della pagina. Per ulteriori informazioni, vedere [Come monitorare i siti Web](http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/).
-   **Web Endpoint Status**: elenco degli endpoint Web configurati per il monitoraggio. Se non sono stati configurati endpoint, fare clic su **Configure Web Endpoint Monitoring** e passare alla sezione **Monitoring** della pagina di gestione **Configure**. Per ulteriori informazioni, vedere [Come monitorare i siti Web](http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/).

-   **Autoscale Status**: nella modalità standard è disponibile la scalabilità automatica delle risorse in modo da spendere solo per l'utilizzo delle risorse necessarie. Per abilitare la scalabilità automatica, scegliere **Configure Autoscale** per passare alla pagina **Scale**. Se il sito Web è in modalità gratuita o condivisa, sarà necessario passare alla modalità standard (dalla pagina **Scale**) prima di poter configurare la scalabilità automatica. **Autoscale Operation Logs**: reindirizza l'utente al portale **Management Services** in cui è possibile visualizzare la cronologia di scalabilità automatica del sito Web. La query prevede solo le ultime 24 ore, ma è possibile modificarla.

-   **Usage Overview**: in questa sezione sono visualizzate le statistiche relative all'utilizzo di CPU, file system e memoria del sito Web.
-   **Linked Resources**: in questa sezione viene visualizzato un elenco di risorse, ad esempio un database SQL o MySQL o un account di archiviazione di Microsoft Azure, che sono collegate al sito Web. Fare clic sul nome della risorsa per gestirla. Se si dispone di un database MySQL, facendo clic sul nome si verrà reindirizzati alla pagina di gestione ClearDB. Da cui sarà possibile visualizzare le metriche delle prestazioni o passare al dashboard ClearDB che consente di aggiornare il database MySQL, se necessario. Se non sono presenti risorse nell'elenco, fare clic su **Manage Linked Resources** per passare alla pagina **Linked Resources** da cui è possibile aggiungere un collegamento a una risorsa per il sito Web.
-   Una sezione **Quick Glance** in cui sono incluse le informazioni di riepilogo e i collegamenti seguenti (a seconda delle impostazioni, alcune delle opzioni riportate di seguito potrebbero non essere visualizzate):
 - **View Applicable Add-ons**: consente di aprire la finestra di dialogo **Purchase from Store** in cui è possibile acquistare componenti aggiuntivi che offrono funzionalità aggiuntive per il sito Web. Alcuni componenti aggiuntivi potrebbero non essere disponibili nell'area geografica dell'utente o per l'ambiente in uso.
 - **View connection strings**: consente di visualizzare le stringhe di connessione del database del sito Web.
 - **Download the publish profile**: fare clic su questo collegamento per scaricare il profilo di pubblicazione per il sito Web. Il profilo di pubblicazione contiene le credenziali dell'utente (nome utente e password) e gli URL per caricare il contenuto nel sito Web tramite FTP e Git. Il file del profilo è in formato XML e può essere visualizzato in un editor di testo.
 - **Set up deployment credentials**: fare clic su questa opzione per creare un nome utente e una password per caricare il contenuto nel sito Web tramite FTP o Git. Dopo avere creato le credenziali di distribuzione FTP/Git, è possibile utilizzarle per caricare contenuto in qualsiasi sito Web incluso nella sottoscrizione tramite FTP o Git. Per visualizzare le credenziali dopo averle create, fare clic su **Download the publish profile**. Il profilo di pubblicazione scaricato è un file di testo in formato XML che può essere visualizzato in un editor di testo. **Nota**: l'autenticazione a un host FTP o a un archivio Git mediante l'utilizzo delle credenziali dell'account Microsoft (Live ID) non è supportata.
 - **Reset your publish profile credentials**: consente di reimpostare il profilo di pubblicazione per il sito Web. I profili di pubblicazione scaricati in precedenza diventeranno non validi.
 - **Set up deployment from source control**: consente di visualizzare una finestra di dialogo in cui è possibile configurare la pubblicazione continua da Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket o Local Git.
 - **Add a new deployment slot**: per i siti nella modalità standard, è possibile utilizzare questa funzionalità per creare uno slot di gestione temporanea per il sito Web. Lo slot di gestione temporanea (sito di gestione temporanea) consente di convalidare il contenuto e la configurazione del sito prima di passare all'ambiente di produzione. È inoltre possibile utilizzare la versione di gestione temporanea del sito per aggiungere gradualmente aggiornamenti del contenuto e quindi effettuare lo scambio alla produzione dopo che gli aggiornamenti nello slot di gestione temporanea sono stati completati. Non è possibile aggiungere uno slot a un sito già in gestione temporanea.
 - **Edit in Visual Studio Online**: fare clic su questo collegamento per modificare il sito Web direttamente online tramite Visual Studio Online dal portale di Microsoft Azure. Questa opzione non verrà visualizzata se non viene abilitata nella pagina **Configure**.
 - **Disconnect from Dropbox**: se è stata configurata una connessione a Dropbox ai fini della distribuzione, questo collegamento consente di effettuare la disconnessione.
 - **Delete Git repository**: se è stato configurato un archivio Git, questo collegamento consente di eliminarlo.
 - **Status**: indica se il sito Web è in esecuzione.
 - **Management Services**: fare clic sul collegamento **Operation Logs** per visualizzare i log delle operazioni per il sito Web dal portale dei servizi di gestione di Microsoft Azure.
 - **Virtual IP Address**: consente di visualizzare l'indirizzo IP virtuale del sito Web se è stata configurata un'associazione SSL basata su IP per il sito Web nella sezione **SSL Bindings** della scheda **Configure**.
 - **Site URL**: specifica l'indirizzo accessibile pubblicamente del sito Web in Internet.
 - **Compute Mode**: specifica se il sito Web è in esecuzione in modalità gratuita, condivisa, base o standard. Per ulteriori informazioni sulle modalità dei gruppi di scala Web, vedere [Come scalare un sito Web](http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-scale-websites).
 - **FTP Hostname**: specifica l'URL da utilizzare per la pubblicazione nel sito Web tramite FTP.
 - **FTPS Hostname**: specifica l'URL da utilizzare per la pubblicazione nel sito Web tramite FTPS.
 - **Deployment User / FTP User**: indica l'account utilizzato per la distribuzione del sito Web in Microsoft Azure tramite FTP o Git.
 - **FTP Diagnostic Logs**: specifica il percorso FTP dei log di diagnostica del sito Web se è stata abilitata la registrazione diagnostica nella pagina di gestione **Configure**.
 - **FTPS Diagnostic Logs**: specifica il percorso FTPS dei log di diagnostica del sito Web se è stata abilitata la registrazione diagnostica nella pagina di gestione **Configure**.
 - **Location**: specifica l'area geografica del data center che ospita il sito Web.
 - **Subscription Name**: specifica il nome della sottoscrizione a cui è associato il sito Web.
 - **Subscription ID**: specifica l'ID sottoscrizione univoco (GUID) della sottoscrizione a cui è associato il sito Web.

Deployments
-----------

Questa scheda viene visualizzata solo se è stata impostata la distribuzione dal controllo del codice sorgente. Nella pagina di distribuzione **Deployments** viene visualizzato un riepilogo di tutte le distribuzioni effettuate nel sito Web utilizzando il metodo di pubblicazione scelto. Se per il sito Web è stata configurata la pubblicazione Git ma non è stata effettuata alcuna distribuzione, nella pagina di gestione **Deployments** vengono fornite informazioni su come utilizzare Git per distribuire l'applicazione Web nel sito Web.

Monitor
-------

Nella pagina di gestione **Monitor** è disponibile un grafico in cui sono visualizzate le informazioni sull'utilizzo per il sito Web. Per impostazione predefinita, questo grafico visualizza le stesse metriche del grafico nella pagina **Dashboard**, come descritto in precedenza nella sezione Dashboard. È inoltre possibile configurare il grafico per visualizzare metriche quali operazioni HTTP completate, reindirizzamenti HTTP, errori HTTP 401, errori HTTP 403, errori HTTP 404 ed errori HTTP 406. Per ulteriori informazioni su queste metriche, vedere [Come monitorare i siti Web](http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/).

WebJobs
-------

La pagina di gestione WebJobs consente di creare attività su richiesta, pianificate o con esecuzione continua per il sito Web. Per ulteriori informazioni, vedere [Come utilizzare la funzionalità WebJobs in Siti Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-create-web-jobs/).

Configure
---------

È possibile utilizzare la pagina di gestione **Configure** per configurare le impostazioni specifiche dell'applicazione seguenti:

-   **General**: consente di impostare la versione di .NET Framework, PHP, Python o Java richiesta dall'applicazione Web. Per i siti in modalità standard o base, è disponibile un'opzione per scegliere una piattaforma a 64 bit. Impostare **Managed Pipeline Mode** su **Classic** solo se sono presenti siti Web legacy che possono essere eseguiti solo su versioni di IIS precedenti (**Integrated** è l'opzione predefinita). Per abilitare l'utilizzo di applicazioni di modelli di richiesta in tempo reale da parte del sito Web, ad esempio una chat, è possibile impostare **Web Sockets** su **On**. Per abilitare la modifica del sito Web direttamente online, impostare **Edit in Visual Studio Online** su **On**.
-   **Certificates**: consente di caricare un certificato SSL per un dominio personalizzato. È possibile caricare i certificati SSL solo in modalità standard o base. I certificati caricati sono visualizzati in questo elenco e possono essere assegnati a qualsiasi sito Web incluso nella sottoscrizione e nell'area geografica specifiche. Sono supportati i certificati con caratteri jolly (certificati con un asterisco).
-   **Domain Names**: consente di visualizzare o aggiungere nomi di dominio personalizzati aggiuntivi per un sito Web. I nomi di dominio personalizzati possono essere utilizzati solo in modalità condivisa, base o standard.
-   **SSL Bindings**: le associazioni SSL ai domini personalizzati possono essere utilizzate solo nelle modalità base e standard. Scegliere una modalità SSL (**SNI**, **IP** o **No SSL**) per un particolare nome di dominio. Se si sceglie SNI o IP, è possibile specificare un certificato per il dominio dai certificati caricati.
-   **Deployments**: questa sezione viene visualizzata solo se è stata abilitata la distribuzione dal controllo del codice sorgente. Utilizzare queste impostazioni per configurare le distribuzioni.
-   **Diagnostica applicazioni**: consente di impostare le opzioni per raccogliere le informazioni di diagnostica per un'applicazione Web che supporta la registrazione. È possibile scegliere di registrare nel file system o in un account di archiviazione di Microsoft Azure e impostare un livello di registrazione per specificare la quantità di informazioni raccolte.
-   **Site Diagnostics**: consente di impostare le opzioni di registrazione per raccogliere le informazioni di diagnostica per il sito Web o abilitare Visual Studio 2012 o Visual Studio 2013 per il debut del sito Web in modalità remota per un massimo di 48 ore.
-   **Monitoring**: per i siti Web in modalità standard, consente di verificare la disponibilità di endpoint HTTP o HTTPS da posizioni geograficamente distribuite.
-   **Developer Analytics**: tramite l'analisi vengono monitorate le prestazioni dell'applicazione Web. Scegliere un componente aggiuntivo di analisi dal negozio di Microsoft Azure o scegliere un provider di analisi personalizzato, ad esempio New Relic.
-   **App Settings**: consente di specificare coppie nome-valore che verranno caricate dall'applicazione Web all'avvio. Per i siti .NET queste impostazioni verranno inserite nelle impostazioni AppSettings della configurazione .NET del sito Web in fase di esecuzione, sostituendo le impostazioni esistenti. Per i siti Web PHP e Node queste impostazioni saranno disponibili come variabili di ambiente in fase di esecuzione.
-   **Connection Strings**: consente di visualizzare le stringhe di connessione alle risorse collegate. Per i siti .NET tali stringhe verranno inserite nelle impostazioni di connectionStrings della configurazione .NET del sito Web in fase di esecuzione, sostituendo le eventuali voci esistenti in cui la chiave è uguale al nome del database collegato. Per i siti Web PHP e Node queste impostazioni saranno disponibili come variabili di ambiente in fase di esecuzione.
-   **Default Documents**: consente di aggiungere il documento predefinito del sito Web all'elenco, se non ancora inserito. Se nell'elenco del sito Web è presente più di un file, accertarsi che il documento predefinito del sito stesso si trovi all'inizio dell'elenco modificando l'ordine dei file nell'elenco.
-   **Handler Mappings**: consente di aggiungere processori script personalizzati per gestire le richieste per tipi di file specifici, ad esempio \*.php.
-   **Virtual Applications and Directories**: consente di configurare applicazioni e directory virtuali associate al sito Web. È inoltre possibile contrassegnare una directory virtuale come un'applicazione nella configurazione del sito.

Per ulteriori informazioni su come configurare un sito Web, vedere la pagina [Come configurare i siti Web](http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-configure-websites).

Scale
-----

Nella pagina di gestione **Scale** è possibile specificare la modalità del gruppo di scala Web (**Free**, **Shared**, **Basic** o **Standard**). Le modalità **Shared**,**Basic** e **Standard** offrono prestazioni e velocità effettiva ottimizzate. Le modalità **Shared**, **Basic** e **Standard** consentono di aumentare il valore di **Instance Count**, ovvero il numero di macchine virtuali utilizzate dal sito Web e da altri siti Web nello stesso gruppo di scala Web.

In modalità **Standard** è inoltre possibile aumentare il numero di core e la capacità di memoria di ogni istanza modificando il valore in **Instance Size**. Ai fini della convenienza, è possibile scegliere l'opzione **Autoscale** per consentire a Microsoft Azure di allocare le risorse per il sito Web in modo dinamico.

Per ulteriori informazioni sulla configurazione delle opzioni di scala per un sito Web, vedere [Come scalare un sito Web](http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-scale-websites).

Linked Resources
----------------

Nella pagina di gestione **Linked Resources** è disponibile un elenco di risorse di Microsoft Azure in uso nel sito Web, ad esempio database SQL, database MySQL e account di archiviazione di Azure. Fare clic sul nome della risorsa per gestirla.

Backups
-------

La pagina di gestione **Backups** consente di creare backup automatici o manuali per il sito Web, ripristinare il sito Web a uno stato precedente o creare un nuovo sito Web in base a uno dei backup esistenti. Per ulteriori informazioni, vedere [Backup di siti Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-backup/) e [Ripristino di un sito Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-restore/).

Icone delle pagine di gestione
------------------------------

Le icone sono visualizzate nella parte inferiore delle pagine di gestione del sito Web. La maggior parte delle icone è visualizzata in più pagine mentre alcune sono visualizzate solo in pagine specifiche. Le icone seguenti sono visualizzate nella parte inferiore della pagina di gestione **Dashboard**:

-   **Browse**: apre la pagina predefinita per il sito Web.
-   **Stop**: arresta il sito Web.
-   **Restart**: riavvia il sito Web.
-   **Manage Domains**: mappa un dominio a questo sito Web. Non disponibile per i siti in modalità di scala **Free**.
-   **Delete**: elimina il sito Web.
-   **WebMatrix**: apre i siti Web supportati in WebMatrix, per consentire la modifica del sito Web e la pubblicazione delle modifiche apportate al sito Web in Microsoft Azure.

Le icone seguenti non sono visualizzate nella parte inferiore della pagina di gestione **Dashboard** ma sono disponibili nella parte inferiore di altre pagine di gestione per lo svolgimento di attività specifiche:

-   **Add Metrics**: è disponibile nella parte inferiore della pagina di gestione **Monitor** e consente di aggiungere metriche al grafico visualizzato nella pagina di gestione Monitor.
-   **Link**: è disponibile nella parte inferiore della pagina **Linked Resources** e consente di creare collegamenti di gestione ad altre
-   risorse di Microsoft Azure. Se ad esempio il sito Web accede a un database SQL, è possibile creare un collegamento di gestione alla risorsa di database facendo clic su **Link**.

