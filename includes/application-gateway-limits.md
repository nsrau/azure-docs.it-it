---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 3d66d825306c5183bdd8d8e611d98904eef2022a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440235"
---
| Risorsa | Limite predefinito | Note |
| --- | --- | --- |
| Gateway applicazione |1000 per sottoscrizione | |
| Configurazioni IP front-end |2 |1 pubblica e 1 privata |
| Porte front-end |40 | |
| Pool di indirizzi back-end |40 | |
| Server back-end per pool |1200 | |
| Listener HTTP |40 | |
| Regole di bilanciamento del carico HTTP |400 |Numero di listener HTTP * n |
| Impostazioni HTTP back-end |40 | |
| Istanze per gateway |75 | |
| Certificati SSL |40 |1 per listener HTTP |
| Certificati di autenticazione |40 | |
| Timeout minimo delle richieste |1 secondo | |
| Timeout massimo delle richieste |24 ore | |
| Numero di siti |40 |1 per listener HTTP |
| Mappe URL per listener |1 | |
| Numero massimo di regole basate sul percorso per mappa di URL|100|
| Configurazioni di reindirizzamento |40| |
| Connessioni WebSocket simultanee |5000| |
|Lunghezza massima dell'URL|8000|
| Dimensioni massime caricamento file Standard |2 GB | |
| Dimensioni massime caricamento file WAF |Gateway WAF medi - 100 MB<br>Gateway WAF grandi - 500 MB| |
|Limite dimensioni corpo WAF (senza file)|128 KB|
