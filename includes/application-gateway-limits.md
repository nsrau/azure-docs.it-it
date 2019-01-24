---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211862"
---
| Risorsa | Limite predefinito | Note |
| --- | --- | --- |
| Gateway applicazione |1000 per sottoscrizione | |
| Configurazione di IP front-end |2 |1 pubblica e 1 privata |
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
| Timeout minimo delle richieste |1 secondo | |
| Timeout massimo delle richieste |24 ore | |
| Numero di siti |100<sup>1</sup> |1 per listener HTTP |
| Mappe URL per listener |1 | |
| Numero massimo di regole basate sul percorso per mappa di URL|100||
| Configurazioni di reindirizzamento |100<sup>1</sup>| |
| Connessioni WebSocket simultanee |5000| |
| Lunghezza massima dell'URL|8000||
| Dimensioni massime caricamento file Standard |2 GB | |
| Dimensioni massime caricamento file WAF |Gateway WAF medi - 100 MB<br>Gateway WAF grandi - 500 MB| |
| Limite dimensioni corpo WAF (senza file)|128 KB||

<sup>1</sup> in caso di SKU WAF-abilitato, è consigliabile limitare il numero di risorse a 40 per ottenere prestazioni ottimali.
