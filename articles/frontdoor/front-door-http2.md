---
title: Azure front door-supporto HTTP2 | Microsoft Docs
description: Questo articolo fornisce informazioni sul supporto HTTP/2 in Azure front door
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448704"
---
# <a name="http2-support-in-azure-front-door"></a>Supporto HTTP/2 nella porta anteriore di Azure

Attualmente, il supporto HTTP/2 è attivo per tutte le configurazioni di porte di Azure front-end. Non è necessaria alcuna azione da parte dei clienti.

HTTP/2 è una revisione principale di HTTP/1.1 che offre prestazioni Web più veloci grazie alla riduzione del tempo di risposta. HTTP/2 viene eseguito gestendo i metodi HTTP, i codici di stato e la semantica noti per migliorare l'esperienza utente. Sebbene HTTP/2 sia progettato per funzionare con HTTP e HTTPS, molti Web browser client supportano solo HTTP/2 su Transport Layer Security (TLS).

> [!NOTE]
> Il supporto del protocollo HTTP/2 è disponibile solo per le richieste da client a sportello anteriore. La comunicazione dalla porta anteriore ai back-end nel pool back-end avviene tramite HTTP/1.1. 

### <a name="http2-benefits"></a>Vantaggi di HTTP/2

I vantaggi di HTTP/2 includono:

*   **Multiplexing e concorrenza**

    Con HTTP 1.1, se si eseguono più richieste di risorse multiple sono necessarie più connessioni TCP e a ogni connessione è associato un overhead delle prestazioni. HTTP/2 consente di richiedere più risorse in un'unica connessione TCP.

*   **Compressione delle intestazioni**

    Comprimendo le intestazioni HTTP per le risorse servite, il tempo di trasferimento è ridotto in modo significativo.

*   **Dipendenze del flusso**

    Le dipendenze del flusso consentono al client di indicare al server le risorse con priorità.


## <a name="http2-browser-support"></a>Supporto browser HTTP/2

Tutti i browser principali hanno implementato il supporto HTTP/2 le versioni correnti. I browser non supportati eseguono automaticamente il fallback a HTTP/1.1.

|Browser|Versione minima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su HTTP/2, vedere le risorse seguenti:

- [Home page delle specifiche HTTP/2](https://http2.github.io/)
- [Domande frequenti su HTTP/2](https://http2.github.io/faq/)
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
