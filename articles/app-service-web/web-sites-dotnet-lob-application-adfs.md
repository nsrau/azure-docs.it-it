<properties 
	pageTitle="Creare un'app line-of-business in Azure con l'autenticazione di AD FS | Microsoft Azure" 
	description="Informazioni su come creare un'app line-of-business nel servizio app di Azure che esegue l'autenticazione con il servizio token di sicurezza locale. In questa esercitazione viene usato ADFS come destinazione del servizio token di sicurezza locale." 
	services="app-service\web" 
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
	ms.date="08/31/2016" 
	ms.author="cephalin"/>

# Creare un'app line-of-business in Azure con l'autenticazione di AD FS

Questo articolo descrive come creare un'applicazione line-of-business ASP.NET MVC nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) usando un'istanza locale di [Active Directory Federation Services](http://technet.microsoft.com/library/hh831502.aspx) come provider di identità. Questo scenario può essere usato quando si vogliono creare applicazioni line-of-business del servizio app di Azure, mentre l'organizzazione richiede che i dati della directory siano archiviati sul posto.

>[AZURE.NOTE] Per una panoramica delle diverse opzioni di autenticazione e autorizzazione aziendali per il servizio app di Azure, vedere [Eseguire l'autenticazione con l'istanza locale di Active Directory nell'app Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Obiettivo di compilazione ##

Si creerà un'applicazione ASP.NET di base nelle app Web di Servizio app di Azure con le seguenti funzionalità:

- Autenticazione degli utenti in ADFS
- Uso di `[Authorize]` per autorizzare gli utenti per diverse azioni
- Configurazione statica per il debug in Visual Studio e pubblicazione nelle app Web di Servizio app di Azure (creazione della configurazione una sola volta, esecuzione del debug e pubblicazione in qualsiasi momento)

<a name="bkmk_need"></a>
## Elementi necessari ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Per completare questa esercitazione sarà necessario quanto segue:

- Distribuzione locale di AD FS. Per una procedura dettagliata end-to-end del lab di test in uso, vedere il post di blog [Test Lab: Standalone STS with AD FS in Azure VM (for test only)](https://blogs.msdn.microsoft.com/cephalin/2014/12/21/test-lab-standalone-sts-with-ad-fs-in-azure-vm-for-test-only/) (Lab di test : Istanza autonoma del servizio token di sicurezza con AD FS in una macchina virtuale di Azure - solo per test)
- Autorizzazioni per creare una o più attendibilità della relying party nel componente di gestione di ADFS
- Visual Studio 2013 Update 4 o versione successiva
- [Azure SDK 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o versioni successive

<a name="bkmk_sample"></a>
## Usare l'applicazione di esempio per il modello line-of-business ##

L'applicazione di esempio in questa esercitazione, [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), è stata creata dal team di Azure Active Directory. Poiché AD FS supporta la specifica WS-Federation, è possibile usarla come modello per creare nuove applicazioni line-of-business in modo semplice. L'applicazione presenta le seguenti funzionalità:

- Uso di [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) per eseguire l'autenticazione con una distribuzione di ADFS locale
- Funzionalità di accesso e di disconnessione
- Uso di [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (invece di Windows Identity Foundation), che rappresenta il futuro di ASP.NET e offre una configurazione molto più semplice per l'autenticazione e l'autorizzazione rispetto a WIF

<a name="bkmk_setup"></a>
## Configurare l'applicazione di esempio ##

2.	Clonare o scaricare la soluzione di esempio da [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) nella directory locale.

	> [AZURE.NOTE] Le istruzioni disponibili in [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) illustrano come configurare l'applicazione con Azure Active Directory. Tuttavia, in questa esercitazione la configurazione viene eseguita senza AD FS, quindi seguire i passaggi descritti qui.

3.	Aprire la soluzione e quindi aprire Controllers\\AccountController.cs in **Esplora soluzioni**.

	Si noterà che il codice genera semplicemente una richiesta di autenticazione per l'utente mediante WS-Federation. Tutte le autenticazioni sono configurate in App\_Start\\Startup.Auth.cs.

4.  Aprire App\_Start\\Startup.Auth.cs. Nel metodo `ConfigureAuth` notare la riga seguente:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	In ambito OWIN questo frammento di codice è il minimo indispensabile per configurare l'autenticazione con WS-Federation. Si tratta di un metodo più semplice ed elegante rispetto a WIF, in cui XML viene inserito in Web.config un po' ovunque. Le uniche informazioni realmente necessarie sono l'identificatore della relying party e l'URL del file di metadati del servizio ADFS. Ad esempio:

	-	Identificatore della relying party: `https://contoso.com/MyLOBApp`
	-	Indirizzo dei metadati: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	In App\_Start\\Startup.Auth.cs modificare le definizioni delle stringhe statiche seguenti:
	<pre class="prettyprint">
	private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
	<mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
	<mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
	<mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
	<mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>
	
	<mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
	</pre>

6.	Ora apportare le modifiche corrispondenti nel file Web.config. Aprire Web.config e modificare le impostazioni dell'app seguenti:
	<pre class="prettyprint">
	&lt;appSettings>
	  &lt;add key="webpages:Version" value="3.0.0.0" />
	  &lt;add key="webpages:Enabled" value="false" />
	  &lt;add key="ClientValidationEnabled" value="true" />
	  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" />
	  <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /></del></mark>
	  <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /></del></mark>
	  <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /></del></mark>
	  <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /></mark>
	  <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /></mark>
	
	&lt;/appSettings>
	</pre>

	Compilare i valori delle chiavi in base a quanto previsto per l'ambiente in uso.

7.	Compilare l'applicazione e verificare che non siano presenti errori.

È tutto. Ora l'applicazione di esempio è pronta per l'uso con ADFS. In seguito sarà comunque necessario configurare un trust della relying party con questa applicazione in AD FS.

<a name="bkmk_deploy"></a>
## Distribuire l'applicazione di esempio nelle app Web di Servizio app di Azure

Qui si pubblicherà l'applicazione in un'app Web nelle app Web del servizio app di Azure mantenendo l'ambiente di debug. Si noti che si pubblicherà l'applicazione prima che questa disponga di un'attendibilità della relying party con ADFS, pertanto l'autenticazione non sarà ancora funzionante. Se tuttavia si esegue questa operazione adesso, è possibile ottenere l'URL dell'app Web da usare per la configurazione del trust della relying party in un secondo tempo.

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Selezionare **Servizio app di Microsoft Azure**.
3. Se non è stato eseguito l'accesso ad Azure, fare clic su **Accedi** e usare l'account Microsoft relativo alla sottoscrizione di Azure per accedere.
4. Dopo l'accesso, fare clic su **Nuovo** per creare un'app Web.
5. Compilare tutti i campi obbligatori. Poiché in un secondo tempo si stabilirà la connessione ai dati locali, non occorre creare un database per questa app Web.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Fare clic su **Crea**. Dopo la creazione dell'app Web, verrà aperta la finestra di dialogo Pubblica sito Web.
7. In **URL di destinazione** sostituire **http** con **https**. Copiare l'intero URL in un editor di testo per un uso successivo. Fare quindi clic su **Pubblica**.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. In Visual Studio aprire **Web.Release.config** nel progetto. Inserire il seguente codice XML nel tag `<configuration>` e sostituire il valore della chiave con l'URL dell'app Web di pubblicazione.
	<pre class="prettyprint">
	&lt;appSettings>
	   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
	&lt;/appSettings></pre>

Al termine, saranno disponibili due identificatori della relying party configurati nel progetto, uno per l'ambiente di debug in Visual Studio e uno per l'app Web pubblicata in Azure. Si procederà all'impostazione di un'attendibilità della relying Party per ciascuno dei due ambienti in ADFS. Durante il debug le impostazioni dell'app in Web.config vengono usate per consentire il funzionamento della configurazione di **Debug** con AD FS. In caso di pubblicazione (per impostazione predefinita viene pubblicata la configurazione di **Release**), viene caricato un file Web.config trasformato che incorpora le modifiche alle impostazioni dell'app nel file Web.Release.config.

Se si desidera collegare l'app Web pubblicata di Azure al debugger (ad esempio se è necessario caricare i simboli di debug del codice nell'app Web pubblicata), è possibile creare un clone della configurazione Debug per il debug di Azure, ma con una trasformazione Web.config personalizzata (ad esempio Web.AzureDebug.config) che usa le impostazioni dell'app disponibili in Web.Release.config. Ciò permette di mantenere una configurazione statica in ambienti diversi.

<a name="bkmk_rptrusts"></a>
## Configurare l'attendibilità della relying party nel componente di gestione di ADFS ##

Prima di poter usare l'applicazione di esempio e autenticarla effettivamente con AD FS, è necessario configurare un trust della relying party in Gestione AD FS. Sarà necessario impostare due attendibilità della relying party separate, una per l'ambiente di debug e una per l'app Web pubblicata.

> [AZURE.NOTE] Assicurarsi di ripetere i passaggi seguenti per entrambi gli ambienti.

4.	Accedere al server ADFS con le credenziali dotate di diritti di gestione per ADFS.
5.	Aprire il componente di gestione di ADFS. Fare clic con il pulsante destro del mouse su **AD FS\\Trusted Relationships\\Relying Party Trusts** e selezionare **Add Relying Party Trust**.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	Nella pagina **Select Data Source** selezionare **Enter data about the relying party manually**.

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	Nella pagina **Specify Display Name** digitare un nome visualizzato per l'applicazione e fare clic su **Next**.
7.	Nella pagina **Choose Protocol** fare clic su **Next**.
8.	Nella pagina **Configure Certificate** fare clic su **Next**.

	> [AZURE.NOTE] Poiché dovrebbe essere già in uso HTTPS, i token crittografati sono facoltativi. Se si vogliono comunque crittografare i token di ADFS in questa pagina, è anche necessario aggiungere della logica di decrittografia di token nel codice. Per altre informazioni, vedere il post sulla [configurazione manuale di middleware WS-Federation OWIN e sull'accettazione di token crittografati](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
  
5.	Prima di passare al prossimo passaggio, è necessario ottenere delle informazioni dal progetto di Visual Studio. Nelle proprietà del progetto prendere nota dell'**URL SSL** dell'applicazione.

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	Nuovamente nel componente di gestione di ADFS, nella pagina **Configure URL** della procedura guidata per l'**aggiunta di un'attendibilità della relying party**, selezionare **Enable support for the WS-Federation Passive protocol** e digitare l'URL SSL del progetto di Visual Studio di cui si è preso nota nel passaggio precedente. Quindi fare clic su **Next**.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE] L'URL specifica dove inviare il client dopo la riuscita dell'autenticazione. Per l'ambiente di debug, l'URL dovrebbe essere <code>https://localhost:&lt;port&gt;/</code>. Per l'app Web pubblicata, deve corrispondere all'URL dell'app Web.

7.	Nella pagina **Configure Identifiers** verificare che l'URL SSL del progetto sia già elencato, quindi fare clic su **Next**. Fare clic su **Next** fino al termine della procedura guidata, accettando le impostazioni predefinite.

	> [AZURE.NOTE] In App\_Start\\Startup.Auth.cs del progetto di Visual Studio questo identificatore viene confrontato con il valore di <code>WsFederationAuthenticationOptions.Wtrealm</code> durante l'autenticazione federata. Per impostazione predefinita, l'URL dell'applicazione nel passaggio precedente viene aggiunto come identificatore della relying party.

8.	A questo punto la configurazione dell'applicazione relying party per il progetto in ADFS è completa. Si procederà quindi alla configurazione di questa applicazione per l'invio delle attestazioni richieste dalla propria applicazione. La finestra di dialogo **Edit Claim Rules** si apre per impostazione predefinita al termine della procedura guidata e sarà pertanto possibile iniziare immediatamente. Configurare almeno le attestazioni seguenti (con gli schemi tra parentesi):

	-	Nome (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name): usata da ASP.NET per attivare `User.Identity.Name`.
	-	Nome dell'entità utente (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn): usata per identificare in modo univoco gli utenti nell'organizzazione.
	-	Appartenenze a gruppi come ruoli (http://schemas.microsoft.com/ws/2008/06/identity/claims/role): può essere usata con la decoration `[Authorize(Roles="role1, role2,...")]` per autorizzare controller/azioni. In realtà, questo approccio potrebbe non essere il più efficiente per l'autorizzazione dei ruoli. Se gli utenti di Active Directory appartengono a centinaia di gruppi di sicurezza, diventano centinaia di attestazioni di ruolo nel token SAML. Un approccio alternativo consiste nell'inviare una singola attestazione di ruolo in modo condizionale in base all'appartenenza dell'utente a un determinato gruppo. Tuttavia, ai fini della presente esercitazione si manterrà una configurazione semplice.
	-	ID nome (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier): può essere usata per la convalida antifalsificazione. Per altre informazioni sul funzionamento con la convalida antifalsificazione, vedere la sezione **Aggiungere funzionalità line-of-business** dell'articolo [Creare un'app line-of-business in Azure con l'autenticazione di Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).

	> [AZURE.NOTE] I tipi di attestazione da configurare per la propria applicazione sono determinati dai requisiti dell'applicazione stessa. Per un elenco delle attestazioni supportate dalle applicazioni Azure Active Directory (ovvero le attendibilità della relying party), vedere ad esempio [Token e tipi di attestazioni supportati](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.	Nella finestra di dialogo Edit Claim Rules fare clic su **Add Rule**.
9.	Configurare il nome, l'UPN e le attestazioni di ruolo come illustrato nello screenshot seguente e quindi fare clic su **Fine**.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	In seguito si creerà un'attestazione ID nome temporanea usando i passaggi descritti nel post di blog [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx) (Identificatori di nome nelle asserzioni SAML).

9.	Fare nuovamente clic su **Add Rule**.
10.	Selezionare **Send Claims Using a Custom Rule** e fare clic su **Next**.
11.	Incollare il seguente linguaggio di regola nella casella **Custom rule**, assegnare il nome **Per Session Identifier** alla regola e fare clic su **Finish**.
	<pre class="prettyprint">
	c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
		=> add(
			store = "_OpaqueIdStore",
			types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
			query = "{0};{1};{2};{3};{4}",
			param = "useEntropy",
			param = c1.Value,
			param = c1.OriginalIssuer,
			param = "",
			param = c2.Value);
	</pre>

	La regola personalizzata sarà simile allo screenshot seguente:

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	Fare nuovamente clic su **Add Rule**.
10.	Selezionare **Transform an Incoming Claim** e fare clic su **Next**.
11.	Configurare la regola come illustrato nello screenshot seguente, usando il tipo di attestazione creato nella regola personalizzata e fare clic su **Fine**.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	Per informazioni dettagliate sui passaggi da eseguire per l'attestazione ID nome temporanea, vedere il post di blog [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx) (Identificatori di nome nelle asserzioni SAML).

12.	Fare clic su **Apply** nella finestra di dialogo **Edit Claim Rules**, che ora sarà simile allo screenshot seguente:

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE] Assicurarsi di ripetere questi passaggi sia per l'ambiente di debug che per l'app Web pubblicata.

<a name="bkmk_test"></a>
## Testare l'autenticazione federata per la propria applicazione

Si è ora pronti per testare la logica di autenticazione dell'applicazione in ADFS. Nell'ambiente di laboratorio usato in questo esempio è presente un utente test che appartiene a un gruppo di test in Active Directory.

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Per testare l'autenticazione nel debugger, sarà sufficiente premere `F5`. Se si desidera testare l'autenticazione nell'app Web pubblicata, passare all'URL dell'app.

Dopo il caricamento dell'applicazione Web, fare clic su **Accedi**. Dovrebbe ora venire visualizzata una finestra di dialogo di accesso o la pagina di accesso presentata da ADFS, a seconda del metodo di autenticazione scelto da ADFS. Di seguito è riportato ciò che viene visualizzato in Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

Dopo l'accesso come utente nel dominio di AD della distribuzione di AD FS, verrà visualizzata di nuovo la home page con **Hello, <Nome utente>!,** nell'angolo. Questo è quanto viene visualizzato nell'ambiente di questo esempio.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Finora, sono stati raggiunti gli obiettivi seguenti:

- L'applicazione ha raggiunto correttamente ADFS e il corrispondente identificatore della relying party è presente nel database di ADFS
- ADFS ha correttamente autenticato un utente AD e ha reindirizzato l'utente alla home page dell'applicazione
- ADFS ha correttamente inviato l'attestazione basata su nome (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) all'applicazione, come indicato dal fatto che il nome utente viene visualizzato in alto a destra.

In caso di assenza dell'attestazione basata su nome, verrebbe visualizzato solo **Hello, !**. Se si esamina il file Views\\Shared\\_LoginPartial.cshtml, si noterà che viene usato `User.Identity.Name` per visualizzare il nome utente. Come già accennato, ASP.NET attiva questa proprietà con l'attestazione nome dell'utente autenticato, se questa è disponibile nel token SAML. Per visualizzare tutte le attestazioni inviate da ADFS, inserire un punto di interruzione in Controllers\\HomeController.cs, nel metodo di azione Index. Dopo l'autenticazione dell'utente, controllare la raccolta `System.Security.Claims.Current.Claims`.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png)

<a name="bkmk_authorize"></a>
## Autorizzare utenti per controller o azioni specifiche

Poiché sono state incluse le appartenenze a gruppi come attestazioni di tipo ruolo nella configurazione del trust della relying party, è possibile usarle direttamente nell'effetto `[Authorize(Roles="...")]` per controller e azioni. In un'applicazione line-of-business in cui si usa il modello Create-Read-Update-Delete (CRUD) è possibile autorizzare ruoli specifici per accedere a ogni azione. Per il momento, si sperimenterà questa funzionalità nel controller Home esistente.

1. Aprire Controllers\\HomeController.cs.
2. Assegnare i metodi di azione `About` e `Contact` in modo simile al codice seguente, usando le appartenenze ai gruppi di sicurezza disponibili per l'utente autenticato.
	<pre class="prettyprint">
	<mark>[Authorize(Roles="Test Group")]</mark>
	public ActionResult About()
	{
	    ViewBag.Message = "Your application description page.";
	
	    return View();
	}
	
	<mark>[Authorize(Roles="Domain Admins")]</mark>
	public ActionResult Contact()
	{
	    ViewBag.Message = "Your contact page.";
	
	    return View();
	}
	</pre>

	Poiché nell'ambiente lab ADFS è stato aggiunto **Test User** a **Test Group**, si userà Test Group per il test dell'autorizzazione in `About`. Per `Contact`, si testerà il caso negativo di **Domain Admins**, a cui **Test User** non appartiene.

3. Avviare il debugger premendo `F5` e accedere, quindi fare clic su **About**. Dovrebbe ora essere possibile visualizzare la pagina `~/About/Index`, se l'utente autenticato è autorizzato per tale azione.
4. Ora fare clic su **Contact**, che nel caso di questo esempio non autorizza **Test User** per l'azione. Tuttavia, il browser viene reindirizzato ad ADFS, che visualizzerà un messaggio analogo al seguente:

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	Se si esamina l'errore nel Visualizzatore eventi del server AD FS, verrà visualizzato questo messaggio di eccezione:
	<pre class="prettyprint">
	Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.
	   in Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
	   in Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
	   in Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
	   in Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
	</pre>

	Il motivo di questo errore è che, per impostazione predefinita, MVC restituisce un codice 401 Unauthorized quando i ruoli di un utente non sono autorizzati. Questo attiva una richiesta di ri-autenticazione al provider di identità (ovvero ADFS). Poiché l'utente è già autenticato, ADFS torna alla stessa pagina, che a sua volta invia un nuovo codice 401, creando un ciclo di reindirizzamento. Verrà eseguito l'override del metodo `HandleUnauthorizedRequest` di AuthorizeAttribute con una logica semplice per visualizzare qualcosa di sensato anziché continuare il ciclo di reindirizzamento.

5. Creare un file denominato AuthorizeAttribute.cs nel progetto e incollare il codice seguente nel file.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	Il codice di override invia un messaggio HTTP 403 (accesso negato) invece di HTTP 401 (non autorizzato) per i casi autenticati ma non autorizzati.

6. Eseguire nuovamente il debug con `F5`. Facendo clic su **Contact** verrà visualizzato un messaggio di errore più informativo (anche se graficamente meno elegante):

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Pubblicare nuovamente l'applicazione nelle app Web di Servizio app di Azure, quindi testare il comportamento dell'applicazione attiva.

<a name="bkmk_data"></a>
## Connettersi ai dati locali

Un motivo per cui si potrebbe voler implementare la propria applicazione line-of-business in ADFS anziché in Azure Active Directory potrebbe essere correlato a questioni di conformità nella conservazione dei dati dell'organizzazione all'esterno dell'organizzazione stessa. Questo può anche significare che il sito Web di Azure deve accedere a database locali, poiché non si è autorizzati a usare il [database SQL](/services/sql-database/) come livello dati per le app Web.

App Web del servizio app di Azure supporta l'accesso ai database locali mediante due approcci: [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [reti virtuali](web-sites-integrate-with-vnet.md). Per altre informazioni, vedere il post relativo all'[uso dell'integrazione con una rete virtuale e delle connessioni ibride con App Web del servizio app di Azure](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>
## Altre risorse

- [Proteggere l'applicazione con SSL e l'attributo Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Eseguire l'autenticazione con l'istanza locale di Active Directory nell'app Azure](web-sites-authentication-authorization.md)
- [Creare un'app line-of-business in Azure con l'autenticazione di Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md)
- [Usare l'opzione di autenticazione dell'organizzazione locale (ADFS) con ASP.NET in Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Eseguire la migrazione di un progetto Web di VS2013 da WIF a Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Informazioni generali su Active Directory Federation Services](http://technet.microsoft.com/library/hh831502.aspx)
- [Specifica WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

<!---HONumber=AcomDC_0831_2016-->