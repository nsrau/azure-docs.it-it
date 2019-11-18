---
title: Autenticazione basata su token (HTTP/2) per il servizio APN negli hub di notifica di Azure | Microsoft Docs
description: Questo argomento illustra come usare la nuova autenticazione basata su token per APNS
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
ms.openlocfilehash: 56689981d6c85c844fefbec6a4ec4aeb041dbc7f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111974"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticazione basata su token (HTTP/2) per il servizio APN

## <a name="overview"></a>Overview

Questo articolo illustra come usare il nuovo protocollo HTTP/2 APNS con l'autenticazione basata su token.

Ecco i vantaggi principali dell'uso del nuovo protocollo:

* La generazione di token è relativamente semplice (rispetto ai certificati)
* Non sono previste scadenze: l'utente ha il controllo completo dei token di autenticazione e della rispettiva revoca.
* Sono consentiti payload fino a 4 KB.
* Commenti e suggerimenti sincroni
* Si usa il protocollo più recente di Apple: i certificati usano ancora il protocollo binario, contrassegnato per la deprecazione

L'uso di questo nuovo meccanismo può essere eseguito in due passaggi:

* Ottenere le informazioni necessarie dal portale per gli account per sviluppatori Apple.
* Configurare l'hub di notifica con le nuove informazioni.

Hub di notifica è ora impostato per usare il nuovo sistema di autenticazione con APNS.

Si noti che se è stata eseguita la migrazione dall'uso delle credenziali del certificato per APNS, le proprietà del token sovrascrivono il certificato nel sistema, ma l'applicazione continua a ricevere le notifiche senza interruzioni.

## <a name="obtaining-authentication-information-from-apple"></a>Recupero delle informazioni di autenticazione da Apple

Per abilitare l'autenticazione basata su token, sono necessarie le proprietà seguenti dell'account sviluppatore Apple:

### <a name="key-identifier"></a>Identificatore di chiave

L'identificatore di chiave può essere ottenuto dalla pagina **chiavi** in **certificati, identificatori & profili**nell'account per sviluppatore Apple:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identificatore dell'applicazione e nome dell'applicazione

Il nome e l'identificatore dell'applicazione sono disponibili anche nella pagina **certificati, identificatori & profili** nell'account sviluppatore:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configurare tramite .NET SDK o il portale di Azure

È possibile configurare l'hub per l'uso dell'autenticazione basata su token con l' [SDK client più recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)o nel portale di Azure. Per abilitare l'autenticazione basata su token nel portale, accedere al portale di Azure e passare al pannello impostazioni dell'hub di notifica **> Apple (APNS)** . Selezionare **token** dalla proprietà **modalità di autenticazione** per aggiornare l'hub con tutte le proprietà del token pertinenti.

![Configura token](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Immettere le proprietà recuperate dall'account per sviluppatori Apple.
* Scegliere la modalità di applicazione (**produzione** o **sandbox**).
* Fare clic sul pulsante **Salva** per aggiornare le credenziali di APNs.

Le credenziali basate su token sono costituite dai campi seguenti:

* **ID chiave**: identificatore della chiave privata generata nel portale Apple Developer; ad esempio, `2USFGKSKLT`.
* **Team ID**: chiamato anche "prefix" o "prefisso app". Questo è l'identificatore dell'organizzazione nel portale per sviluppatori Apple. ad esempio, `S4V3D7CHJR`.
* **ID bundle**: denominato anche "ID app". Identificatore del bundle per l'applicazione. ad esempio, `com.microsoft.nhubsample2019`. Si noti che è possibile usare una chiave per molte app. Questo valore esegue il mapping all'intestazione HTTP `apns-topic` quando si invia una notifica e viene utilizzata per la destinazione dell'applicazione specifica.
* **Token**: detto anche "chiave" o "chiave privata". Questa operazione è ottenuta dal file con estensione P8 generato nel portale Apple Developer. Per la chiave deve essere abilitato APNS, che è selezionato nel portale Apple Developer durante la generazione della chiave. Il valore deve avere l'intestazione o il piè di pagina PEM rimosso quando lo si fornisce al portale/API di NH.
* **Endpoint**: si tratta di un interruttore nel pannello del portale di hub di notifica e di un campo stringa nell'API. I valori validi sono `https://api.push.apple.com` o `https://api.sandbox.push.apple.com`. Hub di notifica usa questo valore per l'ambiente di produzione o sandbox, per l'invio di notifiche. Deve corrispondere al diritto `aps-environment` nell'app, in caso contrario i token del dispositivo APNS generati non corrispondono all'ambiente e le notifiche non vengono inviate.

Ecco un esempio di codice che illustra l'utilizzo corretto:

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
* [Configurare un hub di notifica nel portale di Azure](create-notification-hub-portal.md)
