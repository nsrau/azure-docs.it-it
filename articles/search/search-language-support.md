---
title: Indicizzazione multilingue per query di ricerca non in lingua inglese
titleSuffix: Azure Cognitive Search
description: Azure ricerca cognitiva supporta le lingue 56, sfruttando gli analizzatori di linguaggio di Lucene e la tecnologia di elaborazione del linguaggio naturale di Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793584"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Come creare un indice per più lingue in Azure ricerca cognitiva

Gli indici possono includere campi che contengono contenuto da più linguaggi, ad esempio la creazione di singoli campi per stringhe specifiche della lingua. Per ottenere risultati ottimali durante l'indicizzazione e l'esecuzione di query, assegnare un analizzatore del linguaggio che fornisca le regole linguistiche appropriate. 

Azure ricerca cognitiva offre un'ampia gamma di analizzatori di linguaggio sia da Lucene che da Microsoft che possono essere assegnati a singoli campi usando la proprietà dell'analizzatore. È anche possibile specificare un analizzatore del linguaggio nel portale, come descritto in questo articolo.

## <a name="add-analyzers-to-fields"></a>Aggiungere analizzatori a campi

Un analizzatore del linguaggio viene specificato quando viene creato un campo. L'aggiunta di un analizzatore a una definizione di campo esistente richiede la sovrascrittura e il ricaricamento dell'indice o la creazione di un nuovo campo identico all'originale, ma con un'assegnazione dell'analizzatore. È quindi possibile eliminare il campo inutilizzato per praticità.

1. Accedere al [portale di Azure](https://portal.azure.com) e trovare il servizio di ricerca.
1. Fare clic su **Aggiungi un indice** nella barra di comando del dashboard del servizio per avviare un nuovo indice oppure aprire un indice esistente per impostare un analizzatore nei nuovi campi aggiunti a un indice esistente.
1. Avviare una definizione di campo fornendo un nome.
1. Scegliere il tipo di dati EDM. String. Solo i campi stringa sono disponibili per la ricerca full-text.
1. Impostare l'attributo **ricercabile** per abilitare la proprietà dell'analizzatore. Un campo deve essere basato su testo per poter usare un analizzatore del linguaggio.
1. Scegliere uno degli analizzatori disponibili. 

![Assegnazione degli analizzatori di lingua durante la definizione del campo](media/search-language-support/select-analyzer.png "Assegnazione degli analizzatori di lingua durante la definizione del campo")

Per impostazione predefinita, tutti i campi disponibili per la ricerca usano l' [analizzatore Lucene standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , indipendente dal linguaggio. Per visualizzare l'elenco completo degli analizzatori supportati, vedere [aggiungere analizzatori di lingua a un indice di ricerca cognitiva di Azure](index-add-language-analyzers.md).

Nel portale gli analizzatori sono destinati a essere usati così come sono. Se è necessaria una personalizzazione o una configurazione specifica di filtri e Tokenizer, è necessario [creare un analizzatore personalizzato](index-add-custom-analyzers.md) nel codice. Il portale non supporta la selezione o la configurazione di analizzatori personalizzati.

## <a name="query-language-specific-fields"></a>Campi specifici del linguaggio di query

Dopo aver selezionato l'analizzatore della lingua per un campo, verrà usato per ogni richiesta di ricerca e indicizzazione relativa a quel campo. Quando una query viene eseguita su più campi utilizzando analizzatori diversi, la query verrà elaborata in modo indipendente dagli analizzatori assegnati per ogni campo.

Se la lingua dell'agente che esegue una query è nota, è possibile definire per una richiesta di ricerca un ambito relativo a un campo specifico usando il parametro di query **searchFields** . La query seguente verrà generata solo per la descrizione in polacco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

È possibile eseguire query sull'indice dal portale, usando [**Esplora ricerche**](search-explorer.md) per incollare una query simile a quella illustrata sopra.

## <a name="boost-language-specific-fields"></a>Aumentare i campi specifici della lingua

È possibile che la lingua dell'agente che esegue una query non sia nota, in tal caso la query può essere inviata a tutti i campi contemporaneamente. Se necessario, è possibile definire delle preferenze per i risultati in una determinata lingua usando i [profili di punteggio](index-add-scoring-profiles.md). Nell'esempio seguente, alle corrispondenze trovate nella descrizione in inglese viene assegnato un punteggio più elevato rispetto alle corrispondenze in polacco e francese:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Passaggi successivi

Gli sviluppatori .NET possono configurare gli analizzatori della lingua usando [Azure ricerca cognitiva .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) e la proprietà [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) . 