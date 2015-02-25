<properties 
	pageTitle="Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure" 
	description="Informazioni su come sviluppare un sito Web ASP.NET MVC 5 con il back-end del database SQL e distribuirlo in Azure." 
	services="web-sites, sql-database" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="1/28/2015" 
	ms.author="riande"/> 



# Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure

Questa esercitazione illustrerà come creare un'app Web ASP.NET MVC 5 sicura che consente agli utenti di accedere usando credenziali di Facebook o Google. L'applicazione verrà inoltre distribuita in Azure.

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio Express 2013 per il Web. È possibile iniziare a sviluppare per Azure gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web sicura, basata sui dati, in esecuzione nel cloud e in grado di usare un database cloud.

Si apprenderà:

* Creare un progetto ASP.NET MVC 5 sicuro e pubblicarlo in un sito Web di Azure.
* Usare [OAuth](http://oauth.net/ "http://oauth.net/") e il database di appartenenza ASP.NET per proteggere l'applicazione.
* Usare un database SQL per l'archiviazione di dati in Azure.

Verrà creata una semplice app Web di elenco contatti basata su ASP.NET MVC 5 che usa ADO.NET Entity Framework per l'accesso al database. La seguente illustrazione mostra la pagina di accesso dell'applicazione completata:

![pagina di accesso][rxb]

>[AZURE.NOTE] Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile <a href="/it-it/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">attivare i benefici della sottoscrizione MSDN</a> oppure <a href="/it-it/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">iscriversi per ottenere una versione di valutazione gratuita</a>. Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


Contenuto dell'esercitazione:

- [Configurare l'ambiente di sviluppo](#setupdevenv)
- [Creare un'applicazione ASP.NET MVC 5][createapplication]
- [Distribuire l'applicazione in Azure][deployapp1]
- [Aggiungere un database all'applicazione][adddb]
- [Aggiungere un provider OAuth][]
- [Distribuire l'app in Azure][deployapp11]
- [Passaggi successivi][]


[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Per usare il nuovo certificato SSL per localhost, è necessario installare [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?LinkId=390521) o versione successiva.

<h2><a name="bkmk_createmvc4app"></a>Creare un'applicazione ASP.NET MVC 5</h2>

### Creare il progetto

1. Scegliere **Nuovo progetto** dal menu **File**.

	![Nuovo progetto nel menu File](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. Nella finestra di dialogo **Nuovo progetto** espandere **C#** e selezionare **Web** in **Modelli installati**, quindi selezionare **Applicazione Web ASP.NET**.

1. Assegnare all'applicazione il nome **ContactManager** e fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Nota:** ricordarsi di immettere "ContactManager". Nei blocchi di codice che verranno copiati in seguito si presuppone che il nome del progetto sia ContactManager. 

1. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**. Verificare che l'opzione **Autenticazione** sia impostata su **Account utente individuali*, che la casella **Host nel cloud** sia selezionata e che sia selezionata anche l'impostazione **Sito Web**.

	![Finestra di dialogo Nuovo progetto ASP.NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. La configurazione guidata suggerirà un nome univoco basato su  *ContactManager* (vedere la seguente immagine). Selezionare un'area nelle vicinanze. È possibile usare [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") per trovare il data center a latenza più bassa. 
2. Se non è stato creato un server di database, selezionare **Crea nuovo server** e immettere un nome utente e una password per il database.

	![Configurazione del sito Web di Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

Se si dispone di un server di database, usarlo per creare un nuovo database. I server di database sono una risorsa preziosa e in genere è utile creare più database nello stesso server per attività di test e sviluppo anziché creare un server di database per ogni database. Assicurarsi che il sito Web e il database si trovino nella stessa area.

![Configurazione del sito Web di Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### Impostare intestazione e piè di pagina


1. In **Esplora soluzioni** aprire il file *Layout.cshtml* nella cartella  *Views\Shared*.

	![_Layout.cshtml in Esplora soluzioni][newapp004]

1. Sostituire il markup del file *Layout.cshtml* con il seguente codice. Le modifiche sono evidenziate qui di seguito.

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![modifiche al codice](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Eseguire l'applicazione in locale

1. Premere CTRL+F5 per eseguire l'app.

	La home page dell'applicazione verrà visualizzata nel browser predefinito.

	![Sito Web in esecuzione in locale](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Non è necessario eseguire per il momento altre operazioni per creare l'applicazione che verrà distribuita in Azure. 

## Abilitare SSL per il progetto ##

1. Abilitare SSL. In Esplora soluzioni fare clic sul progetto **ContactManager**, quindi fare clic su F4 per visualizzare la finestra di dialogo delle proprietà. Modificare il valore di **SSL abilitato** in true. Copiare il valore **URL SSL**. L'URL SSL sarà https://localhost:44300/ a meno che non siano stati creati siti Web SSL in precedenza.

	![abilitazione di SSL][rxSSL]
 
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Contact Manager**, quindi scegliere **Proprietà**.
1. Nella scheda a sinistra fare clic su **Web**.
1. Modificare **URL progetto** in modo da usare **URL SSL**, quindi salvare la pagina (CTRL+S).

	![abilitazione di SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Verificare che Internet Explorer sia il browser avviato da Visual Studio, come illustrato nella seguente immagine:

	![browser predefinito](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	Lo strumento di selezione del browser consente di specificare il browser predefinito avviato da Visual Studio.

 	![strumento di selezione del browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	È possibile selezionare più browser in modo che Visual Studio li aggiorni automaticamente ogni volta che vengono apportate modifiche. Per altre informazioni, vedere l'articolo relativo all'[uso del collegamento al browser in Visual Studio 2013].(http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Premere CTRL+F5 per eseguire l'applicazione. Seguire le istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express.

	 ![istruzioni per considerare attendibile il certificato autofirmato generato da IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Leggere quanto riportato nella finestra di dialogo **Avviso di sicurezza**, quindi fare clic su **Sì** se si desidera installare il certificato che rappresenta **localhost**.

 	![avviso relativo al certificato localhost di IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. In Internet Explorer viene visualizzata la pagina *Home* e non sono presenti avvisi SSL.

	 ![IE con SSL localhost e nessun avviso](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Anche Google Chrome accetta il certificato e visualizzerà il contenuto HTTPS senza alcun avviso. In Firefox viene usato un archivio certificati personalizzati, pertanto verrà visualizzato un avviso.

	 ![avviso di Firefox relativo al certificato](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Distribuire l'applicazione in Azure</h2>

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

	![Pubblica nel menu di scelta rapida del progetto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   Viene avviata la procedura guidata **Pubblica sito Web**.

1. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.

	![Pubblica](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	L'applicazione creata verrà ora eseguita nel cloud. Alla successiva distribuzione dell'applicazione, verranno distribuiti solo i file modificati (o nuovi).

	![Esecuzione nel cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Aggiungere un database all'applicazione</h2>

Si passerà quindi all'aggiornamento dell'app, in modo da aggiungere la possibilità di visualizzare e aggiornare i contatti e di archiviare i dati in un database. L'app userà Entity Framework (EF) per creare il database e per leggere e aggiornare i dati.

### Aggiungere classi del modello di dati per i contatti

Creare innanzitutto un semplice modello di dati nel codice.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Modelli, quindi scegliere **Aggiungi** e infine **Classe**.

	![Aggiungi - Classe nel menu di scelta rapida della cartella Modelli](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. Nella finestra di dialogo **Aggiungi nuovo elemento** assegnare al nuovo file di classe il nome  *Contact.cs*, quindi fare clic su **Aggiungi**.

	![Finestra di dialogo Aggiungi nuovo elemento][adddb002]

3. Sostituire il contenuto del file Contacts.cs con il seguente codice.

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

### Creare pagine Web che consentono agli utenti dell'app di usare i contatti

La funzionalità di scaffolding di ASP.NET MVC è in grado di generare automaticamente codice per l'esecuzione di azioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete)).

<h2><a name="bkmk_addcontroller"></a>Aggiungere un controller e una visualizzazione per i dati</h2>

1. Compilare il progetto **(CTRL+MAIUSC+B)**. (Per usare il meccanismo scaffolding, è innanzitutto necessario creare il progetto). 
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Controller, quindi scegliere **Aggiungi** e infine **Controller**.

	![Aggiungi - Controller nel menu di scelta rapida della cartella Controller][addcode001]

5. Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC 5 con visualizzazioni, che usano Entity Framework**, quindi fare clic su **Aggiungi**.
	
	![Finestra di dialogo Aggiungi scaffolding](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. Nell'elenco a discesa **Classe modello** selezionare **Contact (ContactManager.Models)**. (Vedere la seguente immagine.)
1. In **Classe contesto di dati** selezionare **ApplicationDbContext (ContactManager.Models)**. La classe **ApplicationDbContext** verrà usata per il database di appartenenza e per i dati relativi ai contatti.
1. Nella casella di immissione del testo **Nome controller** digitare "CmController" come nome del controller. 

	![Finestra di dialogo del nuovo contesto di dati](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Fare clic su **Aggiungi**.

   In Visual Studio verranno creati metodi e visualizzazioni di un controller per operazioni CRUD di database per oggetti **Contact**.

## Abilitazione delle migrazioni, creazione del database, aggiunta di dati di esempio e di un inizializzatore di dati ##

L'attività successiva consiste nell'abilitare la funzionalità [Migrazioni Code First](http://msdn.microsoft.com/library/hh770484.aspx), in modo da creare il database basato sul modello di dati creato.

1. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.
	![Console di Gestione pacchetti nel menu Strumenti](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. Nella finestra **Console di Gestione pacchetti** immettere il seguente comando:

		enable-migrations
	Il comando **enable-migrations** crea una cartella *Migrations* e inserisce in tale tabella un file *Configuration.cs*, che può essere modificato per effettuare il seeding del database e configurare le migrazioni. 

2. Nella finestra **Console di Gestione pacchetti** immettere il seguente comando:

		add-migration Initial


	Il comando **add-migration Initial** genera un file denominato **&lt;indicatore_data&gt;Initial** nella cartella *Migrations* che crea il database. Il primo parametro ( **Initial** ) è arbitrario e viene usato per creare il nome del file. È possibile visualizzare i nuovi file di classe in **Esplora soluzioni**.
	Nella classe **Initial** il metodo **Up** crea la cartella Contacts e il metodo **Down** (usato quando si desidera tornare allo stato precedente) la rimuove.
3. Aprire il file *Migrations\Configuration.cs*. 
4. Aggiungere il seguente spazio dei nomi. 

    	 using ContactManager.Models;



5. Sostituire il metodo *Seed* con il seguente codice:

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

	Questo codice inizializza (effettua il seeding) il database con le informazioni sui contatti. Per altre informazioni sul seeding del database, vedere la pagina relativa al [seeding e debug di database di Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. In **Console di Gestione pacchetti** immettere il seguente comando:

		update-database

	![Comandi di Console di Gestione pacchetti][addcode009]

	Il comando **update-database** consente di eseguire la prima migrazione al fine di creare il database. Per impostazione predefinita, il database viene creato come database LocalDB di SQL Server Express. 

7. Premere CTRL+F5 per eseguire l'applicazione, quindi fare clic sul collegamento **CM Demo** oppure passare a http://localhost:(port#)/Cm. 

	Nell'applicazione vengono mostrati i dati di seeding e sono disponibili collegamenti per la modifica, i dettagli e l'eliminazione. È possibile creare, modificare, eliminare e visualizzare i dati.

	![Visualizzazione MVC dei dati][rx2]



<h2><a name="addOauth"></a>Aggiungere un provider OAuth2</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") è un protocollo aperto che consente di eseguire l'autenticazione sicura in un metodo semplice e standard da applicazioni Web, per dispositivi mobili e desktop. Il modello Internet ASP.NET MVC usa OAuth per esporre Facebook, Twitter, Google e Microsoft come provider di autenticazione. Benché in questa esercitazione venga usato solo Google come provider di autenticazione, è possibile modificare con facilità il codice per usare un altro provider. I passaggi per l'implementazione di altri provider sono molto simili ai passaggi disponibili in questa esercitazione. Per usare Facebook come provider di autenticazione, vedere l'esercitazione relativa alla [creazione di un'app MVC 5 con autenticazione OAuth2 mediante Facebook, Twitter, LinkedIn e Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Oltre all'autenticazione, nell'esercitazione verranno usati anche i ruoli per implementare l'autorizzazione. Solo gli utenti aggiunti al ruolo *canEdit* saranno in grado di modificare i dati (ovvero creare, modificare o eliminare i contatti).

Seguire le istruzioni fornite nell'esercitazione sulla [creazione di un'app MVC 5 con autenticazione OAuth2 mediante Facebook, Twitter, LinkedIn e Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) nella sezione relativa alla **creazione di un'app Google per OAuth 2 per configurare un'app Google per OAuth2**. Eseguire e testare l'applicazione per verificare che sia possibile accedere usando l'autenticazione di Google.

<h2><a name="mbrDB"></a>Usare l'API di appartenenza</h2>
In questa sezione verranno aggiunti al database di appartenenza un utente locale e il ruolo *canEdit*. Solo gli utenti con ruolo *canEdit* saranno in grado di modificare i dati. È consigliabile assegnare ai ruoli un nome corrispondente alle azioni che consentono di eseguire, quindi il nome *canEdit* è preferibile rispetto ad *admin*. Nel corso dell'evolversi dell'applicazione, sarà possibile aggiungere nuovi ruoli come ad esempio *canDeleteMembers* anziché ruoli con nomi meno descrittivi come *superAdmin*.

1. Aprire il file *migrations\configuration.cs* e aggiungere le seguenti istruzioni  `using`:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Aggiungere il seguente metodo **AddUserAndRole** alla classe:

    
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

1. Chiamare il nuovo metodo dal metodo **Seed**:
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
	</pre>  
<span></span>
	La seguente immagine illustra le modifiche apportate al metodo *Seed*:

	![immagine del codice](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   Questo codice crea un nuovo ruolo denominato *canEdit*, quindi crea un nuovo utente locale *user1@contoso.com* e infine aggiunge *user1@contoso.com* al ruolo *canEdit*. Per altre informazioni, vedere la pagina relativa alle [risorse di ASP.NET Identity](http://curah.microsoft.com/55636/aspnet-identity).

## Usare codice temporaneo per aggiungere al ruolo canEdit nuovi utenti di accesso a social networking ##
In questa sezione sarà possibile modificare temporaneamente il metodo **ExternalLoginConfirmation** nel controller Account per aggiungere nuovi utenti che eseguono la registrazione con un provider OAuth al ruolo *canEdit*. Il metodo **ExternalLoginConfirmation** verrà modificato temporaneamente per aggiungere automaticamente nuovi utenti a un ruolo amministrativo. Il codice temporaneo di registrazione automatica verrà usato fino a quando non sarà disponibile uno strumento per l'aggiunta e la gestione dei ruoli. Ci auguriamo di riuscire a rendere disponibile in futuro uno strumento simile a [WSAT](http://msdn.microsoft.com/it-it/library/ms228053.aspx) che consenta di creare e modificare account utente e ruoli. Più avanti l'esercitazione mostrerà come usare **Esplora server** per aggiungere utenti ai ruoli.  

1. Aprire il file **Controllers\AccountController.cs** e passare al metodo **ExternalLoginConfirmation**.
1. Aggiungere la seguente chiamata ad **AddToRoleAsync** immediatamente prima della chiamata **SignInAsync**:

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   Il codice precedente aggiunge l'utente appena registrato al ruolo "canEdit", in modo da concedere all'utente l'accesso ai metodi di azione per la modifica (edit) dei dati. Di seguito è riportata un'immagine della modifica del codice:

   ![codice](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Più avanti nell'esercitazione l'applicazione verrà distribuita in Azure, a cui si effettuerà l'accesso tramite Google o un altro provider di autenticazione di terze parti. L'account appena registrato verrà aggiunto al ruolo *canEdit*. Chiunque abbia a disposizione l'URL del sito e un ID di Google potrà effettuare la registrazione e aggiornare il database. Per impedire ad altri utenti di eseguire tali operazioni, è possibile arrestare il sito. Per verificare gli utenti appartenenti al ruolo *canEdit*, è possibile esaminare il database.

In **Console di Gestione pacchetti** usare il tasto freccia su per visualizzare il seguente comando:

		Update-Database

Eseguire il comando **Update-Database**, che esegue il metodo **Seed**, che a sua volta esegue il comando **Seed** appena aggiunto. Il comando **AddUserAndRole** consente di creare l'utente *user1@contoso.com* e aggiungerlo al ruolo *canEdit*.

## Proteggere l'applicazione con SSL e l'attributo Authorize ##

In questa sezione verrà applicato l'attributo [Authorize](http://msdn.microsoft.com/it-it/library/system.web.mvc.authorizeattribute.aspx) per limitare l'accesso ai metodi di azione. Gli utenti anonimi saranno in grado di visualizzare il metodo di azione **Index** solo per il controller Home. Gli utenti registrati saranno in grado di visualizzare i dati sui contatti (pagine **Index** e **Details** del controller Cm) e le pagine About e Contact. Solo gli utenti appartenenti al ruolo  *canEdit* saranno in grado di accedere ai metodi di azione che modificano i dati.

1. Aggiungere il filtro [Authorize](http://msdn.microsoft.com/it-it/library/system.web.mvc.authorizeattribute.aspx) e il filtro [RequireHttps](http://msdn.microsoft.com/it-it/library/system.web.mvc.requirehttpsattribute.aspx) all'applicazione. Un approccio alternativo consiste nell'aggiungere l'attributo [Authorize](http://msdn.microsoft.com/it-it/library/system.web.mvc.authorizeattribute.aspx) e l'attributo [RequireHttps](http://msdn.microsoft.com/it-it/library/system.web.mvc.requirehttpsattribute.aspx) a ogni controller, ma ai fini della sicurezza è consigliabile applicarli all'intera applicazione. Grazie all'aggiunta a livello globale, ogni nuovo controller e ogni metodo di azione aggiunti saranno protetti automaticamente e non sarà necessario ricordarsi di applicare gli attributi. Per altre informazioni, vedere la pagina relativa alla [protezione dell'app ASP.NET MVC e al nuovo attributo AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Aprire il file *App_Start\FilterConfig.cs* e sostituire il metodo  *RegisterGlobalFilters* con il seguente codice (che aggiunge i due filtri):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>




	Il filtro [Authorize](http://msdn.microsoft.com/it-it/library/system.web.mvc.authorizeattribute.aspx) applicato nel codice precedente consente di impedire agli utenti anonimi di accedere ai metodi dell'applicazione. È possibile usare l'attributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) per rifiutare esplicitamente il requisito di autorizzazione in un paio di metodi per consentire agli utenti anonimi di accedere e visualizzare la home page. Con [RequireHttps](http://msdn.microsoft.com/it-it/library/system.web.mvc.requirehttpsattribute.aspx) si rende obbligatorio l'accesso all'app Web tramite HTTPS.

1. Aggiungere l'attributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) al metodo **Index** del controller Home. L'attributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) consente di aggiungere all'elenco di metodi consentiti i metodi che si desidera rifiutino in modo esplicito l'autorizzazione. Di seguito è riportata l'immagine di una parte del codice per HomeController:	

  	![codice](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Eseguire una ricerca globale di *AllowAnonymous*. Come è possibile vedere, viene usato nei metodi di accesso e registrazione del controller Account.
1. In *CmController.cs* aggiungere `[Authorize(Roles = "canEdit")]` ai metodi HttpGet e HttpPost che modificano i dati (Create, Edit, Delete, qualsiasi metodi di azione tranne Index e Details) nel controller *Cm*. Di seguito è riportata una parte del codice completato: 

   	![immagine del codice](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. Se si è ancora connessi da una sessione precedente, fare clic sul collegamento **Disconnetti**.
1. Fare clic sul collegamento **About** o **Contact**. Si verrà reindirizzati alla pagina di accesso, poiché gli utenti anonimi non sono autorizzati a visualizzare tali pagine. 
1. Fare clic sul collegamento **Esegui registrazione come nuovo utente** e aggiungere un utente locale con l'indirizzo di posta elettronica *joe@contoso.com*. Assicurarsi che *Joe* sia in grado di visualizzare le pagine Home, About e Contact. 

	![accesso](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Fare clic sul collegamento *CM Demo* e verificare che i dati siano visibili. 
1. Fare clic su un collegamento di modifica nella pagina. Si verrà reindirizzati alla pagina di accesso (perché il nuovo utente locale non è stato aggiunto al ruolo *canEdit*).
1. Accedere con il nome utente *user1@contoso.com* e la password "P_assw0rd1" (il carattere "0" in "word" è uno zero). Si verrà reindirizzati alla pagina di modifica selezionata in precedenza. <br/>
   Se non è possibile effettuare l'accesso con tale account e password, provare a copiare la password dal codice sorgente e incollarla. Se ancora non è possibile accedere, verificare la colonna **UserName** della tabella **AspNetUsers** per accertarsi che  *user1@contoso.com* sia stato aggiunto. 

1. Verificare che sia possibile apportare modifiche ai dati.

<h2><a name="bkmk_deploytowindowsazure11"></a>Distribuire l'app in Azure</h2>

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

	![Pubblica nel menu di scelta rapida del progetto][firsdeploy003]

	Viene avviata la procedura guidata **Pubblica sito Web**.

1. Fare clic sulla scheda **Impostazioni** nella parte sinistra della finestra di dialogo **Pubblica sito Web**. Fare clic sull'icona **v** per selezionare la **stringa di connessione remota** per **ApplicationDbContext**, quindi selezionare **ContactManagerNN_db**.

   
	![impostazioni](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. In **ContactManagerContext** selezionare **Esegui migrazioni Code First**.

	![impostazioni](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Fare clic su **Pubblica**.
1. Accedere come *user1@contoso.com* (con la password "P_assw0rd1") e verificare che sia possibile apportare modifiche ai dati.
1. Effettuare la disconnessione.
1. Passare alla [Google Developers Console](https://console.developers.google.com/) e nella scheda **Credentials** aggiornare gli URI di reindirizzamento e le origini JavaScript in modo che usino l'URL di Azure.
1. Effettuare l'accesso usando Google o Facebook. L'account Google o Facebook verrà aggiunto al ruolo **canEdit**. Se si ottiene un errore HTTP 400 con un messaggio che indica che *l'URI di reindirizzamento nella richiesta: https://contactmanager{my version}.azurewebsites.net/signin-google non corrisponde a un URI di reindirizzamento registrato*, sarà necessario attendere che le modifiche apportate vengano propagate. Se questo errore viene visualizzato dopo più di un minuto, verificare che gli URI siano corretti.

### Arrestare il sito Web per impedire la registrazione da parte di altri utenti  

1. In **Esplora server** passare a **Siti Web**.
4. Fare clic con il pulsante destro del mouse su ogni istanza del sito Web e scegliere **Arresta sito Web**.. 

	![arresto del sito Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	In alternativa, dal portale di gestione di Azure è possibile selezionare il sito Web e fare clic sull'icona di **arresto** nella parte inferiore della pagina.

	![arresto del sito Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Rimuovere AddToRoleAsync, Publish e Test

1. Impostare come commento o rimuovere il seguente codice dal metodo **ExternalLoginConfirmation** nel controller Account: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Compilare il progetto (le modifiche apportate al file verranno salvate e sarà necessario controllare che non si siano verificati errori di compilazione).
5. Fare clic con il pulsante destro del mouse in **Esplora soluzioni**, quindi scegliere **Pubblica**.

	   ![Pubblica nel menu di scelta rapida del progetto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Fare clic sul pulsante **Avvia anteprima**. Vengono distribuiti solo i file che necessitano di aggiornamento.
5. Avviare il sito Web da Visual Studio o dal portale. **Non sarà possibile eseguire la pubblicazione mentre il sito Web è arrestato**.

	![avvio del sito Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Tornare in Visual Studio e fare clic su **Pubblica**.
3. L'app di Azure App verrà aperta nel browser predefinito. Se si è effettuato l'accesso, disconnettersi in modo da visualizzare la home page come un utente anonimo.  
4. Fare clic sul collegamento **About**. Si verrà reindirizzati alla pagina di accesso.
5. Fare clic sul collegamento **Register** nella pagina di accesso, quindi creare un account locale. L'account locale verrà usato per verificare se si è in grado di accedere alle pagine di sola lettura, ma non alle pagine che consentono la modifica dei dati (protette dal ruolo *canEdit*). L'accesso dell'account locale verrà rimosso più avanti nell'esercitazione. 

	![registrazione](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Verificare che sia possibile passare alle pagine *About* e *Contact*.

	![Disconnessione](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Fare clic sul collegamento **CM Demo** per passare al controller **Cm**. In alternativa è possibile aggiungere *Cm* alla fine dell'URL. 

	![pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Fare clic su un collegamento per la modifica. Si verrà reindirizzati alla pagina di accesso. In **Use another service to log in** fare clic su Google o Facebook ed effettuare l'accesso con l'account registrato in precedenza. (Se si lavora velocemente e il cookie della sessione non è scaduto, si accederà automaticamente con l'account Google o Facebook usato in precedenza.)
2. Verificare che sia possibile modificare i dati quando si effettua l'accesso con tale account.
 	**Nota:** non è possibile disconnettersi da Google da questa app e quindi accedere a un account Google diverso con lo stesso browser. Se si usa un browser, sarà necessario passare a Google e disconnettersi. È possibile accedere con un altro account dallo stesso autenticatore di terze parti (ad esempio Google) usando un browser diverso.

Se nelle informazioni sull'account Google non sono stati compilati i campi relativi a nome e cognome, verrà generata un'eccezione di tipo NullReferenceException.


## Esaminare il database SQL di Azure ##

1. In **Esplora server** passare a **ContactDB**
2. Fare clic con il pulsante destro del mouse su **ContactDB** e scegliere **Apri in Esplora oggetti di SQL Server**.
 
	![apertura in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Nota:** se non è possibile espandere **Database SQL** e *can't* visualizzare **ContactDB** da Visual Studio, sarà necessario seguire le seguenti istruzioni per aprire una porta del firewall o un intervallo di porte. Seguire le istruzioni fornite in **Configurare le regole del firewall per Azure**. Dopo l'aggiunta della regola del firewall, potrebbe essere necessario attendere alcuni minuti prima di accedere al database.
 
1. Fare clic con il pulsante destro del mouse sulla tabella **AspNetUsers** e scegliere **Visualizza dati**.

	![pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Controllare che l'ID dell'account Google usato per la registrazione e l'ID di *user1@contoso.com* siano visualizzati nel ruolo **canEdit**. Questi devono essere gli unici utenti nel ruolo **canEdit**. (Questa verifica verrà eseguita nel passaggio successivo.)

	![pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. In **Esplora oggetti di SQL Server** fare clic con il pulsante destro del mouse su **AspNetUserRoles** e scegliere **Visualizza dati**.

	![pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Verificare che gli **ID utente** corrispondano a *user1@contoso.com* e all'account Google usato per la registrazione. 


## Configurare le regole del firewall per Azure ##

Se non è possibile eseguire la connessione a SQL Azure da Visual Studio o se viene visualizzata una finestra di dialogo di errore del tipo "Impossibile aprire il server", attenersi alle istruzioni disponibili in questa sezione.

![errore del firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Sarà necessario aggiungere l'indirizzo IP agli indirizzi IP autorizzati.

1. Nel portale Azure selezionare **Database SQL** nella scheda a sinistra.

	![selezione di SQL][rx6]

1. Fare clic su **ContactDB**.

1. Fare clic sul collegamento **Imposta regole firewall di Microsoft Azure per questo indirizzo IP**.

	![regole del firewall][rx7]

1. Quando viene visualizzata la richiesta "L'indirizzo IP corrente xxx.xxx.xxx.xxx non è incluso nelle regole del firewall esistenti. Aggiornare le regole del firewall?", fare clic su **Sì**. L'aggiunta di questo indirizzo non è spesso sufficiente. Nel caso di alcuni firewall aziendali sarà necessario aggiungere un intervallo di indirizzi IP.

Il passaggio successivo consiste nell'aggiunta di un intervallo di indirizzi IP autorizzati.

1. Nel portale Azure fare clic su **Database SQL**.
1. Selezionare la scheda **Server** e fare clic sul server da configurare.

	![Scheda Server in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Fare clic sulla scheda **Configura**.

1. Aggiungere un nome di regola e gli indirizzi IP di inizio e di fine.

	![intervallo IP][rx9]

1. Nella parte inferiore della pagina fare clic su **Salva**.
1. Se è necessario aggiungere un intervallo di indirizzi IP per la connessione, inviare commenti in merito.

È infine possibile connettersi all'istanza di database SQL da Esplora oggetti di SQL Server (SSOX).

1. Dal menu Visualizza scegliere **Esplora oggetti di SQL Server**.
1. Fare clic con il pulsante destro del mouse su **SQL Server** e scegliere **Aggiungi SQL Server**.
1. Nella finestra di dialogo **Connetti al server** impostare **Autenticazione** su **Autenticazione di SQL Server**. Verranno visualizzati **Nome server** e **Accesso** dal portale Azure.
1. Nel browser passare al portale e selezionare **Database SQL**.
1. Selezionare **ContactDB** e fare clic su **Visualizzare le stringhe di connessione del database SQL**.
1. Nella pagina **Stringhe di connessione** copiare i valori di **Server** e **ID utente**.
 
	![stringa di connessione](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Incollare i valori **Server** e **User ID** nella finestra di dialogo **Connetti al server** in Visual Studio. Il valore **User ID** deve essere inserito nel campo **Accesso**. Immettere la password usata per creare il database SQL.

	![Finestra di dialogo di connessione al server](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

È ora possibile passare al database Contact attenendosi alle istruzioni riportate in precedenza.


## Per aggiungere un utente al ruolo canEdit modificando le tabelle del database

Nei passaggi precedenti dell'esercitazione, per aggiungere utenti al ruolo canEdit è stato usato codice. Un metodo alternativo consiste nel modificare i dati direttamente nelle tabelle di appartenenza. La seguente procedura illustra come usare questo metodo alternativo per aggiungere un utente a un ruolo.

2. In **Esplora oggetti di SQL Server** fare clic con il pulsante destro del mouse su **AspNetUserRoles** e scegliere **Visualizza dati**.

	![pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copiare il valore di *RoleId* e incollarlo nella riga vuota (nuova).
	
	![pagina CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. Nella tabella **AspNetUsers** individuare l'utente da inserire nel ruolo, copiare l'*Id* dell'utente, quindi incollarlo nella colonna **UserId** della tabella **AspNetUserRoles**.

Microsoft sta attualmente lavorando alla creazione di uno strumento per semplificare la gestione di utenti e ruoli.


<h2><a name="nextsteps"></a>Passaggi successivi</h2>

Seguire le esercitazioni basate su questo esempio:

1.	[Creare un'app Web ASP.NET MVC 5 sicura con accesso, conferma tramite posta elettronica e reimpostazione della password](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[App ASP.NET MVC 5 con SMS e messaggio di posta elettronica per autenticazione a due fattori](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Creare un'app ASP.NET MVC 5 con autenticazione OAuth2 mediante Facebook e Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ). Sono fornite istruzioni su come aggiungere dati del profilo al database di registrazione utenti e sull'uso di Facebook come provider di autenticazione.
5.	[Introduzione ad ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Per abilitare i pulsanti di accesso dai social network visualizzati nella parte superiore di questa esercitazione, vedere l'articolo relativo alla [creazione di pulsanti di accesso dall'aspetto gradevole per ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

Per informazioni più avanzate sulla programmazione MVC ed EF, vedere l'interessante pagina di [introduzione a EF e MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) di Tom Dykstra.

Questa esercitazione e l'applicazione di esempio sono state scritte da [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con il supporto di Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

***Inviare commenti e suggerimenti*** sugli aspetti ritenuti utili e su eventuali miglioramenti da apportare, non solo in merito all'esercitazione, ma anche ai prodotti che illustra. I commenti e suggerimenti degli utenti risulteranno utili per definire la priorità dei miglioramenti da apportare. È inoltre possibile richiedere nuovi argomenti e votare gli argomenti esistenti alla pagina [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

<!-- bookmarks -->
[Aggiungere un provider OAuth]: #addOauth
[Usare l'API di appartenenza]:#mbrDB
[Creare uno script di distribuzione dei dati]:#ppd
[Aggiornare il database di appartenenza]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

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

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Informazioni importanti su ASP.NET in Siti Web di Azure]: #aspnetwindowsazureinfo
[Passaggi successivi]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png


























<!--HONumber=42-->
