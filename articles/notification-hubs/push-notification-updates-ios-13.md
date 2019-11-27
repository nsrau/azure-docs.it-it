---
title: Aggiornamenti di hub di notifica di Azure iOS 13 | Microsoft Docs
description: Informazioni sulle modifiche di rilievo di iOS 13 in hub di notifica di Azure
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228150"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Aggiornamenti di Hub di notifica di Azure per iOS 13

Apple ha recentemente apportato alcune modifiche al servizio di push pubblico; le modifiche sono allineate principalmente con le versioni di iOS 13 e Xcode. Questo articolo descrive l'effetto di queste modifiche in hub di notifica di Azure.

## <a name="apns-push-payload-changes"></a>Modifiche del payload push APNS

### <a name="apns-push-type"></a>Tipo push APNS

Apple ora richiede che gli sviluppatori identifichino le notifiche come avvisi o notifiche in background tramite la nuova intestazione `apns-push-type` nell'API APNS. Secondo la [documentazione di Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "il valore di questa intestazione deve riflettere accuratamente il contenuto del payload della notifica. Se non c'è corrispondenza o se l'intestazione manca nei sistemi in cui è obbligatoria, il servizio APN può restituire un errore, ritardare il recapito della notifica o eliminarla del tutto.

Gli sviluppatori devono ora impostare questa intestazione nelle applicazioni che inviano notifiche tramite hub di notifica di Azure. A causa di una limitazione tecnica, i clienti devono usare l'autenticazione basata su token per le credenziali APNS con le richieste che includono questo attributo. Se si usa l'autenticazione basata su certificati per le credenziali di APNS, è necessario passare all'uso dell'autenticazione basata su token.

Gli esempi di codice seguenti illustrano come impostare questo attributo di intestazione nelle richieste di notifica inviate tramite hub di notifica di Azure.

#### <a name="template-notifications---net-sdk"></a>Notifiche del modello-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Notifiche native-.NET SDK

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

Per semplificare la transizione, quando hub di notifica di Azure rileva una notifica che non ha il set di `apns-push-type`, il servizio deduce il tipo di push dalla richiesta di notifica e imposta automaticamente il valore. Tenere presente che è necessario configurare Hub di notifica di Azure per l'uso dell'autenticazione basata su token per impostare l'intestazione richiesta. per altre informazioni, vedere [autenticazione basata su token (http/2) per APNs](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Priorità APNS

Un'altra modifica secondaria, ma una che richiede una modifica all'applicazione back-end che invia le notifiche, è il requisito per le notifiche in background che l'intestazione `apns-priority` deve ora essere impostata su 5. Molte applicazioni impostano l'intestazione `apns-priority` su 10 (che indica il recapito immediato) o non lo impostano e ottengono il valore predefinito (che è anche 10).

L'impostazione di questo valore su 10 non è più consentita per le notifiche in background ed è necessario impostare il valore per ogni richiesta. Se questo valore risulta mancante, Apple non fornirà notifiche in background. Ad esempio:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Modifiche dell'SDK

Per anni, gli sviluppatori iOS hanno usato l'attributo `description` dei dati `deviceToken` inviati al delegato del token push per estrarre il token di push che un'applicazione back-end USA per inviare notifiche al dispositivo. Con Xcode 11, l'attributo `description` modificato in un formato diverso. Il codice esistente usato dagli sviluppatori per questo attributo è ora rotto. L'SDK di hub di notifica di Azure è stato aggiornato in base a questa modifica. aggiornare quindi l'SDK usato dalle applicazioni alla versione 2.0.4 o successiva dell'SDK [iOS di hub di notifica di Azure](https://github.com/Azure/azure-notificationhubs-ios).
