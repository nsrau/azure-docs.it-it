<properties 
	pageTitle="Come usare Controllo di accesso (.NET) - Guida alle funzionalità di Azure" 
	description="Informazioni su come usare Servizio di controllo di accesso (ACS) nell'applicazione Azure per autenticare gli utenti quando provano a ottenere l'accesso a un'app Web." 
	services="active-directory" 
	documentationCenter=".net" 
	authors="msmbaldwin" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="mbaldwin"/>




# Come autenticare gli utenti Web con Microsoft Azure AD Access Control


In questa guida vengono fornite informazioni sull'uso di Microsoft Azure AD Access Control (noto anche come Servizio di controllo di accesso o ACS) per l'autenticazione degli utenti che tentano di accedere a un'applicazione Web da provider di identità quali Microsoft, Google, Yahoo e Facebook.


## Informazioni su ACS

La maggior parte degli sviluppatori non ha esperienza nell'ambito delle identità e non desidera dedicare tempo a sviluppare meccanismi di autenticazione e autorizzazione per le applicazioni e i servizi. ACS è un servizio di Azure che consente di autenticare in modo semplice gli utenti per l'accesso ai servizi e alle applicazioni Web senza bisogno di aggiungere al codice logica di autenticazione complessa.

In ACS sono disponibili le funzionalità seguenti:

-   Integrazione con Windows Identity Foundation (WIF).
-   Supporto per i provider di identità Web più diffusi, tra cui account Microsoft (precedentemente noti come Windows Live ID), Google, Yahoo e Facebook.
-   Supporto per Active Directory Federation Services (ADFS) 2.0.
-   Un servizio di gestione basato sul protocollo OData che offre accesso programmatico alle impostazioni di ACS.
-   Un portale di gestione che consente l'accesso amministrativo alle impostazioni di ACS.

Per ulteriori informazioni sul servizio di controllo di accesso, vedere [Servizio di controllo di accesso 2.0][].

## Concetti

ACS usa l'identità basata sulle attestazioni, un approccio coerente alla creazione di meccanismi di autenticazione per le applicazioni in esecuzione locale o nel cloud. L'identità basata sulle attestazioni offre un metodo comune che le applicazioni e i servizi possono usare per ottenere le informazioni di identità necessarie sugli utenti interni o esterni all'organizzazione oppure su Internet.

Per completare le attività in questa guida è necessario comprendere i termini e i concetti illustrati di seguito:


**Client** - Un browser che tenta di ottenere accesso a un'applicazione Web.

**Applicazione relying party (RP)** - L'app Web. Un'applicazione relying party è un sito Web o un servizio che affida l'autenticazione a un'autorità esterna. In gergo tecnico, si dice che considera attendibile tale autorità. In questa guida viene illustrato come configurare un'applicazione per considerare attendibile ACS.

**Token** - Un utente ottiene accesso a un'applicazione relying party presentando un token valido, emesso da un'autorità che l'applicazione RP considera attendibile. Una raccolta di dati sulla sicurezza emessa quando un client viene autenticato. Contiene un set di attestazioni, ovvero di attributi dell'utente autenticato, ad esempio nome o età o un identificatore per un ruolo utente. I token sono firmati digitalmente, in modo che sia possibile identificare l'autorità emittente, ma non modificare i contenuti.

**Provider di identità (IP)** - Un'autorità che autentica le identità utente ed emette token di sicurezza, ad esempio un account Microsoft (Windows Live ID), Facebook, Google, Twitter e Active Directory. Quando ACS è configurato in modo da considerare attendibile un provider di identità, accetta e convalida i token emessi da tale provider. Poiché ACS può considerare attendibili più provider di identità contemporaneamente, quando un'applicazione considera attendibile ACS può offrire agli utenti la possibilità di eseguire l'autenticazione tramite uno qualsiasi degli IP considerati attendibili da ACS.

**Provider di federazione (FP)** - I provider di identità (IP) hanno una conoscenza diretta degli utenti, li autenticano usando le relative credenziali ed emettono attestazioni sugli utenti. Un provider di federazione è un'autorità di tipo diverso. Anziché autenticare direttamente gli utenti, un provider di federazione funge da broker di autenticazione, ovvero agisce come intermediario tra un'applicazione relying party e uno o più provider di identità. ACS è un provider di federazione.

**Motore regole ACS** - Le regole di trasformazione delle attestazioni convertono le attestazioni in token dai provider di identità attendibili, in modo che possano essere usate da un'applicazione relying party. ACS include un motore regole che applica le regole di trasformazione delle attestazioni specificate per l'applicazione relying party.

**Spazio dei nomi ACS** - Fornisce un ambito univoco per fare riferimento alle risorse di ACS all'interno dell'applicazione. Lo spazio dei nomi contiene una serie di impostazioni, ad esempio il provider di identità considerato attendibile, le applicazioni RP da servire e le regole da applicare ai token in ingresso. Visualizza inoltre gli endpoint usati dall'applicazione e dallo sviluppatore per comunicare con ACS.

Nella figura seguente viene illustrato il funzionamento dell'autenticazione ACS con un'applicazione Web:

![][0]

1.  Il client, in questo caso un browser, richiede una pagina dall'applicazione relying party.
2.  Poiché la richiesta non è stata ancora autenticata, l'applicazione RP reindirizza l'utente all'autorità che considera attendibile, ovvero ACS. ACS presenta all'utente l'elenco dei provider di identità specificati per questa applicazione RP. L'utente seleziona il provider di identità appropriato.
3.  Il client passa alla pagina di autenticazione del provider di identità e chiede all'utente di eseguire l'accesso.
4.  Dopo l'autenticazione del client, ad esempio dopo l'immissione delle credenziali di identità, il provider di identità emette un token di sicurezza.
5.  Dopo l'emissione del token di sicurezza, il provider di identità indica al client di inviarlo ad ACS.
6.  ACS convalida il token di sicurezza emesso dal provider di identità, inserisce le attestazioni di identità contenute nel motore regole ACS, calcola le attestazioni di identità di output ed emette un nuovo token di sicurezza che contiene tali attestazioni di identità di output.
7.  ACS indica al client di inviare il token di sicurezza emesso da ACS all'applicazione RP. L'applicazione convalida la firma nel token di sicurezza, estrae le attestazioni per l'uso da parte della logica di business dell'applicazione e restituisce la pagina richiesta.

## Prerequisiti


Per completare le attività in questa guida è necessario quanto segue:

-	Sottoscrizione di Azure
-	Microsoft Visual Studio 2012 
-	Strumento di gestione delle identità e degli accessi per Visual Studio 2012 (per il download, vedere [Identity and Access Tool][])


## Creazione di uno spazio dei nomi ACS

Per usare Microsoft Azure AD Access Control, creare uno spazio dei nomi ACS. Lo spazio dei nomi fornisce un ambito univoco per fare riferimento alle risorse di ACS all'interno dell'applicazione.

1.  Accedere al [portale di gestione di Azure][] (https://manage.WindowsAzure.com).
    
2.  Fare clic su **Active Directory**.

	![][1]

3.  Per creare un nuovo spazio dei nomi ACS, fare clic su **New**. Verranno selezionati **App Services** e **Access Control**. Fare clic su **Creazione rapida**.

	![][2]

4.  Immettere un nome per lo spazio dei nomi. Azure verificherà che il nome sia univoco.

5.  Selezionare l'area in cui viene usato lo spazio dei nomi. Per prestazioni ottimali, usare l'area in cui si sta distribuendo l'applicazione e quindi fare clic su **Create**.

Azure creerà e attiverà lo spazio dei nomi.

## Creare un'applicazione ASP.NET MVC

In questo passaggio verrà creata un'applicazione ASP.NET MVC. Nei passaggi successiva, questa semplice applicazione Web Forms verrà integrata con ACS.

1.	Avviare Visual Studio 2012 o Visual Studio Express per il Web 2012. Le versioni precedenti non funzioneranno con questa esercitazione.
1.	Fare clic su **File** e quindi su **Nuovo progetto**.
1.	Selezionare il modello Visual C#/Web e quindi selezionare **Applicazione Web ASP.NET MVC 4**.

	In questa guida verrà usata un'applicazione MVC, ma per questa attività è possibile usare qualsiasi tipo di applicazione Web.

	![][3]

1. In **Nome** digitare **MvcACS** e quindi fare clic su **OK**.
1. Nella finestra di dialogo successiva selezionare **Applicazione Internet** e quindi fare clic su **OK**.
1. Modificare il file *Views\\Shared\_LoginPartial.cshtml* e sostituire il contenuto con il codice seguente:

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

1. Premere F5 per eseguire l'applicazione. L'applicazione ASP.NET MVC predefinita verrà visualizzata nel Web browser.

## Integrazione dell'applicazione Web con ACS

In questa attività l'applicazione Web ASP.NET verrà integrata con ACS.

1.	In Esplora soluzioni fare clic sul progetto MvcACS e quindi selezionare **Identity and Access**.

	Se l'opzione **Identity and Access** non viene visualizzata nel menu contestuale installare lo strumento di gestione delle identità e degli accessi. Per informazioni, vedere [Identity and Access Tool].

	![][4]

2.	Nella scheda **Providers** selezionare **Use the Azure Access Control Service**.

    ![][44]

3.  Fare clic sul collegamento **Configure**.

    ![][444]

	Visual Studio richiederà informazioni sullo spazio dei nomi ACS. Immettere lo spazio dei nomi creato in precedenza. In queste immagini è denominato Test, ma lo spazio dei nomi dell'utente avrà un nome diverso. Tornare al portale di gestione di Azure per ottenere la chiave simmetrica.

	![][17]

4.  Nel portale di gestione di Azure fare clic sullo spazio dei nomi ACS e quindi fare clic su **Manage**.

	![][8]

5.	Fare clic su **Management Service** e quindi su **Management Client**.

	![][18]

6.	Fare clic su **Symmetric Key**, fare clic su **Show Key** e quindi copiare il valore della chiave. Fare clic su **Cancel** per uscire dalla pagina di modifica del client di gestione senza apportare modifiche.

	![][19]

7.  In Visual Studio incollare la chiave nel campo **Enter the Management Key for the namespace**, fare clic su **Save management key** e quindi fare clic su **OK**.

	![][20]

	Visual Studio usa le informazioni sullo spazio dei nomi per connettersi al portale di gestione ACS e ottenere le impostazioni per lo spazio dei nomi, tra cui i provider di identità, l'area di autenticazione e l'URL restituito.

8.	Selezionare **Windows Live ID** (account Microsoft) e fare clic su OK.

	![][5]

## Test dell'integrazione con ACS

In questa attività viene illustrato come testare l'integrazione dell'applicazione relying party con ACS.

-	Premere F5 in Visual Studio per eseguire l'app.

Quando l'applicazione è integrata con ACS e viene selezionato Windows Live ID (account Microsoft), anziché aprire l'applicazione Web Forms ASP.NET predefinita, il browser viene reindirizzato alla pagina di accesso per gli account Microsoft. Accedendo con un nome utente e una password validi, l'utente viene reindirizzato all'applicazione MvcACS.

![][6]

Congratulazioni. ACS è stato correttamente integrato con l'applicazione Web ASP.NET. ACS sta gestendo l'autenticazione degli utenti usando le credenziali degli account Microsoft.

## Visualizzazione delle attestazioni inviate da ACS

In questa sezione l'applicazione verrà modificata in modo da visualizzare le attestazioni inviate da ACS. Lo strumento di gestione delle identità e degli accessi ha creato un gruppo di regole che trasmette tutte le attestazioni provenienti dal provider di identità all'applicazione. Si noti che provider di identità diversi inviano attestazioni diverse.

1. Aprire il file *Controllers\\HomeController.cs*. Aggiungere un'istruzione **using** per **System.Threading**:

 	using System.Threading;

1. Nella classe HomeController aggiungere il metodo *Claims*:

    public ActionResult Claims() { ViewBag.Message = "Your claims page.";

        ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;

        return View();
    }

1. Fare clic con il pulsante destro del mouse sul metodo *Claims* e selezionare **Aggiungi visualizzazione**.

![][66]

1. Fare clic su **Aggiungi**.

1. Sostituire il contenuto del file *Views\\Home\\Claims.cshtml* con il codice seguente:

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

1. Eseguire l'applicazione e passare al metodo *Claims*:

![][666]

Per ulteriori informazioni sull'uso delle attestazioni nell'applicazione, vedere la [documentazione di Windows Identity Foundation](http://msdn.microsoft.com/library/hh377151.aspx).

## Visualizzazione dell'applicazione nel portale di gestione ACS

Lo strumento di gestione delle identità e degli accessi in Visual Studio integra automaticamente l'applicazione con ACS.

Quando si seleziona l'opzione per l'uso del Servizio di controllo di accesso di Azure, lo strumento aggiunge l'applicazione come relying party, la configura per l'uso dei provider di identità selezionati, quindi genera e seleziona le regole predefinite di trasformazione delle attestazioni per l'applicazione.

È possibile rivedere e modificare queste impostazioni di configurazione nel portale di gestione ACS. Eseguire la procedura seguente per rivedere le modifiche nel portale.

1.	Accedere al [portale di gestione di Azure](http://manage.WindowsAzure.com).

2.	Fare clic su **Active Directory**.

	![][8]

3.	Selezionare uno spazio dei nomi ACS e fare clic su **Manage**. Verrà visualizzato il portale di gestione ACS.

	![][9]


4.	Fare clic su **Relying party applications**.

	La nuova applicazione MvcACS verrà visualizzata nell'elenco delle applicazioni relying party. L'area di autenticazione verrà impostata automaticamente sulla pagina principale dell'applicazione.

	![][10]


5.	Fare clic su **MvcACS**.

	La pagina di modifica dell'applicazione relying party contiene le impostazioni di configurazione dell'applicazione Web MvcACS. Quando si modificano le impostazioni in questa pagina e si salva, le modifiche vengono applicate immediatamente all'applicazione.

	![][11]

6.	Scorrere in basso nella pagina per visualizzare le altre impostazioni di configurazione per l'applicazione MvcACS, compresi i provider di identità e le regole di trasformazione delle attestazioni.

	![][12]

Nella sezione successiva le funzionalità del portale di gestione ACS verranno usate per apportare una modifica all'applicazione Web, un'operazione estremamente semplice.

## Aggiunta di un provider di identità

Attraverso il portale di gestione ACS è possibile modificare l'autenticazione dell'applicazione MvcACS. In questo esempio si aggiungerà Google come provider di identità per MvcACS.

1.	Fare clic su **Identity providers** nel menu di navigazione e quindi fare clic su **Add**.

	![][13]

2.	Fare clic su **Google** e quindi su **Next**. La casella di controllo dell'app MvcACS è selezionata per impostazione predefinita.

	![][14]

3. Fare clic su Salva.

	![][15]


La procedura è stata completata. Se si torna a Visual Studio, si apre il progetto dell'app MvcACS e si fa clic su **Identity and Access**, nello strumento verranno elencati i provider di identità Windows Live ID e Google.

![][16]

L'effetto sarà visibile al momento dell'esecuzione dell'applicazione. Quando un'applicazione supporta più provider di identità, il browser dell'utente viene innanzitutto indirizzato a una pagina ospitata da ACS che chiede di selezionare un provider di identità.

![][7]

Dopo la selezione di un provider di identità, il browser passa alla pagina di accesso del provider di identità scelto.

## Passaggi successivi

In questo argomento è stata creata un'applicazione Web integrata con ACS, ma questo è solo l'inizio, poiché questo scenario può essere esteso.
 
Ad esempio, è possibile aggiungere ulteriori provider di identità per questa applicazione RP o consentire l'accesso all'applicazione Web agli utenti registrati nelle directory aziendali, ad esempio Servizi di dominio Active Directory.

È inoltre possibile aggiungere allo spazio dei nomi regole che specificano le attestazioni da inviare a un'applicazione per l'elaborazione nella logica di business dell'applicazione.

Per continuare ad esplorare le funzionalità di ACS ed esercitarsi con ulteriori scenari, vedere [Servizio di controllo di accesso 2.0].



  [What is ACS?]: #what-is
  [Concepts]: #concepts
  [Prerequisites]: #pre
  [Create an ASP.NET MVC Application]: #create-web-app
  [Create an Access Control Namespace]: #create-namespace
  [Integrate your Web Application with ACS]: #Identity-Access
  [Test the Integration with ACS]: #Test-ACS
  [View the Application in the ACS Management Portal]: acs-portal
  [Add an Identity Provider]: #add-IP
  [What's Next]: #whats-next
  [vcsb]: #bkmk_viewClaims
  [vpp]: #bkmk_VP

  [Servizio di controllo di accesso 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  [Identity and Access Tool]: http://go.microsoft.com/fwlink/?LinkID=245849
  [portale di gestione di Azure]: http://manage.WindowsAzure.com

  [0]: ./media/active-directory-dotnet-how-to-use-access-control/acs-01.png
  [1]: ./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png
  [2]: ./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png
  [3]: ./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png
  [4]: ./media/active-directory-dotnet-how-to-use-access-control/rzIA.png
[44]: ./media/active-directory-dotnet-how-to-use-access-control/rzPT.png
 [444]: ./media/active-directory-dotnet-how-to-use-access-control/rzC.png
  [5]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png
  [6]: ./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png
  [66]: ./media/active-directory-dotnet-how-to-use-access-control/rzAv.png
  [666]: ./media/active-directory-dotnet-how-to-use-access-control/rzCl.png
  [7]: ./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png
  [8]: ./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png
  [9]: ./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png
  [10]: ./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png
  [11]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png
  [12]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png
  [13]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png
  [14]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png
  [15]: ./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png
  [16]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png
  [17]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png
  [18]: ./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png
  [19]: ./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png
  [20]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png
 

<!---HONumber=July15_HO5-->