---
title: Griglia di eventi di Azure - Guida alla risoluzione dei problemiAzure Event Grid - Troubleshooting guide
description: In questo articolo viene fornito un elenco di codici di errore, messaggi di errore, descrizioni e azioni consigliate.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645073"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Risolvere gli errori di Griglia di eventi di AzureTroubleshoot Azure Event Grid errors
Questa guida alla risoluzione dei problemi fornisce un elenco di codici di errore di Griglia di eventi di Azure, messaggi di errore, le relative descrizioni e le azioni consigliate da eseguire quando si ricevono questi errori. 

## <a name="error-code-400"></a>Codice errore: 400
| Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | Il nome dell'argomento deve avere una lunghezza compresa tra 3 e 50 caratteri. | La lunghezza del nome dell'argomento personalizzato deve avere una lunghezza compresa tra 3 e 50 caratteri. Nel nome dell'argomento sono consentite solo lettere alfanumeriche, cifre e il carattere '-'. Inoltre, il nome non deve iniziare con le seguenti parole riservate: <ul><li>Microsoft</li><li>EventGrid</li><li>System</li></ul> | Scegliere un nome di argomento diverso conforme ai requisiti del nome dell'argomento. |
| HttpStatusCode.BadRequest<br/>400 | Il nome di dominio deve avere una lunghezza compresa tra 3 e 50 caratteri. | La lunghezza del nome di dominio deve avere una lunghezza compresa tra 3 e 50 caratteri. Nel nome dell'argomento sono consentite solo lettere alfanumeriche, cifre e il carattere '-'. Inoltre, il nome non deve iniziare con le seguenti parole riservate:<ul><li>Microsoft</li><li>EventGrid</li><li>System</li> | Scegliere un nome di dominio diverso conforme ai requisiti del nome di dominio. |
| HttpStatusCode.BadRequest<br/>400 | Ora di scadenza non valida. | L'ora di scadenza per la sottoscrizione di eventi determina il momento in cui la sottoscrizione di eventi verrà ritirata. Questo valore deve essere un valore DateTime valido in futuro.| Assicurarsi che l'ora di scadenza della sottoscrizione di eventi in un formato DateTime valido sia impostata in futuro. |

## <a name="error-code-409"></a>Codice di errore: 409
| Codice di errore | Messaggio di errore | Descrizione | Azione consigliata |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | Argomento con il nome specificato già esistente. Scegliere un nome di argomento diverso.   | Il nome dell'argomento personalizzato deve essere univoco in una singola area di Azure per garantire una corretta operazione di pubblicazione. Lo stesso nome può essere usato in diverse aree di Azure.The same name can be used in different Azure regions. | Scegliere un nome diverso per l'argomento. |
| HttpStatusCode.Conflict <br/> 409 | Dominio con specificato già esistente. Scegliere un nome di dominio diverso. | Il nome di dominio deve essere univoco in una singola area di Azure per garantire un'operazione di pubblicazione corretta. Lo stesso nome può essere usato in diverse aree di Azure.The same name can be used in different Azure regions. | Scegliere un nome diverso per il dominio. |
| HttpStatusCode.Conflict<br/>409 | Limite di quota raggiunto. Per altre informazioni su questi limiti, vedere Limiti di Griglia di eventi di Azure.For more information on these limits, see [Azure Event Grid limits](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Ogni sottoscrizione di Azure ha un limite al numero di risorse di Griglia di eventi di Azure che può usare. Alcuni o tutti questi contingenti erano stati superati e non era possibile creare altre risorse. |    Controllare l'utilizzo delle risorse correnti ed eliminare quelli che non sono necessari. Se è ancora necessario aumentare la [aeg@microsoft.com](mailto:aeg@microsoft.com) quota, inviare un messaggio di posta elettronica al numero esatto di risorse necessarie. |


## <a name="next-steps"></a>Passaggi successivi
Se hai bisogno di ulteriore aiuto, pubblica il tuo problema nel [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o apri un ticket di [supporto.](https://azure.microsoft.com/support/options/) 
