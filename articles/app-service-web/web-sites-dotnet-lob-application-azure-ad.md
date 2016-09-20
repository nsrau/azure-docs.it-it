<properties 
	pageTitle="Creare un'app line-of-business in Azure con l'autenticazione di Azure Active Directory | Microsoft Azure" 
	description="Informazioni su come creare un'applicazione line-of-business ASP.NET MVC nel servizio app di Azure che esegue l'autenticazione con Azure Active Directory" 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="09/01/2016" 
	ms.author="cephalin"/>  

# Creare un'app line-of-business in Azure con l'autenticazione di Azure Active Directory #

Questo articolo illustra come creare un'app line-of-business .NET nelle [app Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando la funzionalità [Autenticazione/Autorizzazione](../app-service/app-service-authentication-overview.md). Spiega inoltre come usare l'[API Graph di Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) per eseguire query sui dati della directory nell'applicazione.

Il tenant di Azure Active Directory usato può essere una directory solo di Azure. In alternativa, può essere [sincronizzato con l'istanza di Active Directory locale](../active-directory/active-directory-aadconnect.md) per creare un'esperienza Single Sign-On per gli utenti locali e remoti. In questo articolo si usa la directory predefinita per l'account Azure.

<a name="bkmk_build"></a>
## Obiettivo di compilazione ##

Verrà compilata una semplice applicazione CRUD (Create-Read-Update-Delete) line-of-business nelle app Web di Servizio app di Azure per tenere traccia degli elementi di lavoro con le seguenti funzionalità:

- Autenticazione degli utenti con Azure Active Directory
- Esecuzione di query su utenti e gruppi della directory tramite l'[API Graph di Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Usare il modello MVC ASP.NET *Nessuna autenticazione*

Se è necessario il Controllo degli accessi in base al ruolo per l'applicazione line-of-business in Azure, vedere il [passaggio successivo](#next).

<a name="bkmk_need"></a>
## Elementi necessari ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Per completare questa esercitazione sarà necessario quanto segue:

- Un tenant di Azure Active Directory con utenti in diversi gruppi
- Autorizzazioni per la creazione di applicazioni nel tenant di Azure Active Directory
- Visual Studio 2013 Update 4 o versione successiva
- [Azure SDK 2.8.1 o versione successiva](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>
## Creare e distribuire un'app Web in Azure ##

1. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.

2. Selezionare **Applicazione Web ASP.NET**, denominare un nome al progetto e fare clic su **OK**.

3. Selezionare il modello **MVC**, quindi modificare l'autenticazione in **Nessuna autenticazione**. Assicurarsi che **Ospita nel cloud** sia selezionata e fare clic su **OK**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)  

4. Nella finestra di dialogo **Crea servizio App** fare clic su **Aggiungi un account** (e quindi **Aggiungi un account** nell'elenco a discesa) per accedere al proprio account Azure.

5. Dopo aver eseguito l'accesso, configurare l'app Web. Creare un gruppo di risorse e un nuovo piano di servizio app facendo clic sul pulsante **Nuovo** corrispondente. Fare clic su **Esplora altri servizi di Azure** per continuare.

	![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)  

6. Nella scheda**Servizi** , fare clic su **+** per aggiungere un database SQL per l'app.

	![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)  

7. In **Configura database SQL** fare clic su **Nuovo** per creare un'istanza di SQL Server.

8. In **Configura SQL Server** configurare l'istanza di SQL Server. Fare clic su **OK**, **OK** e su **Crea** per avviare la creazione dell'app in Azure.

9. In **Attività del servizio app di Azure** è possibile vedere quando termina la creazione dell'app. Fare clic su **Pubblica &lt;*nomeapp*> in questa app Web adesso**, quindi fare clic su **Pubblica**.

	Al termine del processo di Visual Studio, nel browser verrà aperta l'app pubblicata.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>
## Configurare l'autenticazione e l'accesso alla directory

1. Accedere al [Portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra fare clic su **Servizi app** > **&lt;*nomeapp*>** > **Autenticazione / Autorizzazione**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)  

3. Attivare l'autenticazione di Azure Active Directory facendo clic su **Sì** > **Azure Active Directory** > **Rapida** > **OK**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)  

4. Fare clic su **Salva** nella barra dei comandi.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)

    Dopo aver salvato le impostazioni di autenticazione, provare a passare di nuovo all'app nel browser. Le impostazioni predefinite applicano l'autenticazione all'intera app. Se non si è già connessi, si viene reindirizzati a una schermata di accesso. Dopo aver effettuato l'accesso, verrà visualizzata l'app protetta tramite HTTPS. Quindi, è necessario abilitare l'accesso ai dati della directory.

5. Passare al [portale classico](https://manage.windowsazure.com).

6. Nel menu a sinistra fare clic su **Active Directory** > **Directory predefinita** > **Applicazioni** > **&lt;*nomeapp*>**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)  

	Questa è l'applicazione Azure Active Directory creata automaticamente dal servizio app per abilitare la Autorizzazione/Autenticazione.

7. Fare clic su **Utenti** e **Gruppi** per assicurarsi di avere alcuni utenti e gruppi nella directory. In caso contrario, creare alcuni utenti e gruppi di test.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)

7. Fare clic su **Configura** per configurare l'applicazione.

8. Scorrere verso il basso fino alla sezione **Chiavi** e aggiungere una chiave selezionando una durata. Fare quindi clic su **Autorizzazioni delegate** e selezionare **Leggi i dati della directory**. Fare clic su **Salva**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)  

8. Dopo aver salvato le impostazioni, scorrere tornando alla sezione **Chiavi** e fare clic su **Copia** per copiare la chiave client.

	![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)  

	>[AZURE.IMPORTANT] Se si esce dalla pagina ora, non sarà mai più possibile accedere a questa chiave client.

9. Configurare quindi l'app Web con questa chiave. Accedere a [Esplora risorse di Azure](https://resources.azure.com) con il proprio account Azure.

10. Nella parte superiore della pagina fare clic su **Lettura/Scrittura** per apportare modifiche in Esplora risorse di Azure.

	![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)  

11. Trovare le impostazioni di autenticazione per l'app disponibili in sottoscrizioni > **& lt;*nomesottoscrizione*>** > **gruppidirisorse** > **& lt;*nomegruppodirisorse*>** > **provider** > **Microsoft.Web** > **siti** > **&lt;*nomeapp*>** > **config** > **authsettings**.

12. Fare clic su **Modifica**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)  

13. Nel riquadro di modifica impostare le proprietà `clientSecret` e `additionalLoginParams` come indicato di seguito.

		...
		"clientSecret": "<client key from the Azure Active Directory application>",
		...
		"additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
		...

14. Fare clic su **Put** nella parte superiore per inviare le modifiche.

	![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)  

14. A questo punto, per verificare se è disponibile il token di autorizzazione per accedere all'API Graph di Azure Active Directory, passare a **https://&lt;*appname*>.azurewebsites.net/.auth/me** nel browser. Se è stato configurato tutto correttamente, nella risposta JSON verrà visualizzata la proprietà `access_token`.

	Il percorso URL `~/.auth/me` è gestito dall'autenticazione/autorizzazione del servizio app per fornire all'utente tutte le informazioni relative alla sessione autenticata. Per altre informazioni, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md).

	>[AZURE.NOTE] `access_token` ha un periodo di scadenza. L'autenticazione/autorizzazione del servizio app offre tuttavia funzionalità di aggiornamento del token con `~/.auth/refresh`. Per altre informazioni sull'uso, vedere [App Service Token Store](https://cgillum.tech/2016/03/07/app-service-token-store/) (Archivio Token del servizio app).

Ora si eseguirà un'operazione utile con i dati della directory.

<a name="bkmk_crud"></a>
## Aggiungere funzionalità line-of-business all'app

Verrà creato un semplice progetto di gestione degli elementi di lavoro CRUD.

5.	Nella cartella ~\\Models creare un file di classe denominato WorkItem.cs e sostituire `public class WorkItem {...}` con il codice seguente:

		using System.ComponentModel.DataAnnotations;

		public class WorkItem
		{
			[Key]
			public int ItemID { get; set; }
			public string AssignedToID { get; set; }
			public string AssignedToName { get; set; }
			public string Description { get; set; }
			public WorkItemStatus Status { get; set; }
		}

		public enum WorkItemStatus
		{
			Open,
			Investigating,
			Resolved,
			Closed
		}

7.	Compilare il progetto per rendere accessibile il nuovo modello alla logica di scaffolding in Visual Studio.

8.	Aggiungere un nuovo elemento di scaffolding `WorkItemsController` nella cartella ~\\Controllers. Fare clic con il pulsante destro del mouse su **Controller**, scegliere **Aggiungi** e selezionare **Nuovo elemento di scaffolding**.

9.	Selezionare **Controller MVC 5 con visualizzazioni che usano Entity Framework** e quindi fare clic su **Aggiungi**.

10.	Selezionare il modello creato, fare clic su **+**, quindi su **Aggiungi** per aggiungere un contesto dei dati e infine fare clic su **Aggiungi**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)  

14.	In ~\\Views\\WorkItems\\Create.cshtml (un elemento sottoposto automaticamente a scaffolding) individuare il metodo helper `Html.BeginForm` e apportare le modifiche evidenziate di seguito:
	<pre class="prettyprint">
	@model WebApplication1.Models.WorkItem
	
	@{
		ViewBag.Title = "Create";
	}
	
	&lt;h2>Create&lt;/h2>
	
	@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>)) 
	{
		@Html.AntiForgeryToken()
	
		&lt;div class="form-horizontal">
			&lt;h4>WorkItem&lt;/h4>
			&lt;hr />
			@Html.ValidationSummary(true, "", new { @class = "text-danger" })
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToID, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type = "hidden"</mark> } })
					@Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
					@Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				&lt;div class="col-md-offset-2 col-md-10">
					&lt;input type="submit" value="Create" class="btn btn-default"<mark> id="submit-button"</mark> />
				&lt;/div>
			&lt;/div>
		&lt;/div>
	}
	
	&lt;div>
		@Html.ActionLink("Back to List", "Index")
	&lt;/div>
	
	@section Scripts {
		@Scripts.Render("~/bundles/jqueryval")
		<mark>&lt;script>
			// People/Group Picker Code
			var maxResultsPerPage = 14;
			var input = document.getElementById("AssignedToName");
	
			// Access token from request header, and tenantID from claims identity
			var token = "@Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]";
			var tenant ="@(System.Security.Claims.ClaimsPrincipal.Current.Claims
							.Where(c => c.Type == "http://schemas.microsoft.com/identity/claims/tenantid")
							.Select(c => c.Value).SingleOrDefault())";
	
			var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
			// Submit the selected user/group to be asssigned.
			$("#submit-button").click({ picker: picker }, function () {
				if (!picker.Selected())
					return;
				$("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
			});
		&lt;/script></mark>
	}
	</pre>
	
	Si noti che `token` e `tenant` vengono usati dall'oggetto `AadPicker` per eseguire chiamate all'API Graph di Azure Active Directory. Si aggiungerà `AadPicker` in un secondo momento.
	
	>[AZURE.NOTE] È anche possibile ottenere `token` e `tenant` dal lato client con `~/.auth/me`, ma si tratterebbe di una chiamata server aggiuntiva. Ad esempio:
	>  
    >     $.ajax({
    >         dataType: "json",
    >         url: "/.auth/me",
    >         success: function (data) {
    >             var token = data[0].access_token;
    >             var tenant = data[0].user_claims
    >                             .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid')
    >                             .val;
    >         }
    >     });
	
15. Apportare le stesse modifiche con ~\\Views\\WorkItems\\Edit.cshtml.

15. L'oggetto `AadPicker` è definito in uno script che è necessario aggiungere al progetto. Fare clic con il pulsante destro del mouse sulla cartella ~\\Scripts, scegliere **Aggiungi** e fare clic sul **file JavaScript**. Digitare `AadPickerLibrary` come nome file e fare clic su **OK**.

16. Copiare il contenuto da [qui](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) in ~\\Scripts\\AadPickerLibrary.js.

	Nello script l'oggetto `AadPicker` chiama l'[API Graph di Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) per cercare gli utenti e i gruppi corrispondenti all'input.

17. Anche ~\\Scripts\\AadPickerLibrary.js usa il [widget di completamento automatico dell'interfaccia utente jQuery](https://jqueryui.com/autocomplete/). È quindi necessario aggiungere l'interfaccia utente di jQuery al progetto. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.

18. In Gestione pacchetti NuGet fare clic su Sfoglia, digitare **jquery-ui** nella barra di ricerca e fare clic su **jQuery.UI.Combined**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)  

19. Nel riquadro di destra fare clic su **Installa**, e quindi su **OK** per continuare.

19. Aprire ~\\App\_Start\\BundleConfig.cs e apportare le modifiche evidenziate di seguito:
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
		bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
					"~/Scripts/jquery-{version}.js"<mark>,
					"~/Scripts/jquery-ui-{version}.js",
					"~/Scripts/AadPickerLibrary.js"</mark>));
	
		bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
					"~/Scripts/jquery.validate*"));
	
		// Use the development version of Modernizr to develop with and learn from. Then, when you're
		// ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
					"~/Scripts/modernizr-*"));
	
		bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
					"~/Scripts/bootstrap.js",
					"~/Scripts/respond.js"));
	
		bundles.Add(new StyleBundle("~/Content/css").Include(
					"~/Content/bootstrap.css",
					"~/Content/site.css"<mark>,
					"~/Content/themes/base/jquery-ui.css"</mark>));
	}
	</pre>

	Sono disponibili altri modi più efficienti per gestire i file CSS e JavaScript nell'app. Tuttavia, per semplicità, sarà sufficiente occuparsi dei bundle caricati con ogni visualizzazione.

12. In ~\\Global.asax aggiungere infine la riga di codice seguente al metodo `Application_Start()`. Fare `Ctrl`+`.` su ogni errore di risoluzione dei nomi per correggerlo.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	> [AZURE.NOTE] Questa riga di codice è necessaria perché il modello MVC predefinito usa <code>[ValidateAntiForgeryToken]</code> in alcune azioni. A causa del comportamento descritto da [Brock Allen](https://twitter.com/BrockLAllen) nella pagina [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (MVC 4, AntiForgeryToken e attestazioni), è possibile che la convalida del token antifalsificazione con HTTP POST non riesca per i motivi seguenti:

	> - Azure Active Directory non invia http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, richiesto per impostazione predefinita dal token antifalsificazione.
	> - Se in Azure Active Directory è prevista la sincronizzazione della directory con AD FS, per impostazione predefinita il trust ADFS non invia l'attestazione http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, anche se è possibile configurare manualmente AD FS per l'invio di questa attestazione.

	> `ClaimTypes.NameIdentifies` specifica l'attestazione `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, che non viene fornita da Azure Active Directory.

20. A questo punto, pubblicare le modifiche. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

21. Fare clic su **Impostazioni**, assicurarsi che sia disponibile una stringa di connessione al database SQL, selezionare **Aggiorna database** per apportare le modifiche allo schema per il modello e fare clic su **Pubblica**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)  

22. Nel browser passare a https://&lt;*appname*>.azurewebsites.net/workitems e fare clic su **Crea nuovo**.

23. Fare clic sulla casella **AssignedToName**. Gli utenti e i gruppi del tenant di Azure Active Directory verranno visualizzati in un elenco a discesa. È possibile digitare per filtrare o usare la chiave `Up` o `Down` oppure fare clic per selezionare l'utente o il gruppo.

	![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)  

24. Fare clic su **Salva** per salvare le modifiche. Quindi, fare clic su **Modifica** nell'elemento di lavoro creato per osservare lo stesso comportamento.

A questo punto è in esecuzione un'app line-of-business in Azure con accesso alla directory. Con l'API Graph è possibile fare molto di più. Vedere [Azure AD Graph API reference](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog) (Informazioni di riferimento sull'API Graph di Azure AD).

<a name="next"></a>
## Passaggio successivo

Se è necessario il controllo degli accessi in base al ruolo per l'app line-of-business in Azure, vedere [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) per un esempio del team di Azure Active Directory. Viene illustrato come abilitare i ruoli per un'applicazione Azure Active Directory e quindi autorizzare gli utenti con `[Authorize]`.

Se l'app line-of-business deve accedere a dati locali, vedere [Accedere alle risorse locali usando connessioni ibride nel servizio app di Azure](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>
## Altre risorse

- [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md)
- [Eseguire l'autenticazione con l'istanza locale di Active Directory nell'app Azure](web-sites-authentication-authorization.md)
- [Creare un'app line-of-business in Azure con l'autenticazione di AD FS](web-sites-dotnet-lob-application-adfs.md)
- [App Service Auth and the Azure AD Graph API (Autenticazione del servizio app e API Graph di Azure AD)](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
- [Esempi e documentazione su Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [Token e tipi di attestazioni supportati di Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[Protect the Application with SSL and the Authorize Attribute]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute

<!---HONumber=AcomDC_0907_2016-->