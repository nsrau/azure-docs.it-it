---
ms.assetid: 
title: Guida alla limitazione delle richieste per Azure Key Vault | Microsoft Docs
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 06/21/2017
ms.openlocfilehash: fe700e22c5323c2a0bdc315e349cd119798bcf40
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-key-vault-throttling-guidance"></a>Guida alla limitazione delle richieste per Azure Key Vault

La limitazione delle richieste è un processo che consente di limitare il numero di chiamate simultanee al servizio di Azure per prevenire l'uso eccessivo delle risorse. Azure Key Vault è progettato per gestire un volume di richieste elevato. In caso di un numero eccessivamente elevato di richieste, la limitazione delle richieste del client aiuta a mantenere le prestazioni ottimali e l'affidabilità del servizio di Azure Key Vault.

I limiti delle richieste variano in base allo scenario. Ad esempio, se si esegue un volume elevato di operazioni di scrittura, la possibilità di limitare le richieste è maggiore rispetto a quando si eseguono solo operazioni di lettura.

## <a name="how-does-key-vault-handle-its-limits"></a>Gestione dei limiti da parte di Key Vault

I limiti del servizio in Key Vault hanno lo scopo di evitare un uso improprio delle risorse e garantire la qualità del servizio per tutti i client di Key Vault. Quando si supera la soglia di un servizio, Key Vault limita le nuove richieste del client per un periodo di tempo. In questo caso, Key Vault restituisce il codice di stato HTTP 429, ovvero Too many requests (Numero di richieste eccessivo), e le richieste hanno esito negativo. Inoltre, le richieste non riuscite che restituiscono un codice 429 valgono per le limitazioni tracciate da Key Vault. 

Nel caso l'utente abbia delle esigenze aziendali per cui sono necessarie delle limitazioni maggiori, contattare Microsoft.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Come limitare le richieste per l'app in risposta ai limiti del servizio

Di seguito sono riportate le **pratiche ottimali** per la limitazione delle richieste per l'app:
- Ridurre il numero di operazioni per ogni richiesta.
- Ridurre la frequenza delle richieste.
- Evitare tentativi immediati. 
    - Tutte le richieste si accumulano per i limiti di consumo.

Quando si implementa la gestione degli errori dell'app, usare il codice di errore HTTP 429 per rilevare la necessità di limitazione delle richieste lato client. Se la richiesta ha di nuovo esito negativo con un codice di errore HTTP 429, vuol dire che ci sono ancora limiti nel servizio di Azure. Continuare a usare il metodo di limitazione delle richieste lato client consigliato, provando a inviare nuovamente la richiesta fino a quando non ha esito positivo.

### <a name="recommended-client-side-throttling-method"></a>Metodo consigliato per la limitazione delle richieste lato client

Quando si genera il codice di errore HTTP 429, iniziare a limitare le richieste del client con un approccio di backoff esponenziale:

1. Attendere 1 secondo e ripetere la richiesta
2. Se viene ancora limitata, attendere 2 secondi e ripetere la richiesta
3. Se viene ancora limitata, attendere 4 secondi e ripetere la richiesta
4. Se viene ancora limitata, attendere 8 secondi e ripetere la richiesta
5. Se viene ancora limitata, attendere 16 secondi e ripetere la richiesta

A questo punto, il codice di risposta HTTP 429 dovrebbe non essere più visualizzato.

## <a name="see-also"></a>Vedere anche

Per un approfondimento sulla limitazione delle richieste nel cloud di Microsoft, vedere [Throttling Pattern](https://docs.microsoft.com/azure/architecture/patterns/throttling) (Modello di limitazione delle richieste).

