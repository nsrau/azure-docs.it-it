---
title: Autenticazione basata su token (HTTP/2) per il servizio APN negli hub di notifica di Azure | Microsoft Docs
description: Questo argomento spiega come sfruttare i vantaggi della nuova autenticazione basata su token per il servizio APN
services: notification-hubs
documentationcenter: .net
author: kpiteira
manager: erikre
editor: 
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 05/17/2017
ms.author: kapiteir
ms.openlocfilehash: 5a21bcd9f12fc3f96b17a556ba15526c35ababe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticazione basata su token (HTTP/2) per il servizio APN
## <a name="overview"></a>Panoramica
Questo articolo illustra come usare il nuovo protocollo HTTP/2 del servizio APN con l'autenticazione basata su token.

Ecco i vantaggi principali dell'uso del nuovo protocollo:
-   La generazione di token è relativamente semplice rispetto ai certificati.
-   Non sono previste scadenze: l'utente ha il controllo completo dei token di autenticazione e della rispettiva revoca.
-   Sono consentiti payload fino a 4 KB.
- Commenti e suggerimenti sincroni
-   Viene usato il protocollo più recente di Apple, mentre i certificati usano ancora il protocollo binario, contrassegnato per l'eliminazione

Per usare questo nuovo meccanismo sono sufficienti due passaggi che richiedono pochi minuti:
1.  Ottenere le informazioni necessarie dal portale per account sviluppatore Apple
2.  Configurare l'hub di notifica con le nuove informazioni

Hub di notifica è ora configurato per l'uso del nuovo sistema di autenticazione con il servizio APN. 

Se è stata eseguita la migrazione dall'uso delle credenziali del certificato per il servizio APN, si noti quanto segue:
- Le proprietà del token sovrascrivono il certificato nel sistema.
- L'applicazione continua tuttavia a ricevere le notifiche senza problemi.

## <a name="obtaining-authentication-information-from-apple"></a>Recupero delle informazioni di autenticazione da Apple
Per abilitare l'autenticazione basata su token, sono necessarie le proprietà seguenti dell'account sviluppatore Apple:
### <a name="key-identifier"></a>Identificatore di chiave
L'identificatore di chiave può esser ottenuto dalla pagina "Keys" (Chiavi) dell'account sviluppatore Apple.

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identificatore dell'applicazione e nome dell'applicazione
Il nome dell'applicazione è disponibile nella pagina relativa agli ID dell'app dell'account sviluppatore. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

L'identificatore dell'applicazione è disponibile tramite la pagina relativa ai dettagli dell'appartenenza dell'account sviluppatore.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Token di autenticazione
Il token di autenticazione può essere scaricato dopo la generazione di un token per l'applicazione. Per informazioni dettagliate su come generare questo token, vedere la [documentazione per sviluppatori di Apple](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Configurazione dell'hub di notifica per l'uso dell'autenticazione basata su token
### <a name="configure-via-the-azure-portal"></a>Eseguire la configurazione tramite il portale di Azure
Per abilitare l'autenticazione basata su token nel portale, accedere a portale di Azure e passare al pannello Hub di notifica > Servizi di notifica > Servizio APN. 

È disponibile una nuova proprietà, ovvero *Modalità di autenticazione*. La selezione del token consente di aggiornare l'hub con tutte le proprietà rilevanti del token.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Immettere le proprietà recuperate dall'account sviluppatore Apple. 
- Scegliere la modalità dell'applicazione (produzione o sandbox). 
- Fare clic su Salva per aggiornare le credenziali del servizio APN. 

### <a name="configure-via-management-api-rest"></a>Configurazione tramite API Gestione (REST)

È possibile usare le [API di gestione](https://msdn.microsoft.com/library/azure/dn495827.aspx) per aggiornare l'hub di notifica per l'uso dell'autenticazione basata su token.
In base alla modalità usata per l'app da configurare, ovvero sandbox o produzione, come indicato nell'account sviluppatore Apple, usare uno degli endpoint corrispondenti:

- Endpoint sandbox: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
- Endpoint di produzione: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> L'autenticazione basata su token richiede una versione **2017-04 o successiva** dell'API.
> 
> 

Ecco un esempio di richiesta PUT per l'aggiornamento di un hub con l'autenticazione basata su token:


        PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
          "Properties": {
            "ApnsCredential": {
              "Properties": {
                "KeyId": "<Your Key Id>",
                "Token": "<Your Authentication Token>",
                "AppName": "<Your Application Name>",
                "AppId": "<Your Application Id>",
                "Endpoint":"<Sandbox/Production Endpoint>"
              }
            }
          }
        

### <a name="configure-via-the-net-sdk"></a>Configurazione tramite .NET SDK
È possibile configurare l'hub per l'uso dell'autenticazione basata su token tramite la [versione più recente dell'SDK client](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

Ecco un esempio di codice che illustra l'utilizzo corretto:


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Ripristino dell'uso dell'autenticazione basata su certificati
È possibile ripristinare in qualsiasi momento l'uso dell'autenticazione basata su certificati usando uno dei metodi precedenti e passando il certificato invece delle proprietà dei token. Questa azione sovrascrive le credenziali archiviate in precedenza.
