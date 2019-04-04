---
title: Esportare ed eliminare dati
titleSuffix: Azure Cognitive Services
description: Eliminare i dati del cliente garantire la privacy e conformità.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895157"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Esportare ed eliminare i dati dei clienti in Language Understanding (LUIS) in Servizi cognitivi

Eliminare i dati del cliente garantire la privacy e conformità. 

## <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dei dati dei clienti
Language Understanding Intelligent Service (LUIS) conserva i contenuti dei clienti per consentire il funzionamento del servizio, ma l'utente LUIS ha il controllo completo su visualizzazione, esportazione ed eliminazione dei propri dati. Questa operazione può essere eseguita tramite il web LUIS [portal](luis-reference-regions.md) o il [le API (noto anche come a livello di codice) di creazione servizio LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

I contenuti dei clienti vengono archiviati e crittografati in archivi Azure a livello di area di Microsoft e includono:

- Contenuti degli account utente raccolti al momento della registrazione
- Dati di training necessari per compilare i modelli
- Registrati query utente usate da [apprendimento attivo](luis-concept-review-endpoint-utterances.md) per contribuire a migliorare il modello
  - Gli utenti possono disattivare la registrazione delle query aggiungendo `&log=false` alla richiesta. Informazioni dettagliate sono disponibili [qui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti
Gli utenti di LUIS hanno il controllo completo per eliminare qualsiasi contenuto, tramite il portale web di LUIS o le API LUIS (noto anche come a livello di codice) di creazione dell'utente. Nella tabella seguente sono disponibili i collegamenti per entrambe le opzioni:

| | **Account utente** | **Applicazione** | **Esempio Utterance(s)** | **Query degli utenti finali** |
| --- | --- | --- | --- | --- |
| **Portale** | [Collegamento](luis-concept-data-storage.md#delete-an-account) | [Collegamento](luis-how-to-start-new-app.md#delete-app) | [Collegamento](luis-concept-data-storage.md#utterances-in-an-intent) | [Espressioni di apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Espressioni connessi](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti
Gli utenti di LUIS hanno controllo completo per visualizzare i dati nel portale, ma deve essere esportato tramite le API (noto anche come a livello di codice) di creazione servizio LUIS. Nella tabella seguente vengono visualizzati i collegamenti ad assistere con le esportazioni di dati tramite le API (noto anche come livello di programmazione) LUIS creazione:

| | **Account utente** | **Applicazione** | **Utterance(s)** | **Query degli utenti finali** |
| --- | --- | --- | --- | --- |
| **API** | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Percorso di apprendimento attivo

Per abilitare [apprendimento attivo](luis-how-to-review-endpoint-utterances.md#enable-active-learning), utterances connesso degli utenti, ricevuti con l'endpoint pubblicati di LUIS, vengono archiviati in aree geografiche di Azure seguenti:

* [Europa](#europe)
* [Australia](#australia)
* [Stati Uniti](#united-states)

Fatta eccezione per i dati di apprendimento attivo (illustrati di seguito), LUIS segue il [procedure consigliate di archiviazione dei dati per i servizi a livello di area](http://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

Il [eu.luis.ai](https://eu.luis.ai) portale e l'Europa (noto anche come a livello di codice API) di creazione sono ospitati in area geografica di Azure Europa. Il portale eu.luis.ai ed Europa (noto anche come a livello di codice API) di creazione supportare la distribuzione di endpoint per le aree geografiche di Azure seguenti:

* Europa
* Francia
* Regno Unito

Durante la distribuzione di queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app verranno archiviate nell'area geografica Europa di Azure per apprendimento attivo. È possibile disabilitare l'apprendimento attivo, vedere [disabilitare apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Per gestire utterances archiviati, vedere [eliminare utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Australia

Il [au.luis.ai](https://au.luis.ai) portale e Australia Authoring (anche noto come API programmatiche) sono ospitati in area geografica Australia di Azure. Il portale au.luis.ai e Australia (noto anche come a livello di codice API) di creazione supportare la distribuzione di endpoint per le aree geografiche di Azure seguenti:

* Australia

Durante la distribuzione di queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app verranno archiviate nell'area geografica Australia di Azure per apprendimento attivo. È possibile disabilitare l'apprendimento attivo, vedere [disabilitare apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Per gestire utterances archiviati, vedere [eliminare utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Stati Uniti

Il [luis.ai](https://www.luis.ai) portale e Stati Uniti (noto anche come a livello di codice API) di creazione sono ospitati in area geografica Stati Uniti di Azure. Il portale luis.ai e Stati Uniti (noto anche come a livello di codice API) di creazione supportare la distribuzione di endpoint per le aree geografiche di Azure seguenti:

* Aree geografiche di Azure non è supportati dall'Europa o creazione di aree Australia

Durante la distribuzione di queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app verranno archiviate nell'area geografica Stati Uniti di Azure per apprendimento attivo. È possibile disabilitare l'apprendimento attivo, vedere [disabilitare apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Per gestire utterances archiviati, vedere [eliminare utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riferimento di aree di LUIS](./luis-reference-regions.md)
