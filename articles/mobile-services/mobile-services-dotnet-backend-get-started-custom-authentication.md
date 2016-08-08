<properties
	pageTitle="Introduzione all'autenticazione personalizzata | Microsoft Azure"
	description="Informazioni su come autenticare gli utenti con un nome utente e una password."
	documentationCenter="Mobile"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="mahender"/>

# Introduzione all'autenticazione personalizzata

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Procedura: Usare l'autenticazione personalizzata per l'applicazione](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).


## Panoramica
Questo argomento descrive come autenticare gli utenti nel back-end .NET di Servizi mobili di Azure rilasciando il proprio token di autenticazione di Servizi mobili. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite usando un nome utente e una password personalizzati per l'app.

>[AZURE.NOTE] Questa esercitazione illustra un metodo avanzato di autenticazione di Servizi mobili con credenziali personalizzate. Per molte app sarà invece più appropriato l'uso dei provider di identità sociale predefiniti, che consentono agli utenti di eseguire l'accesso con Facebook, Twitter, Google, Microsoft Account e Azure Active Directory. Se si tratta della prima esperienza di autenticazione con Servizi mobili, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili].

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

>[AZURE.IMPORTANT] Lo scopo di questa esercitazione è mostrare come rilasciare un token di autenticazione per Servizi mobili. Non considerare il contenuto di questo articolo come linee guida per la sicurezza. Nello sviluppo dell'app è necessario essere al corrente delle implicazioni in termini di sicurezza riguardo l'archiviazione delle password, oltre che prevedere una strategia per la gestione degli attacchi di forza bruta.

## Configurare la tabella degli account

Poiché si sta usando l'autenticazione personalizzata senza affidarsi a un altro provider di identità, sarà necessario archiviare le informazioni di accesso degli utenti. In questa sezione verrà creata una tabella per gli account e verranno configurati i meccanismi di sicurezza basilari. La tabella degli account conterrà i nomi utenti e le password con salting e hashing e, se necessario, sarà possibile includervi anche informazioni aggiuntive sugli utenti.

1. Nella cartella **DataObjects** del progetto back-end aggiungere una nuova identità denominata `Account`.

2. Aggiungere l'istruzione `using` seguente:

		using Microsoft.WindowsAzure.Mobile.Service;

3. Sostituire la definizione della classe con il codice seguente:

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }

    Rappresenta una riga in una nuova tabella dell'account, contenente il nome utente, il salt dell'utente e la password archiviata in modo sicuro.

2. Nella cartella **Modelli** sarà presente una classe derivata da **DbContext** denominata come il servizio mobile. Aprire il contesto e aggiungere la tabella degli account al modello dati, includendo il codice seguente:

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]Nei frammenti di codice in questa esercitazione viene usato `todoContext` come nome del contesto. È necessario aggiornare i frammenti di codice per il contesto del progetto. Successivamente verranno configurate le funzioni di sicurezza per usare questi dati.

5. Creare una classe denominata `CustomLoginProviderUtils` e aggiungervi l'istruzione `using` seguente:

		using System.Security.Cryptography;

6. Aggiungere alla nuova classe i metodi di codice seguenti:


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

	In questo modo sarà possibile generare un nuovo salt lungo, eseguire l'hashing di una password con salt e disporre di un modo sicuro per confrontare due hash.

## Creare l'endpoint di registrazione

A questo punto, si ha tutto quanto necessario per iniziare a creare gli account utente. In questa sezione verrà configurato un endpoint di registrazione per gestire nuove richieste di registrazione. È in questa fase che verranno applicati nuovi criteri di nome utente e password e ci si assicurerà che il nome utente non sia già stato scelto da altri. Quindi, le informazioni utente verrà archiviate in modo sicuro nel database.

1. Creare un la nuova classe riportata di seguito per rappresentare un tentativo di registrazione in arrivo:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Se è necessario raccogliere e archiviare altre informazioni durante la registrazione, è consigliabile farlo qui.

2. Nel progetto back-end del servizio mobile fare clic con il pulsante destro del mouse su **Controller**, fare clic su **Aggiungi** e quindi su **Controller**, creare un nuovo **Controller personalizzato di Servizi mobili di Microsoft Azure** denominato `CustomRegistrationController` e infine aggiungere le istruzioni `using` seguenti:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	Nel codice precedente sostituire il segnaposto con lo spazio dei nomi del progetto.

4. Sostituire la definizione della classe con il codice seguente:

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomRegistrationController : ApiController
	    {
	        public ApiServices Services { get; set; }

	        // POST api/CustomRegistration
	        public HttpResponseMessage Post(RegistrationRequest registrationRequest)
	        {
	            if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
	            }
	            else if (registrationRequest.password.Length < 8)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
	            }

	            todoContext context = new todoContext();
	            Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
	            }
	            else
	            {
	                byte[] salt = CustomLoginProviderUtils.generateSalt();
	                Account newAccount = new Account
	                {
	                    Id = Guid.NewGuid().ToString(),
	                    Username = registrationRequest.username,
	                    Salt = salt,
	                    SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
	                };
	                context.Accounts.Add(newAccount);
	                context.SaveChanges();
	                return this.Request.CreateResponse(HttpStatusCode.Created);
	            }
	        }
	    }

    Ricordare di sostituire la variabile *todoContext* con il nome della classe **DbContext** del progetto. Si noti che questo controller usa l'attributo seguente per consentire tutto il traffico all'endpoint:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]Questo endpoint di registrazione è accessibile da qualsiasi client tramite HTTP. Prima di pubblicare il servizio in un ambiente di produzione, è necessario implementare una sorta di schema per convalidare le registrazioni, ad esempio la verifica basata su SMS o sulla posta elettronica. In questo modo si impedirà a utenti malintenzionati di creare registrazioni fraudolente.

## Creare la proprietà LoginProvider

Uno dei costrutti fondamentali nella pipeline di autenticazione di Servizi mobili è la proprietà **LoginProvider**. In questa sezione verrà creata una proprietà personalizzata, `CustomLoginProvider`. che non verrà inserita nella pipeline come i provider predefiniti, ma fornirà alcune comode funzionalità. Se si usa Visual Studio 2013, potrebbe essere necessario installare il pacchetto NuGet `WindowsAzure.MobileServices.Backend.Security` per aggiungere i riferimenti alla classe `LoginProvider`.

1. Creare una nuova classe `CustomLoginProvider` che deriva da **LoginProvider** e aggiungere le istruzioni `using` seguenti:

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;

3. Sostituire la definizione della classe **CustomLoginProvider** con il codice seguente:

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       Se si tenta di compilare il progetto a questo punto, si verificherà un errore. Per `LoginProvider` è necessario implementare tre metodi astratti. Tale operazione verrà eseguita più avanti.

2. Creare una nuova classe denominata `CustomLoginProviderCredentials` nello stesso file di codice.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	Rappresenta le informazioni sull'utente e sarà resa disponibile all'utente nel back-end tramite [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx). Se si aggiungono attestazioni personalizzate, assicurarsi che vengano acquisite in questo oggetto.

3. Aggiungere l'implementazione seguente del metodo astratto `ConfigureMiddleware` a **CustomLoginProvider**.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	Questo metodo non viene implementato perché **CustomLoginProvider** non si integra nella pipeline di autenticazione.

4. Aggiungere l'implementazione seguente del metodo astratto `ParseCredentials` a **CustomLoginProvider**.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	Questo metodo consentirà di deserializzare sul back-end le informazioni utente contenute in un token di autenticazione in arrivo.

5. Aggiungere l'implementazione seguente del metodo astratto `CreateCredentials` a **CustomLoginProvider**.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

	Questo metodo converte un oggetto [ClaimsIdentity] in un oggetto [ProviderCredentials] usato nella fase di rilascio di un token di autenticazione. È nuovamente consigliabile acquisire le eventuali attestazioni aggiuntive in questo metodo.

6. Aprire il file di progetto WebApiConfig.cs nella cartella App\_Start. La riga di codice seguente verrà creata dopo **ConfigOptions**:

		options.LoginProviders.Add(typeof(CustomLoginProvider));



## Creare l'endpoint di accesso

A questo punto sarà possibile creare un endpoint al quale gli utenti potranno accedere. Il nome utente e la password ricevuti verranno verificati rispetto al database applicando in primo luogo il salt dell'utente, eseguendo l'hashing della password e verificando che il valore in arrivo corrisponda a quello del database. In caso affermativo, si potrà creare una proprietà [ClaimsIdentity] e passarla a **CustomLoginProvider**. L'app client riceve un ID utente e un token di autenticazione per l'ulteriore accesso al servizio mobile.

1. Nel progetto back-end del servizio mobile creare la nuova classe `LoginRequest` seguente:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	Questa classe rappresenta un tentativo di accesso in arrivo.

2. Creare la nuova classe `CustomLoginResult` seguente:

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }

	    }

	Questa classe rappresenta un accesso riuscito con l'ID utente e il token di autenticazione. Si noti che questa classe ha la stessa forma della classe MobileServiceUser sul client, rendendo più semplice il passaggio della risposta di accesso su un client fortemente tipizzato.

2. Fare clic con il pulsante destro del mouse su **Controller**, fare clic su **Aggiungi** e quindi su **Controller**, creare un nuovo **Controller personalizzato di Servizi mobili di Microsoft Azure** denominato `CustomLoginController` e infine aggiungere le istruzioni `using` seguenti:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. Sostituire la definizione della classe **CustomLoginController** con il codice seguente:

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }

	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);

	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       Ricordare di sostituire la variabile *todoContext* con il nome della classe **DbContext** del progetto. Si noti che questo controller usa l'attributo seguente per consentire tutto il traffico all'endpoint:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT] Il `CustomLoginController` per l'uso in produzione dovrebbe contenere anche una strategia di rilevamento di forza bruta. In caso contrario, la soluzione di accesso potrebbe essere vulnerabile agli attacchi.

## Configurare il servizio mobile in modo che richieda l'autenticazione

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## Testare il flusso di accesso con il client di prova

Nell'applicazione client sarà necessario sviluppare una schermata di accesso personalizzata che accetta nomi utente e password e li invia come payload JSON agli endpoint di registrazione e accesso. Per completare questa esercitazione verrà usato invece solo il client di prova predefinito per il back-end .NET di Servizi mobili.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio mobile, quindi fare clic su **Debug** e su **Avvia nuova istanza**.

	Verrà avviata una nuova istanza di debug del progetto back-end del servizio mobile. Dopo il corretto avvio del servizio, verrà visualizzata una pagina iniziale con il messaggio **Questo servizio mobile è attivo e in esecuzione**.

2. Nella pagina di avvio del servizio fare clic su **Prova**, quindi digitare la password impostata per l'impostazione di app **MS\_ApplicationKey** nel file web.config con un nome utente vuoto nella finestra di dialogo di autenticazione.

3. Nella pagina della guida fare clic sull'endpoint **CustomRegistration** e quindi su **Prova**.

    ![][2]

4. Nel corpo sostituire le stringhe di esempio con un nome utente e una password che soddisfino i criteri specificati in precedenza e quindi fare clic su **Invia**.

    ![][3]

	La risposta dovrebbe essere **201/Created**.

5. Fare clic sul pulsante Indietro del browser e ripetere i passaggi 2 e 3 per l'endpoint **CustomLogin**, usando lo stesso nome utente e la stessa password registrati nel passaggio precedente.

    ![][4]

	Si dovrebbe ricevere un messaggio di risposta con un corpo contenente un oggetto JSON **user** con i valori *userId* e *authenticationToken*, che è il token di autenticazione di Servizi mobili generato dall'autenticazione personalizzata. Questo token è sufficiente per concedere all'app client l'accesso all'endpoint TodoItem.

	Creare una copia del valore *authenticationToken*. Questo valore verrà usato per accedere all'endpoint TodoItem con restrizioni.

6. Fare clic sul pulsante Indietro del browser, quindi nella pagina di documentazione sull'API fare clic su **GetTables** e quindi su **Prova**.

7. Nella finestra di dialogo della richiesta GET fare clic sul segno più accanto a **Intestazioni**, digitare il valore `X-ZUMO-AUTH` nella casella a sinistra, incollare il valore *authenticationToken* copiato nella casella di destra e quindi fare clic su **Invia**.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	Il servizio mobile dovrebbe concedere l'accesso all'endpoint e restituire lo stato **200/OK** e un elenco degli elementi TodoItems nella tabella.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT] Se si sceglie di pubblicare anche questo progetto del servizio mobile in Azure a scopo di test, ricordare che i provider di accesso e autenticazione saranno vulnerabili agli attacchi. Assicurarsi che siano finalizzati in modo corretto o che la protezione dei dati di test non sia importante per l'utente. Prestare grande attenzione prima di usare uno schema di autenticazione personalizzato per proteggere un servizio di produzione.

## Accedere usando l'autenticazione personalizzata dal client

Questa sezione descrive i passaggi necessari per accedere agli endpoint di autenticazione personalizzati dal client in modo da ottenere il token di autenticazione necessario per l'accesso al servizio mobile. Poiché il codice client necessario dipende dal client, le indicazioni fornite di seguito sono indipendenti dalla piattaforma.

>[AZURE.NOTE] Le librerie client di Servizi mobili comunicano con il servizio tramite HTTPS. Poiché questa soluzione richiede l'invio di password come testo non crittografato, è necessario assicurarsi di usare HTTPS quando si chiamano questi endpoint tramite richieste REST dirette.

1. Creare gli elementi dell'interfaccia utente necessari nell'app client per consentire agli utenti di immettere un nome utente e una password.

2. Usare il metodo appropriato **invokeApi** nella classe **MobileServiceClient** nella libreria client per chiamare l'endpoint **CustomRegistration**, passando il nome utente e la password forniti dal runtime nel corpo del messaggio.

	È necessario chiamare l'endpoint **CustomRegistration** solo una volta per creare un account per un determinato utente, purché si mantengano le informazioni di accesso utente nella tabella degli account. Per esempi relativi alla chiamata di un'API personalizzata nelle varie piattaforme client supportate, vedere l'articolo relativo all'uso di un'[API personalizzata negli SDK dei client per Servizi mobili di Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

	> [AZURE.IMPORTANT] Poiché questo passaggio di provisioning utente viene eseguito solo una volta, è consigliabile creare l'account utente in modalità fuori banda. Per un endpoint di registrazione pubblico è consigliabile anche implementare un processo di verifica basato su SMS o e-mail o un'altra misura di protezione per impedire la generazione di account fraudolenti. È possibile usare Twilio per inviare messaggi SMS da Servizi mobili. È anche possibile usare SendGrid per inviare e-mail da Servizi mobili. Per altre informazioni sull’utilizzo di SendGrid, vedere l'articolo relativo all'[invio di e-mail da Servizi mobili con SendGrid](store-sendgrid-mobile-services-send-email-scripts.md).

3. Usare nuovamente il metodo appropriato **invokeApi**, questa volta per chiamare l'endpoint **CustomLogin**, passando il nome utente e la password forniti dal runtime nel corpo del messaggio.

	Questa volta è necessario acquisire i valori *userId* e *authenticationToken* restituiti nell'oggetto response dopo aver eseguito correttamente l'accesso.

4. Usare i valori *userId* e *authenticationToken* restituiti per creare un nuovo oggetto **MobileServiceUser** e impostarlo come utente corrente per l'istanza di **MobileServiceClient**, come illustrato nell'argomento [Aggiungere l'autenticazione all'app di Servizi mobili di Azure esistente](mobile-services-dotnet-backend-ios-get-started-users.md). Poiché il risultato di CustomLogin ha la stessa forma dell'oggetto **MobileServiceUser**, dovrebbe essere possibile eseguire il cast diretto del risultato.

L'esercitazione è terminata.


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Aggiungere l'autenticazione all'app di Servizi mobili]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx

<!---HONumber=AcomDC_0727_2016-->