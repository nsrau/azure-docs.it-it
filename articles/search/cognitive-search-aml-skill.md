---
title: Abilità AML personalizzata in skillsets
titleSuffix: Azure Cognitive Search
description: Estendi le funzionalità di Azure ricerca cognitiva skillsets con i modelli Azure Machine Learning.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6a3916a41635a1c76bddbb092294f6d362fc6050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924712"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Abilità AML in una pipeline di arricchimento ricerca cognitiva di Azure

> [!IMPORTANT] 
> Questa competenza è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è previsto alcun supporto per .NET SDK.

L'abilità **AML** consente di estendere l'arricchimento di intelligenza artificiale con un modello di [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) personalizzato (AML). Una volta eseguito il [training e la distribuzione](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)di un modello AML, un'abilità **AML** la integra in un arricchimento di intelligenza artificiale.

Come le competenze predefinite, una competenza **AML** ha input e output. Gli input vengono inviati al servizio AML distribuito come oggetto JSON, che restituisce un payload JSON come risposta insieme a un codice di stato di esito positivo. Si prevede che la risposta includa gli output specificati dalla propria abilità **AML** . Qualsiasi altra risposta è considerata un errore e non vengono eseguiti arricchimenti.

> [!NOTE]
> L'indicizzatore eseguirà un nuovo tentativo due volte per alcuni codici di stato HTTP standard restituiti dal servizio AML. Questi codici di stato HTTP sono:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Prerequisiti

* Un' [area di lavoro AML](../machine-learning/concept-workspace.md)
* Una [destinazione di calcolo di Azure Kubernetes Service AML](../machine-learning/concept-compute-target.md) in questa area di lavoro con un [modello distribuito](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * Per la [destinazione di calcolo deve essere abilitato SSL](../machine-learning/how-to-secure-web-service.md#deploy-on-aks-and-field-programmable-gate-array-fpga). Azure ricerca cognitiva consente l'accesso solo agli endpoint **https**
  * I certificati autofirmati non possono essere usati.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Custom. AmlSkill

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole. I parametri che si sceglie di usare dipendono dall' [autenticazione richiesta dal servizio AML, se disponibile.](#WhatSkillParametersToUse)

| Nome parametro | Descrizione |
|--------------------|-------------|
| `uri` | (Obbligatorio per [Nessuna autenticazione o autenticazione della chiave](#WhatSkillParametersToUse)) [URI di assegnazione del punteggio del servizio AML](../machine-learning/how-to-consume-web-service.md) a cui verrà inviato il payload _JSON_ . È consentito solo lo schema URI **https** . |
| `key` | (Obbligatorio per [l'autenticazione della chiave](#WhatSkillParametersToUse)) [Chiave per il servizio AML](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Obbligatorio per [l'autenticazione del token](#WhatSkillParametersToUse)). ID di risorsa Azure Resource Manager del servizio AML. Deve essere nel formato sottoscrizioni/{GUID}/resourceGroups/{Resource-Group-Name}/Microsoft. MachineLearningServices/Workspaces/{Workspace-Name}/Services/{service_name}. |
| `region` | (Facoltativo per [l'autenticazione del token](#WhatSkillParametersToUse)). [Area](https://azure.microsoft.com/global-infrastructure/regions/) in cui è distribuito il servizio AML. |
| `timeout` | (facoltativo) Se specificato, indica il timeout per il client HTTP che effettua la chiamata API. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Ad esempio, `PT60S` per 60 secondi. Se non impostato, viene scelto un valore predefinito di 30 secondi. Il timeout può essere impostato su un massimo di 230 secondi e un minimo di 1 secondo. |
| `degreeOfParallelism` | Opzionale Quando specificato, indica il numero di chiamate che l'indicizzatore farà in parallelo all'endpoint fornito. È possibile ridurre questo valore se l'endpoint ha un errore troppo elevato di un carico di richiesta o se l'endpoint è in grado di accettare più richieste e si desidera un aumento delle prestazioni dell'indicizzatore.  Se non impostato, viene utilizzato il valore predefinito 5. DegreeOfParallelism può essere impostato su un massimo di 10 e un minimo di 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Parametri delle competenze da usare

I parametri delle competenze di AML sono necessari a seconda dell'autenticazione usata dal servizio AML. I servizi AML forniscono tre opzioni di autenticazione:

* [Autenticazione basata su chiavi](../machine-learning/concept-enterprise-security.md#authentication-for-web-service-deployment). Viene fornita una chiave statica per autenticare le richieste di assegnazione dei punteggi dalle competenze di AML
  * Usare i parametri _URI_ e _Key_
* [Autenticazione basata su token](../machine-learning/concept-enterprise-security.md#authentication). Il servizio AML viene [distribuito usando l'autenticazione basata su token](../machine-learning/how-to-deploy-azure-kubernetes-service.md#authentication-with-tokens). All' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) del servizio ricerca cognitiva di Azure viene concesso il [ruolo lettura](../machine-learning/how-to-assign-roles.md) nell'area di lavoro del servizio AML. La competenza AML usa quindi l'identità gestita del servizio ricerca cognitiva di Azure per l'autenticazione nel servizio AML, senza chiavi statiche obbligatorie.
  * Usare il parametro _resourceId_ .
  * Se il servizio Azure ricerca cognitiva si trova in un'area diversa dall'area di lavoro AML, usare il parametro _Region_ per impostare l'area in cui è stato distribuito il servizio AML
* Nessuna autenticazione. Non è necessaria alcuna autenticazione per usare il servizio AML
  * Usare il parametro _URI_

## <a name="skill-inputs"></a>Input competenze

Non ci sono input predefiniti per questa competenza. È possibile scegliere uno o più campi che sarebbero già disponibili al momento dell'esecuzione di questa skill come input e il payload _JSON_ inviato al servizio AML avrà campi diversi.

## <a name="skill-outputs"></a>Output competenze

Non ci sono output predefiniti per questa competenza. A seconda della risposta restituita dal servizio AML, aggiungere i campi di output in modo che possano essere prelevati dalla risposta _JSON_ .

## <a name="sample-definition"></a>Definizione di esempio

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Struttura JSON di input di esempio

Questa struttura _JSON_ rappresenta il payload che verrà inviato al servizio AML. I campi di primo livello della struttura corrisponderanno ai "nomi" specificati nella `inputs` sezione della definizione di competenze. Il valore di questi campi verrà da `source` (che può essere un campo nel documento o potenzialmente un'altra competenza)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Struttura JSON di output di esempio

L'output corrisponde alla risposta restituita dal servizio AML. Il servizio AML deve restituire solo un payload _JSON_ (verificato osservando l' `Content-Type` intestazione della risposta) e deve essere un oggetto in cui i campi sono arricchimenti corrispondenti ai "nomi" in e il `output` cui valore è considerato l'arricchimento.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Definizione di esempio di shaping in linea

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Struttura JSON di input shaping in linea

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Struttura JSON di output di esempio per la definizione inline

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Casi di errore
Oltre alla mancata disponibilità o all'invio di codici di stato non riusciti, i seguenti sono considerati casi errati:

* Se il servizio AML restituisce un codice di stato di esito positivo, ma la risposta indica che non lo è `application/json` , la risposta viene considerata non valida e non verrà eseguito alcun arricchimento.
* Se il servizio AML restituisce JSON non valido

Nei casi in cui il servizio AML non è disponibile o restituisce un errore HTTP, alla cronologia di esecuzione dell'indicizzatore verrà aggiunto un errore descrittivo con i dettagli disponibili sull'errore HTTP.

## <a name="see-also"></a>Vedere anche

+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Risoluzione dei problemi del servizio AML](../machine-learning/how-to-troubleshoot-deployment.md)