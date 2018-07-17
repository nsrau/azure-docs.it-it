---
title: Esportazione ed eliminazione dei dati dei clienti - LUIS - Servizi cognitivi di Azure | | Microsoft Docs
description: Informazioni di riferimento per l'esportazione e l'eliminazione dei dati dei clienti dal servizio Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: f684b8ab875e2fbb774dc4a29bce25be41b24e6d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378156"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Esportare ed eliminare i dati dei clienti in Language Understanding (LUIS) in Servizi cognitivi

## <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dei dati dei clienti
Language Understanding Intelligent Service (LUIS) conserva i contenuti dei clienti per consentire il funzionamento del servizio, ma l'utente LUIS ha il controllo completo su visualizzazione, esportazione ed eliminazione dei propri dati. A tale scopo, sono disponibili il [portale](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) Web LUIS o le [API programmatiche LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

I contenuti dei clienti vengono archiviati e crittografati in archivi Azure a livello di area di Microsoft e includono:

- Contenuti degli account utente raccolti al momento della registrazione
- Dati di training necessari per compilare i modelli (ovvero finalità ed entità)
- Query degli utenti registrate in fase di esecuzione per consentire di migliorare i modelli utente
  - Gli utenti possono disattivare la registrazione delle query aggiungendo `&log=false` alla richiesta. Informazioni dettagliate sono disponibili [qui](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-resources-faq#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti
Gli utenti LUIS hanno il controllo completo per l'eliminazione di qualsiasi contenuto utente, tramite il portale Web LUIS o le API programmatiche LUIS. Nella tabella seguente sono disponibili i collegamenti per entrambe le opzioni:

| | **Account utente** | **Applicazione** | **Espressione/i** | **Query utente finale** |
| --- | --- | --- | --- | --- |
| **di Microsoft Azure** | [Collegamento](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-account-settings) | [Collegamento](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Collegamento](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Collegamento](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) |
| **API** | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti
Gli utenti LUIS hanno il controllo completo per la visualizzazione dei dati nel portale, che tuttavia devono essere esportati tramite le API programmatiche LUIS. Nella tabella seguente sono disponibili i collegamenti relativi all'esportazione dei dati tramite le API programmatiche LUIS:

| | **Account utente** | **Applicazione** | **Espressione/i** | **Query utente finale** |
| --- | --- | --- | --- | --- |
| **API** | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle aree di LUIS](./luis-reference-regions.md)
