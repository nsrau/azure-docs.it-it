---
title: Autenticazione basata su token (HTTP/2) per APNS in Azure Notification Hubs Documenti Microsoft
description: Informazioni su come usare la nuova autenticazione token per APNS.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263813"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticazione basata su token (HTTP/2) per APNSToken-based (HTTP/2) authentication for APNS

## <a name="overview"></a>Panoramica

In questo articolo viene illustrato come utilizzare il nuovo protocollo APNS HTTP/2 con l'autenticazione basata su token.

Ecco i vantaggi principali dell'uso del nuovo protocollo:

* La generazione di token è relativamente semplice (rispetto ai certificati)Token generation is relatively simple (compared to certificates)
* Non sono previste scadenze: l'utente ha il controllo completo dei token di autenticazione e della rispettiva revoca.
* Sono consentiti payload fino a 4 KB.
* Commenti e suggerimenti sincroni
* Sei sull'ultimo protocollo di Apple: i certificati usano ancora il protocollo binario, contrassegnato per la deprecazione

L'utilizzo di questo nuovo meccanismo può essere eseguito in due fasi:Using this new mechanism can be performed in two steps:

* Ottieni le informazioni necessarie dal portale degli account apple Developer.
* Configurare l'hub di notifica con le nuove informazioni.

Hub di notifica è ora impostato per utilizzare il nuovo sistema di autenticazione con APNS.

Si noti che se è stata eseguita la migrazione dall'utilizzo delle credenziali del certificato per APNS, le proprietà del token sovrascrivono il certificato nel sistema, ma l'applicazione continua a ricevere notifiche senza problemi.

## <a name="obtaining-authentication-information-from-apple"></a>Recupero delle informazioni di autenticazione da Apple

Per abilitare l'autenticazione basata su token, hai bisogno delle seguenti proprietà dal tuo account Apple Developer:

### <a name="key-identifier"></a>Identificatore della chiave

L'identificatore chiave può essere ottenuto dalla pagina **Chiavi** in **Certificati, Identificatori & Profili**, nel tuo account Apple Developer:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identificatore dell'applicazione e nome dell'applicazione

Il nome e l'identificatore dell'applicazione sono disponibili anche nella pagina **Certificati, identificatori & profili** nell'account sviluppatore:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configurare tramite .NET SDK o il portale di AzureConfigure via the .NET SDK or the Azure portal

È possibile configurare l'hub per l'utilizzo dell'autenticazione basata su token tramite [l'SDK client più recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)o nel portale di Azure.You can configure your hub to use token-based authentication using our latest client SDK , or in the Azure portal. Per abilitare l'autenticazione basata su token nel portale, accedere al portale di Azure e passare al riquadro **Impostazioni > Apple (APNS)** dell'hub di notifica. Selezionare **Token** dalla proprietà **Modalità di autenticazione** per aggiornare l'hub con tutte le proprietà del token rilevanti.

![Configurare il token](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Inserisci le proprietà recuperate dal tuo account Apple Developer.
* Scegliere la modalità applicazione (**Produzione** o **Sandbox**).
* Fare clic sul pulsante **Salva** per aggiornare le credenziali APNS.

Le credenziali basate su token sono costituite dai seguenti campi:

* **ID chiave**: Identificatore della chiave privata generata nel portale per sviluppatori Apple; ad esempio, `2USFGKSKLT`.
* **ID team**: chiamato anche "Prefisso" o "Prefisso app". Questo è l'identificatore per l'organizzazione nel portale per sviluppatori Apple; ad esempio, `S4V3D7CHJR`.
* **ID pacchetto**: chiamato anche "ID app". Questo è l'identificatore del bundle per l'applicazione; ad esempio, `com.microsoft.nhubsample2019`. Tieni presente che puoi usare una chiave per molte app. Questo valore esegue `apns-topic` il mapping all'intestazione HTTP quando si invia una notifica e viene utilizzato per l'applicazione specifica.
* **Token**: chiamato anche "Chiave" o "Chiave privata". Questo viene ottenuto dal file .p8 generato sul portale per sviluppatori Apple. La chiave deve avere APNS abilitato (selezionato nel portale per sviluppatori Apple durante la generazione della chiave). Il valore deve avere l'intestazione/piè di pagina PEM rimosso da esso quando viene fornito al portale NH/API.
* **Endpoint:** si tratta di un interruttore nel pannello del portale Hub di notifica e di un campo stringa nell'API. I valori validi sono `https://api.push.apple.com` o `https://api.sandbox.push.apple.com`. Hub di notifica utilizza questo valore per l'ambiente di produzione o sandbox, per l'invio di notifiche. Deve corrispondere `aps-environment` al diritto nell'app, altrimenti i token del dispositivo APNS generati non corrispondono all'ambiente e le notifiche non vengono inviate.

Ecco un esempio di codice che illustra l'utilizzo corretto:Here's a code sample illustrating the correct usage:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md)
* [Configurare un hub di notifica nel portale di AzureConfigure a notification hub in the Azure portal](create-notification-hub-portal.md)
