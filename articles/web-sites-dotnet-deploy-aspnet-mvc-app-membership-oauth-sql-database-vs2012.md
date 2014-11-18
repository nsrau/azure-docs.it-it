<properties linkid="dev-net-tutorials-web-app-with-sql-azure" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande"></tags>

# Distribuire un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure

***Data ultimo aggiornamento: 15 ottobre 2013.***

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/it-it/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Nota</strong><p>&Egrave; disponibile una <a href="/it-it/develop/net/tutorials/web-site-with-sql-database/">versione pi&ugrave; recente di questa esercitazione</a>. &Egrave; comunque possibile seguire questa versione se si desidera utilizzare Visual Studio 2012, tuttavia la nuova versione risulter&agrave; molto pi&ugrave; semplice da seguire.</p></div>

In questa esercitazione verrà illustrato come creare un'applicazione Web ASP.NET MVC 4 sicura che consenta agli utenti di effettuare l'accesso utilizzando credenziali di Facebook, Yahoo e Google. L'applicazione verrà inoltre distribuita in Azure.

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2012, con l'SDK verrà installato automaticamente Visual Studio Express 2012 per il Web. È possibile iniziare a sviluppare per Azure gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web sicura, basata sui dati, in esecuzione nel cloud e in grado di utilizzare un database cloud.

Si apprenderà come:

-   Abilitare il sistema per lo sviluppo in Azure installando Azure SDK.
-   Creare un progetto ASP.NET MVC 4 sicuro e pubblicarlo in un sito Web di Azure.
-   Utilizzare OAuth e il database di appartenenza ASP.NET per proteggere l'applicazione.
-   Distribuire un database di appartenenza in Azure.
-   Utilizzare un database SQL per l'archiviazione di dati in Azure.
-   Utilizzare Visual Studio per aggiornare e gestire il database di appartenenza.

Verrà creata una semplice applicazione Web di elenco contatti basata su ASP.NET MVC 4 che utilizza ADO.NET Entity Framework per l'accesso al database. Nell'illustrazione seguente viene mostrata la pagina di accesso dell'applicazione completata:

![pagina di accesso][pagina di accesso]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

Contenuto dell'esercitazione:

-   [Configurare l'ambiente di sviluppo][Configurare l'ambiente di sviluppo]
-   [Configurare l'ambiente Azure][Configurare l'ambiente Azure]
-   [Creare un'applicazione ASP.NET MVC 4][Creare un'applicazione ASP.NET MVC 4]
-   [Distribuire l'applicazione in Azure][Distribuire l'applicazione in Azure]
-   [Aggiungere un database all'applicazione][Aggiungere un database all'applicazione]
-   [Aggiungere un provider OAuth][Aggiungere un provider OAuth]
-   [Aggiungere ruoli al database di appartenenza][Aggiungere ruoli al database di appartenenza]
-   [Creare uno script di distribuzione dei dati][Creare uno script di distribuzione dei dati]
-   [Distribuire l'app in Azure][Distribuire l'app in Azure]
-   [Aggiornare il database di appartenenza][Aggiornare il database di appartenenza]
-   [Passaggi successivi][Passaggi successivi]

## <a name="bkmk_setupdevenv"></a>Configurare l'ambiente di sviluppo

Per iniziare, installare Azure SDK per .NET Framework per configurare l'ambiente di sviluppo.

1.  Per installare Azure SDK per .NET, fare clic sul collegamento seguente. Se Visual Studio 2012 non è ancora presente, verrà installato tramite questo collegamento. Per completare questa esercitazione, è necessario disporre di Visual Studio 2012.
    [Azure SDK per Visual Studio 2012][Azure SDK per Visual Studio 2012]
2.  Quando viene richiesto di eseguire o installare il file eseguibile di installazione, fare clic su **Esegui**.
3.  Nell'Installazione guidata piattaforma Web fare clic su **Installa** e procedere con l'installazione.

![Installazione guidata piattaforma Web - Azure SDK per .NET][Installazione guidata piattaforma Web - Azure SDK per .NET]

Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo.

## <a name="bkmk_setupwindowsazure"></a>Configurare l'ambiente Azure

Configurare l'ambiente Azure creando un sito Web di Azure e un database SQL.

### Creare un sito Web e un database SQL in Azure

Il sito Web di Azure verrà eseguito in un ambiente di hosting condiviso, ovvero verrà eseguito su macchine virtuali condivise con altri client di Azure. Un ambiente di hosting condiviso è un punto di partenza economicamente conveniente per iniziare a utilizzare il cloud. In seguito, in caso di incremento del traffico Web, è possibile scalare l'applicazione in modo da soddisfare le nuove esigenze tramite l'esecuzione su macchine virtuali dedicate. Se è necessaria un'architettura più complessa, è possibile eseguire la migrazione a un servizio cloud Azure. I servizi cloud vengono eseguiti su macchine virtuali dedicate, che possono essere configurate in base alle proprie esigenze specifiche.

Database SQL di Azure è un servizio di database relazionale basato sul cloud che si avvale delle tecnologie SQL Server. Gli strumenti e le applicazioni utilizzabili con SQL Server sono utilizzabili anche con database SQL.

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic su **Siti Web** nella scheda di sinistra, quindi su **Nuovo**.

![Pulsante New nel portale di gestione][Pulsante New nel portale di gestione]

1.  Fare clic su **CUSTOM CREATE**.

    ![Collegamento Create with Database nel portale di gestione][Collegamento Create with Database nel portale di gestione]

Verrà aperta la procedura guidata **Nuovo sito Web - Creazione personalizzata**.

1.  Nel passaggio **Nuovo sito Web** della procedura guidata immettere nella casella **URL** una stringa da usare come URL univoco per l'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato accanto alla casella di testo. Nell'illustrazione è visualizzato "contactmgr2", ma poiché è probabile che questo URL sia già in uso, sarà necessario sceglierne uno diverso.

    ![Collegamento Create with Database nel portale di gestione][1]

2.  Nell'elenco a discesa **Database** scegliere **Create a new SQL database**.

3.  Nell'elenco a discesa **Regione** scegliere la stessa area selezionata per il sito Web. Questa impostazione consente di specificare il data center in cui verrà eseguita la macchina virtuale. In **DB CONNECTION STRING NAME** immettere *connectionString1*.

    ![Passaggio Nuovo sito Web della procedura guidata Nuovo sito Web - Crea con database][1]

4.  Fare clic sulla freccia a destra nella parte inferiore della finestra di dialogo. Verrà visualizzato il passaggio **Specify database settings** della procedura guidata.

5.  Nella casella **Nome** immettere *ContactDB*.

6.  Nella casella **Server** selezionare **New SQL Database server**. In alternativa, se è stato creato in precedenza un database SQL Server, è possibile selezionare tale database SQL Server dall'elenco a discesa.

7.  Immettere un **LOGIN NAME** e una **PASSWORD** per l'amministratore. Se è stata selezionata l'opzione **New SQL Database server**, in questo passaggio non sarà necessario immettere un nome e una password esistenti, ma un nuovo nome e una nuova password, definiti ora e da utilizzare in seguito per l'accesso al database. Se è stato selezionato un database SQL Server creato in precedenza, verrà richiesta la password del nome account SQL Server creato in precedenza. Per questa esercitazione non verrà selezionata la casella **Advanced**. La casella **Avanzate** consente di impostare la dimensione del database (il valore predefinito è 1 GB ma è possibile aumentarlo fino a 150 GB) e le regole di confronto.

8.  Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo per indicare che le operazioni sono state completate.

    ![Passaggio Impostazioni database della procedura guidata Nuovo sito Web - Crea con database][Passaggio Impostazioni database della procedura guidata Nuovo sito Web - Crea con database]

    Nell'immagine seguente viene mostrato l'utilizzo di un database SQL Server e un accesso esistenti.
    ![Database Settings step of New Website - Create with Database wizard][Database Settings step of New Website - Create with Database wizard]

    Il portale di gestione torna alla pagina Siti Web e nella colonna **Stato** viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna **Status** viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra accanto all'icona **Siti Web** e il numero di database viene visualizzato accanto all'icona **Database SQL**.

## <a name="bkmk_createmvc4app"></a>Creare un'applicazione ASP.NET MVC 4

Il sito Web di Azure è stato creato, ma non include ancora alcun contenuto. Il passaggio successivo consiste nel creare il progetto dell'applicazione Web di Visual Studio che verrà pubblicato in Azure.

### Creare il progetto

1.  Avviare Visual Studio 2012.
2.  Scegliere **Nuovo progetto** dal menu **File**.
3.  Nella finestra di dialogo **New Project** espandere **Visual C#** e selezionare **Web** in **Modelli installati**, quindi selezionare **Applicazione Web ASP.NET MVC4**. Mantenere l'opzione **.NET Framework 4.5** predefinita. Assegnare all'applicazione il nome **ContactManager** e fare clic su **OK**.

    ![New Project dialog box][New Project dialog box]

4.  Nella finestra di dialogo **Nuovo progetto ASP.NET MVC 4** selezionare il modello **Applicazione Internet**. Mantenere l'opzione Razor predefinita **Motore di visualizzazione** quindi fare clic su **OK**.

    ![Finestra di dialogo Nuovo progetto ASP.NET MVC 4][Finestra di dialogo Nuovo progetto ASP.NET MVC 4]

### Impostare intestazione e piè di pagina

1.  In **Esplora soluzioni** espandere la cartella Views\\Shared e aprire il file\* \_Layout.cshtml\*.

    ![File \_Layout.cshtml in Esplora soluzioni][File \_Layout.cshtml in Esplora soluzioni]

2.  Sostituire ogni occorrenza di "My ASP.NET MVC Application" con "Contact Manager".
3.  Sostituire "your logo here" con "CM Demo".

### Eseguire l'applicazione in locale

1.  Premere CTRL+F5 per eseguire l'applicazione. La home page dell'applicazione verrà visualizzata nel browser predefinito.
    ![To Do List home page][To Do List home page]

Non è necessario eseguire per il momento altre operazioni per creare l'applicazione che verrà distribuita in Azure. La funzionalità di database verrà aggiunta in un secondo momento.

## <a name="bkmk_deploytowindowsazure1"></a>Distribuire l'applicazione in Azure

1.  Accedere al [portale di gestione di Azure][2] dal browser.

2.  Nella scheda **Siti Web** fare clic sul nome del sito creato in precedenza.

    ![Applicazione Contact Manager nella scheda Siti Web del portale di gestione][Applicazione Contact Manager nella scheda Siti Web del portale di gestione]

3.  Sul lato destro della finestra fare clic su **Download publish profile**.

    ![Scheda Quickstart e pulsante Download Publishing Profile][Scheda Quickstart e pulsante Download Publishing Profile]

    Questo passaggio consente di scaricare un file che contiene tutte le impostazioni necessarie per la distribuzione di un'applicazione nel sito Web. Il file verrà importato in Visual Studio in modo da non dover immettere le informazioni manualmente.

4.  Salvare il file .*publishsettings* in una cartella a cui sia possibile accedere da Visual Studio.

    ![Salvataggio del file .publishsettings.][Salvataggio del file .publishsettings.]

    [WACOM.INCLUDE [publishsettingsfilewarningchunk][publishsettingsfilewarningchunk]]

5.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

    ![Publish in project context menu][Publish in project context menu]

    Viene visualizzata la procedura guidata **Pubblica sito Web**.

6.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Importa**.

    ![Import publish settings][Import publish settings]

    Verrà visualizzata la finestra di dialogo **Import Publish Profile**.

7.  Se non è stata precedentemente aggiunta la sottoscrizione di Azure in Visual Studio, eseguire i passaggi seguenti. In questa procedura verrà aggiunta la sottoscrizione affinché il sito Web in uso compaia nell'elenco a discesa **Importa da un sito Web di Azure**.

    a. Nella finestra di dialogo **Import Publish Profile** fare clic su **Aggiungi sottoscrizione di Azure**.

    ![Aggiunta sottoscrizione di Azure][Aggiunta sottoscrizione di Azure]

    b. Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Scarica file di sottoscrizione**.

    ![download sottoscrizione][download sottoscrizione]

    c. Nella finestra del browser salvare il file *.publishsettings*.

    ![download file pubblicazione][download file pubblicazione]

    > [WACOM.NOTE]
    > Il file .publishsettings contiene le credenziali (non codificate) usate per amministrare le sottoscrizioni e i servizi di Azure. La procedura consigliata di sicurezza per questo file consiste nell'archiviarlo temporaneamente all'esterno delle directory di origine, ad esempio nella cartella Raccolte\\Documenti, e quindi di eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file .publishsettings può modificare, creare ed eliminare i servizi di Azure.

    d. Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Sfoglia** per individuare il file *.publishsettings*.

    ![download sottoscrizione][download sottoscrizione]

    e. Fare clic su **Importa**.

    ![importazione][importazione]

8.  Nella finestra di dialogo **Importa profilo di pubblicazione** selezionare **Importa da un sito Web di Azure**, selezionare il sito Web nell'elenco a discesa e quindi fare clic su **OK**.

    ![Opzione Import Publish Profile][Opzione Import Publish Profile]

    L'applicazione creata verrà ora eseguita nel cloud. Alla successiva distribuzione dell'applicazione, verranno distribuiti solo i file modificati o nuovi.

    ![Pagina iniziale Elenco azioni in esecuzione in Azure][Pagina iniziale Elenco azioni in esecuzione in Azure]

## <a name="bkmk_addadatabase"></a>Aggiungere un database all'applicazione

Si passerà quindi all'aggiornamento dell'applicazione MVC, in modo da aggiungere la possibilità di visualizzare e aggiornare i contatti e di archiviare i dati in un database. L'applicazione utilizzerà Entity Framework per creare il database e per leggere e aggiornare i dati nel database.

### Aggiungere classi del modello di dati per i contatti

Creare innanzitutto un semplice modello di dati nel codice.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Modelli, quindi scegliere **Aggiungi** e infine **Classe**.

![Aggiungi classe nel menu di scelta rapida della cartella Modelli][Aggiungi classe nel menu di scelta rapida della cartella Modelli]

1.  Nella finestra di dialogo **Aggiungi nuovo elemento** assegnare al nuovo file di classe il nome *Contact.cs*, quindi fare clic su **Aggiungi**.

![Finestra di dialogo Aggiungi nuovo elemento][Finestra di dialogo Aggiungi nuovo elemento]

1.  Sostituire il contenuto del file Contacts.cs con il codice seguente.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }

La classe **Contacts** consente di definire i dati che verranno archiviati per ogni contatto, oltre a una chiave primaria, *ContactID*, necessaria per il database.

### Creare pagine Web che consentono agli utenti dell'app di utilizzare i contatti

La funzionalità di scaffolding di ASP.NET MVC consente di generare automaticamente codice per l'esecuzione di azioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete).

## <a name="bkmk_addcontroller"></a>Aggiungere un controller e una visualizzazione per i dati

1.  Creare il progetto **(CTRL+MAIUSC+B)**. Per utilizzare il meccanismo scaffolding, è innanzitutto necessario creare il progetto.
2.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Controllers, quindi scegliere **Aggiungi** e infine **Controllers**.

    ![Opzione Aggiungi controller nel menu di scelta rapida della cartella Controllers][Opzione Aggiungi controller nel menu di scelta rapida della cartella Controllers]

3.  Nella finestra di dialogo **Aggiungi controller** immettere "HomeController" come nome del controller.
4.  Impostare il modello delle **Opzioni scaffolding** su **MVC Controller with read/write actions and views, using Entity Framework**.
5.  Selezionare **Contact** come classe modello e **\<Nuovo contesto dati...\>** come classe contesto dati.

    ![Finestra di dialogo Aggiungi controller][Finestra di dialogo Aggiungi controller]

6.  Nella finestra di dialogo **Nuovo contesto dati** accettare il valore predefinito *ContactManager.Models.ContactManagerContext*.
    ![Add Controller dialog box][Add Controller dialog box]

7.  Fare clic su **OK**, quindi su **Aggiungi** nella finestra di dialogo **Aggiungi controller**.
8.  Nella finestra di dialogo **Aggiungi controller** verificare che tutte le opzioni siano selezionate e fare clic su **OK**.

    ![Finestra di messaggio Aggiungi controller][Finestra di messaggio Aggiungi controller]

In Visual Studio verranno creati metodi e visualizzazioni di un controller per operazioni CRUD di database per oggetti **Contact**.

## Abilitare le migrazioni, creare il database, aggiungere dati di esempio e un inizializzatore di dati

L'attività successiva consiste nell'abilitare la funzionalità [Migrazioni Code First][Migrazioni Code First], in modo da creare il database basato sul modello di dati creato.

1.  Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.

    ![Console di Gestione pacchetti nel menu Strumenti][Console di Gestione pacchetti nel menu Strumenti]

2.  Nella finestra **Console di Gestione pacchetti** immettere il comando seguente:

        enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations][enable-migrations]
     È necessario specificare il nome del tipo contesto (**ContactManagerContext**) perché il progetto contiene due classi derivate [DbContext][DbContext], l'elemento **ContactManagerContext** appena aggiunto e l'elemento **UsersContext**, utilizzato per il database di appartenenza. La classe **ContactManagerContext** è stata aggiunta dalla procedura guidata di scaffolding di Visual Studio.

    Il comando **enable-migrations** consente di creare una cartella *Migrations* e di inserire in tale tabella il file *Configuration.cs*, che può essere modificato per configurare le migrazioni.

3.  Nella finestra **Console di Gestione pacchetti** immettere il comando seguente:

        add-migration Initial

    Il comando **add-migration Initial** consente di generare un file con nome **\<timbro\_data\>Initial** nella cartella *Migrations* che crea il database. Il primo parametro (**Initial**) è arbitrario e viene usato per creare il nome del file. È possibile visualizzare i nuovi file di classe in **Esplora soluzioni**.

    Nella classe **Initial** il metodo **Up** consente di creare la cartella Contacts e il metodo **Down**, utilizzato quando si desidera tornare allo stato precedente, consente di rimuoverla.

4.  Aprire il file *Migrations\\Configuration.cs*.
5.  Aggiungere gli spazi dei nomi seguenti.

         using ContactManager.Models;

6.  Sostituire il metodo *Seed* con il codice seguente:

        protected override void Seed(ContactManager.Models.ContactManagerContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

    Il codice precedente consente di inizializzare il database con le informazioni sui contatti. Per altre informazioni sul seeding del database, vedere la pagina relativa al [seeding e al debug di database di Entity Framework (EF)][seeding e al debug di database di Entity Framework (EF)].

7.  In **Console di Gestione pacchetti** immettere il comando seguente:

        update-database

    ![Package Manager Console commands][Package Manager Console commands]

    Il comando **update-database** consente di eseguire la prima migrazione al fine di creare il database. Per impostazione predefinita, il database viene creato come database LocalDB di SQL Server Express. Qualora sia installato SQL Server Express, per creare il database verrà utilizzata l'istanza di SQL Server Express.

8.  Premere CTRL+F5 per eseguire l'applicazione.

Nell'applicazione vengono mostrati i dati di seeding e sono disponibili collegamenti per la modifica, i dettagli e l'eliminazione.

![Visualizzazione MVC dei dati][Visualizzazione MVC dei dati]

## <a name="addOauth"></a><span class="short-header">OAuth</span>Aggiungere un provider OAuth

[OAuth][OAuth] è un protocollo aperto che consente di eseguire l'autenticazione sicura in un metodo semplice e standard da applicazioni Web, per dispositivi mobili e desktop. Il modello Internet ASP.NET MVC utilizza OAuth per esporre Facebook, Twitter, Google, Yahoo e Microsoft come provider di autenticazione. Benché in questa esercitazione si utilizzino solo Facebook, Google e Yahoo come provider di autenticazione, è possibile modificare con facilità il codice per utilizzare un altro provider. I passaggi per l'implementazione di altri provider sono molto simili ai passaggi disponibili in questa esercitazione.

Oltre all'autenticazione, nell'esercitazione verranno utilizzati anche i ruoli per implementare l'autorizzazione. Solo gli utenti aggiunti al ruolo canEdit saranno in grado di creare, modificare o eliminare contatti.

## Registrazione presso un provider esterno

Per autenticare utenti con credenziali di provider esterni, è necessario registrare il sito Web con il provider e quindi ottenere una chiave e una chiave privata per la connessione. Per Google e Yahoo non è richiesta alcuna registrazione e non è necessario ottenere chiavi.

In questa esercitazione non vengono riportati tutti i passaggi necessari per la registrazione con questi provider. Tali passaggi non sono in genere difficili. Per registrare correttamente il sito, seguire le istruzioni fornite nei siti dei relativi provider. Per iniziare a registrare il sito, vedere il sito per sviluppatori di:

-   [Facebook][Facebook]
-   [Microsoft][Microsoft]
-   [Twitter][Twitter]

Passare alla pagina [][0]<https://developers.facebook.com/apps></a> ed effettuare l'accesso se necessario. Fare clic su **Register as a Developer** e completare il processo di registrazione. Dopo aver completato la registrazione, fare clic su **Create New App**. Immettere un nome per l'app. Non è necessario immettere uno spazio dei nomi per l'app.

![Creazione di una nuova app FB][Creazione di una nuova app FB]

Immettere localhost per **App Domain** e <http://localhost/> per **Site URL**. Fare clic su **Enabled** per **Sandbox Mode**, quindi fare clic su **Save Changes**.

Per implementare OAuth in questa applicazione, saranno necessari un **ID app** e una **chiave privata app**.
 ![Nuova app FB][Nuova app FB]

## Creazione di utenti test

Nella sezione **Settings** presente nel riquadro sinistro, fare clic su **Developer Roles**. Fare clic sul collegamento **Create** nella riga **Test Users** (non nella riga **Testers**).

![Tester FB][Tester FB]

Fare clic sul collegamento **Modify** per ricevere il messaggio di posta elettronica per gli utenti test (che verrà utilizzato per accedere all'applicazione). Fare clic sul collegamento **See More**, quindi su **Edit** per impostare la password degli utenti test.

## Aggiunta dell'ID e della chiave privata app del provider

Aprire il file *App\_Start\\AuthConfig.cs*. Rimuovere i caratteri di commento dal metodo *RegisterFacebookClient* e aggiungere l'ID e la chiave privata app. Utilizzare i valori ottenuti, poiché quelli illustrati di seguito non funzioneranno. Rimuovere i caratteri di commento dalla chiamata *OAuthWebSecurity.RegisterGoogleClient* e aggiungere *OAuthWebSecurity.RegisterYahooClient* come illustrato di seguito. I provider Google e Yahoo non richiedono alcuna registrazione e non è necessario ottenere chiavi.
Avviso: conservare l'ID e la chiave privata app al sicuro. Un utente malintenzionato che ne entrasse in possesso potrebbe impersonare il proprietario dell'applicazione.

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "enter numeric key here",
                appSecret: "enter numeric secret here");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  Eseguire l'applicazione e fare clic sul collegamento **Log In**.
2.  Fare clic sul pulsante **Facebook**.
3.  Immettere le credenziali di Facebook o le credenziali di uno degli utenti test.
4.  Fare clic su **Okay** per consentire all'applicazione di accedere alle risorse Facebook.
5.  Si verrà reindirizzati alla pagina di registrazione. Se si è eseguito l'accesso utilizzando un account di prova, è possibile modificare il **nome utente** scegliendone uno più breve, ad esempio "Bill FB test". Utilizzare il pulsante di **registrazione** che consentirà di salvare il nome utente e l'alias di posta elettronica nel database di appartenenza.
6.  Registrare un altro utente. Attualmente, un bug nel sistema di accesso non consente di disconnettersi e accedere nuovamente come un altro utente utilizzando lo stesso provider (ad esempio non è possibile disconnettersi dall'account di Facebook per accedere di nuovo con un account di Facebook diverso). Per aggirare questo problema, raggiungere il sito utilizzando un browser diverso e registrare un altro utente. Un utente verrà aggiunto al ruolo di gestione e disporrà di accesso per la modifica dell'applicazione, mentre l'altro avrà unicamente accesso a metodi non di modifica nel sito. Gli utenti anonimi avranno accesso unicamente alla home page.
7.  Registrare un altro utente utilizzando un provider diverso.
8.  **Facoltativo**: è possibile creare un account locale non associato ad alcun provider. La possibilità di creare account locali verrà rimossa più avanti nell'esercitazione. Per creare un account locale, fare clic sul collegamento di **disconnessione** (se si è connessi), quindi fare clic sul collegamento per la **registrazione**. È possibile creare un account locale per attività amministrative non associate ad alcun provider di autenticazione esterno.

## <a name="mbrDB"></a><span class="short-header">Database di appartenenza</span>Aggiunta di ruoli al database di appartenenza

In questa sezione verrà aggiunto al database di appartenenza il ruolo *canEdit*. Solo gli utenti con ruolo canEdit saranno in grado di modificare i dati. È consigliabile assegnare ai ruoli un nome corrispondente alle azioni che consentono di eseguire, quindi il nome *canEdit* è preferibile rispetto ad *admin*. Nel corso dell'evolversi dell'applicazione, sarà possibile aggiungere nuovi ruoli quali *canDeleteMembers*, invece di ruoli con nomi meno descrittivi quali *superAdmin*.

1.  Scegliere **Esplora server** dal menu **Visualizza**.

2.  In **Esplora server** espandere **DefaultConnection**, quindi espandere **Tabelle**.

3.  Fare clic con il pulsante destro del mouse su **UserProfile**, quindi scegliere **Mostra dati tabella**.

    ![Mostra dati tabella][Mostra dati tabella]

4.  Registrare il valore **UserId** per l'utente a cui si assegnerà il ruolo canEdit. Nell'immagine seguente all'utente *ricka* con **UserId** 2 viene assegnato il ruolo canEdit per il sito.

    ![ID utente][ID utente]

5.  Fare clic con il pulsante destro del mouse su **webpages\_Roles**, quindi scegliere **Mostra dati tabella**.
6.  Immettere **canEdit** nella cella **RoleName**. Il valore di **RoleId** sarà 1 se questa è la prima volta che si aggiunge un ruolo. Prendere nota del valore di RoleID. Assicurarsi che non sia presente un carattere di spazio finale, altrimenti "canEdit " nella tabella dei ruoli non corrisponderà a "canEdit" nel codice del controller.

    ![roleID][roleID]

7.  Fare clic con il pulsante destro del mouse su **webpages\_UsersInRoles**, quindi scegliere **Mostra dati tabella**. Immettere il valore **UserId** per l'utente a cui verrà assegnato l'accesso *canEdit* e il ruolo **RoleId**.

    ![Tabella UserId e RoleId][Tabella UserId e RoleId]

La tabella **webpages\_OAuthMembership** contiene il provider OAuth, il provider UserID e il valore UserID per ogni utente OAuth registrato. 
<!--Don't replace "-" with "_" or it won't validate-->
La tabella **webpages-Membership** contiene la tabella di appartenenze ASP.NET. È possibile aggiungere utenti a questa tabella utilizzando il collegamento di registrazione. È consigliabile aggiungere un utente con il ruolo *canEdit* che non sia associato a Facebook o a un altro provider di autorizzazioni di terze parti, in modo da poter avere sempre a disposizione l'accesso *canEdit* anche nel caso in cui il provider di autorizzazioni di terze parti non sia disponibile. La registrazione dell'appartenenza ASP.NET verrà rimossa più avanti nell'esercitazione.

## Protezione dell'applicazione con l'attributo Authorize

In questa sezione verrà applicato l'attributo [Authorize][Authorize] per limitare l'accesso ai metodi di azione. Gli utenti anonimi potranno solo visualizzare la home page. Gli utenti registrati potranno visualizzare i dettagli di contatto, le pagine informative e le pagine di contatto. Solo gli utenti appartenenti al ruolo *canEdit* saranno in grado di accedere ai metodi di azione e di modificare i dati.

1.  Aggiungere il filtro [Authorize][Authorize] e il filtro [RequireHttps][RequireHttps] all'applicazione. Un approccio alternativo consiste nell'aggiungere l'attributo [Authorize][Authorize] e l'attributo [RequireHttps][RequireHttps] a ogni controller, ma è consigliabile applicarli all'intera applicazione, per assicurare una protezione migliore. Grazie all'aggiunta a livello globale, ogni nuovo controller e ogni metodo di azione aggiunti saranno protetti automaticamente e non sarà necessario ricordarsi di applicare gli attributi. Per altre informazioni, vedere la pagina relativa alla [protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous][protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous]. Aprire il file *App\_Start\\FilterConfig.cs* e sostituire il metodo *RegisterGlobalFilters* con il codice seguente.

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

2.  Aggiungere l'attributo [AllowAnonymous][protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous] al metodo **Index**. L'attributo [AllowAnonymous][protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous] consente di aggiungere all'elenco di metodi consentiti i metodi che si desidera rifiutino in modo esplicito l'autorizzazione.
3.  Aggiungere [Authorize(Roles = "canEdit")] ai metodi Get e Post che modificano i dati (creazione, modifica, eliminazione).
4.  Aggiungere i metodi *About* and *Contact*. Di seguito è riportata una parte del codice completato.

        public class HomeController : Controller
        {
            private ContactManagerContext db = new ContactManagerContext();
            [AllowAnonymous]
            public ActionResult Index()
            {
                return View(db.Contacts.ToList());
            }

            public ActionResult About()
            {
                return View();
            }

            public ActionResult Contact()
            {
                return View();
            }

            [Authorize(Roles = "canEdit")]
            public ActionResult Create()
            {
                return View();
            }
            // Methods moved and omitted for clarity.
        }

5.  Rimuovere la registrazione dell'appartenenza ASP.NET. La registrazione dell'appartenenza ASP.NET corrente nel progetto non offre supporto per la reimpostazione delle password e non consente di verificare che la registrazione venga effettuata da una persona reale, ad esempio tramite [CAPTCHA][CAPTCHA]. Dopo l'autenticazione tramite uno dei provider di terze parti, gli utenti possono eseguire la registrazione. In AccountController rimuovere l'attributo *[AllowAnonymous]* dai metodi *Register* di GET e POST. In questo modo si impedirà la registrazione da parte di bot o utenti anonimi.
6.  In *Views\\Shared\_LoginPartial.cshtml* rimuovere il collegamento azione Register.
7.  Abilitare SSL. In Esplora soluzioni fare clic sul progetto **ContactManager**, quindi fare clic su F4 per visualizzare la finestra di dialogo delle proprietà. Modificare il valore di **SSL abilitato** in true. Copiare **SSL URL**.

    ![Abilitazione di SSL][Abilitazione di SSL]

8.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Contact Manager**, quindi scegliere **Proprietà**.
9.  Nella scheda a sinistra fare clic su **Web**.
10. Modificare **URL progetto** affinché utilizzi **URL SSL**.
11. Fare clic su **Crea directory virtuale**.

    ![Abilitazione di SSL][3]

12. Premere CTRL+F5 per eseguire l'applicazione. Nel browser verrà visualizzato un avviso relativo al certificato. Nel caso di questa applicazione è possibile fare clic sul collegamento **Continue to this website**. Verificare che solo gli utenti nel ruolo *canEdit* possano modificare i dati. Verificare che gli utenti anonimi possano solamente visualizzare la home page.

    ![Avviso certificato][Avviso certificato]

    ![Avviso certificato][4]

I siti Web di Azure includono un certificato di sicurezza valido, pertanto questo avviso non verrà visualizzato quando si esegue la distribuzione in Azure.

## <a name="ppd"></a><span class="short-header">Preparazione del database</span>Creazione di uno script di distribuzione dei dati

</p>
Il database di appartenenza non è gestito da Code First di Entity Framework e non è pertanto possibile utilizzare Migrazioni per distribuirlo. Verrà utilizzato il provider [dbDacFx][dbDacFx] per distribuire lo schema del database, quindi verrà configurato il profilo di pubblicazione per eseguire uno script che inserirà i dati di appartenenza iniziali nelle tabelle di appartenenza.

In questa esercitazione si utilizzerà SQL Server Management Studio (SSMS) per creare script di distribuzione dei dati.

Installare SSMS dall'[Area download di Microsoft SQL Server 2012 Express][Area download di Microsoft SQL Server 2012 Express]:

-   [ITA\x64\SQLManagementStudio\_x64\_ITA.exe][ITA\x64\SQLManagementStudio\_x64\_ITA.exe] per sistemi a 64 bit.
-   [ITA\x86\SQLManagementStudio\_x86\_ITA.exe][ITA\x86\SQLManagementStudio\_x86\_ITA.exe] per sistemi a 32 bit.

Se si sceglie il file errato per il proprio sistema, l'installazione non riuscirà e sarà possibile provare con l'altro file.

Tenere presente che si tratta di un download di 600 megabyte e potrebbe richiedere molto tempo per l'installazione nonché il riavvio del computer.

Nella prima pagina del Centro installazione SQL Server fare clic su **Nuova installazione autonoma di SQL Server o aggiunta di funzionalità a un'installazione esistente**, quindi seguire le istruzioni, accettando le scelte predefinite. Nell'immagine di seguito è illustrato il passaggio relativo all'installazione di SSMS.

![Installazione di SQL][Installazione di SQL]

### Creare lo script del database di sviluppo

1.  Eseguire SSMS.
2.  Nella finestra di dialogo **Connetti al server** immettere *(localdb)\\v11.0* come nome server, lasciare **Autenticazione** impostato su **Autenticazione di Windows**, quindi fare clic su **Connetti**. Se è stato installato SQL Express, immettere **.\\SQLEXPRESS**.

    ![Finestra di dialogo Connetti al server][Finestra di dialogo Connetti al server]

3.  Nella finestra **Esplora oggetti** spandere **Database**, fare clic con il pulsante destro del mouse su **aspnet-ContactManager**, scegliere **Attività**, quindi **Genera script**.

    ![Genera script][Genera script]

4.  Nella finestra di dialogo **Genera e pubblica script** fare clic su **Imposta opzioni di generazione script**.
    È possibile saltare il passaggio **Seleziona oggetti** poiché l'opzione predefinita è "Genera script per intero database e tutti gli oggetti di database", che corrisponde all'opzione corretta.

5.  Fare clic su **Advanced**.

    ![Imposta opzioni di generazione script][Imposta opzioni di generazione script]

6.  Nella finestra di dialogo **Opzioni di scripting avanzate** scorrere verso il basso fino a **Tipi di dati per cui generare lo script**, quindi fare clic sull'opzione **Solo dati** nell'elenco a discesa (vedere l'immagine dopo il prossimo passaggio)

7.  Modificare **Script per USE DATABASE** in **False**. Le istruzioni USE non sono valide per database SQL di Azure e non sono necessarie per la distribuzione in SQL Server Express nell'ambiente di test.

    ![Imposta opzioni di generazione script][5]

8.  Fare clic su **OK**.
9.  Nella finestra di dialogo **Genera e pubblica script** la casella **Nome file** specifica la posizione in cui verrà creato lo script. Modificare questo percorso sostituendolo con il percorso della cartella della soluzione (quella che contiene il file *Contacts.sln*) e modificare il nome del file in *aspnet-data-membership.sql*.
10. Fare clic su **Avanti** per passare alla scheda **Riepilogo**, quindi fare nuovamente clic su **Avanti** per creare lo script.

    ![Salvataggio o pubblicazione][Salvataggio o pubblicazione]

11. Fare clic su **Finish**.

## <a name="bkmk_deploytowindowsazure11"></a>Distribuire l'app in Azure

1.  Aprire il file *Web.config* della radice dell'applicazione. Trovare il markup *DefaultConnection*, quindi copiarlo e incollarlo nella riga del markup di *DefaultConnection*. Modificare il nome dell'elemento copiato in *DefaultConnectionDeploy*. Questa stringa di connessione sarà necessaria per distribuire i dati utente nel database di appartenenza.
    ![3 cons str][3 cons str]

2.  Compilare l'applicazione.
3.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

    ![Publish in project context menu][6]

Viene visualizzata la procedura guidata **Pubblica sito Web**.

1.  Fare clic sulla scheda **Impostazioni**. Fare clic sull'icona **v** per selezionare la **Stringa di connessione remota** per **ContactManagerContext** e **DefaultConnectionDeploy**. I tre database elencati utilizzeranno tutti la stessa stringa di connessione. Nel database **ContactManagerContext** vengono archiviati i contatti, **DefaultConnectionDeploy** viene usato unicamente per distribuire i dati relativi agli account utente al database di appartenenza e il database **UsersContext** è il database di appartenenza stesso.

    ![Impostazioni][Impostazioni]

2.  In **ContactManagerContext** selezionare **Execute Code First Migrations**.

    ![Impostazioni][7]

3.  In **DefaultConnectionDeploy** selezionare **Aggiorna database**, quindi fare clic sul collegamento **Configure database updates**.
4.  Fare clic sul collegamento **Aggiungi script SQL** e passare al file *aspnet-data-membership.sql*. È necessario eseguire questa operazione una sola volta. Per la prossima distribuzione si deselezionerà **Aggiorna database** poiché non sarà necessario aggiungere i dati utente alle tabelle di appartenenza.

    ![Aggiunta SQL][Aggiunta SQL]

5.  Fare clic su **Pubblica**.
6.  Passare alla pagina [][0]<https://developers.facebook.com/apps></a> e sostituire le impostazioni per **Domini app** e **URL sito** con quelle relative all'URL di Azure.
7.  Testare l'applicazione. Verificare che solo gli utenti nel ruolo *canEdit* possano modificare i dati. Verificare che gli utenti anonimi possano solamente visualizzare la home page. Verificare che gli utenti autenticati possano spostarsi in tutti i collegamenti che non consentono la modifica dei dati.
8.  La prossima volta che si pubblica l'applicazione, assicurarsi di deselezionare **Aggiorna database** in **DefaultConnectionDeploy**.

    ![Impostazioni][7]

## <a name="ppd2"></a><span class="short-header">Aggiornamento del database</span>Aggiornamento del database di appartenenza

Dopo la distribuzione del sito in Azure, ora che si dispone di più utenti registrati, sarà possibile rendere alcuni di questi membri del ruolo *canEdit*. In questa sezione si utilizzerà Visual Studio per connettersi al database SQL e aggiungere utenti al ruolo *canEdit*.

![Impostazioni][7]

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.
    ![Publish][Publish]

2.  Fare clic sulla scheda **Impostazioni**.
3.  Copiare la stringa di connessione. La stinga di connessione usata in questo esempio è:
    Data Source=tcp:d015leqjqx.database.windows.net,1433;
    Initial Catalog=ContactDB2;User <Id=ricka0@d015lxyze;Password=xyzxyz>
4.  Viene visualizzata la finestra di dialogo di pubblicazione.
5.  Scegliere **Esplora server** dal menu **Visualizza**.

6.  Fare clic sull'icona **Connetti al database**.

    ![Publish][8]

7.  Se viene richiesta l'origine dati, fare clic su **Microsoft SQL Server**.
    ![Publish][9]

8.  Copiare e incollare il **Nome server**, che inizia per *tcp* (come illustrato nell'immagine seguente).
9.  Selezionare **Usa autenticazione di SQL Server**.
10. Immettere il **Nome utente** e la **Password**, presenti nella stringa di connessione copiata.
11. Immettere il nome del database (ContactDB, oppure la stringa dopo "Initial Catalog=" nel database, qualora non lo si fosse denominato ContactDB). Se viene visualizzata una finestra di dialogo di errore, vedere la prossima sezione.
12. Fare clic su **Test connessione**. Se viene visualizzata una finestra di dialogo di errore, vedere la prossima sezione.
    ![add con dlg][add con dlg]

## Errore di accesso di tipo Impossibile aprire il server

Se viene visualizzato un messaggio di errore "Impossibile aprire il server", sarà necessario aggiungere l'indirizzo IP agli IP consentiti.

![Errore del firewall][Errore del firewall]

1.  Nel portale di Azure selezionare **SQL Databases** nella scheda a sinistra.

    ![Selezione di SQL][Selezione di SQL]

2.  Selezionare il database da aprire.

3.  Fare clic sul collegamento **Set up Azure firewall rules for this IP address**.

    ![Regole del firewall][Regole del firewall]

4.  Quando viene visualizzata la richiesta "The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?", fare clic su **Yes**. L'aggiunta di questo indirizzo non è spesso sufficiente. In molti casi sarà necessario aggiungere un intervallo di indirizzi IP.

## Aggiunta di un intervallo di indirizzi IP consentiti

1.  Nel portale di Azure fare clic su **SQL Databases**.
2.  Fare clic sul **Server** che ospita il database.

    ![db server][db server]

3.  Fare clic su **Configure** nella parte superiore della pagina.
4.  Aggiungere un nome di regola e gli indirizzi IP di inizio e di fine.
    ![ip range][ip range]

5.  Nella parte inferiore della pagina fare clic su **Save**.
6.  Sarà ora possibile modificare il database di appartenenza utilizzando i passaggi illustrati in precedenza.

## <a name="nextsteps"></a><span class="short-header">Passaggi successivi</span>Passaggi successivi

Questa esercitazione e l'applicazione di esempio sono state scritte da [Rick Anderson][Rick Anderson] (Twitter [@RickAndMSFT][@RickAndMSFT]) con il supporto di Tom Dykstra, Tom FitzMacken e Barry Dorrans (Twitter [@blowdart][@blowdart]).

Se lo si desidera, inviare commenti e suggerimenti sugli aspetti ritenuti utili e su eventuali miglioramenti da apportare, non solo in merito all'esercitazione ma anche ai prodotti illustrati nell'esercitazione. I commenti e suggerimenti degli utenti risulteranno utili per definire la priorità dei miglioramenti da apportare. In particolare, saranno apprezzati i commenti relativi all'interesse in merito a un'ulteriore automazione per il processo di configurazione e distribuzione del database di appartenenza.

Per ottenere i pulsanti colorati per l'accesso a Facebook, Google e Yahoo, vedere il post di blog [Customizing External Login Buttons in ASP.NET MVC 4][Customizing External Login Buttons in ASP.NET MVC 4]. Per informazioni sull'utilizzo dell'autenticazione di Windows, vedere:

-   [Autenticazione di Azure][Autenticazione di Azure]
-   [Creazione di un sito Intranet mediante ASP.NET MVC][Creazione di un sito Intranet mediante ASP.NET MVC]

Un altro modo per archiviare i dati in un'applicazione di Azure consiste nell'utilizzare Archiviazione di Azure, che offre un servizio di archiviazione di dati non relazionali sotto forma di BLOB e tabelle. Per altre informazioni su ASP.NET MVC e Azure, vedere i collegamenti seguenti.

-   [Applicazione .NET multilivello con tabelle, code e BLOB di archiviazione di Azure][Applicazione .NET multilivello con tabelle, code e BLOB di archiviazione di Azure].
-   [Introduzione ad ASP.NET MVC 4][Introduzione ad ASP.NET MVC 4]
-   [Introduzione a Entity Framework con MVC][Introduzione a Entity Framework con MVC]
-   [OAuth 2.0 and Sign-In][OAuth 2.0 and Sign-In]

Sono state illustrate le procedure per distribuire un'applicazione Web in un sito Web di Azure. Per altre informazioni sulla configurazione, la gestione e il ridimensionamento di siti Web di Azure, vedere le procedure nella pagina [Attività comuni][Attività comuni].

Per altre informazioni sul debug dei siti Web di Azure vedere [Risoluzione dei problemi di Siti Web di Azure in Visual Studio][Risoluzione dei problemi di Siti Web di Azure in Visual Studio].

Per altre informazioni su come distribuire un'applicazione in un servizio Web di Azure, vedere [la versione della presente esercitazione dedicata ai servizi cloud][la versione della presente esercitazione dedicata ai servizi cloud] e [Sviluppo di applicazioni Web con Azure][Sviluppo di applicazioni Web con Azure]. Di seguito sono illustrate alcune delle ragioni per scegliere di eseguire un'applicazione Web ASP.NET in un servizio cloud di Azure anziché in un sito Web di Azure:

-   Sono necessarie autorizzazioni di amministratore nel server Web in cui viene eseguita l'applicazione.
-   È necessario utilizzare Connessione desktop remoto per accedere al server Web in cui viene eseguita l'applicazione.
-   L'applicazione è multilivello e si desidera distribuire il lavoro tra più server virtuali (Web e worker).

Per altre informazioni sui database SQL e l'archiviazione di Azure, vedere l'articolo relativo alle [offerte per l'archiviazione dei dati in Azure][offerte per l'archiviazione dei dati in Azure].

Per altre informazioni sull'utilizzo dei database SQL, vedere l'articolo sull'[utilizzo del database SQL di Azure in ASP.NET Data Access Content Map][utilizzo del database SQL di Azure in ASP.NET Data Access Content Map].

Per altre informazioni su Entity Framework e su Migrazioni Code First, vedere:

-   [Introduzione a Entity Framework con MVC][Introduzione a Entity Framework con MVC]
-   [Migrazioni Code First][10]


  [Visual Studio 2013]: /it-it/develop/net/tutorials/web-site-with-sql-database/ "Visual Studio 2013"
  [Visual Studio 2012]: /it-it/develop/net/tutorials/web-site-with-sql-database-vs2012/ "Visual Studio 2012"
  [versione più recente di questa esercitazione]: /it-it/develop/net/tutorials/web-site-with-sql-database/
  [pagina di accesso]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Configurare l'ambiente di sviluppo]: #bkmk_setupdevenv
  [Configurare l'ambiente Azure]: #bkmk_setupwindowsazure
  [Creare un'applicazione ASP.NET MVC 4]: #bkmk_createmvc4app
  [Distribuire l'applicazione in Azure]: #bkmk_deploytowindowsazure1
  [Aggiungere un database all'applicazione]: #bkmk_addadatabase
  [Aggiungere un provider OAuth]: #addOauth
  [Aggiungere ruoli al database di appartenenza]: #mbrDB
  [Creare uno script di distribuzione dei dati]: #ppd
  [Distribuire l'app in Azure]: #bkmk_deploytowindowsazure11
  [Aggiornare il database di appartenenza]: #ppd2
  [Passaggi successivi]: #nextsteps
  [Azure SDK per Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=254364
  [Installazione guidata piattaforma Web - Azure SDK per .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Pulsante New nel portale di gestione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png
  [Collegamento Create with Database nel portale di gestione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png
  [1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png
  [Passaggio Impostazioni database della procedura guidata Nuovo sito Web - Crea con database]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png
  [Database Settings step of New Website - Create with Database wizard]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png
  [New Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png
  [Finestra di dialogo Nuovo progetto ASP.NET MVC 4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png
  [File \_Layout.cshtml in Esplora soluzioni]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png
  [To Do List home page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png
  [2]: http://manage.windowsazure.com "portal"
  [Applicazione Contact Manager nella scheda Siti Web del portale di gestione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png
  [Scheda Quickstart e pulsante Download Publishing Profile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png
  [Salvataggio del file .publishsettings.]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png
  [publishsettingsfilewarningchunk]: ../includes/publishsettingsfilewarningchunk.md
  [Publish in project context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png
  [Import publish settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png
  [Aggiunta sottoscrizione di Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png
  [download sottoscrizione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png
  [download file pubblicazione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png
  [importazione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png
  [Opzione Import Publish Profile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png
  [Pagina iniziale Elenco azioni in esecuzione in Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png
  [Aggiungi classe nel menu di scelta rapida della cartella Modelli]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png
  [Finestra di dialogo Aggiungi nuovo elemento]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png
  [Opzione Aggiungi controller nel menu di scelta rapida della cartella Controllers]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png
  [Finestra di dialogo Aggiungi controller]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png
  [Add Controller dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
  [Finestra di messaggio Aggiungi controller]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png
  [Migrazioni Code First]: http://msdn.microsoft.com/library/hh770484.aspx
  [Console di Gestione pacchetti nel menu Strumenti]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png
  [enable-migrations]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png
  [DbContext]: http://msdn.microsoft.com/it-it/library/system.data.entity.dbcontext(v=VS.103).aspx
  [seeding e al debug di database di Entity Framework (EF)]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Package Manager Console commands]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png
  [Visualizzazione MVC dei dati]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png
  [OAuth]: http://oauth.net/ "http://oauth.net/"
  [Facebook]: http://developers.facebook.com/
  [Microsoft]: http://go.microsoft.com/fwlink/?LinkID=144070
  [Twitter]: http://dev.twitter.com/
  [0]: https://developers.facebook.com/apps/
  [Creazione di una nuova app FB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png
  [Nuova app FB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png
  [Tester FB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png
  [Mostra dati tabella]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png
  [ID utente]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png
  [roleID]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png
  [Tabella UserId e RoleId]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png
  [Authorize]: http://msdn.microsoft.com/it-it/library/system.web.mvc.authorizeattribute(v=vs.100).aspx
  [RequireHttps]: http://msdn.microsoft.com/it-it/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx
  [protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [Abilitazione di SSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png
  [Avviso certificato]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png
  [dbDacFx]: http://msdn.microsoft.com/it-it/library/dd394698.aspx
  [Area download di Microsoft SQL Server 2012 Express]: http://www.microsoft.com/it-it/download/details.aspx?id=29062
  [ITA\\x64\\SQLManagementStudio\_x64\_ITA.exe]: http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe
  [ITA\\x86\\SQLManagementStudio\_x86\_ITA.exe]: http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe
  [Installazione di SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png
  [Finestra di dialogo Connetti al server]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png
  [Genera script]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png
  [Imposta opzioni di generazione script]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png
  [Salvataggio o pubblicazione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png
  [3 cons str]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png
  [Impostazioni]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
  [Aggiunta SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png
  [Publish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png
  [add con dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png
  [Errore del firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png
  [Selezione di SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png
  [Regole del firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png
  [db server]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png
  [ip range]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Customizing External Login Buttons in ASP.NET MVC 4]: http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/
  [Autenticazione di Azure]: http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication
  [Creazione di un sito Intranet mediante ASP.NET MVC]: http://msdn.microsoft.com/it-it/library/gg703322(v=vs.98).aspx
  [Applicazione .NET multilivello con tabelle, code e BLOB di archiviazione di Azure]: http://www.windowsazure.com/it-it/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Introduzione ad ASP.NET MVC 4]: http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4
  [Introduzione a Entity Framework con MVC]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [OAuth 2.0 and Sign-In]: http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx
  [Attività comuni]: http://www.windowsazure.com/it-it/develop/net/common-tasks/
  [Risoluzione dei problemi di Siti Web di Azure in Visual Studio]: /it-it/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [la versione della presente esercitazione dedicata ai servizi cloud]: http://www.windowsazure.com/it-it/develop/net/tutorials/cloud-service-with-sql-database/
  [Sviluppo di applicazioni Web con Azure]: http://msdn.microsoft.com/it-it/library/Hh674484
  [offerte per l'archiviazione dei dati in Azure]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx
  [utilizzo del database SQL di Azure in ASP.NET Data Access Content Map]: http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb
  [10]: http://msdn.microsoft.com/it-it/library/hh770484
