---
title: Concetti relativi alle chiamate vocali e video in Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui tipi di chiamata di Servizi di comunicazione.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 97a90284ad8d195751eb5dd90675822d00243ea8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665209"
---
# <a name="voice-and-video-concepts"></a>Concetti relativi alle chiamate vocali e video

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

È possibile usare Servizi di comunicazione di Azure per effettuare e ricevere chiamate vocali e video, analizzare i metadati delle chiamate, registrare le chiamate e persino creare alberi di chiamate. Le chiamate possono essere effettuate verso altri dispositivi connessi a Internet e verso telefoni tradizionali. È possibile usare le librerie client JavaScript, Android o iOS di Servizi di comunicazione per creare applicazioni che consentano agli utenti di comunicare tra loro in conversazioni private o di gruppo. Servizi di comunicazione di Azure supporta le chiamate da e verso servizi o bot.

## <a name="call-types-in-azure-communication-services"></a>Tipi di chiamata in Servizi di comunicazione di Azure

In Servizi di comunicazione di Azure è possibile effettuare più tipi di chiamate. Il tipo di chiamate effettuate determina lo schema di segnalazione, i flussi di traffico multimediale e il modello di determinazione prezzi.

### <a name="voice-over-ip-voip"></a>VoIP (Voice Over IP) 

Quando un utente dell'applicazione chiama un altro utente dell'applicazione tramite una connessione Internet o dati, la chiamata viene effettuata tramite VoIP (Voice over IP). In questo caso, sia la segnalazione sia i flussi multimediali passano su Internet.

### <a name="public-switched-telephone-network-pstn"></a>PSTN (Public Switched Telephone Network)

Ogni volta che gli utenti interagiscono con un numero di telefono tradizionale, le chiamate vengono facilitate dalla funzionalità di chiamata vocale PSTN (Public Switched Telephone Network). Per effettuare e ricevere chiamate PSTN, è necessario aggiungere funzionalità di telefonia alla risorsa di Servizi di comunicazione di Azure. In questo caso, per connettere gli utenti, la segnalazione e i flussi multimediali usano una combinazione di tecnologie basate su IP e PSTN.

### <a name="one-to-one-call"></a>Chiamata uno-a-uno

Una chiamata uno-a-uno in Servizi di comunicazione di Azure avviene quando uno degli utenti si connette a un altro utente usando una delle librerie client. La chiamata può essere effettuata tramite VoIP o PSTN.

### <a name="group-call"></a>Chiamata di gruppo

Una chiamata di gruppo in Servizi di comunicazione di Azure avviene quando tre o più partecipanti si connettono tra loro. In una chiamata di gruppo può essere presente una qualsiasi combinazione di utenti connessi tramite VoIP e PSTN. È possibile convertire una chiamata uno-a-uno in una di gruppo aggiungendo alla chiamata altri partecipanti. Uno dei partecipanti può essere costituito da un bot.

### <a name="supported-video-standards"></a>Standard video supportati
È supportato lo standard H.264 (MPEG-4) 

### <a name="video-quality"></a>Qualità video
Per gli SDK nativi (iOS, Android) è supportato il formato fino a Full HD 1080p. Per l'SDK Web (JS) è supportato il formato Standard HD 720p. La qualità dipende dalla larghezza di banda disponibile.  

### <a name="rooms-concept"></a>Concetto di sale
Le sale costituiscono un set di API e SDK che consentono di aggiungere facilmente elementi audio, video, condivisione dello schermo, interazioni PSTN e SMS nel sito Web o nell'applicazione nativa.
Durante l'anteprima è possibile usare l'ID di gruppo per partecipare alla stessa conversazione. È possibile creare tutti gli ID di gruppo necessari e separare gli utenti in base alle "sale". Con il tempo vengono introdotti altri controlli attorno alle "sale". 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chiamate](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Per altre informazioni, vedere gli articoli seguenti:
- Acquisire familiarità con i [flussi di chiamate](../call-flows.md) generali
- [Pianificare la soluzione PSTN](../telephony-sms/plan-solution.md)
- Vedere le informazioni relative alle [funzionalità delle librerie client per le chiamate](../voice-video-calling/calling-sdk-features.md)
