<properties  linkid="dev-net-how-to-access-control" urlDisplayName="Access Control" pageTitle="How to use Access Control (.NET) - Azure feature guide" metaKeywords="Azure Access Control Service authentication C#" description="Learn how to use Access Control Service (ACS) in your Azure application to authenticate users when they try to gain access to a web app." metaCanonical="" services="active-directory" documentationCenter=".NET" title="How to Authenticate Web Users with Azure Active Directory Access Control" authors="juneb" solutions="" manager="" editor="" />

# Come autenticare gli utenti Web con Microsoft Azure AD Access Control

In questa guida vengono fornite informazioni sull'utilizzo di Microsoft Azure AD Access Control (noto anche come Servizio di controllo di accesso o ACS) per l'autenticazione degli utenti che tentano di accedere a un'applicazione Web da provider di identità quali Microsoft, Google, Yahoo e Facebook.

<h2><span  class="short-header">Sommario</span>Sommario</h2>


* [Informazioni su ACS](#what-is)
* [Concetti](#concepts)
* [Prerequisiti](#pre)
* [Creazione di uno spazio dei nomi ACS](#create-namespace)
* [Creazione di un'applicazione ASP.NET MVC](#create-web-app)
* [Integrazione dell'applicazione Web con ACS](#Identity-Access)
* [Test dell'integrazione con ACS](#Test-ACS)
* [Visualizzazione delle attestazioni inviate da ACS](#bkmk_viewClaims)
* [Visualizzazione dell'applicazione nel portale di gestione
  ACS](#bkmk_VP)
* [Aggiunta di un provider di identità](#add-IP)
* [Passaggi successivi](#whats-next)

<h2><span  class="short-header">Informazioni su ACS</span>Informazioni su ACS</h2>


La maggior parte degli sviluppatori non ha esperienza nell'ambito delle identità e non desidera dedicare tempo a sviluppare meccanismi di autenticazione e autorizzazione per le applicazioni e i servizi. ACS è un servizio di Azure che consente di autenticare in modo semplice gli utenti per l'accesso ai servizi e alle applicazioni Web senza bisogno di aggiungere al codice logica di autenticazione complessa.

In ACS sono disponibili le funzionalità seguenti:

* Integrazione con Windows Identity Foundation (WIF).
* Supporto per i provider di identità Web più diffusi, tra cui account
  Microsoft (precedentemente noti come Windows Live ID), Google, Yahoo e
  Facebook.
* Supporto per Active Directory Federation Services (ADFS) 2.0.
* Un servizio di gestione basato sul protocollo OData che offre accesso
  programmatico alle impostazioni di ACS.
* Un portale di gestione che consente l'accesso amministrativo alle
  impostazioni di ACS.

Per ulteriori informazioni su ACS, vedere [Servizio di controllo di accesso 2.0][1].

<h2><span  class="short-header">Concetti</span>Concetti</h2>


ACS utilizza l'identità basata sulle attestazioni, un approccio coerente alla creazione di meccanismi di autenticazione per le applicazioni in esecuzione locale o nel cloud. L'identità basata sulle attestazioni offre un metodo comune che le applicazioni e i servizi possono utilizzare per ottenere le informazioni di identità necessarie sugli utenti interni o esterni all'organizzazione oppure su Internet.

Per completare le attività in questa guida è necessario comprendere i termini e i concetti illustrati di seguito:

**Client** - Un browser che tenta di ottenere accesso a un'applicazione Web.

**Applicazione relying party (RP)** - L'app Web. Un'applicazione relying party è un sito Web o un servizio che affida l'autenticazione a un'autorità esterna. In gergo tecnico, si dice che considera attendibile tale autorità. In questa guida viene illustrato come configurare un'applicazione per considerare attendibile ACS.

**Token** - Un utente ottiene accesso a un'applicazione relying partympresentando un token valido, emesso da un'autorità che l'applicazione RP considera attendibile. Una raccolta di dati sulla sicurezza emessa quando un client viene autenticato. Contiene un set di attestazioni, ovvero di attributi dell'utente autenticato, ad esempio nome o età o un identificatore per un ruolo utente. I token sono firmati digitalmente, in modo che sia possibile identificare l'autorità emittente, ma non modificare i contenuti.

**Provider di identità (IP)** - Un'autorità che autentica le identità utente ed emette token di sicurezza, ad esempio un account Microsoft (Windows Live ID), Facebook, Google, Twitter e Active Directory. Quando ACS è configurato in modo da considerare attendibile un provider di identità, accetta e convalida i token emessi da tale provider. Poiché ACS può considerare attendibili più provider di identità contemporaneamente, quando un'applicazione considera attendibile ACS può offrire agli utenti la possibilità di eseguire l'autenticazione tramite uno qualsiasi degli IP considerati attendibili da ACS.

**Provider di federazione (FP)** - I provider di identità (IP) hanno una conoscenza diretta degli utenti, li autenticano utilizzando le relative credenziali ed emettono attestazioni sugli utenti. Un provider di federazione è un'autorità di tipo diverso. Anziché autenticare direttamente gli utenti, un provider di federazione funge da broker di autenticazione, ovvero agisce come intermediario tra un'applicazione relying party e uno o più provider di identità. ACS è un provider di federazione.

**Motore regole ACS** - Le regole di trasformazione delle attestazioni convertono le attestazioni in token dai provider di identità attendibili, in modo che possano essere utilizzate da un'applicazione relying party. ACS include un motore regole che applica le regole di trasformazione delle attestazioni specificate per l'applicazione relying party.

**Spazio dei nomi ACS** - Fornisce un ambito univoco per fare riferimento alle risorse di ACS all'interno dell'applicazione. Lo spazio dei nomi contiene una serie di impostazioni, ad esempio il provider di identità considerato attendibile, le applicazioni RP da servire e le regole da applicare ai token in ingresso. Visualizza inoltre gli endpoint utilizzati dall'applicazione e dallo sviluppatore per comunicare con ACS.

Nella figura seguente viene illustrato il funzionamento dell'autenticazione ACS con un'applicazione Web:

![](./media/active-directory-dotnet-how-to-use-access-control/acs-01.png)

1.  Il client, in questo caso un browser, richiede una pagina
    dall'applicazione relying party.
2.  Poiché la richiesta non è stata ancora autenticata, l'applicazione
    RP reindirizza l'utente all'autorità che considera attendibile,
    ovvero ACS. ACS presenta all'utente l'elenco dei provider di
    identità specificati per questa applicazione RP. L'utente seleziona
    il provider di identità appropriato.
3.  Il client passa alla pagina di autenticazione del provider di
    identità e chiede all'utente di eseguire l'accesso.
4.  Dopo l'autenticazione del client, ad esempio dopo l'immissione
    delle credenziali di identità, il provider di identità emette un
    token di sicurezza.
5.  Dopo l'emissione del token di sicurezza, il provider di identità
    indica al client di inviarlo ad ACS.
6.  ACS convalida il token di sicurezza emesso dal provider di identità,
    inserisce le attestazioni di identità contenute nel motore regole
    ACS, calcola le attestazioni di identità di output ed emette un
    nuovo token di sicurezza che contiene tali attestazioni di identità
    di output.
7.  ACS indica al client di inviare il token di sicurezza emesso da ACS
    all'applicazione RP. L'applicazione convalida la firma nel token
    di sicurezza, estrae le attestazioni per l'utilizzo da parte della
    logica di business dell'applicazione e restituisce la pagina
    richiesta.

<h2><span  class="short-header">Prerequisiti</span>Prerequisiti</h2>


Per completare le attività in questa guida è necessario quanto segue:

* Sottoscrizione di Azure
* Microsoft Visual Studio 2012
* Strumento di gestione delle identità e degli accessi per Visual Studio
  2012 (per il download, vedere [Identity and Access Tool][2])

<h2><span  class="short-header">Creazione di uno spazio dei nomi ACS</span>Creazione di uno spazio dei nomi ACS</h2>


Per utilizzare Microsoft Azure AD Access Control, creare uno spazio dei nomi ACS. Lo spazio dei nomi fornisce un ambito univoco per fare riferimento alle risorse di ACS all'interno dell'applicazione.

1.  Accedere al [portale di gestione di Azure][3] (https://manage.WindowsAzure.com).

2.  Fare clic su **Active Directory**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png)

3.  Per creare un nuovo spazio dei nomi ACS, fare clic su **New**. Verranno selezionati **App Services** e **Access Control**. Fare clic su **Quick Create**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png)

4.  Immettere un nome per lo spazio dei nomi. Azure verificherà che il nome sia univoco.

5.  Selezionare l'area in cui viene utilizzato lo spazio dei nomi. Per prestazioni ottimali, utilizzare l'area in cui si sta distribuendo l'applicazione e quindi fare clic su **Create**.

Azure creerà e attiverà lo spazio dei nomi.

<h2><span  class="short-header">Creazione di un'applicazione ASP.NET MVC</span>Creazione di un'applicazione ASP.NET MVC</h2>


In questo passaggio verrà creata un'applicazione ASP.NET MVC. Nei passaggi successiva, questa semplice applicazione Web Forms verrà integrata con ACS.

1.  Avviare Visual Studio 2012 o Visual Studio Express per il Web 2012. Le versioni precedenti non funzioneranno con questa esercitazione.
2.  Fare clic su **File** e quindi su **Nuovo progetto**. 3.  Selezionare il modello Visual C#/Web e quindi selezionare **Applicazione Web ASP.NET MVC 4**.
    
    In questa guida verrà utilizzata un'applicazione MVC, ma per questa
    attività è possibile utilizzare qualsiasi tipo di applicazione Web.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png)

4.  In **Nome** digitare **MvcACS** e quindi fare clic su **OK**.
5.  Nella finestra di dialogo successiva selezionare **Applicazione Internet** e quindi fare clic su **OK**.
6.  Modificare il file *Views\\Shared\_LoginPartial.cshtml* e sostituire il contenuto con il codice seguente:
    
        @if (Request.IsAuthenticated)
        {
            string name = "Null ID";
            if (!String.IsNullOrEmpty(User.Identity.Name))
            {
                name = User.Identity.Name;
            }    
            <text>
            Hello, @Html.ActionLink(name, "Manage", "Account", routeValues: null, htmlAttributes: new { @class = "username", title = "Manage" })!
                    @using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm" }))
                    {
                        @Html.AntiForgeryToken()
                        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
                    }
            </text>
        }
        else
        {
            <ul>
                <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
                <li>@Html.ActionLink("Log in", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }

Attualmente User.Identity.Name non viene impostato in ACS, pertanto è necessario apportare la modifica precedente.

1.  Premere F5 per eseguire l'applicazione. L'applicazione ASP.NET MVC predefinita verrà visualizzata nel Web browser.

<h2><span  class="short-header">Integrazione dell'applicazione Web con ACS</span>Integrazione dell'applicazione Web con ACS</h2>


In questa attività l'applicazione Web ASP.NET verrà integrata con ACS.

1.  In Esplora soluzioni fare clic sul progetto MvcACS e quindi selezionare **Identity and Access**.
    
    Se l'opzione **Identity and Access** non viene visualizzata nel menu contestuale installare lo strumento di gestione delle identità e degli accessi. Per informazioni, vedere [Identity and Access Tool][2].
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/rzIA.png)

2.  Nella scheda **Providers** selezionare **Use the Azure Access Control Service**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/rzPT.png)

3.  Fare clic sul collegamento **Configure**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/rzC.png)
    
    Visual Studio richiederà informazioni sullo spazio dei nomi ACS. Immettere lo spazio dei nomi creato in precedenza. In queste immagini è denominato Test, ma lo spazio dei nomi dell'utente avrà un nome diverso. Tornare al portale di gestione di Azure per ottenere la chiave simmetrica.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png)

4.  Nel portale di gestione di Azure fare clic sullo spazio dei nomi ACS e quindi fare clic su **Manage**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png)

5.  Fare clic su **Management Service** e quindi su **Management Client**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png)

6.  Fare clic su **Symmetric Key**, fare clic su **Show Key** e quindi copiare il valore della chiave. Fare clic su **Cancel** per uscire dalla pagina di modifica del client di gestione senza apportare modifiche.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png)

7.  In Visual Studio incollare la chiave nel campo **Enter the Management Key for the namespace**, fare clic su **Save management key** e quindi fare clic su **OK**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png)
    
    Visual Studio utilizza le informazioni sullo spazio dei nomi per connettersi al portale di gestione ACS e ottenere le impostazioni per lo spazio dei nomi, tra cui i provider di identità, l'area di autenticazione e l'URL restituito.

8.  Selezionare **Windows Live ID** (account Microsoft) e fare clic su OK.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png)

<h2><span  class="short-header">Test dell'integrazione con ACS</span>Test dell'integrazione con ACS</h2>


In questa attività viene illustrato come testare l'integrazione dell'applicazione relying party con ACS.

* Premere F5 in Visual Studio per eseguire l'app.

Quando l'applicazione è integrata con ACS e viene selezionato Windows Live ID (account Microsoft), anziché aprire l'applicazione Web Forms ASP.NET predefinita, il browser viene reindirizzato alla pagina di accesso per gli account Microsoft. Accedendo con un nome utente e una password validi, l'utente viene reindirizzato all'applicazione MvcACS.

![](./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png)

A questo punto ACS è stato correttamente integrato con l'applicazione Web ASP.NET. ACS sta gestendo l'autenticazione degli utenti utilizzando le credenziali degli account Microsoft.

<h2><a name="bkmk_viewClaims"></a>Visualizzazione delle attestazioni inviate da ACS</h2>


In questa sezione l'applicazione verrà modificata in modo da visualizzare le attestazioni inviate da ACS. Lo strumento di gestione delle identità e degli accessi ha creato un gruppo di regole che trasmette tutte le attestazioni provenienti dal provider di identità all'applicazione. Si noti che provider di identità diversi inviano attestazioni diverse.

1.  Aprire il file *Controllers\\HomeController.cs*. Aggiungere un'istruzione **using** per **System.Threading**:

 	using System.Threading; 

1. Nella classe HomeController aggiungere il metodo *Claims*:
    
    public ActionResult Claims() \{ ViewBag.Message = "Your claims
    page.";
    
         ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;
        
         return View();
   		 }

2.  Fare clic con il pulsante destro del mouse sul metodo *Claims* e selezionare **Aggiungi visualizzazione**.

	![](./media/active-directory-dotnet-how-to-use-access-control/rzAv.png)

1.  Fare clic su **Aggiungi**.

2.  Sostituire il contenuto del file *Views\\Home\\Claims.cshtml* con il codice seguente:
    
        @{
            ViewBag.Title = "Claims";
        }
        <hgroup class="title">
            <h1>@ViewBag.Title.</h1>
            <h2>@ViewBag.Message</h2>
        </hgroup>
        <h3>Values from Identity</h3>
        <table>
            <tr>
                <td>
                    IsAuthenticated: 
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.IsAuthenticated 
                </td>
            </tr>
            <tr>
                <td>
                    Name: 
                </td>        
                <td>
                    @ViewBag.ClaimsIdentity.Name
                </td>        
            </tr>
        </table>
        <h3>Claims from ClaimsIdentity</h3>
        <table>
            <tr>
                <th>
                    Claim Type
                </th>
                <th>
                    Claim Value
                </th>
            </tr>
                @foreach (System.Security.Claims.Claim claim in ViewBag.ClaimsIdentity.Claims ) {
            <tr>
                <td>
                    @claim.Type
                </td>
                <td>
                    @claim.Value
                </td>
            </tr>
        }
        </table>

3.  Eseguire l'applicazione e passare al metodo *Claims*:

![](./media/active-directory-dotnet-how-to-use-access-control/rzCl.png)

Per ulteriori informazioni sull'utilizzo delle attestazioni nell'applicazione, vedere la [documentazione di Windows Identity Foundation][4].

<h2><a name="bkmk_VP"></a>Visualizzazione dell'applicazione nel portale di gestione ACS</h2>


Lo strumento di gestione delle identità e degli accessi in Visual Studio integra automaticamente l'applicazione con ACS.

Quando si seleziona l'opzione per l'utilizzo del Servizio di controllo di accesso di Azure, lo strumento aggiunge l'applicazione come relying party, la configura per l'utilizzo dei provider di identità selezionati, quindi genera e seleziona le regole predefinite di trasformazione delle attestazioni per l'applicazione.

È possibile rivedere e modificare queste impostazioni di configurazione nel portale di gestione ACS. Eseguire la procedura seguente per rivedere le modifiche nel portale.

1.  Accedere al [portale di gestione di Azure][3].

2.  Fare clic su **Active Directory**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png)

3.  Selezionare uno spazio dei nomi ACS e fare clic su **Manage**. Verrà visualizzato il portale di gestione ACS.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png)

4.  Fare clic su **Relying party applications**.
    
    La nuova applicazione MvcACS verrà visualizzata nell'elenco delle applicazioni relying party. L'area di autenticazione verrà impostata automaticamente sulla pagina principale dell'applicazione.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png)

5.  Fare clic su **MvcACS**.
    
    La pagina di modifica dell'applicazione relying party contiene le impostazioni di configurazione dell'applicazione Web MvcACS. Quando si modificano le impostazioni in questa pagina e si salva, le modifiche vengono applicate immediatamente all'applicazione.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png)

6.  Scorrere in basso nella pagina per visualizzare le altre impostazioni di configurazione per l'applicazione MvcACS, compresi i provider di identità e le regole di trasformazione delle attestazioni.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png)

Nella sezione successiva le funzionalità del portale di gestione ACS verranno utilizzate per apportare una modifica all'applicazione Web, un'operazione estremamente semplice.

<h2><span  class="short-header">Aggiunta di un provider di identità</span>Aggiunta di un provider di identità</h2>


Attraverso il portale di gestione ACS è possibile modificare l'autenticazione dell'applicazione MvcACS. In questo esempio si aggiungerà Google come provider di identità per MvcACS.

1.  Fare clic su **Identity providers** nel menu di navigazione e quindi fare clic su **Add**.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png)

2.  Fare clic su **Google** e quindi su **Next**. La casella di controllo dell'app MvcACS è selezionata per impostazione predefinita.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png)

3.  Fare clic su Save.
    
    ![](./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png)

La procedura è stata completata. Se si torna a Visual Studio, si apre il progetto dell'app MvcACS e si fa clic su **Identity and Access**, nello strumento verranno elencati i provider di identità Windows Live ID e Google.

![](./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png)

L'effetto sarà visibile al momento dell'esecuzione dell'applicazione. Quando un'applicazione supporta più provider di identità, il browser dell'utente viene innanzitutto indirizzato a una pagina ospitata da ACS che chiede di selezionare un provider di identità.

![](./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png)

Dopo la selezione di un provider di identità, il browser passa alla pagina di accesso del provider di identità scelto.

<h2><span  class="short-header">Passaggi successivi</span>Passaggi successivi</h2>


In questo argomento è stata creata un'applicazione Web integrata con ACS, ma questo è solo l'inizio, poiché questo scenario può essere esteso.

Ad esempio, è possibile aggiungere ulteriori provider di identità per questa applicazione RP o consentire l'accesso all'applicazione Web agli utenti registrati nelle directory aziendali, ad esempio Servizi di dominio Active Directory.

È inoltre possibile aggiungere allo spazio dei nomi regole che specificano le attestazioni da inviare a un'applicazione per l'elaborazione nella logica di business dell'applicazione.

Per continuare ad esplorare le funzionalità di ACS ed esercitarsi con ulteriori scenari, vedere [Servizio di controllo di accesso 2.0][1].



[1]: http://go.microsoft.com/fwlink/?LinkID=212360
[2]: http://go.microsoft.com/fwlink/?LinkID=245849
[3]: http://manage.WindowsAzure.com
[4]: http://msdn.microsoft.com/it-it/library/hh377151.aspx