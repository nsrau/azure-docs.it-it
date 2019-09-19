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
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67333380"
---
| Risorsa | Limite predefinito/massimo |
| --- | --- |
| Risorse del servizio front door di Azure per ogni sottoscrizione | 100 |
| Host front-end, che include domini personalizzati per risorsa | 100 |
| Regole di gestione per risorsa | 100 |
| Pool back-end per risorsa | 50 |
| Back-end per ogni pool back-end | 100 |
| Criteri di percorso da soddisfare per una regola di gestione | 25 |
| Regole web application firewall personalizzate per criterio | 10 |
| Criteri web application firewall per risorsa | 100 |
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
- Frontdoor ha un timeout per connessione TCP inattiva di 61 secondi.

#### <a name="front-door-to-application-back-end"></a>Back-end della porta anteriore al back-end dell'applicazione
- Se la risposta è una risposta in blocchi, viene restituito un 200 se o quando viene ricevuto il primo blocco.
- Dopo che la richiesta HTTP è stata trasmessa al back-end, la porta anteriore attende 30 secondi per il primo pacchetto dal back-end. Restituisce quindi un errore 503 al client.
- Una volta ricevuto il primo pacchetto dal back-end, il front-end attende per 30 secondi il timeout di inattività. Restituisce quindi un errore 503 al client.
- La porta anteriore al timeout della sessione TCP di back-end è 30 minuti.

### <a name="upload-and-download-data-limit"></a>Caricare e scaricare il limite di dati

|  | Con la codifica di trasferimento in blocchi (CTE) | Senza blocco HTTP |
| ---- | ------- | ------- |
| **Scaricare** | Non è previsto alcun limite per le dimensioni del download. | Non è previsto alcun limite per le dimensioni del download. |
| **Upload** |  Non esiste alcun limite se ogni caricamento della CTE è inferiore a 2 GB. | Le dimensioni non possono essere superiori a 2 GB. |

### <a name="other-limits"></a>Altri limiti
- Dimensioni massime URL-8.192 byte-specifica la lunghezza massima dell'URL non elaborato (schema + nome host + porta + percorso + stringa di query dell'URL)-dimensione massima della stringa di query-4.096 byte-specifica la lunghezza massima, in byte, della stringa di query.