---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 592e1973344b231693077f8286a41dfd67a8d188
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689117"
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
|Massime regole personalizzate di Web Application firewall|100||

<sup>1</sup> in caso di SKU WAF-abilitato, è consigliabile limitare il numero di risorse su 40 per ottenere prestazioni ottimali.
