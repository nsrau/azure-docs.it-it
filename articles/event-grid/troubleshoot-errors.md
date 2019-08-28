---
title: Griglia di eventi di Azure-Guida alla risoluzione dei problemi
description: Questo articolo fornisce un elenco di codici di errore, messaggi di errore, descrizioni e azioni consigliate.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: c4c4e7980247e0ce41c685873a2d50b3e37fb405
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061465"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Risolvere gli errori di griglia di eventi di Azure
Questa guida alla risoluzione dei problemi fornisce un elenco di codici di errore di griglia di eventi di Azure, i messaggi di errore, le descrizioni e le azioni consigliate da eseguire quando si ricevono questi errori. 

## <a name="error-code-400"></a>Codice errore: 400
| Codice di errore | Messaggio di errore | DESCRIZIONE | Recommendation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. richiesta non valida<br/>400 | Il nome dell'argomento deve avere una lunghezza compresa tra 3 e 50 caratteri. | La lunghezza del nome dell'argomento personalizzato deve avere una lunghezza compresa tra 3 e 50 caratteri. Nel nome dell'argomento sono consentite solo lettere alfanumeriche, cifre e il carattere '-'. Inoltre, il nome non deve iniziare con le parole riservate seguenti: <ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li></ul> | Scegliere un nome di argomento diverso che rispetti i requisiti del nome dell'argomento. |
| HttpStatusCode. richiesta non valida<br/>400 | Il nome di dominio deve avere una lunghezza compresa tra 3 e 50 caratteri. | La lunghezza del nome di dominio deve essere compresa tra 3 e 50 caratteri. Nel nome dell'argomento sono consentite solo lettere alfanumeriche, cifre e il carattere '-'. Inoltre, il nome non deve iniziare con le parole riservate seguenti:<ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li> | Scegliere un nome di dominio diverso che rispetti i requisiti del nome di dominio. |
| HttpStatusCode. richiesta non valida<br/>400 | Data di scadenza non valida. | L'ora di scadenza per la sottoscrizione di eventi determina quando la sottoscrizione dell'evento viene ritirata. Questo valore deve essere un valore DateTime valido in futuro.| Assicurarsi che l'ora di scadenza della sottoscrizione di eventi in un formato DateTime valido e che sia impostata su in futuro. |

## <a name="error-code-409"></a>Codice errore: 409
| Codice di errore | Messaggio di errore | Descrizione | Azione consigliata |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. Conflict <br/>409 | Un argomento con il nome specificato esiste già. Scegliere un nome di argomento diverso.   | Il nome dell'argomento personalizzato deve essere univoco in una singola area di Azure per garantire una corretta operazione di pubblicazione. Lo stesso nome può essere usato in diverse aree di Azure. | Scegliere un nome diverso per l'argomento. |
| HttpStatusCode. Conflict <br/> 409 | Il dominio con l'oggetto specificato esiste già. Scegliere un nome di dominio diverso. | Il nome di dominio deve essere univoco in una singola area di Azure per garantire una corretta operazione di pubblicazione. Lo stesso nome può essere usato in diverse aree di Azure. | Scegliere un nome diverso per il dominio. |
| HttpStatusCode. Conflict<br/>409 | È stato raggiunto il limite di quota. Per altre informazioni su questi limiti, vedere [limiti di griglia di eventi di Azure](../azure-subscription-service-limits.md#event-grid-limits).  | Ogni sottoscrizione di Azure ha un limite al numero di risorse di griglia di eventi di Azure che può usare. Alcune o tutte le quote sono state superate e non è stato possibile creare altre risorse. |  Controllare l'uso delle risorse correnti ed eliminare eventuali non necessarie. Se è ancora necessario aumentare la quota, inviare un messaggio di posta [aeg@microsoft.com](mailto:aeg@microsoft.com) elettronica a con il numero esatto di risorse necessarie. |


## <a name="next-steps"></a>Passaggi successivi
Se è necessaria ulteriore assistenza, pubblicare il problema nel [forum stack overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o aprire un [ticket di supporto](https://azure.microsoft.com/support/options/). 
