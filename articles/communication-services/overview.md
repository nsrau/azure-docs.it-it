---
title: Che cos'è l'offerta Servizi di comunicazione di Azure?
description: Informazioni su come Servizi di comunicazione di Azure consente di sviluppare esperienze utente avanzate con comunicazioni in tempo reale.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d680df0ec5e18cca4d7a42edf45dbd6a50be1472
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888607"
---
# <a name="what-is-azure-communication-services"></a>Che cos'è l'offerta Servizi di comunicazione di Azure?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Servizi di comunicazione di Azure consente di aggiungere in modo semplice funzionalità di comunicazione vocale, video e telefonia tramite IP in tempo reale alle applicazioni. Le librerie client di Servizi di comunicazione consentono anche di aggiungere funzionalità di chat e SMS alle soluzioni di comunicazione.

<br>

> [!VIDEO https://www.youtube.com/embed/49oshhgY6UQ]

<br>
<br>

È possibile usare Servizi di comunicazione per la comunicazione vocale, video, di testo e dati in diversi scenari:

- Comunicazione da browser a browser, da browser ad app e da app ad app
- Interazione dell'utente con bot o altri servizi
- Interazione di utenti e bot tramite PSTN

Sono supportati scenari misti. Ad esempio, in un'applicazione di Servizi di comunicazione possono essere presenti utenti che parlano contemporaneamente da browser e dispositivi tradizionali di telefonia. Servizi di comunicazione può anche combinarsi con il servizio Azure Bot per creare sistemi IVR (Interactive Voice Response) basati su bot.

## <a name="common-scenarios"></a>Scenari comuni

Se non si ha familiarità con Servizi di comunicazione di Azure, le risorse seguenti sono un ottimo punto di partenza:
<br>

| Risorsa                               |Descrizione                           |
|---                                    |---                                   |
|**[Creare una risorsa di Servizi di comunicazione](./quickstarts/create-communication-resource.md)**|È possibile iniziare a usare Servizi di comunicazione di Azure tramite il portale di Azure o la libreria client di amministrazione di Servizi di comunicazione per effettuare il provisioning della prima risorsa di Servizi di comunicazione. Dopo aver ottenuto la stringa di connessione della risorsa di Servizi di comunicazione, è possibile effettuare il provisioning dei primi token di accesso utente.|
|**[Creare i primi token di accesso utente](./quickstarts/access-tokens.md)**|I token di accesso utente vengono usati per autenticare i servizi con la risorsa di Servizi di comunicazione di Azure. Questi token vengono sottoposti a provisioning e rilasciati tramite la libreria client di amministrazione di Servizi di comunicazione.|
|**[Ottenere un numero di telefono](./quickstarts/telephony-sms/get-phone-number.md)**|È possibile usare Servizi di comunicazione di Azure per effettuare il provisioning e rilasciare i numeri di telefono. Questi numeri di telefono possono essere usati per avviare le chiamate in uscita e creare soluzioni di comunicazione SMS.|
|**[Inviare un SMS dall'app](./quickstarts/telephony-sms/send.md)**|La libreria client per gli SMS di Servizi di comunicazione di Azure consente di inviare e ricevere messaggi SMS dalle applicazioni .NET e JavaScript.|
|**[Introduzione alle chiamate vocali e video](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Servizi di comunicazione di Azure consente di aggiungere chiamate vocali e video alle app usando la libreria client per le chiamate. Questa libreria si basa su WebRTC e consente di stabilire comunicazioni peer-to-peer, multimediali e in tempo reale all'interno delle applicazioni.|
|**[Introduzione alle chat](./quickstarts/chat/get-started.md)**|La libreria client per le chat di Servizi di comunicazione di Azure può essere usata per integrare chat in tempo reale nelle applicazioni.|


## <a name="samples"></a>Esempi

Gli esempi seguenti illustrano l'utilizzo end-to-end delle librerie client di Servizi di comunicazione di Azure. È possibile usare questi esempi per il bootstrap di soluzioni di Servizi di comunicazione personalizzate.
<br>

| Nome esempio                               | Descrizione                           |
|---                                    |---                                   |
|**[Esempio hero di chiamate di gruppo](./samples/calling-hero-sample.md)**|Vedere in che modo è possibile usare le librerie client di Servizi di comunicazione per creare un'esperienza di chiamata di gruppo.|
|**[Esempio hero di chat di gruppo](./samples/chat-hero-sample.md)**|Vedere in che modo è possibile usare le librerie client di Servizi di comunicazione per creare un'esperienza di chat di gruppo.|


## <a name="platforms-and-client-libraries"></a>Librerie client e piattaforme

Per informazioni sulle librerie client di Servizi di comunicazione di Azure, vedere le risorse seguenti:

| Risorsa                               | Descrizione                           |
|---                                    |---                                   |
|**[Librerie client e API REST](./concepts/sdk-options.md)**|Le funzionalità di Servizi di comunicazione di Azure sono organizzate concettualmente in sei aree, ciascuna rappresentata da una libreria client. È possibile decidere quali librerie client usare in base alle esigenze di comunicazione in tempo reale.|
|**[Panoramica della libreria client per le chiamate](./concepts/voice-video-calling/calling-sdk-features.md)**|Vedere la panoramica della libreria client per le chiamate di Servizi di comunicazione di Azure.|
|**[Panoramica della libreria client per le chat](./concepts/chat/sdk-features.md)**|Vedere la panoramica della libreria client per le chat di Servizi di comunicazione di Azure.|
|**[Panoramica della libreria client per gli SMS](./concepts/telephony-sms/sdk-features.md)**|Vedere la panoramica della libreria client per gli SMS di Servizi di comunicazione di Azure.|

## <a name="compare-azure-communication-services"></a>Confrontare Servizi di comunicazione di Azure

Sono disponibili altri due prodotti Microsoft per la comunicazione che attualmente non sono direttamente interoperabili con Servizi di comunicazione:

 - Le [API di comunicazione cloud di Microsoft Graph](/graph/cloud-communications-concept-overview), che consentono alle organizzazioni di creare esperienze di comunicazione associate agli utenti di Azure Active Directory con licenze M365. Questa soluzione è ideale per le applicazioni associate ad Azure Active Directory o in cui si vuole estendere le esperienze di produttività di Microsoft Teams. Sono anche disponibili API per la creazione di applicazioni e la personalizzazione all'interno dell'esperienza [Teams](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest).

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/), che semplifica l'aggiunta di comunicazioni di dati e chat a bassa latenza ai giochi. Anche se è possibile usare sistemi di rete e chat per il gioco con Servizi di comunicazione, PlayFab è un'opzione personalizzata e gratuita su Xbox.


## <a name="next-steps"></a>Passaggi successivi

 - [Creare una risorsa di Servizi di comunicazione](./quickstarts/create-communication-resource.md)