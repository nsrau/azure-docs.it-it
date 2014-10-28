<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrating Multi-Tenant Cloud Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan"></tags>

# Integrazione di applicazioni per cloud multitenant con Azure Active Directory

## <a name="introduction"></a>Introduzione

Azure Active Directory (Azure AD) è un servizio moderno basato su REST che fornisce funzionalità di gestione dell'identità e controllo di accesso per le applicazioni cloud. Azure AD si integra facilmente con i servizi cloud nonché con Azure, Microsoft Office 365, Dynamics CRM Online e Windows Intune. Inoltre, le distribuzioni locali di Active Directory esistenti possono sfruttare al meglio Azure AD. Per altre informazioni, vedere la [pagina relativa all'identità][pagina relativa all'identità] su [windowsazure.com][windowsazure.com].

Questa procedura dettagliata è destinata agli sviluppatori di .NET che desiderino integrare un'applicazione multitenant in Azure AD. Si apprenderà come:

-   Consentire ai clienti di effettuare l'iscrizione all'applicazione con Azure AD
-   Abilitare l'accesso Single Sign-On (SSO) con Azure AD
-   Eseguire query sui dati della directory dell'utente mediante l'API Graph di Azure AD

È possibile [scaricare qui][scaricare qui] l'applicazione di esempio complementare per questa procedura dettagliata. È possibile eseguire l'esempio senza apportarvi alcuna modifica, ma può essere necessario modificare l'[assegnazione della porta in Visual Studio][assegnazione della porta in Visual Studio] in modo da usare il protocollo https. Seguire le istruzioni contenute nella pagina collegata, ma impostare il protocollo di binding su "https" nella sezione relativa ai binding del file ApplicationHost.config file. Tutti i frammenti di codice illustrati nei passaggi seguenti sono strati estratti dall'esempio.

> [WACOM.NOTE]
> L'applicazione di esempio della directory multitenant viene fornita solo a scopi illustrativi. Non è consigliabile usare questo esempio (incluse le classi della libreria helper) in fase di produzione.

### Prerequisiti

Per questa procedura dettagliata sono necessari i prerequisiti per sviluppatori riportati di seguito:

-   [Visual Studio 2012][Visual Studio 2012]
-   [WCF Data Services per OData][WCF Data Services per OData]

### Sommario

-   [Introduzione][Introduzione]
-   [Parte 1: Ottenere un ID client per l'accesso ad Azure AD][Parte 1: Ottenere un ID client per l'accesso ad Azure AD]
-   [Parte 2: Abilitare i clienti a effettuare l'iscrizione con Azure AD][Parte 2: Abilitare i clienti a effettuare l'iscrizione con Azure AD]
-   [Parte 3: Abilitare l'accesso Single Sign-On][Parte 3: Abilitare l'accesso Single Sign-On]
-   [Parte 4: Accedere ad Azure AD Graph][Parte 4: Accedere ad Azure AD Graph]
-   [Parte 5: Pubblicare l'applicazione][Parte 5: Pubblicare l'applicazione]
-   [Riepilogo][Riepilogo]

## <a name="getclientid"></a>Parte 1: Ottenere un ID client per l'accesso ad Azure AD

In questa sezione viene descritto come ottenere un ID client e un segreto client dopo aver creato un account del dashboard venditori Microsoft. Un ID client è l'identificatore univoco dell'applicazione, mentre il segreto client è la chiave associata necessaria quando si eseguono richieste con l'ID client. Entrambi i valori sono necessari per integrare l'applicazione in Azure AD.

### Passaggio 1: Creare un account con il dashboard venditori Microsoft

Per sviluppare e pubblicare applicazioni che si integrino in Azure AD è necessario iscriversi per creare un account del [dashboard venditori Microsoft][dashboard venditori Microsoft]. Verrà quindi richiesto di [creare un profilo dell'account][creare un profilo dell'account] come società o come utente individuale. Questo profilo verrà usato per pubblicare le applicazioni in Azure Marketplace o in altri marketplace ed è richiesto per generare un ID client e un segreto client.

Per i nuovi account viene impostato lo stato "Account Pending Approval". Lo stato di "account in attesa di approvazione" non impedisce all'utente di avviare lo sviluppo: è comunque possibile creare gli ID client nonché le bozze di presentazioni dell'app. Sarà tuttavia possibile inviare la presentazione dell'app per ottenere l'approvazione solo dopo che l'account stesso sarà stato approvato. La presentazione dell'app inviata sarà visibile ai clienti in Azure Marketplace solo dopo che sarà stata approvata.

### Passaggio 2: Ottenere un ID client per l'applicazione

Per integrare l'applicazione in Azure AD sono necessari un ID client e un segreto client. Un ID client è l'identificatore univoco dell'applicazione, utilizzato principalmente per identificare un'applicazione per l'accesso Single Sign-On oppure per autenticare le chiamate ad Azure AD Graph. Per altre informazioni sull'ottenimento di un ID client e di un segreto client, vedere [Creare gli ID e i segreti client nel dashboard venditori Microsoft][Creare gli ID e i segreti client nel dashboard venditori Microsoft].

> [WACOM.NOTE]
> L'ID client e il segreto client saranno necessari più avanti in questa procedura dettagliata, pertanto assicurarsi di annotarli.

Per generare un ID client e un segreto client sarà necessario immettere le seguenti proprietà nel dashboard venditori Microsoft:

**App Domain**: Nome host dell'applicazione, ad esempio "contoso.com". Questa proprietà non deve contenere numeri di porta. Durante lo sviluppo, è consigliabile impostare questa proprietà su "localhost".

**App Redirect URL**: URL di reindirizzamento al quale Azure AD invierà una risposta dopo l'accesso dell'utente e quando un'organizzazione ha autorizzato l'applicazione, ad esempio: "<https://contoso.com/>". Durante lo sviluppo, è consigliabile impostare questa proprietà su "https://localhost:&#60;numero porta&#62;"

### Passaggio 3: Configurare l'applicazione per l'uso dell'ID client e del segreto client

Questo passaggio richiede l'ID client e il segreto client generati durante la registrazione sul dashboard venditori Microsoft. L'ID client viene usato per l'accesso SSO ed entrambi (ID client e segreto client) verranno utilizzati in seguito per ottenere un token di accesso per chiamare l'API Graph di Azure AD.

L'applicazione di esempio è predisposta per l'uso di Azure AD ed esegue il caricamento dell'ID client e del segreto client dal file config. Nel file **Web.config** nell'applicazione di esempio, apportare le modifiche seguenti:

1.  Nel nodo **appSettings** sostituire i valori per "clientId" e "SymmetricKey" con l'ID client, il segreto client e il dominio applicazione:

        <appSettings>
            <add key="clientId" value="(Your Client ID value)"/>
            <add key="SymmetricKey" value="(Your Client Secret value)"/>
            <add key="AppHostname" value="(Your App Domain)"/>
        </appSettings>

2.  Nel nodo **audienceUris** di **system.identityModel** inserire l'ID client dopo "spn:":

        <system.identityModel>
            <audienceUris>
                <add value="spn:(Your Client ID value)" />
            </audienceUris>

## <a name="enablesignup"></a>Parte 2: Abilitare i clienti a effettuare l'iscrizione con Azure AD

In questa sezione viene descritto come abilitare i clienti a effettuare l'iscrizione all'applicazione con Azure AD. Affinché un cliente possa usare un'applicazione integrata in Azure AD un amministratore tenant deve prima autorizzarla. Il processo di autorizzazione inizia con una richiesta di consenso inviata dall'applicazione ad Azure, la quale genera una risposta che dovrà essere gestita dall'applicazione. Nella procedura seguente viene descritto come generare una richiesta di consenso e gestire la risposta.

I passaggi contenuti in questa sezione utilizzano le classi helper dell'applicazione di esempio. Questa classi sono contenute nella libreria *Microsoft.IdentityModel.WAAD.Preview* dell'esempio e facilitano la concentrazione sul codice dell'applicazione piuttosto che sulle specifiche dell'identità e del protocollo.

### Passaggio 1: Richiedere il consenso per l'applicazione

Nell'esempio di interazione seguente viene dimostrato il processo di richiesta del consenso per l'applicazione:

1.  Il cliente fa clic sul collegamento "sign up using Azure AD" nella pagina Web dell'applicazione
2.  e viene reindirizzato alla pagina di autorizzazione di Azure AD, con le informazioni sull'applicazione aggiunte alla richiesta.
3.  Il cliente concede o rifiuta il consenso per l'applicazione.
4.  Azure AD reindirizza il cliente all'URL di reindirizzamento dell'app specificato dall'utente quando sono stati generati un ID client e un segreto client sul dashboard venditori Microsoft. La richiesta di reindirizzamento indica il risultato della richiesta di consenso, incluse le informazioni sul tenant se il consenso è stato concesso.

Per generare la richiesta di reindirizzamento al passaggio 2 sopra riportato è necessario aggiungere i parametri querystring all'URL seguente per la pagina di autorizzazione di Azure AD: *<http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx>*

I parametri querystring sono descritti di seguito:

**ApplicationID**: (richiesto) il valore **ClientID** ricevuto nel dashboard venditori Microsoft.

**RequestedPermissions**: (facoltativo) le autorizzazioni che devono essere concesse all'applicazione dal tenant.
Durante lo sviluppo, queste autorizzazioni vengono usate per testare le applicazioni non pubblicate. Per le applicazioni pubblicate, questo parametro verrà ignorato. Verranno invece utilizzate le autorizzazioni richieste nella presentazione dell'applicazione. Per altre informazioni sulla presentazione vedere la Parte 5.
Sono disponibili tre possibili valori per questo parametro:

**DirectoryReader**: concede l'autorizzazione alla lettura dei dati della directory, ad esempio account utente, gruppi e informazioni sull'organizzazione. Abilita l'accesso SSO.

**UserAccountAdministrator**: concede l'autorizzazione alla lettura e alla scrittura dei dati, ad esempio account utente, gruppi e informazioni sull'organizzazione. Abilita l'accesso SSO.

**None**: abilita l'accesso Single Sign-On ma disabilita l'accesso ai dati della directory.

Il valore predefinito è "None" se il parametro non è specificato o lo è in maniera errata.

Di seguito è riportato un esempio di URL valido della richiesta di consenso:
*<https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader>*

Nell'applicazione di esempio, il collegamento "Register" contiene un URL simile per la richiesta di consenso, come illustrato di seguito:

![Accesso][Accesso]

> [WACOM.NOTE]
> Quando si esegue il test dell'applicazione non pubblicata, si avrà un'esperienza simile a quella dei clienti per quanto riguarda il consenso. Tuttavia, la pagina di autorizzazione di un'applicazione non pubblicata ha un aspetto differente dalla pagina di autorizzazione di un'applicazione pubblicata. In un'applicazione pubblicata sono visualizzati il nome e il logo dell'app e i dettagli del server di pubblicazione, laddove nell'applicazione non pubblicata non viene visualizzato alcun dato.

### Passaggio 2: Gestire la risposta di consenso

Dopo che un cliente avrà concesso o negato il consenso per l'applicazione, Azure AD invierà una risposta all'URL di reindirizzamento dell'applicazione. La risposta conterrà i parametri querystring seguenti:

**TenantId**: GUID univoco per il tenant di Azure AD che ha autorizzato l'applicazione. Questo parametro verrà specificato solo se il cliente ha autorizzato l'applicazione.

**Consent**: questo valore verrà impostato su "Granted" se l'applicazione è stata autorizzata o su "Denied" se la richiesta è stata respinta.

Di seguito è riportato un esempio di risposta valida alla richiesta di consenso, che indica che l'applicazione è stata autorizzata:
*<https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted>*

L'applicazione dovrà mantenere il contesto in modo che la richiesta inviata alla pagina di autorizzazione di Azure AD sia collegata alla risposta (e rifiuti qualsiasi risposta priva di una richiesta associata).

<div class="dev-callout"><strong>Nota</strong><p>Dopo aver ottenuto il consenso, potrebbero essere necessari alcuni istanti prima che venga effettuato il provisioning dell'accesso SSO e Graph in Azure AD. Il primo utente di ogni organizzazione che effettua l'iscrizione all'applicazione potrebbe visualizzare errori di accesso fino al completamento del provisioning.</p></div>

Dopo che un cliente avrà concesso il consenso all'applicazione, è importante associare e archiviare il tenant appena creato nell'applicazione con il TenantId restituito dalla risposta di consenso. L'applicazione di esempio contiene un *HttpModule* nello spazio dei nomi *Microsoft.IdentityModel.WAAD.Preview.Consent* che registra automaticamente il TenantId in un "archivio dati" cliente/TenantId su tutte le risposte di consenso con esito positivo. Il relativo codice è indicato di seguito e la registrazione del TenantId in un "archivio dati" cliente/TenantId viene eseguita dal metodo *TrustedIssuers.Add*:

    private void Application_BeginRequest(Object source,
             EventArgs e)
    {
        HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
            if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
                // For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"]; 
            }
        }            
    }

Prima di poter eseguire il test del codice della richiesta/risposta di consento per l'applicazione è necessario ottenere un tenant della directory di Azure AD.

### Passaggio 3: Ottenere un tenant di Azure AD per eseguire il test dell'applicazione

Per testare la capacità dell'applicazione di integrarsi in Azure AD sarà necessario disporre di un tenant di Azure AD. È possibile riutilizzare un tenant già utilizzato per il test di un'altra applicazione. È consigliabile ottenere almeno due tenant per assicurarsi che l'applicazione possa essere testata e utilizzata da più tenant. A tale scopo, non è consigliabile usare un tenant di produzione. [Ottenere un tenant di Azure AD][Ottenere un tenant di Azure AD].

Dopo aver ottenuto un tenant di Azure AD è possibile creare ed eseguire l'applicazione premendo **F5**. È inoltre possibile provare a effettuare l'iscrizione all'applicazione con il nuovo tenant.

<div class="dev-callout"><strong>Nota</strong><p>Se i clienti si iscrivono per ottenere un nuovo tenant di Azure AD &egrave; possibile che il completamento del provisioning di tale tenant richieda alcuni minuti. Nella pagine del consenso potrebbero essere visualizzati alcuni errori fino al completamento del provisioning.</p></div>

## <a name="enablesso"></a>Parte 3: Abilitare l'accesso Single Sign-On

In questa sezione viene illustrato come abilitare l'accesso Single Sign-On (SSO). Il processo inizia con la costruzione di una richiesta di accesso ad Azure AD che esegue l'autenticazione di un utente sull'applicazione, quindi verifica nella risposta di accesso che il cliente appartiene a un tenant che ha autorizzato l'applicazione. Per la richiesta di accesso sono necessari l'ID client di Seller Dashboard e l'ID tenant dell'organizzazione del cliente.

La richiesta di accesso è specifica di un tenant della directory e deve includere un TenantID. È possibile determinare un TenantID dal nome di dominio di un tenant della directory di Azure AD. Vi sono due modi comuni per ottenere questo nome di dominio dall'utente finale al suo accesso:

-   Se l'URL dell'applicazione è *<https://contoso.myapp.com>* o *<https://myapp.com/contoso.com>*, *contoso* e *contoso.com* rappresentano il nome di dominio di Azure AD e *myapp.com* rappresenta l'URL dell'applicazione.
-   L'applicazione può chiedere all'utente di fornire l'indirizzo email oppure il nome di dominio di Azure AD. Questo approccio viene usato nell'applicazione di esempio, in cui l'utente deve immettere il nome di dominio di Azure AD, come illustrato di seguito:

![Accesso][Accesso]

### Passaggio 1: Cercare l'ID tenant

Utilizzando il nome di dominio di Azure AD fornito dal cliente è possibile cercare il relativo ID tenant analizzando i metadati di federazione. Nell'applicazione di esempio questo processo è gestito dal metodo *Domain2TenantId* della classe *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals*.

Per dimostrare questo processo, nei passaggi seguenti viene usato il nome di dominio contoso.com.

1.  Ottenere il file **FederationMetadata.xml** per il tenant di Azure AD. Ad esempio:
    *<https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml>*
2.  Nel file **FederationMetadata.xml** individuare la voce **Entity Descriptor**. L'ID tenant viene incluso come parte della proprietà**entityID** che segue l'indirizzo "<https://sts.windows.net>", come illustrato di seguito:

         <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 

    In questo caso il valore del TenantID è **a7456b11-6fe2-4e5b-bc83-67508c201e4b**.

3.  È consigliabile archiviare il dominio e il TenantID ad esso associato nell'archivio dati cliente/TenantId dell'applicazione. È possibile usare questi due valori assieme per le future richieste di accesso e per eliminare la necessità di ottenere il file **FederationMetadata.xml** ogni volta. L'applicazione di esempio non presenta questa funzione di ottimizzazione.

### Passaggio 2: Generare la richiesta di accesso

Quando un cliente effettua l'accesso all'applicazione, ad esempio facendo clic su un pulsante di accesso, la richiesta di accesso dovrà essere generata tramite il TenantID del cliente e l'ID client dell'applicazione. Nell'applicazione di esempio questa richiesta viene generata dal metodo *GenerateSignInMessage* della classe *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils*. Questo metodo consente di verificare che il TenantID del cliente rappresenti un'organizzazione che ha autorizzato l'applicazione e che genera l'URL di destinazione per il pulsante di accesso, come illustrato di seguito:

![Accesso][Accesso]

Fare clic su questo pulsante per passare alla pagina di accesso di Azure AD nel browser in uso. Dopo aver eseguito l'accesso, Azure AD restituirà una risposta di accesso all'applicazione.

### Passaggio 3: Convalidare l'autorità emittente del token in ingresso con la risposta di accesso

Quando un cliente effettua l'accesso all'applicazione è necessario convalidare che il relativo tenant abbia autorizzato l'applicazione. La risposta di accesso contiene un token, il quale contiene a sua volta proprietà e attestazioni che possono essere ispezionate dall'applicazione.

Per eseguire questa convalida è necessario ottenere il TenantID dalla proprietà Issuer nel token e assicurarsi che esista nell'"archivio dati" cliente/TenantId. Nell'applicazione di esempio questa convalida viene conseguita mediante la creazione di una classe gestore dei token personalizzata che estende la classe *Saml2SecurityTokenHandler* di Windows Identity Foundation. Il gestore del token personalizzato verifica il token di sicurezza in ingresso e rende disponibili le relative attestazioni e proprietà, in modo che sia possibile convalidare il TenantID. Il frammento di codice relativo a questa classe è illustrato di seguito.

Nell'applicazione di esempio il codice originale è presente nello spazio dei nomi *Microsoft.IdentityModel.WAAD.Preview.WebSSO*. Il gestore del token utilizza inoltre il metodo Contains della classe *Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers*, che consente di verificare che il TenantID sia rimasto nell'"archivio dati" del cliente/TenantId.

    /// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

Dopo aver convalidato il token, l'utente potrà accedere all'applicazione. Eseguire l'applicazione e provare a eseguire l'accesso con un account di Azure AD nel tenant autorizzato che è stato creato in precedenza.

## <a name="accessgraph"></a>Parte 4: Accedere ad Azure AD Graph

In questa sezione viene descritto come ottenere un token di accesso e chiamare l'API Graph di Azure AD per accedere ai dati della directory di un tenant. Ad esempio, benché il token ottenuto durante l'accesso contenga dati quali il nome e l'indirizzo di posta elettronica dell'utente, l'applicazione potrebbe richiedere informazioni quali le appartenenze a un gruppo oppure il nome del responsabile dell'utente. È possibile ottenere questa informazioni dalla directory del tenant usando l'API Graph. Per altre informazioni sull'API Graph, vedere [questo argomento][questo argomento]:

Prima che l'applicazione possa chiamare Azure AD Graph deve autenticarsi e ottenere un token di accesso. I token di accesso si ottengono mediante autenticazione dell'applicazione con ID client e segreto client. Nei passaggi successivi verranno illustrate le attività seguenti:

1.  Usare una classe proxy generata per chiamare Azure AD Graph
2.  Acquisire un token di accesso usando Azure Authentication Library (AAL)
3.  Chiamare Azure AD Graph per ottenere un elenco di utenti tenant

<div class="dev-callout"><strong>Nota</strong><p>La libreria helper dell'applicazione di esempio Microsoft.IdentityModel.WAAD.Preview contiene gi&agrave; una classe proxy generata automaticamente (creata aggiungendo un riferimento al servizio all'indirizzo https://graph.windows.net/nome-dominio-chiamato GraphService). L'applicazione utilizzer&agrave; questa classe proxy per chiamare il servizio Azure AD Graph.</p></div>

### Passaggio 1: Usare la classe proxy generata per chiamare Azure AD Graph

In questo passaggio verrà utilizzata l'applicazione di esempio per mostrare le seguenti operazioni:

1.  Creare un endpoint di Azure AD Graph specifico del tenant
2.  Usare l'endpoint per creare un'istanza del proxy per chiamare Graph
3.  Aggiungere l'intestazione di autorizzazione alla richiesta e acquisire il token.

Nell'applicazione di esempio, queste chiamate all'API vengono gestite dal metodo GraphInterface nella classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*, come illustrato nel codice seguente.

    public GraphInterface()
    {
        // 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

### Passaggio 2: Acquisire un token di accesso usando Azure Authentication Library

L'applicazione di esempio utilizza Azure Authentication Library (AAL) per acquisire i token di accesso all'API Graph. Il processo di acquisizione dei token è gestito dal metodo *GetAuthorizationHeader* nella classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* ed è illustrato di seguito.

<div class="dev-callout"><strong>Nota</strong><p>AAL &egrave; disponibile come pacchetto NuGet e pu&ograve; essere installato in Visual Studio.</p></div>

    /// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

Le informazioni seguenti consentono di acquisire il token di accesso, come dimostrato nel codice sopra riportato:

1.  Le informazioni sull'applicazione (ClientID, ServicePrincipalKey e AppHostname)
2.  Le informazioni di destinazione, cioè l'API Graph, a cui si fa riferimento come ServiceRealm
3.  Il TenantDomainName acquisito in precedenza

### Passaggio 3: Chiamare Azure AD Graph per ottenere un elenco di utenti

Il metodo seguente nella classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* consente di ottenere un elenco di tutti gli utenti del tenant tramite il proxy *DataService* generato in precedenza.

    public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

Questo metodo viene chiamato dal file **HomeController.cs** per mostrare l'elenco degli utenti nella scheda Users dell'applicazione Web.

## <a name="publish"></a>Parte 5: Pubblicare l'applicazione

Dopo aver eseguito il test completo dell'applicazione sarà possibile creare una presentazione e pubblicarla in Azure Marketplace. Questa procedura verrà eseguita nel dashboard venditori Microsoft.

<div class="dev-callout"><strong>Nota</strong><p>L'app &egrave; responsabile della gestione di eventuali relazioni di fatturazione con la clientela. Azure Marketplace fornisce solo i collegamenti al sito Web dell'applicazione e le relative informazioni.</p></div>

### Passaggio 1: Creazione di un manifesto e di una presentazione dell'applicazione

Prima di creare una presentazione dell'app è necessario generare un nuovo ID client e segreto client per la versione di produzione dell'applicazione. Nella Parte 1 di questa procedura dettagliata sono stati generati un ID client e un segreto client per una versione di prova dell'applicazione. Ripetere quei passaggi e configurare l'applicazione in modo da usare i nuovi valori, assicurandosi di aver impostato un dominio e un URL di reindirizzamento dell'applicazione.

Sarà quindi necessario creare un manifesto dell'applicazione in cui siano elencate le autorizzazioni richieste dall'applicazione per il consenso del cliente. Questo manifesto sarà scritto in formato XML e governato da un file XSD. Il manifesto dovrà essere caricato come parte della presentazione dell'app che si sta creando.

Sono disponibili tre diversi livelli di autorizzazione, descritti nella Parte 1 di questa procedura dettagliata:

**DirectoryReader**: concede l'autorizzazione alla lettura dei dati della directory, ad esempio account utente, gruppi e informazioni sull'organizzazione. Abilita l'accesso SSO.

**UserAccountAdministrator**: concede l'autorizzazione alla lettura e alla scrittura dei dati, ad esempio account utente, gruppi e informazioni sull'organizzazione. Abilita l'accesso SSO.

**None**: abilita l'accesso Single Sign-On ma disabilita l'accesso ai dati della directory.

Di seguito sono inclusi due esempi di manifesto dell'applicazione. Il primo dimostra le autorizzazioni per un'applicazione di solo accesso SSO e il secondo dimostra le autorizzazioni per un'applicazione di sola lettura:

    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="it-it" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

L'attributo *Policy* negli esempi sopra riportati descrive il tipo di autorizzazione dell'applicazione richiesta. Attualmente è supportato solo l'attributo di autorizzazione "AppOnly". Questo tipo di autorizzazione indica che l'applicazione accede alla directory solo come se stessa.

L'elemento *Reason* facoltativo consente di specificare (in più culture) la giustificazione per il livello di autorizzazione richiesto. Questo testo verrà visualizzato nella pagina del consenso a supporto del cliente quando approva o rifiuta l'applicazione.

Utilizzando il nuovo ID cliente e il manifesto dell'applicazione sarà possibile creare una presentazione dell'app seguendo le istruzioni contenute nell'articolo [Aggiungere app al dashboard venditori Microsoft][Aggiungere app al dashboard venditori Microsoft]. Durante la creazione di una presentazione dell'app assicurarsi di selezionare il tipo di applicazione Azure AD. Al termine della creazione della presentazione fare clic su "submit" per pubblicare l'applicazione in Azure Marketplace. Per il completamento della pubblicazione sarà necessario attendere l'approvazione dell'applicazione.

<div class="dev-callout"><strong>Nota</strong><p>Se viene richiesto di aggiungere informazioni su dati fiscali e pagamento, ignorare questo passaggio in quanto si vende l'applicazione direttamente al cliente e non tramite Microsoft.</p></div>

### Passaggio 2: Finalizzare i test e rendere pubblica l'applicazione

Quando la presentazione dell'app sarà stata approvata, è consigliabile eseguire nuovamente il test end-to-end dell'applicazione. Assicurarsi, ad esempio, che l'applicazione sia stata aggiornata con l'ID client e il segreto client. Controllare l'elenco di controllo del test un'ultima volta, assicurandosi che nella pagina del consenso siano adesso visualizzate le informazioni incluse nella presentazione dell'app.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata si è appreso come integrare l'applicazione multitenant in Azure AD. Il processo includeva i tre passaggi seguenti:

-   Consentire ai clienti di effettuare l'iscrizione all'applicazione con Azure AD
-   Abilitare l'accesso Single Sign-On (SSO) con Azure AD
-   Eseguire query sui dati della directory dell'utente mediante l'API Graph di Azure AD

L'integrazione in Azure AD consente ai clienti di iscriversi e accedere all'applicazione usando un sistema di gestione dell'identità già conosciuto, riducendo o eliminando la necessità di eseguire attività di identificazione separate con l'applicazione. Questa funzionalità offre ai clienti un'esperienza di utilizzo dell'applicazione più scorrevole, risparmiando il tempo normalmente speso nelle attività di gestione.

  [pagina relativa all'identità]: http://www.windowsazure.com/it-it/home/features/identity/
  [scaricare qui]: http://go.microsoft.com/fwlink/?LinkId=271213
  [assegnazione della porta in Visual Studio]: http://msdn.microsoft.com/it-it/library/ms178109(v=vs.100).aspx
  [Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
  [WCF Data Services per OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Introduzione]: #introduction
  [Parte 1: Ottenere un ID client per l'accesso ad Azure AD]: #getclientid
  [Parte 2: Abilitare i clienti a effettuare l'iscrizione con Azure AD]: #enablesignup
  [Parte 3: Abilitare l'accesso Single Sign-On]: #enablesso
  [Parte 4: Accedere ad Azure AD Graph]: #accessgraph
  [Parte 5: Pubblicare l'applicazione]: #publish
  [Riepilogo]: #summary
  [dashboard venditori Microsoft]: https://sellerdashboard.microsoft.com/
  [creare un profilo dell'account]: http://msdn.microsoft.com/it-it/library/jj552460.aspx
  [Creare gli ID e i segreti client nel dashboard venditori Microsoft]: http://msdn.microsoft.com/it-it/library/jj552461.aspx
  [Accesso]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png
  [Ottenere un tenant di Azure AD]: http://g.microsoftonline.com/0AX00en/5
  [questo argomento]: http://msdn.microsoft.com/it-it/library/windowsazure/hh974476.aspx
  [Aggiungere app al dashboard venditori Microsoft]: http://msdn.microsoft.com/it-it/library/jj552465.aspx
