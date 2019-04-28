---
title: 'Servizio Frontdoor di Azure: supporto HTTP2 | Documentazione di Microsoft'
description: Questo articolo fornisce informazioni sul supporto HTTP/2 nel servizio Frontdoor di Azure
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
ms.openlocfilehash: 33e738f31be493d5890fc972ec71e7c6cd733bf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736450"
---
# <a name="http2-support-in-azure-front-door-service"></a>Supporto HTTP/2 nel servizio Frontdoor di Azure
HTTP/2 è una revisione principale del protocollo HTTP/1.1. Fornisce prestazioni Web più veloci, riduce il tempo di risposta e offre un'esperienza utente migliore pur mantenendo invariati i metodi, i codici di stato e la semantica di HTTP. Anche se HTTP/2 è progettato per funzionare con HTTP e HTTPS, molti browser Web client supportano solo HTTP/2 su TLS.

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

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Supporto HTTP/2 nel servizio Frontdoor di Azure

Il supporto HTTP/2 è attualmente attivo per tutte le configurazioni di Frontdoor. Non è necessaria alcuna azione da parte dei clienti.

## <a name="next-steps"></a>Fasi successive

Per altre informazioni su HTTP/2, vedere le risorse seguenti:

- [Home page delle specifiche HTTP/2](https://http2.github.io/)
- [Domande frequenti su HTTP/2](https://http2.github.io/faq/)
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
