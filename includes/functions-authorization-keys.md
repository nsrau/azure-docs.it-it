---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829116"
---
Funzioni consente di usare in fase di sviluppo le chiavi che rendono più difficile accedere agli endpoint di funzione HTTP. A meno che il livello di accesso HTTP in una funzione attivata tramite HTTP non sia impostato su `anonymous`, le richieste devono includere una chiave di accesso all'API. 

Sebbene le chiavi forniscano un meccanismo di sicurezza predefinito, è consigliabile prendere in considerazione opzioni aggiuntive per proteggere un endpoint HTTP nell'ambiente di produzione. Ad esempio, in genere non è consigliabile distribuire il segreto condiviso nelle app pubbliche. Se la funzione viene chiamata da un client pubblico, è consigliabile implementare un altro meccanismo di sicurezza. Per altre informazioni, vedere [Proteggere un endpoint HTTP nell'ambiente di produzione](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

Quando si rinnovano i valori della chiave della funzione, è necessario ridistribuire manualmente i valori di chiave aggiornati a tutti i client che chiamano la funzione.  

#### <a name="authorization-scopes-function-level"></a>Ambiti di autorizzazione (a livello di funzione)

Sono disponibili due ambiti dell'accesso per le chiavi a livello di funzione:

* **Funzione**: queste chiavi si applicano solo alle funzioni specifiche per le quali vengono definite. Quando vengono usate come chiave API, consentono l'accesso solo a tale funzione.

* **Host**: È possibile usare le chiavi con un ambito host per accedere a tutte le funzioni all'interno dell'app per le funzioni. Quando vengono usate come chiave API, consentono l'accesso a tutte le funzioni nell'app per le funzioni. 

Ogni chiave viene denominata per riferimento ed esiste una chiave predefinita (denominata "default") a livello di funzione e di host. Le chiavi di funzione hanno la precedenza sulle chiavi host. Se sono definite due chiavi con lo stesso nome, viene usata sempre la chiave di funzione.

#### <a name="master-key-admin-level"></a>Chiave master (a livello di amministratore) 

Ogni app per le funzioni dispone anche di una chiave host a livello di amministratore denominata `_master`. Oltre a fornire l'accesso a livello di host a tutte le funzioni nell'app, la chiave master offre anche l'accesso amministrativo alle API REST di runtime. Questa chiave non può essere revocata. Quando si imposta un livello di accesso `admin`, le richieste devono usare la chiave master. Qualsiasi altra chiave provoca un errore di accesso.

> [!CAUTION]  
> Date le autorizzazioni elevate concesse dalla chiave master nell'app per le funzioni, è consigliabile non condividere questa chiave con terze parti o distribuirla in applicazioni client native. Fare attenzione quando si sceglie il livello di accesso di amministratore.
