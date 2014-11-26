<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, Azure messaging, secure push" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"/>

# Push sicuro degli hub di notifica di Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/it-it/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal" class="current">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
        <a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

Il supporto per le notifiche push in Microsoft Azure consente di accedere a un'infrastruttura push di facile utilizzo, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili.

A causa di vincoli normativi o di sicurezza, un'applicazione potrebbe talvolta includere nella notifica informazioni che non è possibile trasmettere attraverso l'infrastruttura di notifiche push standard. In questa esercitazione viene descritto come conseguire la stessa esperienza inviando informazioni sensibili attraverso una connessione autenticata e sicura tra il dispositivo client e il back-end dell'app.

A livello generale, il flusso è il seguente:

1.  Il back-end dell'app:

    -   Archivia il payload sicuro nel database back-end.
    -   Invia l'ID di questa notifica al dispositivo (non vengono inviate informazioni sicure).

2.  L'app sul dispositivo, quando riceve la notifica:

    -   Il dispositivo contatta il back-end richiedendo il payload sicuro.
    -   L'app può indicare il payload come una notifica sul dispositivo.

È importante notare che nel flusso precedente (e in questa esercitazione) si presume che il dispositivo archivi un token di autenticazione nell'archiviazione locale, dopo che l'utente ha eseguito l'accesso. Ciò garantisce un'esperienza del tutto uniforme, in quanto il dispositivo può recuperare il payload sicuro della notifica usando questo token. Se invece l'applicazione non archivia i token di autenticazione nel dispositivo o se questi hanno una scadenza, l'app per dispositivo, alla ricezione della notifica, dovrà visualizzare una notifica generica in cui si richiede all'utente di avviare l'app. L'app autentica quindi l'utente e mostra il payload di notifica.

In questa esercitazione sul push sicuro viene illustrato come inviare una notifica push in modo sicuro. Poiché i passaggi qui descritti si basano sull'esercitazione **Utilizzo di Hub di notifica per inviare notifiche agli utenti**, sarà prima necessario completare i passaggi di quest'ultima.

> [AZURE.NOTE] In questa esercitazione si presuppone che sia stato creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (Windows Store)][Introduzione ad Hub di notifica (Windows Store)].
> Si noti inoltre che Windows Phone 8.1 richiede le credenziali di Windows (non di Windows Phone) e che le attività in background non funzionano in Windows Phone 8.0 o Silverlight 8.1. Per le applicazioni per Windows Store, è possibile ricevere le notifiche tramite un'attività in background solo se per l'app è abilitata la schermata di blocco (fare clic sulla casella di controllo nel manifesto dell'app).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modificare il progetto dell'app per Windows Phone

1.  Nel progetto **NotifyUserWindowsPhone** aggiungere il codice seguente al file App.xaml.cs per registrare l'attività di push in background. Aggiungere la riga di codice seguente alla fine del metodo `OnLaunched()`:

        RegisterBackgroundTask();

2.  Sempre nel file App.xaml.cs, aggiungere il codice seguente immediatamente dopo il metodo `OnLaunched()`:

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3.  Aggiungere le istruzioni `using` seguenti all'inizio del file App.xaml.cs:

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4.  Nel menu **File** di Visual Studio fare clic su **Salva tutto**.

## Creare il componente push in background

Il passaggio successivo consiste nella creazione del componente push in background.

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo di primo livello della soluzione (in questo caso **Solution SecurePush**), scegliere **Aggiungi** e quindi fare clic su **Nuovo progetto**.

2.  Espandere **Applicazioni Windows Store**, fare clic su **App per Windows Phone** e quindi su **Componente Windows Runtime (Windows Phone)**. Assegnare al progetto il nome **PushBackgroundComponent** e quindi fare clic su **OK** per creare il progetto.

    ![](./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png)

3.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **PushBackgroundComponent (Windows Phone 8.1)**, quindi scegliere **Aggiungi** e infine **Classe**. Assegnare alla nuova classe il nome **PushBackgroundTask.cs**. Fare clic su **Aggiungi** per generare la classe.

4.  Sostituire l'intero contenuto della definizione dello spazio dei nomi di **PushBackgroundComponent** con il codice seguente e sostituire il segnaposto `{back-end endpoint}` con l'endpoint back-end ottenuto durante la distribuzione del back-end:

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **PushBackgroundComponent (Windows Phone 8.1)**, quindi scegliere **Gestisci pacchetti NuGet**.

6.  Sul lato sinistro fare clic su **Online**.

7.  Nella **casella di ricerca** digitare **Client Http**.

8.  Nell'elenco risultati fare clic su **Librerie client HTTP Microsoft** e quindi su **Installa**. Completare l'installazione.

9.  Di nuovo nella **casella di ricerca** di NuGet digitare **Json.net**. Installare il pacchetto **Json.NET** e quindi chiudere la finestra di Gestione pacchetti NuGet.

10. Aggiungere le istruzioni `using` seguenti all'inizio del file **PushBackgroundTask.cs**:

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** nella cartella del progetto **NotifyUserWindowsPhone (Windows Phone 8.1)**, quindi scegliere **Aggiungi riferimento...**. Nella finestra di dialogo Gestione riferimenti selezionare la casella di controllo accanto a **PushBackgroundComponent**, quindi fare clic su **OK**.

12. In Esplora soluzioni fare doppio clic sul file **Package.appxmanifest** nel progetto **NotifyUserWindowsPhone (Windows Phone 8.1)**. In **Notifiche** impostare **Popup supportati** su **Sì**:

    ![][1]

13. Sempre nel file **Package.appxmanifest** fare clic sul menu **Dichiarazioni** nella parte superiore. Nell'elenco a discesa **Dichiarazioni disponibili** fare clic su **Attività di background** e quindi su **Aggiungi**.

14. Nel file **Package.appxmanifest**, in **Proprietà** selezionare **Notifica Push**.

15. Nel file **Package.appxmanifest**, in **Impostazioni app** digitare **PushBackgroundComponent.PushBackgroundTask** nel campo **Punto di ingresso**.

    ![][2]

16. Scegliere **Salva tutto** dal menu **File**.

## Esecuzione dell'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1.  In Visual Studio eseguire l'applicazione API Web **AppBackend**. Verrà visualizzata una pagina Web ASP.NET.

2.  In Visual Studio eseguire l'app per Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**. Verrà eseguito l'emulatore di Windows Phone e l'app verrà caricata automaticamente.

3.  Nell'interfaccia utente dell'app **NotifyUserWindowsPhone** immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore.

4.  Nell'interfaccia utente dell'app **NotifyUserWindowsPhone** fare clic su **Log in and register**. Fare clic su **Send push**.

  
  [1]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
  [2]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
