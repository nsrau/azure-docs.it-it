---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 299f92484000cb5c59291a5af87f24d89a771fee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296782"
---
In questa sezione si invieranno notizie aggiornate come notifiche modello con tag da un'app console .NET.

1. In Visual Studio creare una nuova applicazione console in Visual C#:
    1. Nel menu selezionare **File** > **Nuovo** > **Progetto**.
    1. In **Crea un nuovo progetto** selezionare **App console (.NET Framework)** per C# nell'elenco di modelli e scegliere **Avanti**.
    1. Immettere un nome per l'app.
    1. Per **Soluzione** scegliere **Aggiungi a soluzione** e selezionare **Crea** per creare il progetto.

1. Scegliere **Strumenti**,  > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti** e quindi eseguire il comando seguente nella finestra della console:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Questa azione aggiunge un riferimento ad Azure Notification Hubs SDK usando il pacchetto [Microsoft.Azure.NotificationHubs].

1. Aprire il file *Program.cs* e aggiungere l'istruzione `using` seguente:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Nella classe `Program` aggiungere il metodo seguente oppure sostituirlo se esiste già:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Questo codice invia una notifica modello per ognuno dei sei tag nella matrice di stringhe. L'uso di tag garantisce che i dispositivi ricevano le notifiche solo per le categorie registrate.

1. Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with full access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultFullSharedAccessSignature* dal dashboard dell'hub di notifica.

1. Nel metodo `Main()` aggiungere le righe seguenti:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Creare l'app console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
