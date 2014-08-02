<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure web sites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure web sites in Azure, including how to configure a web site to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Web Sites" authors="timamm" solutions="" manager="" editor="mollybos" />

Come configurare i siti Web
===========================

Nel portale di gestione di Azure è possibile modificare le opzioni di configurazione per i siti Web e collegare questi ultimi ad altre risorse Azure. Ad esempio, è possibile collegare siti Web a un database SQL per fornire ulteriori funzionalità. È inoltre possibile configurare siti Web in modo da utilizzare un database MySQL nuovo o esistente.

Sommario
--------

-   [Procedura: Modificare le opzioni di configurazione per un sito Web](#howtochangeconfig)
-   [Procedura: Configurare un sito Web per l'uso di un database SQL](#howtoconfigSQL)
-   [Procedura: Configurare un sito Web per l'uso di un database MySQL](#howtoconfigMySQL)
-   [Procedura: Configurare un nome di dominio personalizzato](#howtodomain)
-   [Procedura: Configurare un sito Web per l'uso di SSL](#howtoconfigSSL)
-   [Passaggi successivi](#next)

Procedura: Modificare le opzioni di configurazione per un sito Web
------------------------------------------------------------------

Per modificare le opzioni di configurazione per un sito Web, eseguire la procedura seguente.

1.  Nel portale di gestione aprire le pagine di gestione del sito Web.
2.  Fare clic sulla scheda **Configure** per aprire la pagina di gestione **Configure**.
3.  Impostare come appropriato le opzioni di configurazione seguenti per il sito Web:
    -   **general**
        -   **.NET Framework Version**: se l'applicazione Web utilizza .NET Framework, impostare la versione del framework richiesta.
        -   **PHP Version**: se l'applicazione Web utilizza PHP, impostare la versione di PHP richiesta.
        -   **Java Version**: consente di selezionare la versione di Java visualizzata in modo da abilitarla per l'applicazione Web oppure di selezionare **OFF** per disabilitare Java. Se si abilita Java per l'applicazione Web, l'opzione relativa al **contenitore Web** consente di scegliere tra le versioni Tomcat e Jetty.

            **Nota**: Per motivi tecnici, l'abilitazione di Java per il sito Web disabilita le opzioni relative a .NET, PHP e Python.

        -   **Python Version**: consente di visualizzare la versione di Python (non configurabile).
        -   **Managed Pipeline Mode**: tra le due opzioni **Classic** e **Integrated**, quest'ultima è l'impostazione predefinita. Utilizzare l'opzione Classic solo se sono presenti siti Web legacy che possono essere eseguiti esclusivamente su versioni di IIS precedenti.
        -   **Platform**: per i siti in modalità Standard, consente di scegliere se eseguire l'applicazione in un ambiente a 32 o a 64 bit. I siti in modalità Free e Shared vengono sempre eseguiti in un ambiente a 32 bit.
        -   **Web Sockets**: scegliere **On** per abilitare l'utilizzo di applicazioni di modelli di richiesta in tempo reale da parte del sito Web, ad esempio una chat.
        -   **Always On**: per impostazione predefinita, i siti Web vengono scaricati se sono stati inattivi per un determinato periodo di tempo. Ciò consente al sistema di conservare le risorse. Always On**: per i siti in modalità Standard, consente di abilitare questa opzione se è continuamente necessario ricaricare il sito.** Abilitare l'opzione **Always On** per consentire l'esecuzione affidabile dei lavori Web continui sul sito.****
        -   **Edit in Visual Studio Online**: scegliere **On** per abilitare le modifiche del codice attivo con Visual Studio Online. Dopo avere salvato la modifica della configurazione, nella sezione **Quick Glance** della scheda DASHBOARD verrà visualizzato un collegamento denominato **Edit in Visual Studio Online**. Fare clic sul collegamento per modificare il sito Web direttamente online. Se è necessario eseguire l'autenticazione, è possibile utilizzare le credenziali di distribuzione di base.

            **Nota**: Se è abilitata la funzionalità di distribuzione dal controllo del codice sorgente, è possibile sovrascrivere le modifiche apportate nell'editor di Visual Studio Online per una distribuzione. Si consiglia pertanto di evitare l'uso della funzionalità di distribuzione dal controllo del codice sorgente se si desidera modificare il contenuto del sito direttamente con Visual Studio Online.

    -   **certificates**: in modalità Standard fare clic su **upload** per caricare un certificato SSL per un dominio personalizzato. I certificati caricati sono elencati qui. Sono supportati i certificati con caratteri jolly (certificati con un asterisco). Dopo aver caricato un certificato, è possibile assegnarlo a qualsiasi sito Web nella sottoscrizione e nell'area geografica specifiche. Un certificato asterisco deve essere caricato una sola volta, ma può essere utilizzato per qualsiasi sito nel dominio in cui è valido. È possibile eliminare un certificato solo se non sono presenti associazioni attive in alcun sito per il certificato specificato.
        **Nota:** i domini personalizzati sono disponibili solo nelle modalità Shared e Standard e il supporto SSL per i domini personalizzati è disponibile solo in modalità Standard. Per informazioni sulla configurazione di SSL per un dominio personalizzato in Azure, vedere [Abilitazione di HTTPS per un sito Web di Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).
    -   **domain names**: consente di visualizzare o di aggiungere ulteriori nomi di dominio per il sito Web. È possibile aggiungere domini personalizzati facendo clic su **Manage Domains**. I domini personalizzati sono disponibili solo nelle modalità **Shared** e **Standard**. È possibile modificare la modalità del sito Web nella pagina di gestione **Scale**. I domini personalizzati non sono disponibili in modalità Free. Per ulteriori informazioni sulla configurazione di domini personalizzati, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).
    -   **SSL Bindings**: le associazioni SSL ai domini personalizzati sono disponibili solo in modalità Standard. Scegliere una modalità SSL (**SNI**, **IP** o **No SSL**) per un particolare nome di dominio. Se si sceglie SNI o IP, è possibile specificare un certificato per il dominio dai certificati caricati. Per informazioni sulla configurazione di SSL per un dominio personalizzato in Azure, vedere [Abilitazione di HTTPS per un sito Web di Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/). Per ulteriori informazioni su SNI, vedere la pagina relativa a [Indicazione nome server](http://en.wikipedia.org/wiki/Server_Name_Indication).
    -   **deployments**: utilizzare queste impostazioni per configurare le distribuzioni.
        -   **Git URL**: se è stato creato un archivio GIT nel sito Web di Azure in uso, si tratta del relativo URL, vale a dire la posizione in cui eseguire il push del contenuto.
        -   **Deployment Trigger URL**: questo URL può essere impostato in un archivio GitHub, CodePlex, Bitbucket o in un altro archivio per attivare la distribuzione quando viene eseguito il push di un commit nell'archivio.
        -   **Branch to Deploy**: consente di specificare il ramo che verrà distribuito quando si esegue il push dei contenuti al ramo in questione.
    -   **application diagnostics**: consente l'impostazione di opzioni per la raccolta di tracce di diagnostica da un'applicazione Web il cui codice è stato instrumentato con tracce. Le opzioni di registrazione per la diagnostica delle applicazioni includono:
        -   **Application Logging (File System)**: scegliere **On** per consentire la scrittura dei log applicazioni nel file system del sito Web. Una volta abilitata, la registrazione nel file system rimane attiva per un periodo di 12 ore. È possibile accedere ai log dalla condivisione FTP del sito Web. Il collegamento alla condivisione FTP è disponibile nel **Dashboard**. In **Quick Glance** scegliere **FTP Diagnostic Logs** o **FTPS Diagnostic Logs**.
        -   **Application Logging (Storage)**: scegliere **On** per consentire la scrittura dei log applicazioni in un account di archiviazione di Azure. La registrazione in un account di archiviazione non presenta alcun limite di tempo e rimane abilitata fino alla relativa disabilitazione. Per impostazione predefinita, i log vengono archiviati in una tabella denominata WAWSAppLogTable.
        -   **Logging Level**: quando è abilitata la registrazione, questa opzione consente di specificare la quantità di informazioni registrata (Error, Warning, Information o Verbose).
        -   **Diagnostic Storage**: fare clic su **Manage Connection** per visualizzare la finestra di dialogo **Manage diagnostic storage** con le seguenti opzioni per il salvataggio dei log nell'account di archiviazione di Azure in uso:
            -   **Storage Account Name**: scegliere l'account di archiviazione in cui si desidera salvare i log.
            -   **Storage Access Key**: consente di visualizzare la chiave per l'account di archiviazione scelto. Se è stata rigenerata la chiave per l'account di archiviazione, digitare manualmente la nuova chiave in questo punto oppure utilizzare uno dei pulsanti **Synchronize**. I pulsanti per la sincronizzazione sono disponibili solo se l'utente attualmente connesso dispone di accesso all'account di archiviazione selezionato.
            -   **Synchronize Primary Key**: consente di recuperare la chiave primaria più recente dell'account di archiviazione di Azure in uso.
            -   **Synchronize Secondary Key**: consente di recuperare la chiave secondaria più recente dell'account di archiviazione di Azure in uso.
                **Nota:** per ulteriori informazioni sulle chiavi di accesso alle risorse di archiviazione di Azure, vedere [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](http://www.windowsazure.com/en-us/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys).
    -   **site diagnostics**: consente di impostare le opzioni per raccogliere le informazioni di diagnostica per il sito Web, tra cui:
        -   **Web Server Logging**: consente di specificare se abilitare la registrazione del server Web per il sito Web. I log del server Web vengono salvati nel formato file di log esteso W3C. È possibile salvare i log in Azure Storage oppure nel file system.
            **Suggerimento**: La dimensione massima di archiviazione dei log nel file system è di 100 megabyte. Se si ritiene necessario conservare una cronologia dalle dimensioni maggiori, utilizzare Azure Storage, che offre una capacità di archiviazione decisamente superiore.
            -   Per salvare i log del server Web in un account di archiviazione di Azure, scegliere **Storage** e quindi scegliere **manage storage**. Nella finestra di dialogo **Manage Storage for Site Diagnostics** utilizzare l'opzione **Storage Account** per scegliere l'account di archiviazione di Azure per il contenitore in cui verranno archiviati i log. Selezionare l'opzione **Azure Blob Container** per scegliere il contenitore in cui verranno archiviati i log oppure selezionare **Create a new blob container** per abilitare la casella **Blob Name** in cui è possibile specificare un nome per il nuovo contenitore.
                **Nota**: Se non si dispone ancora di un account di archiviazione, visitare la sezione **Storage** del portale Azure e fare clic su **New** per creare un account.
            -   Se si sceglie **File System** i log verranno salvati sul sito FTP elencato nella sezione **FTP Diagnostic Logs** della pagina di gestione del Dashboard. L'abilitazione dell'archiviazione nel file system consente inoltre di attivare la casella **Quota** in cui è possibile impostare la quantità massima di spazio su disco per i file di log. La dimensione minima è 25 MB, mentre la massima è 100 MB. Il valore predefinito è 35 MB. Quando viene raggiunta la quota, i file più obsoleti vengono successivamente sovrascritti da quelli più recenti.
            -   Per impostazione predefinita, i log del server Web nell'account di archiviazione di Azure non vengono mai eliminati. Per specificare un periodo di tempo trascorso il quale i log vengono automaticamente eliminati, selezionare **Set Retention** e immettere il numero di giorni per la conservazione dei log nella casella **Retention Period**. È inoltre possibile utilizzare l'opzione **Set Retention** per l'archiviazione sul file system.
        -   **Detailed Error Messages**: consente di specificare se registrare i messaggi di errore dettagliati per il sito Web. Se questa opzione è abilitata, i messaggi di errore dettagliati verranno salvati come file HTM sul sito FTP elencato nella sezione FTP Diagnostic Logs della pagina di gestione del Dashboard. Dopo aver eseguito la connessione al sito FTP specificato, passare a /LogFiles/DetailedErrors/ per recuperare i file HTM che contengono i messaggi di errore dettagliati.
        -   **Failed Request Tracing**: consente di specificare se abilitare la traccia delle richieste non riuscite. Se questa opzione è abilitata, i risultati della traccia delle richieste non riuscite verranno salvati come file XML sul sito FTP elencato nella sezione FTP Diagnostic Logs della pagina di gestione del Dashboard. Dopo aver eseguito la connessione al sito FTP specificato, passare a /LogFiles/W3SVC########\# (dove ########\# rappresenta un identificatore univoco per il sito Web) per recuperare i file XML che contengono i risultati della traccia delle richieste non riuscite.
            **Importante**
            La cartella /LogFiles/W3SVC########\#/ contiene un file XSL e uno o più file XML. Verificare che il file XSL venga scaricato nella stessa directory dei file XML in quanto il file XSL fornisce le funzionalità per la formattazione e il filtro dei contenuti del file XML per la visualizzazione in Internet Explorer.
        -   **Remote Debugging**: impostare questa opzione su **On** per abilitare il debug remoto in Visual Studio 2012 o Visual Studio 2013. Se abilitata, è possibile utilizzare il debug remoto in Visual Studio per connettersi direttamente al sito Web di Azure.
             **Nota**: Il debug remoto sarà abilitato solo per 48 ore e non funzionerà con nomi sito o nomi utente con più di 20 caratteri.
    -   **monitoring**: per i siti Web in modalità Standard, consente di verificare la disponibilità degli endpoint HTTP o HTTPS. È possibile verificare un endpoint da un massimo di tre posizioni geograficamente distribuite. Un test di monitoraggio ha esito negativo se il codice della risposta HTTP è maggiore di o uguale a 400 o se la risposta richiede più di 30 secondi. Un endpoint è considerato disponibile se il test di monitoraggio ha esito positivo da tutte le posizioni specificate.
    -   **developer analytics**: scegliere **Add-on** per selezionare un componente aggiuntivo di analisi da un elenco oppure passare ad Azure Store per selezionarne uno. Scegliere **Custom** per selezionare un provider di analisi, ad esempio New Relic, da un elenco. Se si utilizza un provider personalizzato, è necessario immettere la chiave di licenza nella casella **Provider Key**.
         **Nota**: Per ulteriori informazioni sull'uso dell'applicazione New Relic con Siti Web di Azure, vedere [Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure](http://www.windowsazure.com/en-us/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
    -   **app settings**: consente di specificare coppie nome-valore che verranno caricate dall'applicazione Web all'avvio. Per i siti di .NET queste impostazioni verranno inserite nelle impostazioni di AppSettings della configurazione di .NET in fase di esecuzione, sostituendo le impostazioni esistenti. Per i siti PHP e Node queste impostazioni saranno disponibili come variabili di ambiente in fase di esecuzione.
    -   **connection strings**: consente di visualizzare le stringhe di connessione per le risorse collegate. Per i siti di .NET tali stringhe verranno inserite nelle impostazioni di connectionStrings della configurazione di .NET in fase di esecuzione, sostituendo le voci esistenti in cui la chiave è uguale al nome del database collegato. Per i siti PHP e Node queste impostazioni saranno disponibili come variabili di ambiente in fase di esecuzione, prefissate dal tipo di connessione. I prefissi delle variabili di ambiente sono i seguenti:
        -   SQL Server: SQLCONNSTR\_
        -   MySQL: MYSQLCONNSTR\_
        -   Database SQL: SQLAZURECONNSTR\_
        -   Personalizzato: CUSTOMCONNSTR\_

        Ad esempio, se una stringa di connessione MySql venisse denominata connectionstring1, l'accesso avverrebbe attraverso la variabile di ambiente `MYSQLCONNSTR_connectionString1`.
        **Nota**: Le stringhe di connessione vengono create quando si collega una risorsa del database a un sito Web e vengono lette solo quando sono visualizzate nella pagina di gestione configurazione.
    -   **default documents**: consente di aggiungere il documento predefinito del sito Web all'elenco, se non ancora inserito. Il documento predefinito di un sito Web è la pagina Web visualizzata quando un utente visita un sito Web e non ne specifica una pagina particolare. Supponendo quindi che il sito Web sia http://contoso.com, se il documento predefinito è impostato su default.htm, quando un utente specifica http://www.contoso.com nel browser, verrà indirizzato a http://www.contoso.com/default.htm. Se nell'elenco del sito Web sono presenti più file, accertarsi che il documento predefinito del sito stesso si trovi all'inizio dell'elenco modificando l'ordine dei file nell'elenco.
    -   **handler mappings**: aggiungere processori script personalizzati per gestire le richieste per estensioni di file specifiche. Specificare l'estensione di file da gestire nella casella **Extension** (ad esempio \*.php o handler.fcgi). Le richieste di file che corrispondono a questo modello saranno elaborate dal processore script specificato nella casella **Script Processor Path**. Per il processore script è necessario un percorso assoluto (è possibile utilizzare il percorso D:\\home\\site\\wwwroot per fare riferimento alla directory radice del sito). È possibile specificare argomenti della riga di comando facoltativi per il processore script nella casella **Additional Arguments (Optional)**.
    -   **virtual applications and directories** : per configurare applicazioni e directory virtuali associate al sito Web, specificare ogni directory virtuale e il corrispondente percorso fisico relativo alla radice del sito. È facoltativamente possibile selezionare la casella di controllo **Application** in modo da contrassegnare una directory virtuale come applicazione nella configurazione del sito.

4 .  Per salvare le modifiche alla configurazione, fare clic su **Save** nella parte inferiore della pagina di gestione **Configure**.

## Procedura: Configurare un sito Web per l'uso di un database SQL

Per collegare un sito Web a un database SQL, eseguire la procedura seguente:

1.  Nel [portale di gestione](http://manage.windowsazure.com), selezionare **Web Sites** per visualizzare l'elenco di siti Web creati dall'account attualmente connesso.

2.  Selezionare un sito Web dall'apposito elenco per visualizzare le pagine di **gestione** del sito Web.

3.  Fare clic sulla scheda **Linked Resources**. Verrà visualizzato un messaggio nella pagina **Linked Resources** che indica che **non si dispone di risorse collegate**.

4.  Fare clic su **Link a Resource** per visualizzare la procedura guidata di **collegamento delle risorse**.

5.  Fare clic su **Create a new resource** per visualizzare un elenco dei tipi di risorse che è possibile collegare al sito Web in uso.

6.  Fare clic su **SQL Database** per visualizzare la procedura guidata **Link Database**.

7.  Completare i campi richiesti alle pagine 3 e 4 della procedura guidata **Link Database** e quindi fare clic sul segno di spunta **Finish** a pagina 4.

Verrà creato un database SQL con i parametri specificati, che verrà quindi collegato al sito Web.

## Procedura: Configurazione di un sito Web per l'uso di un database MySQL
Per configurare un sito Web per l'uso di un database MySQL, eseguire la stessa procedura prevista per l'uso di un database SQL, ma nella procedura guidata **Link a Resource** scegliere **MySQL Database** invece di **SQL Database**.

In alternativa, è possibile creare il sito Web con l'opzione **Custom Create**. Nel menu a discesa **Database** scegliere **Create a new MySQL database** oppure **Use an existing MySQL database**.

## Procedura: Configurare un nome di dominio personalizzato
--------------------------------------------------------

Per informazioni sulla configurazione di un sito Web per l'uso di un nome di dominio personalizzato, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).

Procedura: Configurare un sito Web per l'uso di SSL
---------------------------------------------------

Per informazioni sulla configurazione di SSL per un dominio personalizzato in Azure, vedere [Abilitazione di HTTPS per un sito Web di Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).

Passaggi successivi
-------------------

-   [Come applicare la scalabilità ai siti Web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/)

-   [Come monitorare i siti Web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/)


