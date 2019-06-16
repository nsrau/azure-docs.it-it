---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/09/2018
ms.author: estfan
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161949"
---
Per stimare in modo più accurato i costi di utilizzo, prendere in considerazione il numero di messaggi o eventi che potrebbero arrivare ogni giorno, invece di basare i calcoli solo sull'intervallo di polling. Quando un evento o un messaggio soddisfa i criteri di trigger, molti trigger provano immediatamente a leggere tutti gli altri eventi in attesa o i messaggi che soddisfano i criteri. Questo comportamento significa che anche quando si seleziona un intervallo di polling più lungo, il trigger viene attivato in base al numero di eventi in attesa o di messaggi che soddisfano le condizioni per l'avvio dei flussi di lavoro. I trigger che seguono questo comportamento includono il bus di servizio di Azure e l'hub eventi di Azure.

Si supponga, ad esempio, di configurare un trigger che controlla un endpoint ogni giorno. Quando il trigger controlla l'endpoint e trova 15 eventi che soddisfano i criteri, il trigger viene attivato ed esegue il flusso di lavoro corrispondente 15 volte. App per la logica conteggia tutte le azioni eseguite da questi 15 flussi di lavoro, incluse le richieste del trigger. Per calcolare i costi potenziali, provare a usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).