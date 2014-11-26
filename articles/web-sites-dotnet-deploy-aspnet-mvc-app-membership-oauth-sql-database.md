<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Website with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande" />

# Distribuire un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure

***Ultimo aggiornamento: 2 aprile 2014.***

In questa esercitazione verrà illustrato come creare un'app Web ASP.NET MVC 5 sicura che consente agli utenti di effettuare l'accesso utilizzando credenziali di Facebook o Google. L'applicazione verrà inoltre distribuita in Azure.

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio Express 2013 per il Web. È possibile iniziare a sviluppare per Azure gratuitamente. Se si desidera utilizzare Visual Studio 2012, vedere l'[esercitazione precedente][esercitazione precedente]. Questa versione dell'esercitazione è molto più semplice rispetto alla versione precedente.

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web sicura, basata sui dati, in esecuzione nel cloud e in grado di utilizzare un database cloud.

Si apprenderà come:

-   Creare un progetto ASP.NET MVC 5 sicuro e pubblicarlo in un sito Web di Azure.
-   Utilizzare [OAuth][OAuth], [OpenID][OpenID] e il database di appartenenza ASP.NET per proteggere l'applicazione.
-   Utilizzare la nuova API di appartenenza per aggiungere utenti e ruoli.
-   Utilizzare un database SQL per l'archiviazione di dati in Azure.

Verrà creata una semplice applicazione Web di elenco contatti basata su ASP.NET MVC 5 che utilizza ADO.NET Entity Framework per l'accesso al database. Nell'illustrazione seguente viene mostrata la pagina di accesso dell'applicazione completata:

![pagina di accesso][pagina di accesso]

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione MSDN][attivare i benefici della sottoscrizione MSDN] oppure [iscriversi per una valutazione gratuita.][iscriversi per una valutazione gratuita.]

Contenuto dell'esercitazione:

-   [Configurare l'ambiente di sviluppo][Configurare l'ambiente di sviluppo]
-   [Configurare l'ambiente Azure][Configurare l'ambiente Azure]
-   [Creare un'applicazione ASP.NET MVC 5][Creare un'applicazione ASP.NET MVC 5]
-   [Distribuire l'applicazione in Azure][Distribuire l'applicazione in Azure]
-   [Aggiungere un database all'applicazione][Aggiungere un database all'applicazione]
-   [Aggiungere un provider OAuth][Aggiungere un provider OAuth]
-   [Utilizzo dell'API di appartenenza][Utilizzo dell'API di appartenenza]
-   [Distribuire l'app in Azure][Distribuire l'app in Azure]
-   [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Per utilizzare il nuovo certificato SSL per localhost, è necessario installare [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC] o versione successiva.

## <a name="bkmk_setupwindowsazure"></a>Configurare l'ambiente Azure

Configurare l'ambiente Azure creando un sito Web di Azure e un database SQL.

### Creare un sito Web e un database SQL in Azure

Il sito Web di Azure verrà eseguito in un ambiente di hosting condiviso, ovvero verrà eseguito su macchine virtuali condivise con altri client di Azure. Un ambiente di hosting condiviso è un punto di partenza economicamente conveniente per iniziare a utilizzare il cloud. In seguito, in caso di incremento del traffico Web, è possibile scalare l'applicazione in modo da soddisfare le nuove esigenze tramite l'esecuzione su macchine virtuali dedicate. Se è necessaria un'architettura più complessa, è possibile eseguire la migrazione a un servizio cloud Azure. I servizi cloud vengono eseguiti su macchine virtuali dedicate, che possono essere configurate in base alle proprie esigenze specifiche.

Database SQL di Azure è un servizio di database relazionale basato sul cloud che si avvale delle tecnologie SQL Server. Gli strumenti e le applicazioni utilizzabili con SQL Server sono utilizzabili anche con database SQL.

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic su **Siti Web** nella scheda di sinistra, quindi su **Nuovo**.

    ![Pulsante New nel portale di gestione][Pulsante New nel portale di gestione]

2.  Fare clic su **Sito Web** e quindi su **Creazione personalizzata**.

    ![Collegamento Create with Database nel portale di gestione][Collegamento Create with Database nel portale di gestione]

    Verrà aperta la procedura guidata **Nuovo sito Web - Creazione personalizzata**.

3.  Nel passaggio **Crea sito Web** della procedura guidata immettere nella casella **URL** una stringa da usare come URL univoco per l'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato accanto alla casella di testo. L'URL mostrato nell'illustrazione è probabilmente già in uso, quindi sarà necessario scegliere un URL diverso.

    ![Collegamento Create with Database nel portale di gestione][1]

4.  Nell'elenco a discesa **Database** scegliere **Create a free SQL database**.

5.  Nell'elenco a discesa **Regione** scegliere la stessa area selezionata per il sito Web.
    Questa impostazione specifica in quale data center verrà eseguita la macchina virtuale.
6.  Nella casella **DB Connection String Name** non modificare il valore predefinito *DefaultConnection*.
7.  Fare clic sulla freccia a destra nella parte inferiore della casella.
    La procedura guidata procede al passaggio **Specifica impostazioni di database**.

8.  Nella casella **Nome** immettere *ContactDB*. Vedere l'immagine di seguito.
9.  Nella casella **Server** selezionare **New SQL Database server**. Vedere l'immagine di seguito. In alternativa, se è stato creato in precedenza un database SQL Server, è possibile selezionare tale database SQL Server dall'elenco a discesa.
10. Impostare l'opzione **Regione** sulla stessa area specificata per la creazione del sito Web.
11. Immettere un **Login Name** e una **Password** per l'amministratore. Se è stata selezionata l'opzione **New SQL Database server**, in questo passaggio non sarà necessario immettere un nome e una password esistenti, ma un nuovo nome e una nuova password, definiti ora e da utilizzare in seguito per l'accesso al database. Se è stato selezionato un database SQL Server creato in precedenza, verrà richiesta la password del nome account SQL Server creato in precedenza. Per questa esercitazione non verrà selezionata la casella **Advanced**. Per un database gratuito è possibile impostare solo le regole di confronto.
12. Fare clic sul segno di spunta nella parte inferiore destra della finestra di dialogo per indicare che le operazioni sono state completate.

    ![Passaggio Impostazioni database della procedura guidata Nuovo sito Web - Crea con database][Passaggio Impostazioni database della procedura guidata Nuovo sito Web - Crea con database]

    Nell'immagine seguente viene mostrato l'utilizzo di un database SQL Server e un accesso esistenti.

    ![Passaggio Impostazioni database della procedura guidata Nuovo sito Web - Crea con database][2]

    Il portale di gestione torna alla pagina Siti Web e nella colonna **Stato** viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna **Status** viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra accanto all'icona **Siti Web** e il numero di database viene visualizzato accanto all'icona **Database SQL**.

## <a name="bkmk_createmvc4app"></a>Creare un'applicazione ASP.NET MVC 5

Il sito Web di Azure è stato creato, ma non include ancora alcun contenuto. Il passaggio successivo consiste nel creare l'app Web di Visual Studio che verrà pubblicata in Azure.

### Creare il progetto

1.  Scegliere **Nuovo progetto** dal menu **File**.

![New Project in File menu][New Project in File menu]

1.  Nella finestra di dialogo **Nuovo progetto** espandere **C\#** e selezionare **Web** in **Modelli installati**, quindi selezionare **Applicazione Web ASP.NET**.

2.  Assegnare all'applicazione il nome **ContactManager** e fare clic su **OK**.

![New Project dialog box][New Project dialog box]

**Nota:** nell'immagine viene mostrato "MyExample" come nome, ma è necessario immettere "ContactManager". Nei blocchi di codice che verranno copiati in seguito si presuppone che il nome del progetto sia ContactManager.

1.  Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**, **deselezionare** la casella di controllo **Create remote resources** e quindi fare clic su **OK**. (La casella di controllo potrebbe essere denominata **Host in the cloud** anziché **Create remote resources**).

![New ASP.NET Project dialog box][New ASP.NET Project dialog box]

### Impostare intestazione e piè di pagina

1.  In **Esplora soluzioni** aprire il file *Layout.cshtml* nella cartella *Views\\Shared*.

    ![File \_Layout.cshtml in Esplora soluzioni][File \_Layout.cshtml in Esplora soluzioni]

2.  Sostituire le due occorrenze di "My ASP.NET MVC Application" con "Contact Manager".
3.  Sostituire "Application name" con "CM Demo".

4.  Aggiornare il primo collegamento Action e sostituire *Home* con *Cm* per utilizzare il controller *Cm*.

    ![code changes][code changes]

### Eseguire l'applicazione in locale

1.  Premere CTRL+F5 per eseguire l'applicazione.

    La home page dell'applicazione verrà visualizzata nel browser predefinito.

    ![Web site running locally][Web site running locally]

Non è necessario eseguire per il momento altre operazioni per creare l'applicazione che verrà distribuita in Azure. La funzionalità di database verrà aggiunta in un secondo momento.

## <a name="bkmk_deploytowindowsazure1"></a>Distribuire l'applicazione in Azure

1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

![Publish in project context menu][Publish in project context menu]

Viene visualizzata la procedura guidata **Pubblica sito Web**.

1.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Siti Web di Azure**.

![Import publish settings][Import publish settings]

1.  Fare clic sul pulsante **Sign In** e accedere al portale di Azure.

![sign in][sign in]

Dopo avere eseguito l'accesso, verrà visualizzata la finestra di dialogo **Seleziona sito Web esistente**.

1.  Selezionare il sito Web creato nella prima parte di questa esercitazione e quindi fare clic su **OK**.

![select web site][select web site]

1.  Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.

    ![Publish][Publish]

    L'applicazione creata verrà ora eseguita nel cloud. Alla successiva distribuzione dell'applicazione, verranno distribuiti solo i file modificati o nuovi.

    ![Running in Cloud][Running in Cloud]

## <a name="bkmk_addadatabase"></a>Aggiungere un database all'applicazione

Si passerà quindi all'aggiornamento dell'applicazione MVC, in modo da aggiungere la possibilità di visualizzare e aggiornare i contatti e di archiviare i dati in un database. L'applicazione utilizzerà Entity Framework per creare il database e per leggere e aggiornare i dati nel database.

### Aggiungere classi del modello di dati per i contatti

Creare innanzitutto un semplice modello di dati nel codice.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Modelli, quindi scegliere **Aggiungi** e infine **Classe**.

    ![Aggiungi classe nel menu di scelta rapida della cartella Modelli][Aggiungi classe nel menu di scelta rapida della cartella Modelli]

2.  Nella finestra di dialogo **Aggiungi nuovo elemento** assegnare al nuovo file di classe il nome *Contact.cs*, quindi fare clic su **Aggiungi**.

    ![Finestra di dialogo Aggiungi nuovo elemento][Finestra di dialogo Aggiungi nuovo elemento]

3.  Sostituire il contenuto del file Contacts.cs con il codice seguente.

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

3.  Nella finestra di dialogo **Add Scaffolding** selezionare **MVC 5 Controller with views, using EF**, quindi fare clic su **Aggiungi**.

    ![Finestra di dialogo Add Scaffold][Finestra di dialogo Add Scaffold]

4.  Nell'elenco a discesa **Classe modello** selezionare **Contact (ContactManager.Models)**. Vedere l'immagine di seguito.
5.  In **Classe contesto di dati** selezionare **ApplicationDbContext (ContactManager.Models)**. La classe **ApplicationDbContext** verrà utilizzata per il database di appartenenza e per i dati relativi ai contatti.
6.  Nella casella di immissione del testo **Nome controller** digitare "CmController" per il nome del controller.

    ![New data ctx dlg][New data ctx dlg]

7.  Fare clic su **Aggiungi**.

In Visual Studio verranno creati metodi e visualizzazioni di un controller per operazioni CRUD di database per oggetti **Contact**.

## Abilitare le migrazioni, creare il database, aggiungere dati di esempio e un inizializzatore di dati

L'attività successiva consiste nell'abilitare la funzionalità [Migrazioni Code First][Migrazioni Code First], in modo da creare il database basato sul modello di dati creato.

1.  Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.
    ![Package Manager Console in Tools menu][Package Manager Console in Tools menu]

2.  Nella finestra **Console di Gestione pacchetti** immettere il comando seguente:

        enable-migrations

    Il comando **enable-migrations** consente di creare una cartella *Migrations* e di inserire in tale tabella il file *Configuration.cs*, che può essere modificato per effettuare il seeding del database e configurare le migrazioni.

3.  Nella finestra **Console di Gestione pacchetti** immettere il comando seguente:

        add-migration Initial

    Il comando **add-migration Initial** consente di generare un file con nome **\<timbro\_data\>Initial** nella cartella *Migrations* che crea il database. Il primo parametro (**Initial**) è arbitrario e viene usato per creare il nome del file. I nuovi file di classe saranno visualizzati in **Esplora soluzioni**.
    Nella classe **Initial** il metodo **Up** consente di creare la cartella Contacts e il metodo **Down**, usato per tornare allo stato precedente, consente di rimuoverla.

4.  Aprire il file *Migrations\\Configuration.cs*.
5.  Aggiungere lo spazio dei nomi seguente.

         using ContactManager.Models;

6.  Sostituire il metodo *Seed* con il codice seguente:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
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

    Questo codice consente di inizializzare (effettuare il seeding) il database con le informazioni sui contatti. Per altre informazioni sul seeding del database, vedere la pagina relativa al [seeding e al debug di database di Entity Framework (EF)][seeding e al debug di database di Entity Framework (EF)].

7.  In **Console di Gestione pacchetti** immettere il comando seguente:

        update-database

    ![Package Manager Console commands][Package Manager Console commands]

    Il comando **update-database** consente di eseguire la prima migrazione al fine di creare il database. Per impostazione predefinita, il database viene creato come database LocalDB di SQL Server Express.

8.  Premere CTRL+F5 per eseguire l'applicazione, quindi fare clic sul collegamento **CM Demo** oppure passare a <http://localhost>:(port\#)/Cm.

    Nell'applicazione vengono mostrati i dati di seeding e sono disponibili collegamenti per la modifica, i dettagli e l'eliminazione. È possibile creare, modificare, eliminare e visualizzare i dati.

    ![Visualizzazione MVC dei dati][Visualizzazione MVC dei dati]

## <a name="addOauth"></a><span class="short-header">OAuth</span>Aggiungere un protocollo OAuth2 e un provider OpenID

[OAuth][OAuth] è un protocollo aperto che consente di eseguire l'autenticazione sicura in un metodo semplice e standard da applicazioni Web, per dispositivi mobili e desktop. Il modello Internet ASP.NET MVC utilizza OAuth e [OpenID][OpenID] per esporre Facebook, Twitter, Google e Microsoft come provider di autenticazione. Benché in questa esercitazione si utilizzi solo Google come provider di autenticazione, è possibile modificare con facilità il codice per utilizzare un altro provider. I passaggi per l'implementazione di altri provider sono molto simili ai passaggi disponibili in questa esercitazione. Per utilizzare Facebook come provider di autenticazione, vedere l'esercitazione relativa alla [creazione di un'app ASP.NET MVC 5 con Facebook e Google OAuth2 e accesso OpenID][creazione di un'app ASP.NET MVC 5 con Facebook e Google OAuth2 e accesso OpenID].

Oltre all'autenticazione, nell'esercitazione verranno utilizzati anche i ruoli per implementare l'autorizzazione. Solo gli utenti aggiunti al ruolo *canEdit* saranno in grado di modificare i dati, ovvero creare, modificare o eliminare i contatti.

1.  Seguire le istruzioni fornite nell'esercitazione e [Creare un'app ASP.NET MVC 5 App con Facebook e Google OAuth2 e accesso OpenID][creazione di un'app ASP.NET MVC 5 con Facebook e Google OAuth2 e accesso OpenID] in **Creazione di un'app Google per OAuth2**.
2.  Aprire il file *App\_Start\\Startup.Auth.cs*. Rimuovere i caratteri di commento dal metodo *app.UseGoogleAuthentication()* e immettere le informazioni relative a **clientId** e **clientSecret**.

<!-- -->

             public void ConfigureAuth(IAppBuilder app)          {             // Enable the application to use a cookie to store information for the signed in user
                app.UseCookieAuthentication(new CookieAuthenticationOptions             {                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                   LoginPath = new PathString("/Account/Login")             });             // Use a cookie to temporarily store information about a user logging in with a third party login provider
                app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

                app.UseGoogleAuthentication(
                   clientId: "000-000.apps.googleusercontent.com",
                   clientSecret: "00000000000");          }  

1.  Eseguire l'applicazione e fare clic sul collegamento **Log In**.
2.  In **Use another service to log in** fare clic sul pulsante **Google**.

    ![Accesso di Google][Accesso di Google]

3.  Immettere le credenziali.
4.  Il server di autenticazione di Google richiederà l'autorizzazione affinché l'app possa visualizzare l'indirizzo di posta elettronica dell'utente e le informazioni di base relative all'account. Fare clic su **Accetta**.
    ![GOOG asking for permission][GOOG asking for permission]
5.  Si verrà reindirizzati alla pagina di registrazione. Per impostazione predefinita, il nome utente corrisponderà all'alias di posta elettronica utilizzato per la registrazione, ma è possibile modificarlo se si desidera. Fare clic su **Register**.

    ![register][register]

## <a name="mbrDB"></a><span class="short-header">Database di appartenenza</span>Utilizzo dell'API di appartenenza

In questa sezione verranno aggiunti al database di appartenenza un utente locale e il ruolo *canEdit*. Solo gli utenti con ruolo *canEdit* saranno in grado di modificare i dati. È consigliabile assegnare ai ruoli un nome corrispondente alle azioni che consentono di eseguire, quindi il nome *canEdit* è preferibile rispetto ad *admin*. Nel corso dell'evolversi dell'applicazione, sarà possibile aggiungere nuovi ruoli quali *canDeleteMembers*, invece di ruoli con nomi meno descrittivi quali *superAdmin*.

1.  Aprire il file *migrations\\configuration.cs* e aggiungere le istruzioni `using` seguenti:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

2.  Aggiungere il metodo **AddUserAndRole** seguente alla classe:

         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

3.  Chiamare il nuovo metodo dal metodo **Seed**:

         protected override void Seed(ContactManager.Models.ApplicationDbContext context) { AddUserAndRole(context); context.Contacts.AddOrUpdate(p => p.Name, // Code removed for brevity }

    <span></span>
    Nelle immagini seguenti sono illustrate le modifiche apportate al metodo *Seed*:

    </p>
    ![Immagine del codice][Immagine del codice]

Questo codice consente di creare un nuovo ruolo denominato *canEdit*, quindi di creare un nuovo utente locale <*user1@contoso.com*> e infine di aggiungere <*user1@contoso.com>\* al ruolo *canEdit*. Per altre informazioni, vedere la [pagina relativa alle risorse di ASP.NET Identity][pagina relativa alle risorse di ASP.NET Identity].

## Usare codice temporaneo per aggiungere al ruolo canEdit nuovi utenti di accesso a social networking

In questa sezione sarà possibile modificare temporaneamente il metodo **ExternalLoginConfirmation** nel controller Account per aggiungere nuovi utenti che eseguono l'autenticazione con un provider OAuth oppure OpenID al ruolo *canEdit*. Il metodo **ExternalLoginConfirmation** verrà modificato temporaneamente per aggiungere automaticamente nuovi utenti a un ruolo amministrativo. Il codice temporaneo di registrazione automatica verrà utilizzato fino a quando non sarà disponibile uno strumento per l'aggiunta e la gestione dei ruoli. Ci auguriamo di riuscire a rendere disponibile in futuro uno strumento simile a [WSAT][WSAT] per consentire di creare e modificare account utente e ruoli. Più avanti nell'esercitazione verrà mostrato come utilizzare **Esplora server** per aggiungere utenti ai ruoli.

1.  Aprire il file **Controllers\\AccountController.cs** e passare al metodo **ExternalLoginConfirmation**.
2.  Aggiungere la chiamata seguente a **AddToRoleAsync** immediatamente prima della chiamata **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

Il codice riportato sopra consente di aggiungere l'utente appena registrato al ruolo "canEdit", in modo da concedere all'utente l'accesso ai metodi di azione per la modifica dei dati. Di seguito è riportata un'immagine della modifica del codice:

![Codice][Codice]

Più avanti nell'esercitazione l'applicazione verrà distribuita in Azure, a cui si effettuerà l'accesso tramite Google o un altro provider di autenticazione di terze parti. L'account appena registrato verrà aggiunto al ruolo *canEdit*. Chiunque abbia a disposizione l'URL del sito e un ID di Google potrà effettuare la registrazione e aggiornare il database. Per impedire ad altri utenti di eseguire tali operazioni, è possibile arrestare il sito. Per verificare gli utenti appartenenti al ruolo *canEdit*, è possibile esaminare il database.

In **Console di Gestione pacchetti** fare clic sul tasto di direzione per visualizzare il comando seguente:

        Update-Database

Eseguire il comando **Update-Database**, che consente di eseguire il metodo **Seed**, che a sua volta consente di eseguire il comando **AddUserAndRole** appena aggiunto. Il comando **AddUserAndRole** consente di creare l'utente <*user1@contoso.com>\* e aggiungerlo al ruolo *canEdit*.

## Proteggere l'applicazione con SSL e l'attributo Authorize

In questa sezione verrà applicato l'attributo [Authorize][Authorize] per limitare l'accesso ai metodi di azione. Gli utenti anonimi saranno in grado di visualizzare il metodo di azione **Index** solo per il controller Home. Gli utenti registrati saranno in grado di visualizzare i dati sui contatti (pagine **Index** e **Details** del controller Cm) e le pagine About e Contact. Solo gli utenti appartenenti al ruolo *canEdit* saranno in grado di accedere ai metodi di azione e di modificare i dati.

1.  Aggiungere il filtro [Authorize][Authorize] e il filtro [RequireHttps][RequireHttps] all'applicazione. Un approccio alternativo consiste nell'aggiungere l'attributo [Authorize][Authorize] e l'attributo [RequireHttps][RequireHttps] a ogni controller, ma è consigliabile applicarli all'intera applicazione, per assicurare una protezione migliore. Grazie all'aggiunta a livello globale, ogni nuovo controller e ogni metodo di azione aggiunti saranno protetti automaticamente e non sarà necessario ricordarsi di applicare gli attributi. Per altre informazioni, vedere la pagina relativa alla [protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous][protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous]. Aprire il file *App\_Start\\FilterConfig.cs* e sostituire il metodo *RegisterGlobalFilters* con il codice seguente, che consente di aggiungere due filtri:

         public static void RegisterGlobalFilters(GlobalFilterCollection filters) { filters.Add(new HandleErrorAttribute()); filters.Add(new System.Web.Mvc.AuthorizeAttribute()); filters.Add(new RequireHttpsAttribute()); } 

    <span></span>

    </p>
    Nell'immagine seguente è illustrato il codice modificato:

    ![Immagine del codice][3]

    Il filtro [Authorize][Authorize] applicato nel codice riportato sopra consente di impedire agli utenti anonimi di accedere ai metodi dell'applicazione. È possibile utilizzare l'attributo [AllowAnonymous][protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous] per rifiutare esplicitamente il requisito di autorizzazione in un paio di metodi, per consentire agli utenti anonimi di effettuare l'accesso e visualizzare la home page. Con [RequireHttps][RequireHttps] si rende obbligatorio l'accesso all'app Web tramite HTTPS.

2.  Aggiungere l'attributo [AllowAnonymous][protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous] al metodo Index del controller Home. L'attributo [AllowAnonymous][protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous] consente di aggiungere all'elenco di metodi consentiti i metodi che si desidera rifiutino in modo esplicito l'autorizzazione. Di seguito è riportata l'immagine di una parte del codice per HomeController:

    ![Codice][4]

3.  Eseguire una ricerca globale di *AllowAnonymous*. Come si può vedere, viene usato nei metodi di accesso e registrazione del controller Account.
4.  In *CmController.cs* aggiungere `[Authorize(Roles = "canEdit")]` ai metodi HttpGet e HttpPost che consentono di modificare i dati (Create, Edit, Delete, qualsiasi metodo di azione ad eccezione di Index e Details) nel controller *Cm*. Di seguito è riportata una parte del codice completato:

    ![Immagine del codice][5]

## Abilitare SSL per il progetto

1.  Abilitare SSL. In Esplora soluzioni fare clic sul progetto **ContactManager**, quindi fare clic su F4 per visualizzare la finestra di dialogo delle proprietà. Modificare il valore di **SSL abilitato** in true. Copiare **SSL URL**. L'URL SSL sarà <https://localhost:44300/> a meno che non siano stati creati siti Web SSL in precedenza.

    ![Abilitazione di SSL][Abilitazione di SSL]

2.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Contact Manager**, quindi scegliere **Proprietà**.
3.  Nella scheda a sinistra fare clic su **Web**.
4.  Modificare **URL progetto** in modo da utilizzare **URL SSL**, quindi salvare la pagina (CTRL+S).

    ![Abilitazione di SSL][6]

5.  Verificare che Internet Explorer sia il browser avviato da Visual Studio, come illustrato nell'immagine seguente:

    ![Browser predefinito][Browser predefinito]

    Lo strumento di selezione del browser consente di specificare il browser predefinito avviato da Visual Studio.

![browser selector][browser selector]

    You can select multiple browsers and have Visual Studio update each browser when you make changes. For more information see [Using Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

1.  Premere CTRL+F5 per eseguire l'applicazione. Seguire le istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express.

    ![Istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express][Istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express]

2.  Leggere quanto riportato nella finestra di dialogo **Avviso di sicurezza** e quindi fare clic su **Sì** se si desidera installare il certificato che rappresenta **localhost**.

![localhost IIS Express certificate warning][localhost IIS Express certificate warning]

1.  In Internet Explorer viene visualizzata la pagina *Home* e non viene visualizzato alcun avviso SSL.

    ![IE SSL localhost e nessun avviso][IE SSL localhost e nessun avviso]

    Anche Google Chrome accetta il certificato e visualizzerà il contenuto HTTPS senza alcun avviso. In Firefox viene usato un archivio certificati personalizzati, pertanto verrà visualizzato un avviso. Per questa applicazione, è possibile fare clic su **Sono consapevole dei rischi**.

    ![Avviso di Firefox relativo al certificato][Avviso di Firefox relativo al certificato]

2.  Se si è ancora connessi da una sessione precedente, fare clic sul collegamento **Disconnetti**.
3.  Fare clic sui collegamenti **About** o **Contact**. Si verrà reindirizzati alla pagina di accesso, poiché gli utenti anonimi non sono autorizzati a visualizzare tali pagine.
4.  Fare clic sul collegamento **Register as a new user** e aggiungere un utente locale con l'indirizzo di posta elettronica <*joe@contoso.com*>. Assicurarsi che *Joe* sia in grado di visualizzare le pagine Home, About e Contact.

    ![Accesso][Accesso]

5.  Fare clic sul collegamento *CM Demo* e verificare che i dati siano visibili.
6.  Fare clic su un collegamento di modifica nella pagina. Si verrà reindirizzati alla pagina di accesso, poiché il nuovo utente locale non è stato aggiunto al ruolo *canEdit*.
7.  Eseguire l'accesso con il nome utente <*user1@contoso.com>\* e la password "P\_assw0rd1" (il carattere "0" in "word" è uno zero). Si verrà reindirizzati alla pagina di modifica selezionata in precedenza.

Se non è possibile effettuare l'accesso con tale account e password, provare a copiare la password dal codice sorgente e incollarla. Se ancora non è possibile accedere, verificare la colonna **UserName** della tabella **AspNetUsers** per accertarsi che il nome utente <*user1@contoso.com>\* sia stato aggiunto.

1.  Verificare che sia possibile apportare modifiche ai dati.

## <a name="bkmk_deploytowindowsazure11"></a>Distribuire l'app in Azure

1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

    ![Publish in project context menu][7]

    Viene visualizzata la procedura guidata **Pubblica sito Web**.

2.  Fare clic sulla scheda **Impostazioni** nella parte sinistra della finestra di dialogo **Pubblica sito Web** dialog box. Fare clic sull'icona **v** per selezionare la **Stringa di connessione remota** per **ApplicationDbContext**, quindi selezionare **ContactDB**.

    ![Impostazioni][Impostazioni]

3.  In **ContactManagerContext** selezionare **Execute Code First Migrations**.

    ![Impostazioni][8]

4.  Fare clic su **Pubblica**.

5.  Effettuare l'accesso come<*user1@contoso.com>\* e verificare che sia possibile apportare modifiche ai dati.

6.  Effettuare la disconnessione.

7.  Effettuare l'accesso utilizzando Google o Facebook. L'account Google o Facebook verrà aggiunto al ruolo **canEdit**.

### Arrestare il sito Web per impedire la registrazione da parte di altri utenti

1.  In **Esplora server** passare a **Siti Web**.
2.  Fare clic con il pulsante destro del mouse su ogni istanza del sito Web, quindi selezionare **Arresta sito Web**.

    ![Opzione Arresta sito Web][Opzione Arresta sito Web]

    In alternativa, dal portale di gestione di Azure è possibile selezionare il sito Web, quindi fare clic sull'icona di **arresto** nella parte inferiore della pagina.

    ![Opzione Arresta sito Web][9]

### Rimuovere AddToRoleAsync, Publish e Test

1.  Impostare come commento o rimuovere il codice seguente dal metodo **ExternalLoginConfirmation** nel controller Account:
     `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
2.  Creare il progetto. Le modifiche apportate al file verranno salvate. Controllare che non si siano verificati errori di compilazione.
3.  Fare clic con il pulsante destro del mouse in **Esplora soluzioni**, quindi scegliere **Pubblica**.

    ![Publish in project context menu][Publish in project context menu]

4.  Fare clic sul pulsante **Start Preview**. Vengono distribuiti solo i file che necessitano di aggiornamento.
5.  Avviare il sito Web da Visual Studio o dal portale. **Non sarà possibile eseguire la pubblicazione mentre il sito Web è arrestato**.

    ![Avvio del sito Web][Avvio del sito Web]

6.  Tornare in Visual Studio e fare clic su **Pubblica**.
7.  L'app di Azure App verrà aperta nel browser predefinito. Se si è effettuato l'accesso, disconnettersi in modo da visualizzare la home page come un utente anonimo.
8.  Fare clic sul collegamento **About**. Si verrà reindirizzati alla pagina di accesso.
9.  Fare clic sul collegamento **Register** nella pagina di accesso, quindi creare un account locale. L'account locale verrà utilizzato per verificare se si è in grado di accedere alle pagine di sola lettura ma non alle pagine che consentono la modifica dei dati, protette dal ruolo *canEdit*. L'accesso dell'account locale verrà rimosso più avanti nell'esercitazione.

    ![Opzione Register][Opzione Register]

10. Verificare che sia possibile passare alle pagine *About* e *Contact*.

    ![Disconnessione][Disconnessione]

11. Fare clic sul collegamento **CM Demo** per passare al controller **Cm**. In alternativa è possibile aggiungere *Cm* alla fine dell'URL.

    ![Pagina CM][Pagina CM]

12. Fare clic su un collegamento per la modifica. Si verrà reindirizzati alla pagina di accesso. In **Use another service to log in** fare clic su Google o Facebook ed effettuare l'accesso con l'account utilizzato in precedenza per la registrazione. (Se si lavora velocemente e il cookie della sessione non è scaduto, l'accesso verrà eseguito automaticamente con l'account di Google o Facebook utilizzato in precedenza).
13. Verificare che sia possibile modificare i dati quando si effettua l'accesso con tale account.
    **Nota:** non è possibile disconnettersi da Google da questa app e quindi effettuare l'accesso a un account Google diverso con lo stesso browser. Se si utilizza un browser, sarà necessario passare a Google e disconnettersi. È possibile effettuare l'accesso con un altro account dallo stesso autenticatore di terze parti, ad esempio Google, utilizzando un browser diverso.

Se nelle informazioni sull'account Google non sono stati compilati i campi relativi a nome e cognome, verrà generata un'eccezione di tipo NullReferenceException.

## Esaminare il database SQL di Azure

1.  In **Esplora soluzioni** passare a **ContactDB**
2.  Fare clic con il pulsante destro del mouse su **ContactDB**, quindi scegliere **Apri in Esplora oggetti di SQL Server**.

    ![Apertura in SSOX][Apertura in SSOX]

**Nota:** se non è possibile espandere **SQL Databases** e *non è possibile* visualizzare **ContactDB** da Visual Studio, sarà necessario seguire le istruzioni seguenti per aprire una porta del firewall o un intervallo di porte. Seguire le istruzioni fornite in **Configurazione delle regole del firewall per Azure**. Dopo l'aggiunta della regola del firewall, potrebbe essere necessario attendere alcuni minuti prima di accedere al database.

1.  Fare clic con il pulsante destro del mouse sulla tabella **AspNetUsers**, quindi scegliere **View Data**.

    ![Pagina CM][10]

2.  Controllare che l'ID dell'account Google usato per la registrazione e l'ID di <*user1@contoso.com*> siano visualizzati nel ruolo **canEdit**. È necessario che questi siano gli unici utenti nel ruolo **canEdit**. Questa verifica verrà eseguita nel passaggio successivo.

    ![Pagina CM][11]

3.  In **Esplora oggetti di SQL Server** fare clic con il pulsante destro del mouse su **AspNetUserRoles** e scegliere **Visualizza dati**.

    ![Pagina CM][12]

Verificare che i valori di **UserId** corrispondano a <*user1@contoso.com>\* e all'account Google usato per la registrazione.

## Configurare le regole del firewall per Azure

Se non è possibile eseguire la connessione a SQL Azure da Visual Studio o se viene visualizzata una finestra di dialogo di errore di tipo "Impossibile aprire il server", attenersi alle istruzioni disponibili in questa sezione.

![Errore del firewall][Errore del firewall]

Sarà necessario aggiungere l'indirizzo IP agli indirizzi IP autorizzati.

1.  Nel portale di Azure selezionare **SQL Databases** nella scheda a sinistra.

    ![Selezione di SQL][Selezione di SQL]

2.  Fare clic su **ContactDB**.

3.  Fare clic sul collegamento **Set up Azure firewall rules for this IP address**.

    ![Regole del firewall][Regole del firewall]

4.  Quando viene visualizzata la richiesta "The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?", fare clic su **Yes**. L'aggiunta di questo indirizzo non è spesso sufficiente. Nel caso di alcuni firewall aziendali sarà necessario aggiungere un intervallo di indirizzi IP.

Il passaggio successivo consiste nell'aggiunta di un intervallo di indirizzi IP autorizzati.

1.  Nel portale di Azure fare clic su **SQL Databases**.
2.  Selezionare la scheda **Servers** e quindi fare clic sul server da configurare.

    ![Servers tab in Azure][Servers tab in Azure]

3.  Fare clic sulla scheda **Configura**.

4.  Aggiungere un nome di regola e gli indirizzi IP di inizio e di fine.

    ![Intervallo IP][Intervallo IP]

5.  Nella parte inferiore della pagina fare clic su **Save**.
6.  Se è necessario aggiungere un intervallo di indirizzi IP per la connessione, inviare commenti in merito.

È infine possibile connettersi all'istanza di database SQL da Esplora oggetti di SQL Server (SSOX).

1.  Dal menu Visualizza scegliere **Esplora oggetti di SQL Server**.
2.  Fare clic con il pulsante destro del mouse su **SQL Server**, quindi scegliere **Aggiungi SQL Server**.
3.  Nella finestra di dialogo **Connetti al server** impostare **Autenticazione** su **Autenticazione di SQL Server**. Verranno visualizzati il **Nome server** e l'**Accesso** dal portale di Azure.
4.  Nel browser passare al portale e selezionare **SQL Databases**.
5.  Selezionare **ContactDB**, quindi fare clic su **View SQL Database connection strings**.
6.  Nella pagina **Connection Strings** copiare i valori di **Server** e **User ID**.

    ![Stringa di connessione][Stringa di connessione]

7.  Incollare i valori di **Server** e **User ID** nella finestra di dialogo **Connetti al server** in Visual Studio. Il valore **User ID** deve essere inserito nella voce **Accesso**. Immettere la password utilizzata per creare il database SQL.

    ![Finestra di dialogo Connetti al server][Finestra di dialogo Connetti al server]

È ora possibile passare al database Contact attenendosi alle istruzioni riportate in precedenza.

## Per aggiungere un utente al ruolo canEdit modificando le tabelle del database

Nei passaggi precedenti dell'esercitazione, per aggiungere utenti al ruolo canEdit è stato utilizzato codice. Un metodo alternativo consiste nel modificare i dati direttamente nelle tabelle di appartenenza. Nella procedura seguente viene illustrato come utilizzare questo metodo alternativo per aggiungere un utente a un ruolo.

1.  In **Esplora oggetti di SQL Server** fare clic con il pulsante destro del mouse su **AspNetUserRoles** e scegliere **Visualizza dati**.

    ![Pagina CM][12]

2.  Copiare il valore di *RoleId* e incollarlo nella riga vuota (nuova).

    ![Pagina CM][13]

3.  Nella tabella **AspNetUsers** individuare l'utente da inserire nel ruolo, copiare l'*Id* dell'utente, quindi incollarlo nella colonna **UserId** della tabella **AspNetUserRoles**.

Microsoft sta attualmente lavorando alla creazione di uno strumento per semplificare la gestione di utenti e ruoli.

## Considerazioni sulla registrazione locale

La registrazione dell'appartenenza ASP.NET corrente nel progetto non offre supporto per la reimpostazione delle password e non consente di verificare che la registrazione venga effettuata da una persona reale, ad esempio tramite [CAPTCHA][CAPTCHA]. Dopo l'autenticazione tramite uno dei provider di terze parti, gli utenti possono eseguire la registrazione. Se si sceglie di disabilitare la registrazione locale, attenersi alla procedura seguente:

1.  In AccountController rimuovere l'attributo *[AllowAnonymous]* dai metodi *Register* di GET e POST. In questo modo si impedirà la registrazione da parte di bot o utenti anonimi.
2.  Nella cartella *Views\\Shared* rimuovere dal file \*\_LoginPartial.cshtml\* il collegamento azione Register.
3.  Nel file *Views\\Account\\Login.cshtml* rimuovere il collegamento azione Register.
4.  Distribuire l'app.

## <a name="nextsteps"></a><span class="short-header">Passaggi successivi</span>Passaggi successivi

Vedere l'esercitazione relativa alla [creazione di un'app ASP.NET MVC 5 con Facebook e Google OAuth2 e accesso OpenID][creazione di un'app ASP.NET MVC 5 con Facebook e Google OAuth2 e accesso OpenID] per istruzioni su come aggiungere dati del profilo al database di registrazione utente e sull'utilizzo di Facebook come provider di autenticazione.

Per abilitare i pulsanti di accesso dai social network visualizzati nella parte superiore di questa esercitazione, vedere l'articolo relativo alla [creazione di pulsanti di accesso dall'aspetto gradevole per ASP.NET MVC 5][creazione di pulsanti di accesso dall'aspetto gradevole per ASP.NET MVC 5].

Per altre informazioni su ASP.NET MVC, vedere l'esercitazione relativa all'[introduzione ad ASP.NET MVC 5][introduzione ad ASP.NET MVC 5]. Per informazioni più avanzate sulla programmazione MVC ed EF, vedere la pagina relativa all'[introduzione a EF e MVC][introduzione a EF e MVC] di Tom Dykstra.

Questa esercitazione e l'applicazione di esempio sono state scritte da [Rick Anderson][Rick Anderson] (Twitter [@RickAndMSFT][@RickAndMSFT]) con il supporto di Tom Dykstra e Barry Dorrans (Twitter [@blowdart][@blowdart]).

Se lo si desidera, inviare commenti e suggerimenti sugli aspetti ritenuti utili e su eventuali miglioramenti da apportare, non solo in merito all'esercitazione ma anche ai prodotti illustrati nell'esercitazione. I commenti e suggerimenti degli utenti risulteranno utili per definire la priorità dei miglioramenti da apportare. È inoltre possibile richiedere nuovi argomenti e votare gli argomenti esistenti alla pagina [Show Me How With Code][Show Me How With Code].

<!-- bookmarks -->
<!-- images-->

  [esercitazione precedente]: /it-it/develop/net/tutorials/web-site-with-sql-database-vs2012/
  [OAuth]: http://oauth.net/ "http://oauth.net/"
  [OpenID]: http://openid.net/
  [pagina di accesso]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png
  [attivare i benefici della sottoscrizione MSDN]: /it-it/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [iscriversi per una valutazione gratuita.]: /it-it/pricing/free-trial/?WT.mc_id=A261C142F
  [Configurare l'ambiente di sviluppo]: #setupdevenv
  [Configurare l'ambiente Azure]: #bkmk_setupwindowsazure
  [Creare un'applicazione ASP.NET MVC 5]: #bkmk_createmvc4app
  [Distribuire l'applicazione in Azure]: #bkmk_deploytowindowsazure1
  [Aggiungere un database all'applicazione]: #bkmk_addadatabase
  [Aggiungere un provider OAuth]: #addOauth
  [Utilizzo dell'API di appartenenza]: #mbrDB
  [Distribuire l'app in Azure]: #bkmk_deploytowindowsazure11
  [Passaggi successivi]: #nextsteps
  [Visual Studio 2013 Update 2 RC]: http://go.microsoft.com/fwlink/?LinkId=390521
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Pulsante New nel portale di gestione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
  [Collegamento Create with Database nel portale di gestione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png
  [1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png
  [Passaggio Impostazioni database della procedura guidata Nuovo sito Web - Crea con database]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png
  [New Project in File menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png
  [New Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png
  [New ASP.NET Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG
  [code changes]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png
  [Web site running locally]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png
  [Publish in project context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png
  [Import publish settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG
  [sign in]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG
  [select web site]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png
  [Publish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png
  [Running in Cloud]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG
  [Aggiungi classe nel menu di scelta rapida della cartella Modelli]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png
  [Finestra di dialogo Aggiungi nuovo elemento]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
  [Opzione Aggiungi controller nel menu di scelta rapida della cartella Controllers]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png
  [Finestra di dialogo Add Scaffold]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png
  [New data ctx dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG
  [Migrazioni Code First]: http://msdn.microsoft.com/library/hh770484.aspx
  [Package Manager Console in Tools menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png
  [Package Manager Console commands]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png
  [Visualizzazione MVC dei dati]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png
  [creazione di un'app ASP.NET MVC 5 con Facebook e Google OAuth2 e accesso OpenID]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
  [Accesso di Google]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG
  [GOOG asking for permission]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG
  [register]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG
  [Immagine del codice]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG
  [pagina relativa alle risorse di ASP.NET Identity]: http://curah.microsoft.com/55636/aspnet-identity
  [WSAT]: http://msdn.microsoft.com/it-it/library/ms228053.aspx
  [Codice]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG
  [Authorize]: http://msdn.microsoft.com/it-it/library/system.web.mvc.authorizeattribute.aspx
  [RequireHttps]: http://msdn.microsoft.com/it-it/library/system.web.mvc.requirehttpsattribute.aspx
  [protezione dell'app ASP.NET MVC 4 e al nuovo attributo AllowAnonymous]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png
  [Abilitazione di SSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png
  [Browser predefinito]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG
  [browser selector]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png
  [Istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG
  [localhost IIS Express certificate warning]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG
  [IE SSL localhost e nessun avviso]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG
  [Avviso di Firefox relativo al certificato]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG
  [Accesso]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png
  [Impostazioni]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png
  [Opzione Arresta sito Web]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png
  [Avvio del sito Web]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png
  [Opzione Register]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG
  [Disconnessione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG
  [Pagina CM]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png
  [Apertura in SSOX]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png
  [Errore del firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
  [Selezione di SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
  [Regole del firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
  [Servers tab in Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG
  [Intervallo IP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png
  [Stringa di connessione]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG
  [Finestra di dialogo Connetti al server]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [creazione di pulsanti di accesso dall'aspetto gradevole per ASP.NET MVC 5]: http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/
  [introduzione ad ASP.NET MVC 5]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [introduzione a EF e MVC]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Show Me How With Code]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
