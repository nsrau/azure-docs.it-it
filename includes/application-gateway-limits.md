---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 65ed28c967164be4d239cd4d59b6b36f06caeced
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238745"
---
| Resource | Limite predefinito o massimo | Note |
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
| Dimensioni massime di certificato SSL |V1 SKU - 10 KB<br>V2 SKU - 25 KB| |
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
