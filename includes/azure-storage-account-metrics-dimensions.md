---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711424"
---
| Nome della dimensione | Descrizione |
| ------------------- | ----------------- |
| **GeoType** | Transazione da un cluster primario o secondario. I valori disponibili includono **Primary** e **Secondary**. È applicabile all'archiviazione con ridondanza geografica e accesso in lettura durante la lettura di oggetti da un tenant secondario. |
| **ResponseType** | Tipo di risposta della transazione. I valori disponibili includono: <br/><br/> <li>**ServerOtherError**: tutti gli altri errori lato server ad eccezione di quelli descritti. </li> <li>**ServerBusyError**: richiesta autenticata che ha restituito un codice di stato HTTP 503. </li> <li>**ServerTimeoutError**: richiesta autenticata con timeout che ha restituito un codice di stato HTTP 500. Il timeout si è verificato a causa di un errore del server. </li> <li>**AuthorizationError**: richiesta autenticata con esito negativo a causa dell'accesso non autorizzato ai dati o di un errore di autorizzazione. </li> <li>**NetworkError**: richiesta autenticata con esito negativo a causa di errori di rete. Questo errore si verifica in genere quando un cliente chiude prematuramente una connessione prima della scadenza del timeout. </li><li>**ClientAccountBandwidthThrottlingError**: la richiesta è limitata per la larghezza di banda per il superamento dei [limiti di scalabilità dell'account di archiviazione](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).</li><li>**ClientAccountRequestThrottlingError**: la richiesta è limitata per la frequenza di richieste per il superamento dei [limiti di scalabilità dell'account di archiviazione](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).<li>**ClientThrottlingError**: altro errore di limitazione sul lato client. ClientAccountBandwidthThrottlingError e ClientAccountRequestThrottlingError sono esclusi.</li> <li>**ClientTimeoutError**: richiesta autenticata con timeout che ha restituito un codice di stato HTTP 500. Se il timeout di rete o il timeout della richiesta del client è impostato su un valore inferiore rispetto a quanto previsto dal servizio di archiviazione, si tratta di un timeout previsto. In caso contrario, viene segnalato come ServerTimeoutError. </li> <li>**ClientOtherError**: tutti gli altri errori sul lato client ad eccezione di quelli descritti. </li> <li>**Operazione riuscita**: richiesta con esito positivo</li> <li> **SuccessWithThrottling**: la richiesta è riuscita quando un client SMB viene limitato al primo tentativo, ma ha esito positivo nei tentativi successivi.</li> |
| **ApiName** | Nome dell'operazione. 
| **autenticazione** | Tipo Authentication usato nelle transazioni. I valori disponibili includono: <br/> <li>**AccountKey**: la transazione viene autenticata con la chiave dell'account di archiviazione.</li> <li>**SAS**: la transazione viene autenticata con firme di accesso condiviso.</li> <li>**OAuth**: la transazione viene autenticata con i token di accesso OAuth.</li> <li>**Anonymous**: la transazione viene richiesta in modo anonimo. Non include le richieste preliminari.</li> <li>**AnonymousPreflight**: la transazione è una richiesta preliminare.</li> |