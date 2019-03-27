---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 5ad1339c04444bcb4cc550be26e239e65227d2ce
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58494841"
---
| Risorsa | Limite predefinito | Note |
| --- | --- | --- |
| Gateway applicazione di Azure |1.000 per ogni sottoscrizione | |
| Configurazioni IP front-end |2 |1 pubblica e 1 privata |
| Porte front-end |100<sup>1</sup> | |
| Pool di indirizzi back-end |100<sup>1</sup> | |
| Server back-end per pool |1200 | |
| Listener HTTP |100<sup>1</sup> | |
| Regole di bilanciamento del carico HTTP |100<sup>1</sup> | |
| Back-end impostazioni HTTP |100<sup>1</sup> | |
| Istanze per gateway |32 | |
| Certificati SSL |100<sup>1</sup> |1 per listener HTTP |
| Certificati di autenticazione |100 | |
| Certificati radice trusted |100 | |
| Timeout della richiesta minima |1 secondo | |
| Timeout della richiesta massima |24 ore | |
| Numero di siti |100<sup>1</sup> |1 per listener HTTP |
| Mappe URL per listener |1 | |
| Numero massimo di regole basate sul percorso per mappa di URL|100||
| Configurazioni di reindirizzamento |100<sup>1</sup>| |
| Connessioni WebSocket simultanee |Gateway di medie dimensioni 20 KB<br> Gateway di grandi dimensioni 50k| |
| Lunghezza massima dell'URL|8.000||
| Dimensioni massime caricamento file, Standard |2 GB | |
| Dimensioni massime caricamento file WAF |Gateway WAF medi, 100 MB<br>Gateway di grandi dimensioni WAF, 500 MB| |
| Limite di dimensioni del corpo di WAF, senza i file|128 KB||

<sup>1</sup> in caso di SKU WAF-abilitato, è consigliabile limitare il numero di risorse su 40 per ottenere prestazioni ottimali.
