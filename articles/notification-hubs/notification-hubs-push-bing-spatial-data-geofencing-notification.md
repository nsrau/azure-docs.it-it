---
title: Inviare notifiche push con Hub di notifica di Azure e dati spaziali di Bing | Microsoft Docs
description: In questa esercitazione si apprenderà come recapitare le notifiche push in base alla posizione con Hub di notifica di Azure e i dati spaziali di Bing.
services: notification-hubs
documentationcenter: windows
keywords: notifiche push,notifiche push
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 510e2648db3076a0e3ee2535c6058f7ed212f558
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387490"
---
# <a name="tutorial-send-location-based-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Esercitazione: Inviare notifiche push in base alla posizione con Hub di notifica di Azure e i dati spaziali di Bing

In questa esercitazione si apprenderà come recapitare le notifiche push in base alla posizione con Hub di notifica di Azure e i dati spaziali di Bing.

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Configurare l'origine dati
> * Configurare l'applicazione UWP
> * Configurare il servizio back-end
> * Testare le notifiche push nelle app della piattaforma UWP (Universal Windows Platform)

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) o versione successiva ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* Versione più recente di [Azure SDK](https://azure.microsoft.com/downloads/).
* [Account Bing Maps Dev Center](https://www.bingmapsportal.com/) (è possibile crearne uno gratuitamente e associarlo all'account Microsoft).

## <a name="set-up-the-data-source"></a>Configurare l'origine dati

1. Accedere a [Bing Maps Dev Center](https://www.bingmapsportal.com/).
2. Sulla barra di spostamento in alto, selezionare **Origini dati** e quindi **Gestisci origini dati**.

    ![](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Se non si dispone di un'origine dati esistente, viene visualizzato un collegamento per creare un'origine dati. Selezionare **Carica dati come origine dati**. È anche possibile usare il menu **Origini dati** > **Carica dati**.

    ![](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Creare un file `NotificationHubsGeofence.pipe` sul disco rigido con il contenuto seguente: In questa esercitazione si usa un file di esempio basato su pipe che incornicia un'area del lungomare di San Francisco:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Il file pipe rappresenta questa entità:

    ![](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Nella pagina **Carica un'origine dati**, procedere come segue:
   1. Selezionare **pipe** per **Formato dati**.
   2. Individuare e selezionare il file `NotificationHubGeofence.pipe` creato nel passaggio precedente.
   3. Selezionare il pulsante **Carica**.

      > [!NOTE]
      > È possibile che venga richiesto di specificare una nuova chiave per **Master Key** (Chiave master) diversa da **Query Key** (Chiave di query). Creare semplicemente una nuova chiave tramite il dashboard e aggiornare la pagina di caricamento dell'origine dati.
6. Dopo aver caricato il file di dati, è necessario assicurarsi di pubblicare l'origine dati. Selezionare **Origini dati** -> **Gestisci origini dati** come in precedenza.
7. Selezionare l'origine dati nell'elenco e scegliere **Pubblica** nella colonna **Azioni**.

    ![](./media/notification-hubs-geofence/publish-button.png)
8. Passare alla scheda **Origini dati pubblicati** e verificare di visualizzare l'origine dati nell'elenco.

    ![](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Selezionare **Modifica**. Vengono visualizzate (riepilogo) le posizioni introdotte nei dati.

    ![](./media/notification-hubs-geofence/bing-maps-data-details.png)

    A questo punto, il portale non visualizza i limiti del recinto virtuale creato. È sufficiente avere la conferma che la posizione specificata sia in prossimità del punto corretto.
10. Ora sono disponibili tutti i requisiti per l'origine dati. Per altre informazioni sull'URL della richiesta per la chiamata API, in Bing Maps Dev Center fare clic su **Data sources** (Origini dati) e selezionare **Data Source Information** (Informazioni origine dati).

    ![](./media/notification-hubs-geofence/bing-maps-data-info.png)

    L'**URL query** su cui è possibile eseguire query per verificare se il dispositivo si trova o meno entro i limiti di una posizione. Per eseguire questa verifica è sufficiente eseguire una chiamata GET con l'URL della query, aggiungendo i parametri seguenti:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Bing Maps esegue automaticamente i calcoli per determinare se il dispositivo è all'interno del confine virtuale. Dopo l'esecuzione della richiesta tramite un browser (o cURL), si ottiene una risposta JSON standard:

    ![](./media/notification-hubs-geofence/bing-maps-json.png)

    Questa risposta viene restituita solo quando il punto è effettivamente entro i limiti designati. In caso contrario, si ottiene un bucket **results** vuoto:

    ![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Configurare l'applicazione UWP

1. In Visual Studio avviare un nuovo progetto di tipo **App vuota (Windows universale)** .

    ![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Una volta completata la creazione del progetto, saranno disponibili tutte le potenzialità per l'app stessa. Ora si procederà alla configurazione dell'intera struttura del recinto virtuale. Poiché a questo scopo si vogliono usare i servizi di Bing per questa soluzione, è disponibile un endpoint API REST pubblico che consente di eseguire query su frame di posizione specifici:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Per renderlo funzionante, specificare i parametri seguenti:

   * **ID origine dati** e **Nome origine dati**: nell'API Bing Maps le origini dati contengono diversi metadati suddivisi in bucket, ad esempio posizioni e orario di ufficio dell'operazione.  
   * **Nome entità** : l'entità che si vuole usare come punto di riferimento per la notifica.
   * **Chiave API di Bing Maps**: la chiave ottenuta in precedenza durante la creazione dell'account Bing Dev Center.

     Ora che l'origine dati è pronta, è possibile iniziare a usare l'applicazione UWP.
2. Abilitare i servizi di posizione per l'applicazione. Aprire il `Package.appxmanifest` file in **Esplora soluzioni**.

    ![](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Nella scheda delle proprietà del pacchetto appena aperta passare alla scheda **Funzionalità** e selezionare **Posizione**.

    ![](./media/notification-hubs-geofence/vs-package-location.png)
4. Creare una nuova cartella denominata `Core` nella soluzione e aggiungervi un nuovo file denominato `LocationHelper.cs`:

    ![](./media/notification-hubs-geofence/vs-location-helper.png)

    La classe `LocationHelper` include il codice per ottenere la posizione dell'utente tramite l'API di sistema:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Per altre informazioni su come ottenere la posizione dell'utente nelle app UWP, vedere [Ottenere la posizione dell'utente](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx).
5. Per verificare che l'acquisizione della posizione funzioni effettivamente, aprire il lato del codice della pagina principale (`MainPage.xaml.cs`). Creare un nuovo gestore eventi per l'evento `Loaded` nel costruttore `MainPage`.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    Ecco l'implementazione del gestore eventi:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Eseguire l'applicazione e consentire di accedere alla posizione.

    ![](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Una volta avviata l'applicazione, sarà possibile visualizzare le coordinate nella finestra **Output** :

    ![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Dopo aver stabilito che l'acquisizione della posizione funziona, è possibile rimuovere il gestore dell'evento per Loaded, perché non verrà più usato.
8. Il passaggio successivo consiste nell'acquisire le modifiche della posizione. Nella classe `LocationHelper` aggiungere il gestore dell'evento per `PositionChanged`:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    L'implementazione mostra le coordinate della posizione nella finestra **Output**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>Configurare il servizio back-end

1. Scaricare l' [esempio di back-end .NET da GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers).
2. Al termine del download, aprire la cartella `NotifyUsers` e successivamente il file `NotifyUsers.sln` in Visual Studio.
3. Impostare il progetto `AppBackend` come **Progetto di avvio** e avviarlo.

    ![](./media/notification-hubs-geofence/vs-startup-project.png)

    Il progetto è già configurato per l'invio di notifiche push ai dispositivi di destinazione, quindi sarà necessario eseguire solo due operazioni, ovvero specificare la stringa di connessione con quella corretta per l'hub di notifica e aggiungere l'identificazione del limite per inviare la notifica solo quando l'utente si trova all'interno del recinto virtuale.

4. Per configurare la stringa di connessione, nella cartella `Models` aprire `Notifications.cs`. La funzione `NotificationHubClient.CreateClientFromConnectionString` deve contenere le informazioni sull'hub di notifica che è possibile ottenere dal [portale di Azure](https://portal.azure.com), ovvero in **Impostazioni** nella pagina **Criteri di accesso**. Salvare il file di configurazione aggiornato.
5. Creare un modello per il risultato dell'API Bing Maps. Il modo più semplice è aprire la cartella `Models` e scegliere **Aggiungi** > **Classe**. Denominarlo `GeofenceBoundary.cs`. Al termine, copiare JSON dalla risposta API ottenuta nella prima sezione. In Visual Studio, usare **Modifica** > **Incolla speciale** > **Incolla JSON come classi**.

    In questo modo si assicura che l'oggetto viene deserializzato esattamente nel modo previsto. Il set di classi risultante sarà analogo alla classe seguente:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Aprire quindi `Controllers` > `NotificationsController.cs`. Aggiornare la chiamata Post per tenere conto della longitudine e latitudine della destinazione. A questo scopo, è sufficiente aggiungere due stringhe alla firma della funzione: `latitude` e `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Creare una nuova classe all'interno del progetto chiamata `ApiHelper.cs`. Verrà usata per connettersi a Bing per controllare le intersezioni dei limiti del punto. Implementare una funzione `IsPointWithinBounds` come illustrato nel codice seguente:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Assicurarsi di sostituire l'endpoint API con l'URL della query ottenuto in precedenza da Bing Dev Center (lo stesso vale per la chiave API).

    Se sono presenti risultati per la query, significa che il punto specificato si trova entro i limiti del recinto virtuale, quindi la funzione viene restituita `true`. Se non sono presenti risultati, significa che il punto è all'esterno del frame di ricerca, quindi la funzione restituisce `false`.
8. In `NotificationsController.cs` creare un controllo prima dell'istruzione switch:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Testare le notifiche push nell'app UWP

1. Nell'app UWP si sarà in grado di testare le notifiche. Nella classe `LocationHelper` creare una nuova funzione `SendLocationToBackend`:

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Impostare `POST_URL` nel percorso dell'applicazione web distribuita. Per ora è possibile eseguirla in locale, ma quando si lavora alla distribuzione di una versione pubblica, sarà necessario ospitarla presso un provider esterno.
2. Registrare l'app UWP per le notifiche push. In Visual Studio fare clic su **Progetto** > **Store** > **Associa applicazione a Store**.

    ![](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Dopo l'accesso con l'account per sviluppatore, assicurarsi di selezionare un'app esistente o crearne una nuova a cui si deve associare il pacchetto.
4. Usare Dev Center e aprire l'app creata. Fare clic su **Servizi** > **Notifiche push** > **Sito dei servizi Live**.

    ![](./media/notification-hubs-geofence/ms-live-services.png)
5. Nel sito prendere nota del **segreto dell'applicazione** e del **SID del pacchetto**. Saranno necessari entrambi nel portale di Azure. Aprire l'hub di notifica, fare clic su **Impostazioni** > **Servizi di notifica** > **Windows (WNS)** e immettere le informazioni nei campi obbligatori.

    ![](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Scegliere **Salva**.
7. In **Esplora soluzioni** aprire **Riferimenti** e scegliere **Gestisci pacchetti NuGet**. Aggiungere un riferimento alla **libreria gestita del bus di servizio di Microsoft Azure**. Cercare semplicemente `WindowsAzure.Messaging.Managed` e aggiungerlo al progetto.

    ![](./media/notification-hubs-geofence/vs-nuget.png)
8. A scopo di test, creare di nuovo il gestore eventi `MainPage_Loaded` e aggiungervi questo frammento di codice:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    Il codice registra l'app con l'hub di notifica. Ora si è pronti per iniziare.
9. In `LocationHelper`, all'interno del gestore `Geolocator_PositionChanged`, è possibile aggiungere un frammento di codice di test che inserirà la posizione all'interno del recinto virtuale in modo forzato:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Poiché non vengono passate le coordinate reali (è possibile che al momento non ci si trovi entro i limiti) e si usano valori di test predefiniti, al momento dell'aggiornamento verrà visualizzata una notifica:

    ![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile eseguire un paio di passaggi per assicurarsi che la soluzione sia pronta per la produzione.

1. Prima è necessario assicurarsi che i recinti virtuali siano dinamici. A questo scopo è necessario eseguire altre operazioni con l'API Bing per poter caricare nuovi limiti nell'origine dati esistente. Per altre informazioni, vedere [Documentazione dell'API dei servizi dati spaziali di Bing](https://msdn.microsoft.com/library/ff701734.aspx).
2. Dato che lo scopo è assicurarsi che le notifiche siano recapitate ai partecipanti corretti, è consigliabile usare l' [assegnazione di tag](notification-hubs-tags-segment-push-message.md).

La soluzione illustrata in questa esercitazione descrive uno scenario in cui è possibile avere un'ampia gamma di piattaforme di destinazione, quindi il recinto virtuale non limita le funzionalità specifiche del sistema. Ciò premesso, la piattaforma UWP (Universal Windows Platform) offre funzionalità per [rilevare automaticamente i recinti virtuali](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).
