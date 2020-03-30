---
title: File di inclusione
description: File di inclusione
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335084"
---
| Risorsa | Limite |
| --- | --- |
| Risorse di Azure Front Door per sottoscrizioneAzure Front Door resources per subscription | 100 |
| Host front-end, che include domini personalizzati per risorsa | 500 |
| Regole di gestione per risorsa | 500 |
| Pool back-end per risorsa | 50 |
| Back-end per pool back-end | 100 |
| Criteri di percorso da soddisfare per una regola di gestione | 25 |
| URL in una singola chiamata di eliminazione della cache | 100 |
| Regole web application firewall personalizzate per criterio | 100 |
| Criteri firewall applicazione Web per sottoscrizioneWeb application firewall policy per subscription | 100 |
| Condizioni di corrispondenza del firewall dell'applicazione Web per regola personalizzataWeb application firewall match conditions per custom rule | 10 |
| Intervalli di indirizzi IP del firewall dell'applicazione Web per condizione di corrispondenza | 600 |
| Valori di corrispondenza delle stringhe del firewall dell'applicazione Web per condizione di corrispondenza | 10 |
| Lunghezza valore corrispondenza stringa firewall applicazione Web | 256 |
| Web application firewall POST body nome del nome del nome del nome del parametro | 256 |
| Lunghezza nome intestazione HTTP del firewall dell'applicazione Web | 256 |
| Lunghezza del nome del cookie del firewall dell'applicazione Web | 256 |
| Dimensione del corpo della richiesta HTTP del firewall dell'applicazione Web controllata | 128 KB |
| Lunghezza del corpo della risposta personalizzata del firewall dell'applicazione Web | 2 KB |

### <a name="timeout-values"></a>Valori di timeout
#### <a name="client-to-front-door"></a>Da client a Frontdoor
* Frontdoor ha un timeout per connessione TCP inattiva di 61 secondi.

#### <a name="front-door-to-application-back-end"></a>Porta anteriore per l'applicazione back-end
* Se la risposta è una risposta in blocchi, viene restituito un valore 200 se o quando viene ricevuto il primo blocco.
* Dopo che la richiesta HTTP viene inoltrata al back-end, Front Door attende 30 secondi per il primo pacchetto dal back-end. Quindi restituisce un errore 503 al client. Questo valore è configurabile tramite il campo sendRecvTimeoutSeconds nell'API.
    * Per gli scenari di memorizzazione nella cache, questo timeout non è configurabile e pertanto, se una richiesta viene memorizzata nella cache e richiede più di 30 secondi per il primo pacchetto da Front Door o dal back-end, viene restituito un errore 504 al client. 
* Dopo aver ricevuto il primo pacchetto dal back-end, Front Door attende 30 secondi in un timeout di inattività. Quindi restituisce un errore 503 al client. Questo valore di timeout non è configurabile.
* Il timeout della sessione TCP front-end al back-end è di 90 secondi.

### <a name="upload-and-download-data-limit"></a>Limite di caricamento e download dei dati

|  | Con codifica di trasferimento in blocchi (CTE) | Senza suddivisione in blocchi HTTP |
| ---- | ------- | ------- |
| **Scarica** | Non c'è limite alla dimensione del download. | Non c'è limite alla dimensione del download. |
| **Carica** |    Non c'è limite finché ogni caricamento CTE è inferiore a 2 GB. | La dimensione non può essere superiore a 2 GB. |

### <a name="other-limits"></a>Altri limiti
* Dimensione massima dell'URL - 8.192 byte - Specifica la lunghezza massima dell'URL non elaborato (schema, nome host, porta, percorso e stringa di query dell'URL)
* Dimensione massima stringa di query - 4.096 byte - Specifica la lunghezza massima della stringa di query, in byte.
* Dimensione massima intestazione risposta HTTP dall'URL del probe di integrità - 4.096 byte - Specifica la lunghezza massima di tutte le intestazioni di risposta dei probe di integrità. 
