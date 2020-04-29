---
title: Azure front door-supporto HTTP2 | Microsoft Docs
description: Questo articolo fornisce informazioni sul supporto HTTP/2 in Azure front door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 8a3ae8065553b34a72528cb0f2681e327dc90097
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985185"
---
# <a name="http2-support-in-azure-front-door"></a>Supporto HTTP/2 nella porta anteriore di Azure

Attualmente, il supporto HTTP/2 è attivo per tutte le configurazioni di porte di Azure front-end. Non è necessaria alcuna azione da parte dei clienti.

HTTP/2 è una revisione principale del protocollo HTTP/1.1. Fornisce prestazioni Web più veloci, riduce il tempo di risposta e offre un'esperienza utente migliore pur mantenendo invariati i metodi, i codici di stato e la semantica di HTTP. Anche se HTTP/2 è progettato per funzionare con HTTP e HTTPS, molti browser Web client supportano solo HTTP/2 su TLS.

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

Tutti i browser principali hanno implementato il supporto HTTP/2 le versioni correnti. Per i browser non supportati viene eseguito il fallback automatico a HTTP/1.1.

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
