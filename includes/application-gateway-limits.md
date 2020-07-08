---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334930"
---
| Risorsa | Limite | Nota |
| --- | --- | --- |
| Gateway applicazione di Azure |1.000 per sottoscrizione | |
| Configurazioni IP front-end |2 |1 pubblica e 1 privata |
| Porte front-end |100<sup>1</sup> | |
| Pool di indirizzi back-end |100<sup>1</sup> | |
| Server back-end per pool |1200 | |
| Listener HTTP |200<sup>1</sup> |Limitato a 100 listener attivi che eseguono il routing del traffico. Listener attivi = numero totale di listener: listener non attivi.<br>Se una configurazione predefinita all'interno di una regola di routing è impostata su instradare il traffico (ad esempio, ha un listener, un pool back-end e le impostazioni HTTP), questo viene conteggiato anche come listener.|
| Regole di bilanciamento del carico HTTP |100<sup>1</sup> | |
| Back-end impostazioni HTTP |100<sup>1</sup> | |
| Istanze per gateway |SKU V1-32<br>SKU V2-125 | |
| Certificati SSL |100<sup>1</sup> |1 per listener HTTP |
| Dimensioni massime del certificato SSL |SKU V1-10 KB<br>SKU V2-16 KB| |
| Certificati di autenticazione |100 | |
| Certificati radice trusted |100 | |
| Valore minimo timeout richiesta |1 secondo | |
| Massimo timeout richiesta |24 ore | |
| Numero di siti |100<sup>1</sup> |1 per listener HTTP |
| Mappe URL per listener |1 | |
| Numero massimo di regole basate sul percorso per mappa di URL|100||
| Configurazioni di reindirizzamento |100<sup>1</sup>| |
| Connessioni WebSocket simultanee |Gateway medio 20.000<br> Gateway di grandi dimensioni 50.000| |
| Lunghezza massima dell'URL|32 KB| |
| Dimensioni massime dell'intestazione per HTTP/2 |4KB| |
| Dimensioni massime di caricamento file, standard |2 GB | |
| Dimensioni massime caricamento file WAF |V1 gateway WAF medi, 100 MB<br>V1 gateway WAF di grandi dimensioni, 500 MB<br>V2 WAF, 750 MB| |
| Limite dimensioni corpo WAF, senza file|128 KB||
| Numero massimo di regole personalizzate WAF|100||
| Numero massimo di esclusioni WAF|100||

<sup>1</sup> nel caso di SKU abilitati per WAF, è consigliabile limitare il numero di risorse a 40 per ottenere prestazioni ottimali.
