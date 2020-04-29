---
title: includere il file
description: File di inclusione
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335084"
---
| Risorsa | Limite |
| --- | --- |
| Risorse di Azure front door per sottoscrizione | 100 |
| Host front-end, che include domini personalizzati per risorsa | 500 |
| Regole di gestione per risorsa | 500 |
| Pool back-end per risorsa | 50 |
| Back-end per ogni pool back-end | 100 |
| Criteri di percorso da soddisfare per una regola di gestione | 25 |
| URL in una singola chiamata di eliminazione della cache | 100 |
| Regole web application firewall personalizzate per criterio | 100 |
| Criteri di Web Application Firewall per sottoscrizione | 100 |
| Condizioni di corrispondenza del firewall applicazione Web per regola personalizzata | 10 |
| Intervalli di indirizzi IP del firewall applicazione Web per condizione di corrispondenza | 600 |
| Valori di corrispondenza stringa del firewall applicazione Web per condizione di corrispondenza | 10 |
| Lunghezza valore corrispondenza stringa del firewall applicazione Web | 256 |
| Lunghezza del nome del parametro del corpo del firewall dell'applicazione Web | 256 |
| Lunghezza del nome dell'intestazione HTTP del Web Application Firewall | 256 |
| Lunghezza del nome del cookie del Web Application Firewall | 256 |
| Dimensioni del corpo della richiesta HTTP del Web Application Firewall ispezionate | 128 KB |
| Lunghezza del corpo della risposta personalizzata del firewall applicazione Web | 2 KB |

### <a name="timeout-values"></a>Valori di timeout
#### <a name="client-to-front-door"></a>Da client a Frontdoor
* Frontdoor ha un timeout per connessione TCP inattiva di 61 secondi.

#### <a name="front-door-to-application-back-end"></a>Back-end della porta anteriore al back-end dell'applicazione
* Se la risposta è una risposta in blocchi, viene restituito un 200 se o quando viene ricevuto il primo blocco.
* Dopo che la richiesta HTTP è stata trasmessa al back-end, la porta anteriore attende 30 secondi per il primo pacchetto dal back-end. Restituisce quindi un errore 503 al client. Questo valore può essere configurato tramite il campo sendRecvTimeoutSeconds nell'API.
    * Per gli scenari di memorizzazione nella cache, questo timeout non è configurabile e quindi, se una richiesta viene memorizzata nella cache e richiede più di 30 secondi per il primo pacchetto dall'sportello anteriore o dal back-end, viene restituito un errore 504 al client. 
* Una volta ricevuto il primo pacchetto dal back-end, il front-end attende per 30 secondi il timeout di inattività. Restituisce quindi un errore 503 al client. Questo valore di timeout non è configurabile.
* La porta anteriore al timeout della sessione TCP di back-end è 90 secondi.

### <a name="upload-and-download-data-limit"></a>Caricare e scaricare il limite di dati

|  | Con la codifica di trasferimento in blocchi (CTE) | Senza blocco HTTP |
| ---- | ------- | ------- |
| **Scarica** | Non è previsto alcun limite per le dimensioni del download. | Non è previsto alcun limite per le dimensioni del download. |
| **Carica** |    Non esiste alcun limite se ogni caricamento della CTE è inferiore a 2 GB. | Le dimensioni non possono essere superiori a 2 GB. |

### <a name="other-limits"></a>Altri limiti
* Dimensioni massime URL-8.192 byte-specifica la lunghezza massima dell'URL non elaborato (schema + nome host + porta + percorso + stringa di query dell'URL)
* Dimensioni massime stringa di query-4.096 byte-specifica la lunghezza massima, in byte, della stringa di query.
* Dimensioni massime dell'intestazione della risposta HTTP dall'URL del probe di integrità-4.096 byte-è stata specificata la lunghezza massima di tutte le intestazioni di risposta dei probe di integrità. 
