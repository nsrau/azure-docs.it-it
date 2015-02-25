<properties pageTitle="Introduzione all'autenticazione personalizzata | Mobile Dev Center" description="Informazioni su come autenticare gli utenti con un nome utente e una password." documentationCenter="windows" authors="mattchenderson" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="mahender"/>

# Introduzione all'autenticazione personalizzata

Questo argomento descrive come autenticare gli utenti nel back-end .NET di Servizi mobili di Azure rilasciando il proprio token di autenticazione di Servizi mobili. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite usando un nome utente e una password personalizzati per l'app.

>[AZURE.NOTE] Questa esercitazione illustra un metodo avanzato di autenticazione di Servizi mobili con credenziali personalizzate. Per molte app sarà invece più appropriato l'uso dei provider di identità sociale predefiniti, che consentono agli utenti di eseguire l'accesso con Facebook, Twitter, Google, Microsoft Account e Azure Active Directory. Se si tratta della prima esperienza di autenticazione con Servizi mobili, vedere l'esercitazione [Introduzione agli utenti].

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Configurare la tabella degli account]
2. [Creare l'endpoint di registrazione]
3. [Creare la proprietà LoginProvider]
4. [Creare l'endpoint di accesso]
5. [Configurare il servizio mobile in modo che richieda l'autenticazione]
6. [Testare il flusso di accesso con il client di prova]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

>[AZURE.NOTE] Lo scopo di questa esercitazione è mostrare come rilasciare un token di autenticazione per Servizi mobili. Non considerare il contenuto di questo articolo come linee guida per la sicurezza. Nello sviluppo dell'app è necessario essere al corrente delle implicazioni in termini di sicurezza riguardo l'archiviazione delle password, oltre che prevedere una strategia per la gestione degli attacchi di forza bruta.


## <a name="table-setup"></a>Configurare la tabella degli account

Poiché si sta usando l'autenticazione personalizzata senza affidarsi a un altro provider di identità, sarà necessario archiviare le informazioni di accesso degli utenti. In questa sezione verrà creata una tabella per gli account e verranno configurati i meccanismi di sicurezza basilari. La tabella degli account conterrà i nomi utenti e le password con salting e hashing e, se necessario, sarà possibile includervi anche informazioni aggiuntive sugli utenti.

1. Nella cartella  `DataObjects` del progetto back-end, creare una nuova entità denominata  `Account`:

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    Questa rappresenterà una riga nella nuova tabella e conterrà il nome utente, il salt di tale utente e la password archiviata in modo sicuro.

2. Nella cartella  `Models` sarà presente una classe  `DbContext` denominata come il proprio servizio mobile. Nella parte restante di questa esercitazione verrà usato  `todoContext` come esempio e sarà necessario aggiornare i frammenti di codice di conseguenza. Aprire il contesto e aggiungere la tabella degli account al modello dati, includendo il codice seguente:

        public DbSet<Account> Accounts { get; set; }

3. Quindi, configurare le funzioni di sicurezza per elaborare questi dati. Sarà necessario prevedere un mezzo per generare un nuovo salt lungo e un modo sicuro per confrontare due hash. Creare una classe denominata  `CustomLoginProviderUtils` e aggiungervi i metodi seguenti:


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


## <a name="register-endpoint"></a>Creare l'endpoint di registrazione

A questo punto, si ha tutto quanto necessario per iniziare a creare gli account utente. In questa sezione verrà configurato un endpoint di registrazione per gestire nuove richieste di registrazione. È in questa fase che verranno applicati nuovi criteri di nome utente e password e ci si assicurerà che il nome utente non sia già stato scelto da altri. Quindi, le informazioni utente verrà archiviate in modo sicuro nel database.

1. Creare un oggetto per rappresentare un tentativo di registrazione in arrivo:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Se si vogliono raccogliere altre informazioni al momento della registrazione, è possibile includerle in questa fase.

1. Nel progetto di back-end di Servizi mobili, aggiungere un nuovo controller personalizzato, denominato CustomRegistrationController, e incollarlo nel codice seguente:

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
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
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

    Assicurarsi di aver autorizzato tutto il traffico diretto a questo endpoint associando al controller il codice seguente:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>Creare la proprietà LoginProvider

Uno dei costrutti fondamentali nella pipeline di autenticazione di Servizi mobili è la proprietà  `LoginProvider`. In questa sezione verrà creata una proprietà  `CustomLoginProvider` personalizzata, che non verrà inserita nella pipeline come i provider predefiniti, ma fornirà alcune comode funzionalità.

1. Creare una nuova classe  `CustomLoginProvider`, che deriva da  `LoginProvider`:

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

       `LoginProvider` ha altri tre metodi astratti che verranno implementati in seguito.

2. Creare una nuova classe denominata  `CustomLoginProviderCredentials`, che rappresenta le informazioni sull'utente e sarà resa disponibile all'utente sul back-end tramite  `ServiceUser.getIdentitiesAsync()`. Se si aggiungono attestazioni personalizzate, assicurarsi che vengano acquisite in questo oggetto.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. Aggiungere l'implementazione seguente del metodo astratto  `ConfigureMiddleware` a  `CustomLoginProvider`. In questo caso, il metodo è no-op perché  `CustomLoginProvider` non si integra nella pipeline di autenticazione.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. Aggiungere l'implementazione seguente del metodo astratto  `ParseCredentials` a  `CustomLoginProvider`. Questo metodo consentirà di deserializzare sul back-end le informazioni utente contenute in un token di autenticazione in arrivo.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. Aggiungere l'implementazione seguente del metodo astratto  `CreateCredentials` a  `CustomLoginProvider`. Questo metodo traduce un  `ClaimsIdentity` in un oggetto  `ProviderCredentials` usato nella fase di rilascio del token di autenticazione. Sarà nuovamente opportuno acquisire qualsiasi attestazione aggiuntiva in questa fase.

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

## <a name="login-endpoint"></a>Creare l'endpoint di accesso

A questo punto sarà possibile creare un endpoint al quale gli utenti potranno accedere. Il nome utente e la password ricevuti verranno verificati rispetto al database applicando in primo luogo il salt dell'utente, eseguendo l'hashing della password e verificando che il valore in arrivo corrisponda a quello del database. In caso positivo, sarà possibile creare una proprietà  `ClaimsIdentity` e passarla a  `CustomLoginProvider`. L'app client riceverà quindi un ID utente e un token di autenticazione per l'ulteriore accesso al servizio mobile.

1. Nel progetto di back-end di Servizi mobili, creare un oggetto che rappresenti un tentativo di accesso in arrivo:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

1. Aggiungere un nuovo controller personalizzato, denominato  `CustomLoginController` e incollare il codice seguente:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    Assicurarsi di aver autorizzato tutto il traffico diretto a questo endpoint associando al controller il codice seguente:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.NOTE] Il  `CustomLoginController` per l'uso in produzione dovrebbe contenere anche una strategia di rilevamento di forza bruta. In caso contrario, la soluzione di accesso potrebbe essere vulnerabile agli attacchi.

## <a name="require-authentication"></a>Configurare il servizio mobile in modo che richieda l'autenticazione

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a>Testare il flusso di accesso con il client di prova

Nell'applicazione client, sarà necessario sviluppare una schermata di accesso personalizzato che accetta nomi utente e password, inviandoli come payload JSON agli endpoint di registrazione e accesso. Per completare questa esercitazione verrà usato invece solo il client di prova predefinito per il back-end .NET di Servizi mobili.

>[AZURE.NOTE] I componenti di Mobile Services SDK comunicheranno con il servizio tramite HTTPS. Se si pianifica di accedere a questo endpoint tramite una chiamata REST diretta è necessario assicurarsi di usare HTTPS per chiamare il servizio mobile, in quanto le password vengono inviate come testo non crittografato.

1. Per avviare una nuova istanza di debug del progetto di back-end di Servizi mobili in Visual Studio, fare clic con il pulsante destro del mouse sul progetto e selezionare **Debug -> Avvia nuova istanza**

    ![][0]

2. Fare clic su **Prova**

    ![][1]

3. Selezionare l'endpoint di registrazione. È possibile consultare la documentazione di base relativa all'API. Fare clic su **Prova**.

    ![][2]

4. Nel corpo, sostituire le stringhe di esempio con un nome utente e una password che soddisfino i criteri specificati prima, quindi fare clic su **Invia**. La risposta dovrebbe essere **201/Created**.

    ![][3]

5. Ripetere questo processo per l'endpoint di accesso. Dopo aver inviato lo stesso nome utente e password registrati in precedenza, si dovrebbero ricevere il proprio ID utente e un token di autenticazione.

    ![][4]


<!-- Anchors. -->
[Configurare la tabella degli account]: #table-setup
[Creare l'endpoint di registrazione]: #register-endpoint
[Creare la proprietà LoginProvider]: #login-provider
[Creare l'endpoint di accesso]: #login-endpoint
[Configurare il servizio mobile in modo che richieda l'autenticazione]: #require-authentication
[Testare il flusso di accesso con il client di prova]: #test-login


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Introduzione agli utenti]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started

<!--HONumber=42-->
