---
title: Griglia di eventi di Azure-Guida alla risoluzione dei problemi
description: Questo articolo fornisce un elenco di codici di errore, messaggi di errore, descrizioni e azioni consigliate.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592992"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Risolvere gli errori di griglia di eventi di Azure
Questa guida alla risoluzione dei problemi fornisce le informazioni seguenti: 

- Codici di errore di griglia di eventi di Azure
- messaggi di errore
- Descrizioni degli errori
- Azioni consigliate da eseguire quando si ricevono questi errori. 

## <a name="error-code-400"></a>Codice errore: 400
| Codice errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. richiesta non valida<br/>400 | Il nome dell'argomento deve avere una lunghezza compresa tra 3 e 50 caratteri. | La lunghezza del nome dell'argomento personalizzato deve avere una lunghezza compresa tra 3 e 50 caratteri. Nel nome dell'argomento sono consentite solo lettere alfanumeriche, cifre e il carattere '-'. Inoltre, il nome non deve iniziare con le parole riservate seguenti: <ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li></ul> | Scegliere un nome di argomento diverso che rispetti i requisiti del nome dell'argomento. |
| HttpStatusCode. richiesta non valida<br/>400 | Il nome di dominio deve avere una lunghezza compresa tra 3 e 50 caratteri. | La lunghezza del nome di dominio deve essere compresa tra 3 e 50 caratteri. Nel nome di dominio sono consentite solo lettere alfanumeriche, cifre e il carattere '-'. Inoltre, il nome non deve iniziare con le parole riservate seguenti:<ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li> | Scegliere un nome di dominio diverso che rispetti i requisiti del nome di dominio. |
| HttpStatusCode. richiesta non valida<br/>400 | Data di scadenza non valida. | L'ora di scadenza per la sottoscrizione di eventi determina quando la sottoscrizione dell'evento viene ritirata. Questo valore deve essere un valore DateTime valido in futuro.| Assicurarsi che l'ora di scadenza della sottoscrizione di eventi in un formato DateTime valido e che sia impostata su in futuro. |

## <a name="error-code-409"></a>Codice di errore: 409
| Codice errore | Messaggio di errore | Descrizione | Azione consigliata |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. Conflict <br/>409 | Un argomento con il nome specificato esiste già. Scegliere un nome di argomento diverso.   | Il nome dell'argomento personalizzato deve essere univoco in una singola area di Azure per garantire una corretta operazione di pubblicazione. Lo stesso nome può essere usato in diverse aree di Azure. | Scegliere un nome diverso per l'argomento. |
| HttpStatusCode. Conflict <br/> 409 | Il dominio con l'oggetto specificato esiste già. Scegliere un nome di dominio diverso. | Il nome di dominio deve essere univoco in una singola area di Azure per garantire un'operazione di pubblicazione corretta. Lo stesso nome può essere usato in diverse aree di Azure. | Scegliere un nome diverso per il dominio. |
| HttpStatusCode. Conflict<br/>409 | È stato raggiunto il limite di quota. Per altre informazioni su questi limiti, vedere [limiti di griglia di eventi di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Ogni sottoscrizione di Azure ha un limite al numero di risorse di griglia di eventi di Azure che può usare. Alcune o tutte le quote sono state superate e non è stato possibile creare altre risorse. |    Controllare l'uso delle risorse correnti ed eliminare eventuali non necessarie. Se è ancora necessario aumentare la quota, inviare un messaggio di posta elettronica a [aeg@microsoft.com](mailto:aeg@microsoft.com) con il numero esatto di risorse necessarie. |

## <a name="error-code-403"></a>Codice errore: 403

| Codice errore | Messaggio di errore | Descrizione | Azione consigliata |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. Forbidden <br/>403 | La pubblicazione in {topic/Domain} da client {IpAddress} è stata rifiutata a causa di regole di filtro IpAddress. | L'argomento o il dominio ha regole del firewall IP configurate e l'accesso è limitato solo agli indirizzi IP configurati. | Aggiungere l'indirizzo IP alle regole del firewall IP, vedere [configurare il firewall IP](configure-firewall.md) |
| HttpStatusCode. Forbidden <br/> 403 | La pubblicazione in {topic/Domain} da parte del client viene rifiutata perché la richiesta proviene dall'endpoint privato e non è stata trovata alcuna connessione all'endpoint privato corrispondente per la risorsa. | L'argomento o il dominio ha endpoint privati e la richiesta di pubblicazione proviene da un endpoint privato non configurato o approvato. | Configurare un endpoint privato per l'argomento/dominio. [Configurare gli endpoint privati](configure-private-endpoints.md) |

Controllare anche se il webhook è dietro un gateway applicazione Azure o un Web Application Firewall. In caso contrario, disabilitare le regole del firewall seguenti ed eseguire di nuovo HTTP POST:

- 920300 (richiesta mancante di un'intestazione Accept)
- 942430 (argomenti limitati al rilevamento di anomalie dei caratteri SQL (args): numero di caratteri speciali superato (12))
- 920230 (codifica più URL rilevata)
- 942130 (attacco SQL injection: rilevato tautologia SQL).
- 931130 (possibile attacco RFI (Remote File Inclusion) = riferimento all'esterno del dominio/collegamento)



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, pubblicare il problema nel [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) oppure aprire un [ticket di supporto](https://azure.microsoft.com/support/options/). 
