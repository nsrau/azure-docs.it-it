---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095488"
---
### <a name="create-a-web-project"></a>Creare un progetto Web

1. In **Visual Studio** selezionare **File** > **Nuova soluzione**.

1. Selezionare **.NET Core** > **App** > **ASP.NET Core** > **API** > **Avanti**.
  
1. Nella finestra di dialogo **Configura la nuova API Web ASP.NET Core** selezionare **.NET Core 3.1** in **Framework di destinazione**.

1. Immettere *PushDemoApi* in **Nome progetto** e quindi selezionare **Crea**.

1. Avviare il debug (**CMD** + **INVIO**) per testare l'app basata su modello.

    > [!NOTE]
    > L'app basata su modello è configurata per l'uso di **WeatherForecastController** come *launchUrl*. L'impostazione viene eseguita in **Proprietà** > **launchSettings.json**.  
    >
    > Se viene visualizzato il messaggio **Il certificato di sviluppo trovato non è valido**:
    >
    > 1. Fare clic su **Sì** per consentire l'esecuzione dello strumento "dotnet dev-certs https" per risolvere il problema. Lo strumento "dotnet dev-certs https" chiede quindi di immettere una password per il certificato e la password del keychain.
    >
    > 1. Fare clic su **Sì** quando viene chiesto di **installare e considerare attendibile il nuovo certificato**, quindi immettere la password del Keychain.

1. Espandere la cartella **Controllers**, quindi eliminare **WeatherForecastController.cs**.

1. Eliminare **WeatherForecast.cs**.

1. Premere **CTRL** + **clic** sul progetto **PushDemoApi**, quindi scegliere **Nuovo file** dal menu **Aggiungi**.

1. Configurare i valori di configurazione locali usando lo [strumento Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager). La separazione dei segreti dalla soluzione assicura che non finiscano nel controllo del codice sorgente. Aprire il **Terminale** e quindi passare alla directory del file di progetto ed eseguire i comandi seguenti:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Sostituire i valori segnaposto con il nome dell'hub di notifica e i valori della stringa di connessione. Ne è stata presa nota nella sezione [Creare un hub di notifica](#create-a-notification-hub). Se non è stato fatto, è possibile cercarli in [Azure](https://portal.azure.com).

    **NotificationsHub:Name**:  
    Vedere *Nome* nel riepilogo **Informazioni di base** all'inizio della finestra **Panoramica**.  

    **NotificationHub:ConnectionString**:  
    Vedere *DefaultFullSharedAccessSignature* in **Criteri di accesso**

    > [!NOTE]
    > Per gli scenari di produzione è possibile usare opzioni come [Azure Key Vault](https://azure.microsoft.com/services/key-vault) per archiviare la stringa di connessione in modo sicuro. Per semplicità, i segreti verranno aggiunti alle impostazioni dell'applicazione [Servizio app di Azure](https://azure.microsoft.com/services/app-service/).

### <a name="authenticate-clients-using-an-api-key-optional"></a>Autenticare i client usando una chiave API (facoltativo)

Le chiavi API non sono sicure come i token, ma sono sufficienti ai fini di questa esercitazione. Una chiave API può essere configurata facilmente tramite il [middleware ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Aggiungere la **chiave API** ai valori di configurazione locali.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Sostituire il valore segnaposto con il valore effettivo e prenderne nota.

1. Premere**CTRL** + **clic** sul progetto **PushDemoApi**, scegliere **Nuova cartella** dal menu **Aggiungi**, quindi fare clic su **Aggiungi** e specificare *Authentication* in **Nome cartella**.

1. Premere **CTRL** + **clic** sulla cartella **Authentication**, quindi scegliere **Nuovo file** dal menu **Aggiungi**.

1. Selezionare **Generale** > **Classe vuota**, immettere *ApiKeyAuthOptions.cs* in **Nome**, quindi fare clic su **Nuovo** e aggiungere l'implementazione seguente.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Aggiungere alla cartella **Authentication** un'altra **classe vuota** denominata *ApiKeyAuthHandler.cs*, quindi aggiungere l'implementazione seguente.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > Un [gestore di autenticazione](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) è un tipo che implementa il comportamento di uno schema, in questo caso uno schema di chiave API personalizzato.

1. Aggiungere alla cartella **Authentication** un'altra **classe vuota** denominata *ApiKeyAuthenticationBuilderExtensions.cs*, quindi aggiungere l'implementazione seguente.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Questo metodo di estensione semplifica il codice di configurazione del middleware in **Startup.cs**, rendendolo più leggibile e generalmente più semplice da seguire.

1. In **Startup.cs** aggiornare il metodo **ConfigureServices** per configurare l'autenticazione della chiave API sotto la chiamata al metodo **services.AddControllers**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Sempre in **Startup.cs** aggiornare il metodo **Configure** per chiamare i metodi di estensione **UseAuthentication** e **UseAuthorization** sull'interfaccia **IApplicationBuilder** dell'app. Assicurarsi che questi metodi vengano chiamati dopo **UseRouting** e prima di **app.UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Chiamando **UseAuthentication** viene registrato il middleware che usa gli schemi di autenticazione registrati in precedenza (da **ConfigureServices**). Questo metodo deve essere chiamato prima di qualsiasi middleware che dipende dagli utenti da autenticare.

### <a name="add-dependencies-and-configure-services"></a>Aggiungere le dipendenze e configurare i servizi

ASP.NET Core supporta lo schema progettuale di software per l'[inserimento di dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1), una tecnica per ottenere l'[IoC (Inversion of Control)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.  

L'uso dell'hub di notifica e dell'[SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)è incapsulato in un servizio. Il servizio viene registrato e reso disponibile mediante un'astrazione idonea.

1. Premere **CTRL** + **clic** sulla cartella **Dependencies** e quindi scegliere **Gestisci pacchetti NuGet**.

1. Cercare **Microsoft.Azure.NotificationHubs** e assicurarsi che sia selezionato.

1. Fare clic su **Aggiungi pacchetti**, quindi fare clic su **Accetta** quando viene chiesto di accettare le condizioni di licenza.

1. Premere**CTRL** + **clic** sul progetto **PushDemoApi**, scegliere **Nuova cartella** dal menu **Aggiungi**, quindi fare clic su **Aggiungi** e specificare *Models* in **Nome cartella**.

1. Premere **CTRL** + **clic** sulla cartella **Models**, quindi scegliere **Nuovo file** dal menu **Aggiungi**.

1. Selezionare **Generale** > **Classe vuota**, immettere *PushTemplates.cs* in **Nome**, quindi fare clic su **Nuovo** e aggiungere l'implementazione seguente.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Questa classe contiene i payload di notifica in formato token per le notifiche generiche e automatiche richieste da questo scenario. I payload sono definiti al di fuori dell'[installazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) per consentire la sperimentazione senza dover aggiornare le installazioni esistenti tramite il servizio. Questa modalità di gestione delle modifiche alle installazioni non rientra nell'ambito di questa esercitazione. Per gli scenari di produzione, prendere in considerazione i [modelli personalizzati](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

1. Selezionare **Generale** > **Classe vuota**, immettere *DeviceInstallation.cs* in **Nome**, quindi fare clic su **Nuovo** e aggiungere l'implementazione seguente.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Aggiungere alla cartella **Models** un'altra **classe vuota** denominata *NotificationRequest.cs*, quindi aggiungere l'implementazione seguente.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Aggiungere alla cartella **Models** un'altra **classe vuota** denominata *NotificationHubOptions.cs*, quindi aggiungere l'implementazione seguente.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Aggiungere al progetto **PushDemoApi** una nuova cartella denominata *Services*.

1. Aggiungere alla cartella **Services** un'**interfaccia vuota** denominata *INotificationService.cs*, quindi aggiungere l'implementazione seguente.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Aggiungere alla cartella **Services** una **classe vuota** denominata *NotificationHubsService.cs*, quindi aggiungere il codice seguente per implementare l'interfaccia **INotificationService**:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > L'espressione tag fornita a **SendTemplateNotificationAsync** è limitata a 20 tag. È limitata a 6 per la maggior parte degli operatori, ma in questo caso l'espressione contiene solo operatori OR (| |). Se la richiesta contiene più di 20 tag, è necessario suddividerla in più richieste. Per altre informazioni, vedere [Espressioni di routing e tag](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396).

1. In **Startup.cs** aggiornare il metodo **ConfigureServices** per aggiungere **NotificationHubsService** come implementazione singleton di **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Creare l'API Notifiche

1. Premere **CTRL** + **clic** sulla cartella **Controllers**, quindi scegliere **Nuovo file** dal menu **Aggiungi**.

1. Selezionare **ASP.NET Core** > **Classe controller API Web**, immettere *NotificationsController* in **Nome**, quindi fare clic su **Nuovo**.

1. Aggiungere gli spazi dei nomi seguenti all'inizio del file.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Aggiornare il controller basato su modello in modo che derivi da **ControllerBase** e sia decorato con l'attributo **ApiController**.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > La classe di base **Controller** fornisce il supporto per le viste, che non è però necessario in questo caso, quindi è possibile usare **ControllerBase** al suo posto.

1. Se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional), si dovrebbe anche decorare **NotificationsController** con l'attributo **Authorize**.

    ```cs
    [Authorize]
    ```

1. Aggiornare il costruttore in modo che accetti l'istanza registrata di **INotificationService** come argomento e la assegni a un membro di sola lettura.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. In **launchSettings.json** (all'interno della cartella **Properties**) impostare **launchUrl** da `weatherforecast` a *api/notifications* in modo che corrisponda all'URL specificato nell'attributo **Route** di **RegistrationsController**.

1. Avviare il debug (**CMD** + **INVIO**) per verificare che l'app usi il nuovo **NotificationsController** e restituisca uno stato **401 Non autorizzato**.

    > [!NOTE]
    > Visual Studio non può avviare automaticamente l'app nel browser. D'ora in avanti si userà [Postman](https://www.postman.com/downloads) per testare l'API.

1. In una nuova scheda di **[Postman](https://www.postman.com/downloads)** impostare la richiesta su **GET** e immettere l'indirizzo seguente.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > L'indirizzo di localhost deve corrispondere al valore di **applicationUrl** in **Properties** > **launchSettings.json**. Il valore predefinito dovrebbe essere `https://localhost:5001;http://localhost:5000`, che è tuttavia da verificare se si riceve una risposta 404.

1. Se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional), assicurarsi di configurare le intestazioni della richiesta in modo da includere il valore di **apikey**.

   | Chiave                            | Valore                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <chiave_api>                 |

1. Fare clic sul pulsante **Send** (Invia).

    > [!NOTE]
    > Si dovrebbe ricevere uno stato **200 OK** con del contenuto **JSON**.
    >
    > Se si riceve un avviso di **verifica dei certificati SSL**, è possibile disattivare l'impostazione di richiesta di verifica dei certificati SSL di **[Postman](https://www.postman.com/downloads)** in **Settings** (Impostazioni).

1. Sostituire i metodi della classe basata su modello con il codice seguente.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Creare l'app per le API

Si creerà ora un'[app per le API](https://azure.microsoft.com/services/app-service/api/) in [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/) per l'hosting del servizio back-end.  

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Fare clic su **Crea una risorsa**, quindi cercare e scegliere **App per le API** e fare clic su **Crea**.

1. Aggiornare i campi seguenti, quindi fare clic su **Crea**.

    **Nome app:**  
    Immettere un nome univoco globale per **App per le API**

    **Sottoscrizione:**  
    Scegliere la stessa **sottoscrizione** di destinazione in cui è stato creato l'hub di notifica.

    **Gruppo di risorse:**  
    Scegliere lo stesso **gruppo di risorse** in cui è stato creato l'hub di notifica.

    **Piano di servizio app/Località:**  
    Crea un nuovo **piano di servizio app**  

    > [!NOTE]
    > Passare dall'opzione predefinita a un piano che includa il supporto di **SSL**. In caso contrario, sarà necessario eseguire i passaggi appropriati quando si usa l'app per dispositivi mobili per evitare che le richieste **http** vengano bloccate.

    **Application Insights:**  
    Mantenere l'opzione suggerita (verrà creata una nuova risorsa con quel nome) o selezionare una risorsa esistente.

1. Dopo il provisioning dell'**app per le API**, passare a tale risorsa.

1. Prendere nota della proprietà **URL** nel riepilogo **Informazioni di base** all'inizio della pagina **Panoramica**. Questo URL è l'*endpoint back-end* che verrà usato più avanti in questa esercitazione.

    > [!NOTE]
    > L'URL usa il nome dell'app per le API specificato prima, con il formato `https://<app_name>.azurewebsites.net`.

1. Selezionare **Configurazione** nell'elenco (sotto **Impostazioni**).  

1. Per ognuna delle impostazioni seguenti fare clic su **Nuova impostazione applicazione** per compilare i campi **Nome** e **Valore**, quindi fare clic su **OK**.

   | Nome                               | Valore                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <valore_chiave_api>                |
   | `NotificationHub:Name`             | <valore_nome_hub>               |
   | `NotificationHub:ConnectionString` | <valore_stringa_connessione_hub>  |

   > [!NOTE]
   > Sono le stesse impostazioni definite in precedenza nelle impostazioni utente. Dovrebbe essere possibile copiarle. L'impostazione **Authentication:ApiKey** è obbligatoria solo se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional). Per gli scenari di produzione è possibile usare opzioni come [Azure Key Vault](https://azure.microsoft.com/services/key-vault). Queste opzioni sono state aggiunte come impostazioni dell'applicazione per semplicità in questo caso.

1. Dopo aver aggiunto tutte le impostazioni dell'applicazione, fare clic su **Salva**, quindi su **Continua**.

### <a name="publish-the-backend-service"></a>Pubblicare il servizio back-end

L'app verrà ora distribuita nell'app per le API per renderla accessibile da tutti i dispositivi.  

1. Impostare la configurazione da **Debug** a **Rilascio**, se non è già stato fatto.

1. Premere **CTRL** + **clic** sul progetto **PushDemoApi** e quindi scegliere **Pubblica in Azure** dal menu **Pubblica**.

1. Se richiesto, seguire il flusso di autenticazione. Usare l'account usato nella sezione precedente [Creare l'app per le API](#create-the-api-app).

1. Selezionare dall'elenco l'**app per le API del servizio app di Azure** creata in precedenza come destinazione della pubblicazione e quindi fare clic su **Pubblica**.

Al termine della procedura guidata, l'app viene pubblicata in Azure e aperta. Prendere nota dell'**URL**, se non è già stato fatto. Questo URL è l'*endpoint back-end* usato più avanti in questa esercitazione.

### <a name="validating-the-published-api"></a>Convalida dell'API pubblicata

1. In **[Postman](https://www.postman.com/downloads)** aprire una nuova scheda, impostare la richiesta su **POST** e immettere l'indirizzo seguente. Sostituire il segnaposto con l'indirizzo di base di cui si è preso nota nella sezione precedente [Pubblicare il servizio back-end](#publish-the-backend-service).

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > L'indirizzo di base deve essere in formato ``https://<app_name>.azurewebsites.net/``

1. Se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional), assicurarsi di configurare le intestazioni della richiesta in modo da includere il valore di **apikey**.

   | Chiave                            | valore                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <chiave_api>                 |

1. Scegliere l'opzione **raw** (non elaborato) per il **corpo**, quindi scegliere **JSON** dall'elenco delle opzioni di formato e includere del contenuto **JSON** segnaposto:

    ```json
    {}
    ```

1. Fare clic su **Send**.

    > [!NOTE]
    > Si dovrebbe ricevere uno stato **400 Richiesta non valida** dal servizio.

1. Ripetere i passaggi da 1 a 4, specificando questa volta l'endpoint delle richieste per verificare di ricevere la stessa risposta **400 Richiesta non valida**.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Non è ancora possibile testare l'API usando dati di richiesta validi, in quanto questa operazione richiede informazioni specifiche della piattaforma dall'app per dispositivi mobili client.
