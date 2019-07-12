---
title: Intestazioni HTTP specifiche di Verizon per il motore regole della rete CDN di Azure | Microsoft Docs
description: Questo articolo descrive come usare le intestazioni HTTP specifiche di Verizon con il motore regole della rete CDN di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593255"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Intestazioni HTTP specifiche di Verizon per il motore regole della rete CDN di Azure

Pe i prodotti **Azure CDN Premium di Verizon**, quando una richiesta HTTP viene inviata al server di origine, il server POP (Point Of Presence) può aggiungere una o due intestazioni riservate (o intestazioni speciali proxy) nella richiesta client al POP. Queste intestazioni si aggiungono alle intestazioni di inoltro standard ricevute. Per informazioni sulle intestazioni di richiesta standard, vedere [Request fields](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields) (Campi di richiesta).

Se si vuole impedire l'aggiunta di una di queste intestazioni riservate nella richiesta POP della rete CDN (rete per la distribuzione di contenuti) di Azure al server di origine, è necessario creare una regola con la [funzionalità Intestazioni speciali proxy](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) nel motore regole. In questa regola, escludere l'intestazione che si vuole rimuovere dall'elenco predefinito di intestazioni nel campo delle intestazioni. Se si è abilitata la [funzionalità Intestazioni di risposta di debug per la cache](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers), assicurarsi di aggiungere le intestazioni `X-EC-Debug` necessarie. 

Ad esempio, per rimuovere il `Via` intestazione, il campo intestazioni della regola deve includere l'elenco di intestazioni seguente: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Regola Intestazioni speciali proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

La tabella seguente descrive le intestazioni che possono essere aggiunte dal POP della rete CDN Verizon:

Intestazione della richiesta | Descrizione | Esempio
---------------|-------------|--------
[Via](#via-request-header) | Identifica il server POP che ha trasmesso tramite proxy la richiesta al server di origine. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | Indica l'indirizzo IP del richiedente.| 10.10.10.10
X-Forwarded-Proto | Indica il protocollo della richiesta. | http
X-Host | Indica il nome host della richiesta. | cdn.mydomain.com
X-Midgress | Indica se la richiesta è stata trasmessa tramite proxy da un server CDN aggiuntivo. Ad esempio, da un server POP a un server shield di origine o da un server POP a un server gateway ADN. <br />Questa intestazione viene aggiunta alla richiesta solo in caso di traffico midgress. In questo caso l'intestazione viene impostata su 1 per indicare che la richiesta è stata trasmessa tramite proxy da un server CDN aggiuntivo.| 1
[Host](#host-request-header) | Identifica l'host e la porta in cui trovare il contenuto richiesto. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: Identifica l'elenco di failover dei server Gateway ADN assegnati a un'origine cliente. <br />Shield origine: Indica il set di server shield di origine assegnato a un'origine cliente. | `icn1,hhp1,hnd1`
X-EC- _&lt;nome&gt;_ | Le intestazioni di richiesta che iniziano con *X-EC* (ad esempio X-EC-Tag, [X-EC-Debug](cdn-http-debug-headers.md)) sono riservate per l'uso da parte della rete CDN.| waf-production

## <a name="via-request-header"></a>Intestazione di richiesta Via
Il formato tramite il quale l'intestazione di richiesta `Via` identifica un server POP è specificato dalla sintassi seguente:

`Via: Protocol from Platform (POP/ID)` 

I termini usati nella sintassi sono definiti nel modo seguente:
- Protocollo: Indica che la versione del protocollo (ad esempio HTTP/1.1) usato per proxy la richiesta. 

- Piattaforma: Indica la piattaforma su cui è stato richiesto il contenuto. I codici seguenti sono validi per questo campo: 

    Codice | Piattaforma
    -----|---------
    ECAcc | HTTP Large
    ECS   | HTTP Small
    ECD   | Application Delivery Network (ADN)

- POP: Indica la [POP](cdn-pop-abbreviations.md) che ha gestito la richiesta. 

- ID: Solo per uso interno.

### <a name="example-via-request-header"></a>Esempio di intestazione di richiesta Via

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Intestazione di richiesta Host
I server POP sovrascriveranno l'intestazione `Host` quando entrambe le condizioni seguenti sono vere:
- L'origine del contenuto richiesto è un server di origine cliente.
- L'opzione Intestazione host HTTP dell'origine cliente corrispondente non è vuota.

L'intestazione di richiesta `Host` verrà sovrascritta per riflettere il valore definito nell'opzione Intestazione host HTTP.
Se l'opzione Intestazione host HTTP dell'origine cliente è vuota, l'intestazione di richiesta `Host` inviata dal richiedente verrà inoltrata al server di origine del cliente.

## <a name="x-gateway-list-request-header"></a>Intestazione di richiesta X-Gateway-List
Un server POP aggiungerà/sovrascriverà l'intestazione di richiesta X-Gateway-List quando è soddisfatta una delle condizioni seguenti:
- La richiesta punta alla piattaforma ADN.
- La richiesta viene inoltrata a un server di origine del cliente protetto dalla funzionalità Shield origine.

