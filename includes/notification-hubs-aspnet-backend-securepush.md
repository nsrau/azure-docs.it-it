---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260777"
---
## <a name="webapi-project"></a>Progetto WebAPI

1. In Visual Studio aprire il progetto **AppBackend** creato nell'esercitazione **Notify Users** (Inviare notifiche agli utenti).
2. In Notifications.cs sostituire l'intera classe **Notifications** con il codice seguente. Assicurarsi di sostituire i segnaposto con la stringa di connessione con accesso completo per l'hub di notifica e il nome dell'hub. È possibile ottenere questi valori dal [portale di Azure](https://portal.azure.com). Questo modulo rappresenta ora le diverse notifiche sicure che verranno inviate. In un'implementazione completa le notifiche verranno archiviate in un database; per semplicità, in questo caso, verranno archiviate in memoria.
   
   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

1. In NotificationsController.cs sostituire il codice all'interno della definizione della classe **NotificationsController** con il codice seguente. Questo componente implementa un modo per il recupero della notifica da parte del dispositivo. Inoltre, ai fini di questa esercitazione, fornisce all'utente un modo per attivare un push sicuro ai propri dispositivi. Notare che a Hub di notifica verrà inviata una notifica non elaborata, che contiene l'ID della notifica senza alcun messaggio:
   
   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Notare che il metodo `Post` non invia ora una notifica di tipo avviso popup, ma una notifica non elaborata che contiene solo l'ID notifica e non contenuto sensibile. Assicurarsi inoltre di impostare come commento l'operazione send per le piattaforme per le quali non sono configurate le credenziali nell'hub di notifica, in caso contrario verranno restituiti errori.

1. L'app verrà ora nuovamente distribuita in un sito Web di Azure in modo da renderla accessibile da tutti i dispositivi. Fare clic con il pulsante destro del mouse sul progetto **AppBackend** e scegliere **Pubblica**.
2. Selezionare un sito Web Azure come destinazione di pubblicazione. Sign in with your Azure account and select an existing or new Website, and make a note of the **destination URL** property in the **Connection** tab. We will refer to this URL as your *backend endpoint* later in this tutorial. Fare clic su **Pubblica**.
