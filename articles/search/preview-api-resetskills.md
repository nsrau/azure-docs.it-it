---
title: Reimposta le competenze (API-Version = 2019-050-06-Preview)
titleSuffix: Azure Cognitive Search
description: API REST di anteprima usata per l'arricchimento incrementale quando è necessaria una rielaborazione parziale o completa di un insieme di competenze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832164"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Reimposta le competenze (API-Version = 2019-05-06-Preview)

> [!IMPORTANT] 
> L'arricchimento incrementale è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

La richiesta **Reset Skills** specifica le competenze da elaborare per la successiva esecuzione dell'indicizzatore. Per gli indicizzatori per i quali è abilitata la memorizzazione nella cache, è possibile richiedere in modo esplicito l'elaborazione degli aggiornamenti delle competenze che l'indicizzatore non può rilevare. Se, ad esempio, si apportano modifiche esterne, ad esempio le revisioni a una competenza personalizzata, è possibile usare questa API per rieseguire la competenza. Gli output, ad esempio un archivio informazioni o un indice di ricerca, vengono aggiornati usando i dati riutilizzabili della cache e il nuovo contenuto in base alle competenze aggiornate.

È possibile reimpostare un oggetto [Skills](https://docs.microsoft.com/rest/api/searchservice/create-skillset) esistente usando un HTTP PUT, specificando il nome del file di competenze da aggiornare nell'URI della richiesta. È necessario usare **API-Version = 2019-05-06-Preview** per la richiesta.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

In alternativa, usare POST e inserire il nome dell'indicizzatore nel corpo della richiesta:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Intestazioni della richiesta  

 La tabella seguente descrive le intestazioni della richiesta obbligatorie e facoltative.  

|Intestazione di richiesta|Description|  
|--------------------|-----------------|  
|*Content-Type:*|Obbligatorio. Impostare il valore su `application/json`|  
|*api-key:*|Obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **reimpostazione** del set di competenze deve includere una `api-key` intestazione impostata sulla chiave amministratore, anziché su una chiave di query.|  

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dalla pagina Panoramica del servizio nell'portale di Azure. Vedere [creare un servizio ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/search-create-service-portal) per la Guida all'esplorazione delle pagine.  

## <a name="request-body-syntax"></a>Sintassi del corpo della richiesta  

Il corpo della richiesta è una matrice di nomi di competenze.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Risposta  

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto. 

## <a name="see-also"></a>Vedi anche

+ [Cerca API REST](https://docs.microsoft.com/rest/api/searchservice)
+ [Codici di stato HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Panoramica dell'arricchimento di intelligenza artificiale](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Configurare la memorizzazione nella cache e l'arricchimento incrementale](search-howto-incremental-index.md)
+ [Arricchimento incrementale](cognitive-search-incremental-indexing-conceptual.md)