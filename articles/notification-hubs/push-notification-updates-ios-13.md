---
title: Aggiornamenti di Azure Notification Hubs iOS 13 Documenti Microsoft
description: Informazioni sulle modifiche di rilievo di iOS 13 negli hub di notifica di AzureLearn about iOS 13 breaking changes in Azure Notification Hubs
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228150"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Aggiornamenti di Azure Notification Hubs per iOS 13Azure Notification Hubs updates for iOS 13

Apple ha recentemente apportato alcune modifiche al proprio servizio push pubblico; le modifiche per lo più allineati con le versioni di iOS 13 e Xcode. Questo articolo descrive l'impatto di queste modifiche sugli hub di notifica di Azure.This article describes the impact of these changes on Azure Notification Hubs.

## <a name="apns-push-payload-changes"></a>Modifiche al payload push APNSAPNS push payload changes

### <a name="apns-push-type"></a>Tipo di push APNS

Apple ora richiede che gli sviluppatori identifichino le notifiche come avviso o notifiche in background tramite la nuova `apns-push-type` intestazione nell'API APNS. Secondo la [documentazione di Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "Il valore di questa intestazione deve riflettere accuratamente il contenuto del payload della notifica. Se si verifica una mancata corrispondenza o se l'intestazione non è presente nei sistemi richiesti, gli APN possono restituire un errore, ritardare il recapito della notifica o interromperla del tutto."

Gli sviluppatori devono ora impostare questa intestazione nelle applicazioni che inviano notifiche tramite Hub di notifica di Azure.Developers must now set this header in applications that send notifications through Azure Notification Hubs. A causa di una limitazione tecnica, i clienti devono utilizzare l'autenticazione basata su token per le credenziali APNS con richieste che includono questo attributo. Se si utilizza l'autenticazione basata su certificati per le credenziali APNS, è necessario passare all'utilizzo dell'autenticazione basata su token.

Gli esempi di codice seguenti illustrano come impostare questo attributo di intestazione nelle richieste di notifica inviate tramite Hub di notifica di Azure.The following code samples show how to set this header attribute in notification requests sent through Azure Notification Hubs.

#### <a name="template-notifications---net-sdk"></a>Notifiche di modelli - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Notifiche native - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Chiamate REST dirette

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Per assistenza durante questa transizione, quando Hub di notifica di Azure `apns-push-type` rileva una notifica che non dispone dell'impostazione, il servizio deduce il tipo di push dalla richiesta di notifica e imposta automaticamente il valore. Tenere presente che è necessario configurare hub di notifica di Azure per usare l'autenticazione basata su token per impostare l'intestazione richiesta. Per ulteriori informazioni, vedere [Autenticazione basata su token (HTTP/2) per APNS](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Priorità APNS

Un'altra modifica minore, ma che richiede una modifica all'applicazione back-end `apns-priority` che invia notifiche, è il requisito che per le notifiche in background l'intestazione deve ora essere impostata su 5. Molte applicazioni `apns-priority` impostano l'intestazione su 10 (che indica la consegna immediata) oppure non la impostano e ottengono il valore predefinito (che è anche 10).

L'impostazione di questo valore su 10 non è più consentita per le notifiche in background ed è necessario impostare il valore per ogni richiesta. Apple non recapiterà notifiche in background se questo valore non è presente. Ad esempio:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Modifiche SDK

Per anni, gli sviluppatori `description` iOS `deviceToken` hanno usato l'attributo dei dati inviati al delegato del token push per estrarre il token push utilizzato da un'applicazione back-end per inviare notifiche al dispositivo. Con Xcode 11, tale `description` attributo è stato modificato in un formato diverso. Il codice esistente utilizzato dagli sviluppatori per questo attributo è ora interrotto. Abbiamo aggiornato l'SDK di Hub di notifica di Azure per supportare questa modifica, quindi aggiornare l'SDK usato dalle applicazioni alla versione 2.0.4 o più recente di [Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
