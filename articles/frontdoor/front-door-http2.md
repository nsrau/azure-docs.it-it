---
title: Porta anteriore di Azure - Supporto HTTP2 Documenti Microsoft
description: Questo articolo illustra il supporto HTTP/2 in Azure Front Door
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
ms.openlocfilehash: 415a89cbccd6b43d64ba1a553d44ac003df5806a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471626"
---
# <a name="http2-support-in-azure-front-door"></a>Supporto HTTP/2 in Azure Front Door

Il supporto HTTP/2 è attualmente attivo per tutte le configurazioni di Frontdoor. Non è necessaria alcuna azione da parte dei clienti.

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su HTTP/2, vedere le risorse seguenti:

- [Home page delle specifiche HTTP/2](https://http2.github.io/)
- [Domande frequenti su HTTP/2](https://http2.github.io/faq/)
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
