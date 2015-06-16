<properties 
	pageTitle="creazione di un'app ASP.NET MVC con autenticazione e database SQL e alla distribuzione in Servizio app di Azure" 
	description="Informazioni su come sviluppare un'app ASP.NET MVC 5 con il back-end del database SQL, aggiungere autenticazione e autorizzazione e distribuirla in Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="riande"/>



# creazione di un'app ASP.NET MVC con autenticazione e database SQL e alla distribuzione in Servizio app di Azure

In questa esercitazione verrà illustrato come creare un'app Web ASP.NET MVC 5 sicura che consente agli utenti di effettuare l'accesso utilizzando credenziali di Facebook o Google. L'applicazione verrà inoltre distribuita nel [Servizio app](http://go.microsoft.com/fwlink/?LinkId=529714).

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio Express 2013 per il Web. È possibile iniziare a sviluppare per Azure gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web sicura, basata sui dati, in esecuzione nel cloud e in grado di utilizzare un database cloud.

Si apprenderà come:

* Creare un progetto ASP.NET MVC 5 sicuro e pubblicarlo in [Servizio app per app Web](http://go.microsoft.com/fwlink/?LinkId=529714) in Azure App Service.
* Utilizzare [OAuth](http://oauth.net/ "http://oauth.net/") e il database di appartenenza ASP.NET per proteggere l'applicazione.
* Utilizzare un database SQL per l'archiviazione di dati in Azure.

Verrà creata una semplice app Web di elenco contatti basata su ASP.NET MVC 5 che usa ADO.NET Entity Framework per l'accesso al database. Nell'illustrazione seguente viene mostrata la pagina di accesso dell'applicazione completata:

![pagina di accesso][rxb]

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile <a href="/it-it/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">attivare i benefici della sottoscrizione MSDN</a> oppure <a href="/it-it/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">iscriversi per ottenere una versione di valutazione gratuita</a>.

>Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per configurare l'ambiente di sviluppo, è necessario installare [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkId=390521) o versione superiore e la versione più recente di [Azure SDK per Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Il presente articolo è stato scritto per Visual Studio Update 4 e SDK 2.5.1.

## Creazione di un'applicazione ASP.NET MVC 5

### Creare il progetto

1. Scegliere **Nuovo progetto** dal menu **File**.

	![Nuovo progetto nel menu File](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. Nella finestra di dialogo **Nuovo progetto** espandere **C#** e selezionare **Web** in **Modelli installati**, quindi selezionare **Applicazione Web ASP.NET**.

1. Assegnare all'applicazione il nome **ContactManager** e fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**Nota:** assicurarsi di immettere "ContactManager". Nei blocchi di codice che verranno copiati in seguito si presuppone che il nome del progetto sia ContactManager.

1. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**. Verificare che **Autenticazione** sia impostata su **Singoli account utente** e che le opzioni **Ospita nel cloud** e **App Web** siano selezionate.

	![Finestra di dialogo Nuovo progetto ASP.NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. La configurazione guidata suggerirà un nome univoco basato su *ContactManager*. È necessario decidere se creare un nuovo gruppo di risorse e piano di servizio app. Per indicazioni sulla decisione di creare o meno un nuovo piano o gruppo di risorse, vedere [Panoramica approfondita dei piani di Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Per questa esercitazione, è consigliabile creare almeno un nuovo gruppo di risorse. Selezionare un'area nelle vicinanze. È possibile usare [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") per trovare il data center a latenza più bassa. Sarà necessario impostare il database nel passaggio successivo, pertanto non fare clic ancora su **OK**.

   ![Nuovo piano e gruppo di risorse](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. Se non è stato creato un server di database, selezionare **Crea nuovo server** e immettere un nome, un nome utente e una password per il database.

   ![Utilizzare il nuovo database](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

3. Se si dispone di un server di database, usarlo per creare un nuovo database. I server di database sono una risorsa preziosa e in genere è utile creare più database nello stesso server per attività di test e sviluppo anziché creare un server di database per ogni database. Assicurarsi che l'app e il database si trovino nella stessa area.

    ![Utilizzare database esistente](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/useexistingdb.png)

### Impostare intestazione e piè di pagina


1. In **Esplora soluzioni** aprire il file *Layout.cshtml* nella cartella *Views\\Shared*.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. Sostituire il markup nel file *Layout.cshtml* con il codice seguente. Le modifiche sono evidenziate qui di seguito.

<pre>
			&lt;!DOCTYPE html>
			&lt;html>
			&lt;head>
			    &lt;meta charset="utf-8" />
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0">
			    &lt;title>@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title>
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head>
			&lt;body>
			    &lt;div class="navbar navbar-inverse navbar-fixed-top">
			        &lt;div class="container">
			            &lt;div class="navbar-header">
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                &lt;/button>
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div>
			            &lt;div class="navbar-collapse collapse">
			                &lt;ul class="nav navbar-nav">
			                    &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li>
			                &lt;/ul>
			                @Html.Partial("_LoginPartial")
			            &lt;/div>
			        &lt;/div>
			    &lt;/div>
			    &lt;div class="container body-content">
			        @RenderBody()
			        &lt;hr />
			        &lt;footer>
			            &lt;p>&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p>
			        &lt;/footer>
			    &lt;/div>
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body>
			&lt;/html>
</pre>

### Eseguire l'applicazione in locale

1. Premere CTRL+F5 per eseguire l'app.

	La home page dell'applicazione verrà visualizzata nel browser predefinito.

	![Applicazione Web in esecuzione in locale](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Non è necessario eseguire per il momento altre operazioni per creare l'applicazione che verrà distribuita in Azure.

## Abilitare SSL per il progetto ##

1. Abilitare SSL. In Esplora soluzioni fare clic sul progetto **ContactManager**, quindi fare clic su F4 per visualizzare la finestra di dialogo delle proprietà. Modificare **SSL Enabled** in true. Copiare **SSL URL**. L'URL SSL sarà https://localhost:44300/ a meno che non siano state create app Web SSL in precedenza.

	![Abilitazione di SSL][rxSSL]
 
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Contact Manager**, quindi scegliere **Proprietà**.
1. Nella scheda a sinistra fare clic su **Web**.
1. Modificare **URL progetto** in modo da utilizzare **URL SSL**, quindi salvare la pagina (CTRL+S).

	![Abilitazione di SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. Verificare che Internet Explorer sia il browser avviato da Visual Studio, come illustrato nell'immagine seguente:

	![Browser predefinito](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	Lo strumento di selezione del browser consente di specificare il browser predefinito avviato da Visual Studio.

 	![Selezione browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

	È possibile selezionare più browser in modo che Visual Studio li aggiorni automaticamente ogni volta che vengono apportate modifiche. Per altre informazioni, vedere l'articolo relativo all'[uso di Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Premere CTRL+F5 per eseguire l'applicazione. Seguire le istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express.

	 ![Istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. Leggere quanto riportato nella finestra di dialogo **Avviso di sicurezza** e quindi fare clic su **Sì** se si desidera installare il certificato che rappresenta **localhost**.

 	![Avviso relativo al certificato di IIS Express per localhost](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. In Internet Explorer viene visualizzata la pagina *Home* e non viene visualizzato alcun avviso SSL.

	 ![IE SSL localhost e nessun avviso](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	Anche Google Chrome accetta il certificato e visualizzerà il contenuto HTTPS senza alcun avviso. In Firefox viene utilizzato un archivio certificati personalizzati, pertanto verrà visualizzato un avviso.

	 ![Avviso di Firefox relativo al certificato](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## Distribuzione dell'applicazione in Azure

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

	![Opzione Pubblica nel menu di scelta rapida del progetto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
   Viene visualizzata la procedura guidata **Pubblica sito Web**.

1. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.

	![Opzione Pubblica](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	L'applicazione creata verrà ora eseguita nel cloud. Alla successiva distribuzione dell'applicazione, verranno distribuiti solo i file modificati o nuovi.

	![Esecuzione nel cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## Aggiunta di un database all'applicazione

Si passerà quindi all'aggiornamento dell'app, in modo da aggiungere la possibilità di visualizzare e aggiornare i contatti e di archiviare i dati in un database. L'app userà Entity Framework per creare il database e per leggere e aggiornare i dati nel database.

### Aggiungere classi del modello di dati per i contatti

Creare innanzitutto un semplice modello di dati nel codice.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Modelli, quindi scegliere **Aggiungi** e infine **Classe**.

	![Aggiungi classe nel menu di scelta rapida della cartella Modelli](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. Nella finestra di dialogo **Aggiungi nuovo elemento** assegnare al nuovo file di classe il nome *Contact.cs*, quindi fare clic su **Aggiungi**.

	![Finestra di dialogo Aggiungi nuovo elemento][adddb002]

3. Sostituire il contenuto del file Contacts.cs con il codice seguente.

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

La caratteristica scaffolding di ASP.NET MVC consente di generare automaticamente codice per l'esecuzione di azioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete).

## Aggiunta di un controller e di una visualizzazione per i dati

1. Creare il progetto **(CTRL+MAIUSC+B)**. Per utilizzare il meccanismo scaffolding, è innanzitutto necessario creare il progetto. 
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Controller, quindi scegliere **Aggiungi** e infine **Controller**.

	![Opzione Aggiungi controller nel menu di scelta rapida della cartella Controllers][addcode001]

5. Nella finestra di dialogo **Add Scaffolding** selezionare **MVC 5 Controller with views, using EF**, quindi fare clic su **Aggiungi**.
	
	![Finestra di dialogo Add Scaffold](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)


1. Nell'elenco a discesa **Classe modello** selezionare **Contact (ContactManager.Models)**. Vedere l'immagine di seguito.
1. In **Classe contesto di dati** selezionare **ApplicationDbContext (ContactManager.Models)**. La classe **ApplicationDbContext** verrà utilizzata per il database di appartenenza e per i dati relativi ai contatti.
1. Nella casella di immissione del testo **Nome controller** digitare "CmController" per il nome del controller. 

	![Finestra di dialogo relativa ai nuovi dati contesto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. Fare clic su **Aggiungi**.

   In Visual Studio verranno creati metodi e visualizzazioni di un controller per operazioni CRUD di database per oggetti **Contact**.

## Abilitazione delle migrazioni, creazione del database, aggiunta di dati di esempio e di un inizializzatore di dati ##

L'attività successiva consiste nell'abilitare la caratteristica [Migrazioni Code First](http://msdn.microsoft.com/library/hh770484.aspx), in modo da creare il database basato sul modello di dati creato.

1. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**. ![Console di Gestione pacchetti nel menu Strumenti](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. Nella finestra **Console di Gestione pacchetti** immettere il comando seguente:

		enable-migrations
	Il comando **enable-migrations** consente di creare una cartella *Migrations* e di inserire in tale tabella il file *Configuration.cs*, che può essere modificato per effettuare il seeding del database e configurare le migrazioni.

2. Nella finestra **Console di Gestione pacchetti** immettere il comando seguente:

		add-migration Initial


	Il comando **add-migration Initial** consente di generare un file con nome **&lt;indicatore_data&gt;Initial** nella cartella *Migrations* che crea il database. Il primo parametro (**Initial**) è arbitrario e viene utilizzato per creare il nome del file. È possibile visualizzare i nuovi file di classe in **Esplora soluzioni**. Nella classe **Initial** il metodo **Up** consente di creare la cartella Contacts e il metodo **Down**, utilizzato quando si desidera tornare allo stato precedente, consente di rimuoverla.
3. Aprire il file *Migrations\\Configuration.cs*. 
4. Aggiungere lo spazio dei nomi seguente. 

    	 using ContactManager.Models;



5. Sostituire il metodo *Seed* con il codice seguente:

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

	Questo codice consente di inizializzare (effettuare il seeding) il database con le informazioni sui contatti. Per ulteriori informazioni sul seeding del database, vedere la pagina relativa al [seeding e al debug di database di Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. In **Console di Gestione pacchetti** immettere il comando seguente:

		update-database

	![Comandi di Console di Gestione pacchetti][addcode009]

	Il comando **update-database** consente di eseguire la prima migrazione al fine di creare il database. Per impostazione predefinita, il database viene creato come database LocalDB SQL Server Express.

7. Premere CTRL+F5 per eseguire l'applicazione, quindi fare clic sul collegamento **CM Demo** oppure passare a https://localhost:(port#)/Cm.

	Nell'applicazione vengono mostrati i dati di seeding e sono disponibili collegamenti per la modifica, i dettagli e l'eliminazione. È possibile creare, modificare, eliminare e visualizzare i dati.

	![Visualizzazione MVC dei dati][rx2]



## Aggiungere un provider OAuth2

[OAuth](http://oauth.net/ "http://oauth.net/") è un protocollo aperto che consente di eseguire l'autenticazione sicura in un metodo semplice e standard da applicazioni Web, per dispositivi mobili e desktop. Il modello Internet ASP.NET MVC usa OAuth per esporre Facebook, Twitter, Google e Microsoft come provider di autenticazione. Benché in questa esercitazione venga usato solo Google come provider di autenticazione, è possibile modificare con facilità il codice per usare un altro provider. I passaggi per l'implementazione di altri provider sono molto simili ai passaggi disponibili in questa esercitazione. Per usare Facebook come provider di autenticazione, vedere l'esercitazione relativa alla creazione di un'[app MVC 5 con autenticazione OAuth2 mediante Facebook, Twitter, LinkedIn e Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Oltre all'autenticazione, nell'esercitazione verranno utilizzati anche i ruoli per implementare l'autorizzazione. Solo gli utenti aggiunti al ruolo *canEdit* saranno in grado di modificare i dati, ovvero creare, modificare o eliminare i contatti.

Seguire le istruzioni fornite nell'esercitazione sulla creazione di un'[app MVC 5 con autenticazione OAuth2 mediante Facebook, Twitter, LinkedIn e Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) nella sezione relativa alla **creazione di un'app Google per OAuth 2 per configurare un'app Google per OAuth2**. Eseguire e testare l'applicazione per verificare che sia possibile accedere usando l'autenticazione di Google.

## Utilizzo dell'API di appartenenza
In questa sezione verranno aggiunti al database di appartenenza un utente locale e il ruolo *canEdit*. Solo gli utenti con ruolo *canEdit* saranno in grado di modificare i dati. È consigliabile assegnare ai ruoli un nome corrispondente alle azioni che consentono di eseguire, quindi il nome *canEdit* è preferibile rispetto ad *admin*. Nel corso dell'evolversi dell'applicazione, sarà possibile aggiungere nuovi ruoli quali *canDeleteMembers*, invece di ruoli con nomi meno descrittivi quali *superAdmin*.

1. Aprire il file *migrations\\configuration.cs* e aggiungere le istruzioni `using` seguenti:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Aggiungere il metodo **AddUserAndRole** seguente alla classe:

    
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

1. Richiamare il nuovo metodo dal metodo **Seed**: <pre> protected override void Seed(ContactManager.Models.ApplicationDbContext context) { <mark>AddUserAndRole(context);</mark> context.Contacts.AddOrUpdate(p => p.Name, // Code removed for brevity } </pre> <span></span> Nelle immagini seguenti vengono mostrate le modifiche al metodo *Seed*:

	![Immagine del codice](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

   Questo codice consente di creare un nuovo ruolo denominato *canEdit*, quindi di creare un nuovo utente locale *user1@contoso.com* e infine di aggiungere *user1@contoso.com* al ruolo *canEdit*. Per ulteriori informazioni, consultare le esercitazioni su [ASP.NET Identity](http://www.asp.net/identity/overview/features-api).

## Utilizzo di codice temporaneo per aggiungere al ruolo canEdit nuovi utenti di accesso a social networking  ##
In questa sezione sarà possibile modificare temporaneamente il metodo **ExternalLoginConfirmation** nel controller Account per aggiungere nuovi utenti che eseguono l'autenticazione con un provider OAuth oppure OpenID al ruolo *canEdit*. Il metodo **ExternalLoginConfirmation** verrà modificato temporaneamente per aggiungere automaticamente nuovi utenti a un ruolo amministrativo. Il codice temporaneo di registrazione automatica verrà utilizzato fino a quando non sarà disponibile uno strumento per l'aggiunta e la gestione dei ruoli. Ci auguriamo di riuscire a rendere disponibile in futuro uno strumento simile a [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) per consentire di creare e modificare account utente e ruoli.

1. Aprire il file **Controllers\\AccountController.cs** e passare al metodo **ExternalLoginConfirmation**.
1. Aggiungere la chiamata seguente a **AddToRoleAsync** immediatamente prima della chiamata **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   Il codice riportato sopra consente di aggiungere l'utente appena registrato al ruolo "canEdit", in modo da concedere all'utente l'accesso ai metodi di azione per la modifica dei dati. 
	<pre> 
		// POST: /Account/ExternalLoginConfirmation
	      [HttpPost]
	      [AllowAnonymous]
	      [ValidateAntiForgeryToken]
	      public async Task<ActionResult> ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
	      {
	         if (User.Identity.IsAuthenticated)
	         {
	            return RedirectToAction("Index", "Manage");
	         }
	         if (ModelState.IsValid)
	         {
	            // Get the information about the user from the external login provider
	            var info = await AuthenticationManager.GetExternalLoginInfoAsync();
	            if (info == null)
	            {
	               return View("ExternalLoginFailure");
	            }
	            var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
	            var result = await UserManager.CreateAsync(user);
	            if (result.Succeeded)
	            {
	               result = await UserManager.AddLoginAsync(user.Id, info.Login);
	               if (result.Succeeded)
	               {
	                  <mark>await UserManager.AddToRoleAsync(user.Id, "canEdit");</mark>
	                  await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
	                  return RedirectToLocal(returnUrl);
	               }
	            }
	            AddErrors(result);
	         }
	         ViewBag.ReturnUrl = returnUrl;
	         return View(model);
	      }
	</pre>

Più avanti nell'esercitazione l'applicazione verrà distribuita in Azure, a cui si effettuerà l'accesso tramite Google o un altro provider di autenticazione di terze parti. L'account appena registrato verrà aggiunto al ruolo *canEdit*. Chiunque abbia a disposizione l'URL dell'app Web e un ID di Google potrà effettuare la registrazione e aggiornare il database. Per impedire ad altri utenti di eseguire tali operazioni, è possibile arrestare il sito. Per verificare gli utenti appartenenti al ruolo *canEdit*, è possibile esaminare il database.

In **Console di Gestione pacchetti** fare clic sul tasto di direzione per visualizzare il comando seguente:

		Update-Database

Eseguire il comando **Update-Database**, che consente di eseguire il metodo **Seed**, che a sua volta consente di eseguire il comando **AddUserAndRole** appena aggiunto. Usando **AddUserAndRole** sarà possibile creare l'utente *user1@contoso.com* e aggiungerlo al ruolo *canEdit*.

## Protezione dell'applicazione con SSL e l'attributo Authorize ##

In questa sezione verrà applicato l'attributo [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) per limitare l'accesso ai metodi di azione. Gli utenti anonimi saranno in grado di visualizzare il metodo di azione **Index** solo per il controller Home. Gli utenti registrati saranno in grado di visualizzare i dati sui contatti (pagine **Index** e **Details** del controller Cm) e le pagine About e Contact. Solo gli utenti appartenenti al ruolo *canEdit* saranno in grado di accedere ai metodi di azione e di modificare i dati.

1. Aggiungere il filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) e il filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) all'applicazione. Un approccio alternativo consiste nell'aggiungere l'attributo [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) e l'attributo [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) a ogni controller, ma è consigliabile applicarli all'intera applicazione, per assicurare una protezione migliore. Grazie all'aggiunta a livello globale, ogni nuovo controller e ogni metodo di azione aggiunti saranno protetti automaticamente e non sarà necessario ricordarsi di applicare gli attributi. Per ulteriori informazioni, vedere la pagina relativa alla [protezione dell'app ASP.NET MVC e al nuovo attributo AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Aprire *App_Start\\FilterConfig.cs* file e sostituire il metodo *RegisterGlobalFilters* metodo con il codice seguente (che aggiunge due filtri): <pre> public static void RegisterGlobalFilters(GlobalFilterCollection filters) { filters.Add(new HandleErrorAttribute()); <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute()); filters.Add(new RequireHttpsAttribute());</mark> } </pre>




	Il filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) applicato nel codice riportato sopra consente di impedire agli utenti anonimi di accedere ai metodi dell'applicazione. È possibile utilizzare l'attributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) per rifiutare esplicitamente il requisito di autorizzazione in un paio di metodi, per consentire agli utenti anonimi di effettuare l'accesso e visualizzare la home page. Con [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) si rende obbligatorio l'accesso all'app Web tramite HTTPS.

1. Aggiungere l'attributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) al metodo Index del controller Home.**** L'attributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) consente di aggiungere all'elenco di metodi consentiti i metodi che si desidera rifiutino in modo esplicito l'autorizzazione. <pre> public class HomeController : Controller { <mark>[AllowAnonymous]</mark> public ActionResult Index() { return View(); } </pre>

2. Eseguire una ricerca globale di *AllowAnonymous*. Come si può vedere, viene utilizzato nei metodi di accesso e registrazione del controller Account.
1. In *CmController.cs* aggiungere `[Authorize(Roles = "canEdit")]` ai metodi HttpGet e HttpPost che consentono di modificare i dati (Create, Edit, Delete, qualsiasi metodo di azione ad eccezione di Index e Details) nel controller *Cm*. Di seguito è riportata una parte del codice completato: <pre> // GET: Cm/Create <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create() { return View(new Contact { Address = "123 N 456 W", City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT", Zip = "59405"}); } // POST: Cm/Create // To protect from overposting attacks, please enable the specific properties you want to bind to, for // more details see http://go.microsoft.com/fwlink/?LinkId=317598. [HttpPost][ValidateAntiForgeryToken] <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact) { if (ModelState.IsValid) { db.Contacts.Add(contact); db.SaveChanges(); return RedirectToAction("Index"); } return View(contact); } // GET: Cm/Edit/5 <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Edit(int? id) { if (id == null) { return new HttpStatusCodeResult(HttpStatusCode.BadRequest); } Contact contact = db.Contacts.Find(id); if (contact == null) { return HttpNotFound(); } return View(contact); } </pre>

1. Se si è ancora connessi da una sessione precedente, fare clic sul collegamento **Disconnetti**.
1. Fare clic sui collegamenti **About** o **Contact**. Si verrà reindirizzati alla pagina di accesso, poiché gli utenti anonimi non sono autorizzati a visualizzare tali pagine. 
1. Fare clic sul collegamento **Esegui la registrazione come nuovo utente** e aggiungere un utente locale con l'indirizzo di posta elettronica *joe@contoso.com*. Assicurarsi che *Joe* sia in grado di visualizzare le pagine Home, About e Contact.
	![Accesso](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. Fare clic sul collegamento *CM Demo* e verificare che i dati siano visibili.
1. Fare clic su un collegamento di modifica nella pagina. Si verrà reindirizzati alla pagina di accesso, poiché il nuovo utente locale non è stato aggiunto al ruolo *canEdit*.
1. Accedere utilizzando il nome utente *user1@contoso.com* e la password "P_assw0rd1" (il carattere "0" in "Word" è uno zero). Si verrà reindirizzati alla pagina di modifica selezionata in precedenza. <br/> Se non è possibile effettuare l'accesso con tale account e password, provare a copiare la password dal codice sorgente e incollarla. Se ancora non è possibile accedere, verificare la colonna **UserName** della tabella **AspNetUsers** per accertarsi che *user1@contoso.com* sia stato aggiunto. 
1. Verificare che sia possibile apportare modifiche ai dati.

## Distribuire l'app in Azure

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

	![Opzione Pubblica nel menu di scelta rapida del progetto][firsdeploy003]

	Viene visualizzata la procedura guidata **Pubblica sito Web**.

1. Fare clic sulla scheda **Impostazioni** nella parte sinistra della finestra di dialogo **Pubblica sito Web** dialog box. Fare clic sull'icona  **v** per selezionare la **stringa di connessione remota** per **ApplicationDbContext**, quindi selezionare **ContactManagerNN_db**.

   
	![Scheda Impostazioni](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. In **ContactManagerContext** selezionare **Execute Code First Migrations**.

	![Scheda Impostazioni](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. Fare clic su **Pubblica**.
1. Effettuare l'accesso come *user1@contoso.com* (utilizzando la password "P_assw0rd1") e verificare che sia possibile modificare i dati.1. Effettuare la disconnessione.
1. Passare alla [Google Developers Console](https://console.developers.google.com/) e nella scheda **Credentials** aggiornare gli URI di reindirizzamento e le origini JavaScript in modo che usino l'URL di Azure.
1. Effettuare l'accesso utilizzando Google o Facebook. L'account Google o Facebook verrà aggiunto al ruolo **canEdit**. Se viene visualizzato un errore HTTP 400 e il messaggio *L'URI di reindirizzamento nella richiesta: https://contactmanager{my version}.azurewebsites.net/signin-google non corrisponde a un URI di reindirizzamento registrato.*, sarà necessario attendere che le modifiche apportate vengano propagate. Se questo errore viene visualizzato dopo qualche minuto, verificare che gli URI siano corretti.

### Arrestare l'app Web per impedire la registrazione da parte di altri utenti  

1. In **Esplora server**, accedere ad **App Web**.
4. Fare clic con il pulsante destro del mouse sull'app Web e selezionare **Interrompi**. 

	![Opzione Arresta app Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s1.png)

	In alternativa, dal [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) è possibile selezionare il sito Web, quindi fare clic sull'icona **Interrompi** nella parte inferiore della pagina.

	![Opzione Arresta il portale app Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### Rimuovere AddToRoleAsync, Publish e Test

1. Impostare come commento o rimuovere il codice seguente dal metodo **ExternalLoginConfirmation** nel controller Account: `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Creare il progetto. Le modifiche apportate al file verranno salvate. Controllare che non si siano verificati errori di compilazione.
5. Fare clic con il pulsante destro del mouse in **Esplora soluzioni**, quindi scegliere **Pubblica**.

	   ![Opzione Pubblica nel menu di scelta rapida del progetto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. Fare clic sul pulsante **Start Preview**. Vengono distribuiti solo i file che necessitano di aggiornamento.
5. Avviare l'app Web da Visual Studio o dal portale. **Non sarà possibile eseguire la pubblicazione durante l'interruzione dell'app Web**.

	![Opzione Avvia app Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Tornare in Visual Studio e fare clic su **Pubblica**.
3. L'app di Azure App verrà aperta nel browser predefinito. Se si è effettuato l'accesso, disconnettersi in modo da visualizzare la home page come un utente anonimo.  
4. Fare clic sul collegamento **About**. Si verrà reindirizzati alla pagina di accesso.
5. Fare clic sul collegamento **Register** nella pagina di accesso, quindi creare un account locale. L'account locale verrà utilizzato per verificare se si è in grado di accedere alle pagine di sola lettura ma non alle pagine che consentono la modifica dei dati, protette dal ruolo *canEdit*. L'accesso dell'account locale verrà rimosso più avanti nell'esercitazione. 

	![Opzione Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. Verificare che sia possibile passare alle pagine *About* e *Contact*.

	![Disconnetti](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. Fare clic sul collegamento **CM Demo** per passare al controller **Cm**. In alternativa è possibile aggiungere *Cm* alla fine dell'URL.

	![Pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. Fare clic su un collegamento per la modifica. Si verrà reindirizzati alla pagina di accesso. In **Use another service to log in** fare clic su Google o Facebook ed effettuare l'accesso con l'account utilizzato in precedenza per la registrazione. (Se si lavora velocemente e il cookie della sessione non è scaduto, l'accesso verrà eseguito automaticamente con l'account di Google o Facebook utilizzato in precedenza).
2. Verificare che sia possibile modificare i dati quando si effettua l'accesso con tale account. **Nota:** non è possibile disconnettersi da Google da questa app e quindi effettuare l'accesso a un account Google diverso con lo stesso browser. Se si utilizza un browser, sarà necessario passare a Google e disconnettersi. È possibile effettuare l'accesso con un altro account dallo stesso autenticatore di terze parti, ad esempio Google, utilizzando un browser diverso.

Se nelle informazioni sull'account Google non sono stati compilati i campi relativi a nome e cognome, verrà generata un'eccezione di tipo NullReferenceException.


## Esaminare il database SQL di Azure ##

1. In **Esplora soluzioni** passare a **ContactDB**
2. Fare clic con il pulsante destro del mouse su **ContactDB**, quindi scegliere **Apri in Esplora oggetti di SQL Server**.
 
	![Apertura in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. Se non è stata effettuata la connessione a questo database in precedenza, potrebbe essere richiesto di aggiungere una regola del firewall per consentire l'accesso all'indirizzo IP corrente. L'indirizzo IP sarà precompilato. È sufficiente, fare clic su **Aggiungi regola firewall** per abilitare l'accesso.

  ![Opzioni Aggiungi regola firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)
        
  Quindi, accedere al database con il nome utente e la password specificati durante la creazione del database.
 
1. Fare clic con il pulsante destro del mouse sulla tabella **AspNetUsers**, quindi scegliere **View Data**.

	![Pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. Prendere nota dell'ID dell'account Google usato per la registrazione nel ruolo **canEdit**t e dell'ID *user1@contoso.com*. È necessario che questi siano gli unici utenti nel ruolo **canEdit**. Questa verifica verrà eseguita nel passaggio successivo.

	![Pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. In **Esplora oggetti di SQL Server** fare clic con il pulsante destro del mouse su **AspNetUserRoles** e scegliere **Visualizza dati**.

	![Pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
Verificare che i valori **UserId** provengano da *user1@contoso.com* e dall'account Google registrato. 


## Passaggi successivi

Completare le esercitazioni basate su questo esempio:

1.	[Creare un'app Web ASP.NET MVC 5 sicura con accesso, conferma tramite posta elettronica e reimpostazione della password](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[App ASP.NET MVC 5 con SMS e messaggio di posta elettronica per autenticazione a due fattori](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Creare un'app ASP.NET MVC 5 con autenticazione OAuth2 mediante Facebook e Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) Sono fornite istruzioni su come aggiungere dati del profilo al database di registrazione utenti e sull'uso di Facebook come provider di autenticazione.
5.	[Introduzione ad ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Per abilitare i pulsanti di accesso dai social network visualizzati nella parte superiore di questa esercitazione, vedere l'articolo relativo alla [creazione di pulsanti di accesso dall'aspetto gradevole per ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

Per informazioni più avanzate sulla programmazione MVC ed EF, vedere la pagina relativa all'[introduzione a EF e MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) di Tom Dykstra.

Questa esercitazione e l'applicazione di esempio sono state scritte da [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con il supporto di Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

Se lo si desidera, ***inviare commenti e suggerimenti*** sugli aspetti ritenuti utili e su eventuali miglioramenti da apportare, non solo in merito all'esercitazione ma anche ai prodotti illustrati nell'esercitazione. I commenti e suggerimenti degli utenti risulteranno utili per definire la priorità dei miglioramenti da apportare. È inoltre possibile richiedere nuovi argomenti e votare gli argomenti esistenti alla pagina [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png

<!--HONumber=54--> 