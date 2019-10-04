---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180431"
---
Il tipo di servizio e di sottoscrizione determina il numero di query al secondo che è possibile effettuare. Assicurarsi che l'applicazione includa la logica necessaria per rimanere entro la quota. Se il limite di query al secondo viene raggiunto o superato, la richiesta avrà esito negativo e verrà restituito un codice di stato HTTP 429. La risposta include l'intestazione `Retry-After`, che indica il tempo di attesa necessario prima dell'invio di un'altra richiesta.

## <a name="denial-of-service-versus-throttling"></a>Confronto tra Denial of Service (DoS) e limitazione

Il servizio distingue tra un attacco Denial of Service e una violazione del limite di query al secondo. Se il servizio sospetta un attacco Denial of Service, la richiesta avrà esito positivo, con codice di stato HTTP 200 OK. Il corpo della risposta sarà tuttavia vuoto.