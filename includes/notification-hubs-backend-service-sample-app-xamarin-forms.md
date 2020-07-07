---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448699"
---
### <a name="create-the-xamarinforms-solution"></a>Creare la soluzione Xamarin.Forms

1. In **Visual Studio** creare una nuova soluzione **Xamarin.Forms** usando **App Forms vuota** come modello e immettendo *PushDemo* in **Nome progetto**.

    > [!NOTE]
    > Nella finestra di dialogo **Configura App Forms vuota** verificare che il valore di **Identificatore dell'organizzazione** corrisponda al valore usato in precedenza e che siano selezionate entrambe le destinazioni **Android** e **iOS**.

1. Premere **CTRL** + **clic** sulla soluzione *PushDemo*, quindi scegliere **Aggiorna pacchetti NuGet**.
1. Premere **CTRL** + **clic** sulla soluzione *PushDemo* e quindi scegliere **Gestisci pacchetti NuGet**.
1. Cercare **Newtonsoft.Json** e verificare che sia selezionato.
1. Fare clic su **Aggiungi pacchetti**, quindi fare clic su **Accetta** quando viene chiesto di accettare le condizioni di licenza.
1. Compilare ed eseguire l'app su ogni piattaforma di destinazione (**CMD** + **INVIO**) per testare le esecuzioni dell'app basata su modello nei dispositivi.

### <a name="implement-the-cross-platform-components"></a>Implementare i componenti multipiattaforma

1. Premere**CTRL** + **clic** sul progetto **PushDemo**, scegliere **Nuova cartella** dal menu **Aggiungi**, quindi fare clic su **Aggiungi** e specificare *Models* in **Nome cartella**.

1. Premere **CTRL** + **clic** sulla cartella **Models**, quindi scegliere **Nuovo file** dal menu **Aggiungi**.

1. Selezionare **Generale** > **Classe vuota**, immettere *DeviceInstallation.cs*, quindi aggiungere l'implementazione seguente.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Aggiungere alla cartella **Models** una **enumerazione vuota** denominata *PushDemoAction.cs* con l'implementazione seguente.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Aggiungere al progetto **PushDemo** una nuova cartella denominata *Services*, quindi aggiungere alla cartella una **classe vuota** denominata *ServiceContainer.cs* con l'implementazione seguente.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > È una versione ridotta della classe [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) dal repository [XamCAT](https://github.com/xamcat/mobcat-library). Verrà usata come contenitore IoC (Inversion of Control) leggero.

1. Aggiungere alla cartella **Services** un'**interfaccia vuota** denominata *IDeviceInstallationService.cs*, quindi aggiungere il codice seguente.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Questa interfaccia verrà implementata e avviata tramite bootstrap da ogni destinazione in un secondo momento per fornire le funzionalità specifiche della piattaforma e le informazioni su **DeviceInstallation** richieste dal servizio back-end.

1. Aggiungere alla cartella **Services** un'altra **interfaccia vuota** denominata *INotificationRegistrationService.cs*, quindi aggiungere il codice seguente.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Questa interfaccia gestirà l'interazione tra il client e il servizio back-end.

1. Aggiungere alla cartella **Services** un'altra **interfaccia vuota** denominata *INotificationActionService.cs*, quindi aggiungere il codice seguente.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Questa interfaccia viene usata come meccanismo semplice per centralizzare la gestione delle azioni di notifica.

1. Aggiungere alla cartella **Services** un'**interfaccia vuota** denominata *IPushDemoNotificationActionService.cs* che deriva da *INotificationActionService*, con l'implementazione seguente.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Questo tipo è specifico dell'applicazione **PushDemo** e usa l'enumerazione **PushDemoAction** per identificare l'azione che viene attivata in modo fortemente tipizzato.

1. Aggiungere alla cartella **Services** una **classe vuota** denominata *NotificationRegistrationService.cs* che implementa l'interfaccia *INotificationRegistrationService* con il codice seguente.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > L'argomento **apiKey** è obbligatorio solo se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional).

1. Aggiungere alla cartella **Services** una **classe vuota** denominata *PushDemoNotificationActionService.cs* che implementa l'interfaccia *IPushDemoNotificationActionService* con il codice seguente.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Aggiungere al progetto **PushDemo** una **classe vuota** denominata *Config.cs* con l'implementazione seguente.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Questa classe viene usata come metodo semplice per mantenere i segreti fuori dal controllo del codice sorgente. È possibile sostituire questi valori come parte di una compilazione automatizzata o eseguirne l'override usando una classe parziale locale. Questa operazione verrà eseguita nel prossimo passaggio.
    >
    > Il campo **ApiKey** è obbligatorio solo se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional).

1. Aggiungere al progetto **PushDemo** un'altra **classe vuota** denominata *Config.local_secrets.cs* con l'implementazione seguente.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Sostituire i valori segnaposto con i propri. Si dovrebbe aver preso nota di questi valori durante la compilazione del servizio back-end. L'URL dell'**app per le API** dovrebbe essere ``https://<api_app_name>.azurewebsites.net/``. Ricordare di aggiungere ``*.local_secrets.*`` al file gitignore per evitare di eseguire il commit di questo file.
    >
    > Il campo **ApiKey** è obbligatorio solo se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional).

1. Aggiungere al progetto **PushDemo** una **classe vuota** denominata *Bootstrap.cs* con l'implementazione seguente.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > Il metodo **Begin** viene chiamato da ogni piattaforma quando l'app viene avviata passando un'implementazione specifica della piattaforma di **IDeviceInstallationService**.
    >
    > Il costruttore **NotificationRegistrationService** **apiKey** è obbligatorio solo se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional).

### <a name="implement-the-cross-platform-ui"></a>Implementare l'interfaccia utente multipiattaforma

1. Nel progetto **PushDemo** aprire **MainPage.xaml** e sostituire il controllo **StackLayout** con il codice seguente.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. In **MainPage.xaml.cs** aggiungere ora un campo sottostante **readonly** in cui archiviare un riferimento all'implementazione di **INotificationRegistrationService**.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. Nel costruttore **MainPage** risolvere l'implementazione di **INotificationRegistrationService** usando **ServiceContainer** e assegnarla al campo sottostante *_notificationRegistrationService_*.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implementare i gestori eventi per gli eventi **Clicked** dei pulsanti **RegisterButton** e **DeregisterButton** chiamando i metodi **Register**/**Deregister** corrispondenti.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. In **App.xaml.cs** verificare che gli spazi dei nomi seguenti siano referenziati.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implementare il gestore dell'evento **ActionTriggered** di **IPushDemoNotificationActionService**.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Nel costruttore **App** risolvere l'implementazione di **IPushNotificationActionService** usando **ServiceContainer** e sottoscrivere l'evento **ActionTriggered** di **IPushDemoNotificationActionService**.

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Serve semplicemente a dimostrare la ricezione e la propagazione delle azioni di notifica push. In genere queste azioni vengono gestite in modo automatico, ad esempio passando a una visualizzazione specifica o aggiornando alcuni dati anziché visualizzare un avviso tramite la classe **Page** radice, **MainPage** in questo caso.
