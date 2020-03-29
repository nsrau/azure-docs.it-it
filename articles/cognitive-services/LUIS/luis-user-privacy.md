---
title: Esportare & eliminare i dati - LUISExport to delete data - LUIS
titleSuffix: Azure Cognitive Services
description: Hai il pieno controllo sulla visualizzazione, l'esportazione e l'eliminazione dei loro dati. Elimina i dati dei clienti per garantire la privacy e la conformità.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273352"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Esportare ed eliminare i dati dei clienti in Language Understanding (LUIS) in Servizi cognitivi

Elimina i dati dei clienti per garantire la privacy e la conformità.

## <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dei dati dei clienti
Language Understanding Intelligent Service (LUIS) conserva i contenuti dei clienti per consentire il funzionamento del servizio, ma l'utente LUIS ha il controllo completo su visualizzazione, esportazione ed eliminazione dei propri dati. Questa operazione può essere eseguita tramite il [portale](luis-reference-regions.md) Web LUIS o le [API LUIS Authoring (note anche come a livello di codice).](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

I contenuti dei clienti vengono archiviati e crittografati in archivi Azure a livello di area di Microsoft e includono:

- Contenuti degli account utente raccolti al momento della registrazione
- Dati di training necessari per creare i modelli
- Query utente registrate utilizzate [dall'apprendimento attivo](luis-concept-review-endpoint-utterances.md) per contribuire a migliorare il modello
  - Gli utenti possono disattivare la registrazione delle query aggiungendo `&log=false` alla richiesta. Informazioni dettagliate sono disponibili [qui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti
Gli utenti LUIS hanno il controllo completo per eliminare qualsiasi contenuto utente, tramite il portale Web LUIS o le API LUIS Authoring (note anche come a livello di codice). Nella tabella seguente sono disponibili i collegamenti per entrambe le opzioni:

| | **Account utente** | **Applicazione** | **Espressione di esempio** | **Query utente finale** |
| --- | --- | --- | --- | --- |
| **Portale** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Espressioni di apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Espressioni registrate](luis-concept-data-storage.md#disable-logging-utterances) |
| **Api** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti
Gli utenti DI LUIS hanno il controllo completo per visualizzare i dati nel portale, tuttavia devono essere esportati tramite le API LUIS Authoring (noto anche come programmatico). Nella tabella seguente vengono visualizzati i collegamenti che facilitano l'esportazione dei dati tramite le API LUIS Authoring (note anche come a livello di codice):

| | **Account utente** | **Applicazione** | **Utterance(s)** | **Query utente finale** |
| --- | --- | --- | --- | --- |
| **Api** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Posizione dell'apprendimento attivo

Per abilitare [l'apprendimento attivo,](luis-how-to-review-endpoint-utterances.md#enable-active-learning)le espressioni registrate dagli utenti, ricevute negli endpoint LUIS pubblicati, vengono archiviate nelle aree geografiche di Azure seguenti:To enable active learning , users' logged utterances, received at the published LUIS endpoints, are stored in the following Azure geographies:

* [Europa](#europe)
* [Australia](#australia)
* [Stati Uniti](#united-states)

Ad eccezione dei dati di apprendimento attivi (dettagliati di seguito), LUIS segue le procedure di archiviazione dei [dati per i servizi regionali.](https://azuredatacentermap.azurewebsites.net/)

### <a name="europe"></a>Europa

Il portale [eu.luis.ai](https://eu.luis.ai) e Europe Authoring (noto anche come API programmatiche ) sono ospitati nella geografia Europa di Azure. Il portale eu.luis.ai e Europe Authoring (noto anche come API a livello di codice) supportano la distribuzione di endpoint nelle aree geografiche di Azure seguenti:The eu.luis.ai portal and Europe Authoring (also known as Programmatic APIs) support deployment of endpoints to the following Azure geographies:

* Europa
* Francia
* Regno Unito

Quando si esegue la distribuzione in queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app verranno archiviate nell'area geografica dell'Europa di Azure per l'apprendimento attivo. È possibile disabilitare l'apprendimento attivo, vedere [Disabilitare l'apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Per gestire le espressioni archiviate, vedere [Eliminare l'espressione](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Australia

Il portale [di au.luis.ai](https://au.luis.ai) e australia Authoring (noto anche come API programmatiche) sono ospitati nella geografia di Azure in Australia. Il portale au.luis.ai e l'emodifica di Australia (noto anche come API a livello di codice) supportano la distribuzione di endpoint nelle aree geografiche di Azure seguenti:The data portal and Australia Authoring (also known as Programmatic APIs) support deployment of endpoints to the following Azure geographies:

* Australia

Quando si esegue la distribuzione in queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app verranno archiviate nell'area geografica di Azure in Australia per l'apprendimento attivo. È possibile disabilitare l'apprendimento attivo, vedere [Disabilitare l'apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Per gestire le espressioni archiviate, vedere [Eliminare l'espressione](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>Stati Uniti

Il portale [luis.ai](https://www.luis.ai) e la creazione di stati Uniti (noto anche come API a livello di codice) sono ospitati nell'area geografica di Azure negli Stati Uniti.The Azure portal and United States Authoring (also known as Programmatic APIs) are hosted in Azure's Azure's Azure's Azure's Azure's geography. Il portale luis.ai e la creazione e modifica degli Stati Uniti (noto anche come API a livello di codice) supportano la distribuzione di endpoint nelle aree geografiche di Azure seguenti:The user portal portal and United States Authoring (also known as Programmatic APIs) support deployment of endpoints to the following Azure geographies:

* Aree geografiche di Azure non supportate dalle aree di creazione e modifica in Europa o in AustraliaAzure geographies not supported by the Europe or Australia authoring regions

Quando si esegue la distribuzione in queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app verranno archiviate nell'area geografica degli Stati Uniti di Azure per l'apprendimento attivo. È possibile disabilitare l'apprendimento attivo, vedere [Disabilitare l'apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Per gestire le espressioni archiviate, vedere [Eliminare l'espressione](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle aree di LUIS](./luis-reference-regions.md)
