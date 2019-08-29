---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: cd0ec07ebfffc839fedcdc76c9aff8e86556a7de
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130094"
---
| Risorsa | Limite predefinito/massimo | Nota |
| --- | --- | --- |
| Gateway applicazione di Azure |1\.000 per sottoscrizione | |
| Configurazioni IP front-end |2 |1 pubblica e 1 privata |
| Porte front-end |100<sup>1</sup> | |
| Pool di indirizzi back-end |100<sup>1</sup> | |
| Server back-end per pool |1200 | |
| Listener HTTP |100<sup>1</sup> | |
| Regole di bilanciamento del carico HTTP |100<sup>1</sup> | |
| Back-end impostazioni HTTP |100<sup>1</sup> | |
| Istanze per gateway |32 | |
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
| Lunghezza massima dell'URL|8\.000||
| Dimensioni massime di caricamento file, standard |2 GB | |
| Dimensioni massime caricamento file WAF |Gateway WAF medi, 100 MB<br>Gateway WAF di grandi dimensioni, 500 MB| |
| Limite dimensioni corpo WAF, senza file|128 KB||
| Numero massimo di regole personalizzate WAF|100||
| Numero massimo di esclusioni WAF|100||

<sup>1</sup> nel caso di SKU abilitati per WAF, è consigliabile limitare il numero di risorse a 40 per ottenere prestazioni ottimali.
