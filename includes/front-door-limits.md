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
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333380"
---
| Resource | Limite predefinito o massimo |
| --- | --- |
| Risorse di Azure del servizio di ingresso principale per ogni sottoscrizione | 100 |
| Host front-end, che include i domini personalizzati per ogni risorsa | 100 |
| Regole di gestione per risorsa | 100 |
| Pool back-end per ogni risorsa | 50 |
| Back-end per ogni pool back-end | 100 |
| Criteri di percorso da soddisfare per una regola di gestione | 25 |
| Regole web application firewall personalizzate per criterio | 10 |
| Criteri web application firewall per risorsa | 100 |
| Condizioni di corrispondenza di Web application firewall per ogni regola personalizzata | 10 |
| Web application firewall intervalli di indirizzi IP per ogni condizione di corrispondenza | 600 |
| Corrispondono ai valori di Web application firewall stringa per ogni condizione di corrispondenza | 10 |
| Lunghezza del valore di corrispondenza stringa di Web application firewall | 256 |
| Firewall applicazione Web lunghezza del nome parametro corpo POST | 256 |
| Firewall applicazione Web lunghezza del nome dell'intestazione HTTP | 256 |
| Lunghezza del nome cookie del Web application firewall | 256 |
| Web application firewall HTTP del corpo della richiesta controllato | 128 KB |
| Lunghezza del corpo della risposta personalizzata Web application firewall | 2 KB |

### <a name="timeout-values"></a>Valori di timeout
#### <a name="client-to-front-door"></a>Da client a Frontdoor
- Frontdoor ha un timeout per connessione TCP inattiva di 61 secondi.

#### <a name="front-door-to-application-back-end"></a>Porta di ingresso al back-end dell'applicazione
- Se la risposta è una risposta in blocchi, venga restituito 200 se o quando viene ricevuto il primo blocco.
- Dopo la richiesta HTTP viene inoltrata al back-end, porta principale attende 30 secondi per il primo pacchetto dal back-end. Viene restituito un errore 503 al client.
- Dopo il primo pacchetto è stato ricevuto dal back-end, porta principale attende 30 secondi in un timeout di inattività. Viene restituito un errore 503 al client.
- Porta di ingresso per il timeout della sessione TCP back-end è di 30 minuti.

### <a name="upload-and-download-data-limit"></a>Caricare e scaricare dati limite

|  | Con la codifica (CTE) di trasferimento chunked | Senza la suddivisione in blocchi HTTP |
| ---- | ------- | ------- |
| **Scaricare** | Non è previsto alcun limite alla dimensione del download. | Non è previsto alcun limite alla dimensione del download. |
| **Upload** |  Non è previsto alcun limite, purché ogni caricamento CTE è minore di 2 GB. | Le dimensioni non possono essere superiori a 2 GB. |

### <a name="other-limits"></a>Altri limiti
- Dimensione massima dell'URL - 8.192 byte - specifica la lunghezza massima dell'URL non elaborato (combinazione di nome host porta + percorso + stringa dell'URL di query) - dimensione massima delle stringhe di Query - corrispondono a 4.096 byte - specifica la lunghezza massima della stringa di query, in byte.