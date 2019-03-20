---
title: File di inclusione
description: File di inclusione
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: e3fa5616518675d8475937ec63afdd8e1742e8c6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553522"
---
| Risorsa | Limite predefinito |
| --- | --- |
| Risorse di Azure del servizio di ingresso principale per ogni sottoscrizione | 100 |
| Host front-end, che include i domini personalizzati per ogni risorsa | 100 |
| Regole di gestione per risorsa | 100 |
| Pool back-end per ogni risorsa | 50 |
| Back-end per ogni pool back-end | 100 |
| Criteri di percorso da soddisfare per una regola di gestione | 25 |
| Regole web application firewall personalizzate per criterio | 10 |
| Criteri web application firewall per risorsa | 100 |

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
| **Upload** |  Non è previsto alcun limite, purché ogni caricamento CTE è inferiore a 28,6 MB. | La dimensione non può essere maggiore di 28,6. MB. |
