<properties 
	pageTitle="Creare un'app Web .NET MVC in Servizio app di Azure con l'autenticazione di Azure Active Directory" 
	description="Informazioni su come creare un'applicazione line-of-business ASP.NET MVC in Azure Web Service che esegue l'autenticazione con Azure Active Directory" 
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
	ms.date="04/09/2015" 
	ms.author="cephalin"/>

# Creare un'app Web .NET MVC in Servizio app di Azure con l'autenticazione di Azure Active Directory #

Questo articolo spiega come creare un'applicazione line-of-business ASP.NET MVC in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando [Azure Active Directory](/services/active-directory/) come provider di identità. Spiega inoltre come usare la [libreria client di Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) per eseguire query sui dati di directory nell'applicazione.

Il tenant di Azure Active Directory usato può includere una directory di tipo solo Azure oppure può essere sincronizzato con l'istanza locale di Active Directory (AD) per creare un'esperienza Single Sign-On per gli utenti locali o remoti.

>[AZURE.NOTE]La funzionalità Easy Auth nelle app Web di Servizio app di Azure consente di configurare l'autenticazione semplice con un tenant di Azure Active Directory eseguendo un numero ridotto di operazioni. Per altre informazioni, vedere [Usare Active Directory per l'autenticazione nel servizio app di Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Obiettivo di compilazione ##

Verrà compilata una semplice applicazione CRUD (Create-Read-Update-Delete) line-of-business nelle app Web di Servizio app di Azure per tenere traccia degli elementi di lavoro con le seguenti funzionalità:

- Autenticazione degli utenti con Azure Active Directory
- Funzionalità di accesso e di disconnessione
- Uso di `[Authorize]` per autorizzare gli utenti per diverse azioni CRUD
- Esecuzione di query sui dati di Azure Active Directory tramite l'[API di Azure Active Directory Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Uso di [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (invece di Windows Identity Foundation, ovvero WIF), che rappresenta il futuro di ASP.NET e offre una configurazione molto più semplice per l'autenticazione e l'autorizzazione rispetto a WIF

<a name="bkmk_need"></a>
## Prerequisiti ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per completare questa esercitazione sarà necessario quanto segue:

- Un tenant di Azure Active Directory con utenti in diversi gruppi
- Autorizzazioni per la creazione di applicazioni nel tenant di Azure Active Directory
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o versioni successive

<a name="bkmk_sample"></a>
## Usare l'applicazione di esempio per il modello line-of-business ##

L'applicazione di esempio in questa esercitazione, [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet), è stata creata dal team di Azure Active Directory e può essere usata come modello per creare con facilità nuove applicazioni line-of-business. Include le funzionalità predefinite seguenti:

- Uso di [OpenID Connect](http://openid.net/connect/) per l'autenticazione con Azure Active Directory
- Controller `Roles` che contiene un filtro di ricerca di Azure Active Directory e permette di eseguire con facilità il mapping degli utenti o dei gruppi di Azure Active Directory con i ruoli applicazione.
- Controller di esempio `TaskTracker` che illustra come autorizzare ruoli diversi per azioni specifiche nell'applicazione, incluso l'uso standard di `[Authorize]`. 

![](./media/web-sites-dotnet-lob-application-azure-ad/role-management.png)

<a name="bkmk_run" />
## Eseguire l'applicazione di esempio ##

1.	Clonare o scaricare la soluzione di esempio da [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) alla directory locale.

2.	Seguire le istruzioni disponibili in [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md) per configurare il progetto e l'applicazione di Azure Active Directory.

	> [AZURE.NOTE]Le autorizzazioni configurate nell'applicazione di Azure Active Directory richiedono solo il ruolo <strong>Utente</strong>, non il ruolo **Amministratore globale**.
	
3.	Al termine della configurazione dell'applicazione, premere `F5` per eseguirla.

4.	Dopo il caricamento dell'applicazione, fare clic su **Accedi**.

5.	Se l'applicazione Azure Active Directory e le impostazioni corrispondenti in Web.config sono state configurate correttamente, dovrebbe essere visualizzata la schermata di accesso. È sufficiente accedere con l'account usato per creare l'applicazione Azure Active Directory nel portale di Azure, poiché è il proprietario predefinito dell'applicazione Azure Active Directory.
	
	> [AZURE.NOTE]In Startup.Auth.cs del progetto di esempio si noti che l'applicazione include un metodo denominato <code>AddOwnerAdminClaim</code>, che viene usato per aggiungere il proprietario dell'applicazione al ruolo Amministratore. Ciò permette di iniziare immediatamente a gestire i ruoli applicazione nel controller <code>Roles</code>.
	
4.	Dopo l'accesso, fare clic su **Ruoli** per gestire i ruoli applicazione.

5.	In **Cerca utenti e gruppi** iniziare a digitare il nome dell'utente o del gruppo desiderato. Si noterà che l'elenco a discesa mostra un elenco filtrato di utenti e/o gruppi dal tenant di Azure Active Directory.

	![](./media/web-sites-dotnet-lob-application-azure-ad/select-user-group.png)

	> [AZURE.NOTE]In Views\Roles\Index.cshtml, si noterà che la vista usa un oggetto JavaScript denominato <code>AadPicker</code> (definito in Scripts\AadPickerLibrary.js) per accedere all'azione di <code>ricerca</code> nel controller <code>Roles</code>. <pre class="prettyprint">var searchUrl = window.location.protocol + "//" + window.location.host + "<mark>/Roles/Search</mark>"; ... var picker = new <mark>AadPicker(searchUrl, maxResultsPerPage, input, token, tenant)</mark>;</pre> In Controllers\RolesController.cs verrà visualizzata l'azione di <code>ricerca</code>, che invia la richiesta all'API di Azure Active Directory Graph e restituisce la risposta alla pagina. Lo stesso metodo verrà usato successivamente per creare semplici funzionalità nell'applicazione.

6.	Selezionare un utente o un gruppo dall'elenco a discesa, quindi selezionare un ruolo e fare clic su **Assegna ruolo**.

<a name="bkmk_deploy"></a>
## Distribuire l'applicazione di esempio nelle app Web di Servizio app di Azure

In questo scenario si pubblicherà l'applicazione in un'app Web in Servizio app di Azure. In [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/GroupClaims/README.md) sono già disponibili istruzioni per la distribuzione in App Web del servizio app, ma questa procedura annulla anche la configurazione dell'ambiente di debug locale. Verrà quindi illustrato come effettuare la distribuzione mantenendo la configurazione di debug.

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Selezionare **App Web di Microsoft Azure**.

3. Se non è stato effettuato l'accesso ad Azure, fare clic su **Accedi** e usare l'account Microsoft relativo alla sottoscrizione di Azure.

4. Dopo l'accesso, fare clic su **Nuovo** per creare una nuova app Web in Azure.

5. Compilare tutti i campi obbligatori. Sarà necessaria una connessione al database per permettere all'applicazione di archiviare mapping dei ruoli, token memorizzati nella cache ed eventuali dati dell'applicazione.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Fare clic su **Create**. Dopo la creazione dell'app Web, verrà aperta la finestra di dialogo **Pubblica sito Web**.

7. In **URL di destinazione** sostituire **http** con **https**. Copiare l'intero URL in un editor di testo. Verrà usato successivamente. Quindi fare clic su **Avanti**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Deselezionare la casella di controllo **Abilita autenticazione a livello aziendale**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-disable-organizational-authentication.png)

9. Invece di fare clic su **Pubblica** per eseguire la pubblicazione sul Web, fare clic su **Chiudi**. Fare clic su **Sì** per salvare le modifiche apportate al profilo di pubblicazione.

2. Nel [portale di gestione di Azure](https://manage.windowsazure.com) passare al tenant di Azure Active Directory e fare clic sulla scheda **Applicazioni**.

2. Fare clic su **Aggiungi** nella parte inferiore della pagina.

3. Selezionare **Applicazione Web e/o API Web**.

4. Specificare un nome per l'applicazione e quindi fare clic su **Avanti**.

5. In Proprietà dell'app impostare **URL accesso** sull'URL dell'app Web salvato in precedenza (ad esempio `https://<site-name>.azurewebsites.net`) e **URI ID app** su `https://<aad-tenanet-name>/<app-name>`. Fare quindi clic su **Completa**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

6. Al termine della creazione dell'applicazione, fare clic su **Configura**.

7. In **Chiavi** creare una nuova chiave selezionando **1 anno** nell'elenco a discesa.

8. In **Autorizzazioni per altre applicazioni** per la voce **Azure Active Directory** selezionare **Accedere alla directory dell'organizzazione** nell'elenco a discesa **Autorizzazioni delegate**.

	> [AZURE.NOTE]Le autorizzazioni precise necessarie dipendono dalla funzionalità desiderata per l'applicazione. Alcune autorizzazioni richiedono la configurazione del ruolo **Amministratore globale**, ma le autorizzazioni necessarie per questa esercitazione richiedono solo il ruolo **Utente**.

9.  Fare clic su **Save**.

10.  Prima di uscire dalla pagina di configurazione salvata, copiare le informazioni seguenti in un editor di testo.

	-	ID client
	-	Chiave (se si esce dalla pagina, non sarà più possibile visualizzare la chiave)

11. In Visual Studio aprire **Web.Release.config** nel progetto. Inserire il codice XML seguente nel tag `<configuration>` e quindi sostituire il valore di ogni chiave con le informazioni salvate per la nuova applicazione di Azure Active Directory.
	<pre class="prettyprint">
&lt;appSettings>
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
&lt;/appSettings></pre>Assicurarsi che il valore di ida:PostLogoutRedirectUri termini con una barra "/".

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

2. Fare clic su **Pubblica** per eseguire la pubblicazione in App Web del servizio app di Azure.

Al termine, saranno disponibili due applicazioni di Azure Active Directory configurate nel portale di gestione di Azure, una in Visual Studio per l'ambiente di debug e una in Azure per l'app Web pubblicata. Durante il debug, le impostazioni dell'app incluse in Web.config vengono usate per permettere il funzionamento della configurazione **Debug** con Azure Active Directory. Quando viene eseguita la pubblicazione (per impostazione predefinita, la configurazione **Release** viene pubblicata), viene caricato un file Web.config trasformato che incorpora le modifiche alle impostazioni dell'app nel file Web.Release.config.

Se si desidera collegare l'app Web pubblicata al debugger (ad esempio se è necessario caricare i simboli di debug del codice nell'app Web pubblicata), è possibile creare un clone della configurazione Debug per il debug di Azure, ma con una trasformazione Web.config personalizzata (ad esempio Web.AzureDebug.config) che usa le impostazioni di Azure Active Directory disponibili in Web.Release.config. Ciò permette di mantenere una configurazione statica in ambienti diversi.

<a name="bkmk_crud"></a>
## Aggiungere funzionalità line-of-business all'applicazione di esempio

In questa parte dell'esercitazione si apprenderà come implementare le funzionalità line-of-business desiderate nell'applicazione di esempio. Verrà creato un semplice strumento di gestione degli elementi di lavoro CRUD, simile al controller TaskTracker ma con scaffolding e schema progettuale CRUD standard. Verrà anche usato il file Scripts\AadPickerLibrary.js incluso per arricchire l'applicazione con i dati dell'API Graph di Azure Active Directory.

5.	Nella cartella Models creare un nuovo modello denominato WorkItem.cs, quindi sostituire il codice con il codice riportato di seguito:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebAppGroupClaimsDotNet.Models
		{
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
		}

6.	Aprire DAL\GroupClaimContext.cs e aggiungere il codice evidenziato:
	<pre class="prettyprint">
public class GroupClaimContext : DbContext
{
    public GroupClaimContext() : base("GroupClaimContext") { }

    public DbSet&lt;RoleMapping> RoleMappings { get; set; }
    public DbSet&lt;Task> Tasks { get; set; }
    <mark>public DbSet&lt;WorkItem> WorkItems { get; set; }</mark>
    public DbSet&lt;TokenCacheEntry> TokenCacheEntries { get; set; }
}</pre>

7.	Compilare il progetto per rendere accessibile il nuovo modello alla logica di scaffolding in Visual Studio.

8.	Aggiungere il nuovo elemento con scaffolding `WorkItemsController` alla cartella Controllers. A tale scopo, fare clic con il pulsante destro del mouse su **Controllers**, scegliere **Aggiungi** e quindi selezionare **New scaffolded item**.

9.	Selezionare **Controller MVC 5 con visualizzazioni che usano Entity Framework** e quindi fare clic su **Aggiungi**.

10.	Selezionare il modello appena creato e fare clic su **Aggiungi**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Aprire Controllers\WorkItemsController.cs

11. Aggiungere gli effetti [Authorize] evidenziati alle azioni corrispondenti seguenti.
	<pre class="prettyprint">
...

<mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
public class WorkItemsController : Controller
{
	...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public ActionResult Create()
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> Delete(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> DeleteConfirmed(int id)
    ...
}</pre>Poiché i mapping dei ruoli vengono gestiti nel controller Roles, è sufficiente assicurarsi che ogni azione autorizzi i ruoli corretti.

	> [AZURE.NOTE]Si potrebbe osservare l'effetto <code>[ValidateAntiForgeryToken]</code> su alcune azioni. A causa del comportamento descritto da [Brock Allen](https://twitter.com/BrockLAllen) in [MVC 4, AntiForgeryToken e attestazioni](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), la convalida del token antifalsificazione POST HTTP potrebbe avere esito negativo in quanto: + Azure Active Directory non invia il http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, richiesto per impostazione predefinita dal token antifalsificazione. + Se Azure Active Directory prevede la sincronizzazione con la directory di ADFS, per impostazione predefinita il trust ADFS non invia l'attestazione http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, anche se è possibile configurare manualmente ADFS per l'invio di questa attestazione, come illustrato nel passaggio successivo.

12.  In App_Start\Startup.Auth.cs aggiungere la riga di codice seguente nel metodo `ConfigureAuth`:

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` specifica l'attestazione `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, che non viene fornita da Azure Active Directory. Dopo avere definito le autorizzazioni, è possibile passare alla funzionalità effettiva delle azioni.

13.	In Create() ed Edit() aggiungere il codice seguente per rendere disponibili alcune variabili a JavaScript in un secondo momento: ViewData["token"] = GraphHelper.AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value); ViewData["tenant"] = ConfigHelper.Tenant;

14.	In Views\WorkItems\Create.cshtml (un elemento sottoposto automaticamente a scaffolding) individuare il metodo helper `Html.BeginForm` e modificarlo come indicato di seguito:
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
{
    @Html.AntiForgeryToken()

    &lt;div class="form-horizontal">
        &lt;h4>WorkItem&lt;/h4>
        &lt;hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })

        &lt;div class="form-group">
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type="hidden"</mark> } })
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
                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> />
            &lt;/div>
        &lt;/div>
    &lt;/div>

    <mark>&lt;script>
            // People/Group Picker Code
            var maxResultsPerPage = 14;
            var searchUrl = window.location.protocol + "//" + window.location.host + "/Roles/Search";
            var input = document.getElementById("AssignedToName");
            var token = "@ViewData["token"]";
            var tenant = "@ViewData["tenant"]";

            var picker = new AadPicker(searchUrl, maxResultsPerPage, input, token, tenant);

            // Submit the selected user/group to be asssigned.
            $("#submit-button").click({ picker: picker }, function () {
                if (!picker.Selected())
                    return;
                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
            });
    &lt;/script></mark>

}</pre>Nello script l'oggetto AadPicker cerca l'azione `~/Roles/Search` per gli utenti e i gruppi Azure Active Directory corrispondenti all'input. Dopo la selezione del pulsante di invio, l'oggetto AadPicker salva l'ID utente nel campo nascosto `AssignedToID`.

15. È ora possibile eseguire l'app nel debugger di Visual Studio oppure effettuare la pubblicazione nelle app Web di Servizio app di Azure. Accedere come proprietario dell'applicazione e passare a `~/WorkItems/Create`. Per l'app line-of-business pubblicata si passa a `https://mylobapp.azurewebsites.net/WorkItems/Create`. Si osserverà che è possibile usare lo stesso filtro di ricerca AadPicker per selezionare un utente Azure Active Directory.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Completare la compilazione del modulo, quindi fare clic su **Crea**. La pagina ~/WorkItems/Index mostra ora l'elemento di lavoro appena creato. Come è possibile notare nella schermata seguente, la colonna `AssignedToID` è stata rimossa da Views\WorkItems\Index.cshtml.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Apportare ora modifiche analoghe alla visualizzazione **Edit**. In Views\WorkItems\Edit.cshtml apportare al metodo helper `Html.BeginForm` modifiche uguali a quelle illustrate per Views\WorkItems\Create.cshtml nel passaggio precedente, sostituendo "Create" con "Edit" nel codice evidenziato sopra.

L'operazione è terminata.

Dopo avere configurato le autorizzazioni e le funzionalità line-of-business per le diverse azioni del controller WorkItems, è possibile provare ad accedere come utente di diversi ruoli applicazione.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Altre risorse

- [Proteggere l'applicazione con SSL e l'attributo Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Usare Active Directory per l'autenticazione nel servizio app di Azure](web-sites-authentication-authorization.md)
- [Creare un'app Web .NET MVC nel servizio app di Azure con l'autenticazione ADFS](web-sites-dotnet-lob-application-adfs.md)
- [Esempi e documentazione su Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [Blog di Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Eseguire la migrazione di un progetto Web di VS2013 da WIF a Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Nuove connessioni ibride di Azure innovative #hybridCloud](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Analogie tra Active Directory e Azure Active Directory](http://technet.microsoft.com/library/dn518177.aspx)
- [Sincronizzazione della directory con Single Sign-On](http://technet.microsoft.com/library/dn441213.aspx)
- [Token e tipi di attestazioni supportati di Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!---HONumber=62-->