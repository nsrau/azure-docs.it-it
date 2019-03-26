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
ms.openlocfilehash: a3a43c56a49c243390eac964d31988b7d30fbb56
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407794"
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
| **Upload** |  Non è previsto alcun limite, purché ogni caricamento CTE è minore di 2 GB. | Le dimensioni non possono essere superiori a 2 GB. |
