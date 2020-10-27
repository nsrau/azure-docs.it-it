---
title: Notifiche in Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Inviare notifiche agli utenti di app basate su Servizi di comunicazione di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3e68e65a5c2ed73a8fb6d8e5d01c645e05ca5157
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320708"
---
# <a name="communication-services-notifications"></a>Notifiche di Servizi di comunicazione

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Le librerie client Chat e Chiamate di Servizi di comunicazione creano un canale di messaggistica in tempo reale che consente di eseguire il push dei messaggi di segnalazione nei client connessi in modo efficiente e affidabile. In questo modo è possibile integrare funzionalità avanzate di comunicazione in tempo reale nelle applicazioni senza la necessità di implementare una logica di polling HTTP complessa. Nelle applicazioni per dispositivi mobili però questo canale di segnalazione rimane connesso solo quando l'applicazione è attiva in primo piano. Per consentire agli utenti di ricevere chiamate in arrivo o messaggi di chat mentre l'applicazione è in background, occorre usare le notifiche push.

Le notifiche push permettono di inviare informazioni dall'applicazione ai dispositivi mobili degli utenti. È possibile usarle per visualizzare una finestra di dialogo, riprodurre un suono o visualizzare l'interfaccia utente delle chiamate in arrivo. Servizi di comunicazione di Azure supporta l'integrazione con [Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview) e [Hub di notifica di Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) che consentono di aggiungere notifiche push alle app.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Attivare le notifiche push tramite Griglia di eventi di Azure

L'integrazione tra Servizi di comunicazione di Azure e [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) consente di inviare notifiche degli eventi in tempo reale in modo affidabile, scalabile e sicuro. È possibile sfruttare questa integrazione per creare un servizio di notifica che recapiti le notifiche push sui dispositivi mobili degli utenti creando una sottoscrizione di Griglia di eventi che attiva una [funzione di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) o un webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagramma che mostra l'integrazione di Servizi di comunicazione con Griglia di eventi.":::

Per altre informazioni, vedere [Gestione degli eventi in Servizi di comunicazione di Azure](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Recapitare notifiche push con Hub di notifica di Azure

È possibile connettere un hub di notifica di Azure alla risorsa di Servizi di comunicazione per inviare automaticamente notifiche push al dispositivo mobile di un utente quando riceve una chiamata in arrivo. Queste notifiche push devono essere usate per riattivare l'applicazione dal background e visualizzare l'interfaccia utente che consente all'utente di accettare o rifiutare la chiamata. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagramma che mostra l'integrazione di Servizi di comunicazione con Griglia di eventi.":::

Servizi di comunicazione usa Hub di notifica di Azure come servizio pass-through per comunicare con i vari servizi di notifica push specifici della piattaforma usando l'API di [invio diretto](https://docs.microsoft.com/rest/api/notificationhubs/direct-send). Questo consente di riutilizzare le risorse e le configurazioni esistenti di Hub di notifica di Azure per recapitare alle applicazioni notifiche di chiamata affidabili e a bassa latenza.

> [!NOTE]
> Attualmente sono supportate solo le notifiche push per le chiamate.

### <a name="notification-hub-provisioning"></a>Provisioning di un hub di notifica 

Per recapitare le notifiche push ai dispositivi client tramite Hub di notifica, [creare un hub di notifica](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal) nella stessa sottoscrizione della risorsa di Servizi di comunicazione. Hub di notifica di Azure deve essere configurato per il sistema PNS (Platform Notification System) che si vuole usare. Per informazioni su come ottenere notifiche push nell'app client da Hub di notifica, vedere [Introduzione a Hub di notifica](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) e selezionare la piattaforma client di destinazione dall'elenco a discesa nella parte superiore della pagina.

> [!NOTE]
> Attualmente sono supportate le piattaforme APN e FCM.

Una volta configurato l'hub di notifica, è possibile associarlo alla risorsa di Servizi di comunicazione fornendo la stringa di connessione dell'hub tramite il client di Azure Resource Manager o il portale di Azure. La stringa di connessione deve contenere le autorizzazioni di invio. È consigliabile creare un altro criterio di accesso con le sole autorizzazioni di invio specificamente per l'hub. Per altre informazioni, vedere [Sicurezza di Hub di notifica](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security).

> [!IMPORTANT]
> per abilitare le notifiche VOIP di Apple Push Notification Service, è necessario impostare il nome dell'hub di notifica in modo che corrisponda all'ID bundle dell'applicazione con il suffisso `.voip`. Vedere [Usare APNS VOIP tramite Hub di notifica](https://docs.microsoft.com/azure/notification-hubs/voip-apns).

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Uso del client di Azure Resource Manager per configurare l'hub di notifica

Per accedere ad Azure Resource Manager, eseguire il comando seguente ed effettuare l'accesso con le proprie credenziali.

```console
armclient login
```

 Eseguire quindi il comando seguente per effettuare il provisioning dell'hub di notifica:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Uso del portale di Azure per configurare l'hub di notifica

Nel portale passare alla risorsa di Servizi di comunicazione di Azure. All'interno della risorsa di Servizi di comunicazione selezionare Notifiche push nel menu a sinistra della pagina di Servizi di comunicazione e connettere l'hub di notifica di cui è stato effettuato il provisioning in precedenza. È necessario specificare qui la stringa di connessione e l'ID della risorsa:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Diagramma che mostra l'integrazione di Servizi di comunicazione con Griglia di eventi.":::

> [!NOTE]
> Se la stringa di connessione dell'hub di notifica di Azure viene aggiornata, è necessario aggiornare anche la risorsa di Servizi di comunicazione.

#### <a name="device-registration"></a>Registrazione del dispositivo 

Per informazioni su come registrare l'handle del dispositivo con Servizi di comunicazione, vedere la [guida di avvio rapido sulle chiamate vocali](../quickstarts/voice-video-calling/getting-started-with-calling.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](https://docs.microsoft.com/azure/event-grid/overview)
* Per altre informazioni sui concetti di Hub di notifica di Azure, vedere [Documentazione di Hub di notifica di Azure](https://docs.microsoft.com/azure/notification-hubs/)
