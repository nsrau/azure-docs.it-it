---
title: Supporto HTTP/2 nella rete CDN di Azure| Microsoft Docs
description: Informazioni sul supporto HTTP/2 e la rete CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67849981"
---
# <a name="http2-support-in-azure-cdn"></a>Supporto HTTP/2 nella rete CDN di Azure

HTTP/2 è una revisione principale del protocollo HTTP/1.1\. Fornisce prestazioni Web più veloci, riduce il tempo di risposta e offre un'esperienza utente migliore pur mantenendo invariati i metodi, i codici di stato e la semantica di HTTP. Anche se HTTP/2 è progettato per funzionare con HTTP e HTTPS, molti browser Web client supportano solo HTTP/2 su TLS.

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

## <a name="enabling-http2-support-in-azure-cdn"></a>Abilitazione del supporto HTTP/2 nella rete CDN di Azure

Il supporto HTTP/2 è attualmente attivo per tutti i profili di rete CDN di Azure. Non è necessaria alcuna azione da parte dei clienti.

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare i vantaggi di HTTP/2 in azione, vedere [questa demo di Akamai](https://http2.akamai.com/demo).

Per altre informazioni su HTTP/2, vedere le risorse seguenti:

*   [Home page delle specifiche HTTP/2](https://http2.github.io/)
*   [Domande frequenti su HTTP/2](https://http2.github.io/faq/)
*   [Informazioni su HTTP/2 di Akamai](https://http2.akamai.com/)

Per altre informazioni sulle funzionalità disponibili della rete CDN di Azure, vedere [Panoramica della rete CDN di Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).