---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334930"
---
| Risorsa | Limite | Note |
| --- | --- | --- |
| Gateway applicazione di Azure |1.000 per abbonamento | |
| Configurazioni IP front-end |2 |1 pubblica e 1 privata |
| Porte front-end |100<sup>1</sup> | |
| Pool di indirizzi back-end |100<sup>1</sup> | |
| Server back-end per pool |1200 | |
| Listener HTTP |200<sup>1 (in vi: "</sup> |Limite di 100 listener attivi che instradano il traffico. Listener attivi - numero totale di listener - listener non attivi.<br>Se una configurazione predefinita all'interno di una regola di routing è impostata per instradare il traffico (ad esempio, ha un listener, un pool back-end e impostazioni HTTP), questo viene conteggiato anche come listener.|
| Regole di bilanciamento del carico HTTPHTTP load-balancing rules |100<sup>1</sup> | |
| Back-end impostazioni HTTP |100<sup>1</sup> | |
| Istanze per gateway |SKU V1 - 32<br>SKU V2 - 125 | |
| Certificati SSL |100<sup>1</sup> |1 per listener HTTP |
| Dimensione massima certificato SSL |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Certificati di autenticazione |100 | |
| Certificati radice trusted |100 | |
| Timeout minimo richiesta |1 secondo | |
| Massimo timeout richiesta |24 ore | |
| Numero di siti |100<sup>1</sup> |1 per listener HTTP |
| Mappe URL per listener |1 | |
| Numero massimo di regole basate sul percorso per mappa di URL|100||
| Configurazioni di reindirizzamento |100<sup>1</sup>| |
| Connessioni WebSocket simultanee |Gateway medi 20k<br> Gateway grandi 50k| |
| Lunghezza massima dell'URL|32KB| |
| Dimensione massima dell'intestazione per HTTP/2Maximum header size for HTTP/2 |Da 4 KB| |
| Dimensione massima caricamento file, Standard |2 GB | |
| Dimensioni massime caricamento file WAF |Gateway V1 WAF medi, 100 MB<br>V1 Gateway WAF di grandi dimensioni, 500 MB<br>V2 WAF, 750 MB| |
| Limite dimensione del corpo WAF, senza file|128 KB||
| Numero massimo di regole personalizzate WAF|100||
| Numero massimo di esclusioni WAF|100||

<sup>1</sup> In caso di SKU abilitati per WAF, è consigliabile limitare il numero di risorse a 40 per ottenere prestazioni ottimali.
