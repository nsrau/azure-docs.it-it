---
title: Architettura client e server
titleSuffix: An Azure Communication Services concept document
description: Informazioni sull'architettura dei servizi di comunicazione.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 38d6b131c972b4fd890af53624fdd6a36b3ecb6c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938144"
---
# <a name="client-and-server-architecture"></a>Architettura client e server

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Ogni applicazione di servizi di comunicazione Azure avrà **applicazioni client** che usano i **Servizi** per semplificare la connettività da persona a persona. In questa pagina vengono illustrati gli elementi architetturali comuni in diversi scenari.

## <a name="user-access-management"></a>Gestione degli accessi degli utenti

Le librerie client dei servizi di comunicazione di Azure richiedono `user access tokens` l'accesso sicuro alle risorse di servizi di comunicazione. `User access tokens` deve essere generato e gestito da un servizio attendibile a causa della natura sensibile del token e della stringa di connessione necessaria per generarli. La mancata corretta gestione dei token di accesso può comportare addebiti aggiuntivi a causa di un uso improprio delle risorse. Si consiglia vivamente di utilizzare un servizio attendibile per la gestione degli utenti. Il servizio attendibile genererà i token e li passerà di nuovo al client usando la crittografia corretta. Un flusso di architettura di esempio è disponibile di seguito:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagramma che illustra l'architettura del token di accesso utente.":::

Per ulteriori informazioni, vedere [procedure di gestione delle identità migliori](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)

## <a name="browser-communication"></a>Comunicazione del browser

Le librerie client JavaScript per le comunicazioni di Azure consentono alle applicazioni Web di usare un'interazione di testo, voce e video avanzata. L'applicazione interagisce direttamente con i servizi di comunicazione di Azure tramite la libreria client per accedere al piano dati e fornire comunicazioni di testo, voce e video in tempo reale. Un flusso di architettura di esempio è disponibile di seguito:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagramma che illustra l'architettura del browser per i servizi di comunicazione.":::

## <a name="native-app-communication"></a>Comunicazione delle app native

Molti scenari sono particolarmente serviti con le applicazioni native. Servizi di comunicazione di Azure supporta la comunicazione da browser a app e da app a app.  Quando si compila un'esperienza di applicazione nativa, le notifiche push consentono agli utenti di ricevere chiamate anche quando l'applicazione non è in esecuzione. I servizi di comunicazione di Azure semplificano le notifiche push integrate a Google Firebase, Apple Push Notification Service e le notifiche push di Windows. Un flusso di architettura di esempio è disponibile di seguito:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagramma che illustra l'architettura dei servizi di comunicazione per la comunicazione delle app native.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Voce e SMS sulla rete PSTN (Public Telephony Network)

La comunicazione sul sistema telefonico può aumentare significativamente la portata dell'applicazione. Per supportare gli scenari vocali e SMS PSTN, servizi di comunicazione Azure consente di [acquisire i numeri di telefono](../quickstarts/telephony-sms/get-phone-number.md) direttamente dalla portale di Azure o usando le API REST e le librerie client. Una volta acquisiti, i numeri di telefono possono essere utilizzati per raggiungere i clienti tramite chiamate PSTN e SMS sia negli scenari in ingresso che in uscita. Un flusso di architettura di esempio è disponibile di seguito:

> [!Note]
> Durante l'anteprima pubblica, il provisioning dei numeri di telefono degli Stati Uniti è disponibile per i clienti con indirizzi di fatturazione situati negli Stati Uniti e in Canada. 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagramma che illustra l'architettura PSTN dei servizi di comunicazione.":::

Per ulteriori informazioni sulle soluzioni PSTN e SMS, vedere [pianificare la soluzione PSTN e SMS](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>Utenti che comunicano con bot e altri servizi

Servizi di comunicazione Azure supporta la comunicazione da uomo a sistema tramite canali di testo e vocali, con servizi che accedono direttamente al piano dati di servizi di comunicazione di Azure. Ad esempio, è possibile avere un bot per rispondere alle telefonate in arrivo o partecipare a una chat Web. Servizi di comunicazione di Azure fornisce librerie client che consentono di effettuare questi scenari per la chiamata e la chat. Un flusso di architettura di esempio è disponibile di seguito:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagramma che illustra l'architettura di bot per servizi di comunicazione.":::

## <a name="networking"></a>Rete

Potrebbe essere necessario scambiare dati arbitrari tra gli utenti, ad esempio per sincronizzare una realtà mista condivisa o un'esperienza di gioco. Il piano dati in tempo reale usato per la comunicazione di testo, voce e video è disponibile direttamente in due modi:

- **Chiamare la libreria client** : i dispositivi in una chiamata hanno accesso alle API per l'invio e la ricezione di dati tramite il canale di chiamata. Questo è il modo più semplice per aggiungere la comunicazione dati a un'interazione esistente.
- **Stun/Turn** : i servizi di comunicazione di Azure rendono i servizi disponibili per gli standard e diventano disponibili per l'utente. In questo modo è possibile creare un livello di trasporto altamente personalizzato su queste primitive standardizzate. È possibile creare un client conforme agli standard o usare librerie open source, ad esempio [WinRTC](https://github.com/microsoft/winrtc).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creazione di token di accesso utente](../quickstarts/access-tokens.md)

Per altre informazioni, vedere gli articoli seguenti:

- Informazioni sull' [autenticazione](../concepts/authentication.md)
- Informazioni sulle [soluzioni PSTN e SMS](../concepts/telephony-sms/plan-solution.md)

- [Aggiungere la chat all'app](../quickstarts/chat/get-started.md)
- [Aggiungere la chiamata vocale all'app](../quickstarts/voice-video-calling/getting-started-with-calling.md)
