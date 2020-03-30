---
title: Indicizzazione in più lingue per le query di ricerca non in lingua inglese
titleSuffix: Azure Cognitive Search
description: Ricerca cognitiva di Azure supporta 56 lingue, sfruttando gli analizzatori linguistici di Lucene e della tecnologia di elaborazione del linguaggio naturale di Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793584"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Come creare un indice per più lingue in Ricerca cognitiva di AzureHow to create an index for multiple languages in Azure Cognitive Search

Gli indici possono includere campi con contenuto di più lingue, ad esempio la creazione di singoli campi per stringhe specifiche della lingua. Per ottenere risultati ottimali durante l'indicizzazione e l'esecuzione di query, assegnare un analizzatore di lingua che fornisca le regole linguistiche appropriate. 

Ricerca cognitiva di Azure offre un'ampia selezione di analizzatori del linguaggio sia da Lucene che da Microsoft che possono essere assegnati a singoli campi usando la proprietà Analyzer. È anche possibile specificare un analizzatore di linguaggio nel portale, come descritto in questo articolo.

## <a name="add-analyzers-to-fields"></a>Aggiungere analizzatori ai campi

Un analizzatore del linguaggio viene specificato quando viene creato un campo. L'aggiunta di un analizzatore a una definizione di campo esistente richiede la sovrascrittura (e il ricaricamento) dell'indice o la creazione di un nuovo campo identico all'originale, ma con un'assegnazione di analizzatore. È quindi possibile eliminare il campo inutilizzato a proprio piacimento.

1. Accedere al [portale](https://portal.azure.com) di Azure e trovare il servizio di ricerca.
1. Fare clic su **Aggiungi un indice** nella barra di comando del dashboard del servizio per avviare un nuovo indice oppure aprire un indice esistente per impostare un analizzatore nei nuovi campi aggiunti a un indice esistente.
1. Iniziare una definizione di campo specificando un nome.
1. Scegliere il tipo di dati Edm.String. Solo i campi stringa sono ricercabili full-text.
1. Impostare l'attributo **Ricercabile** per abilitare la proprietà Analyzer. Un campo deve essere basato su testo per poter utilizzare un analizzatore del linguaggio.
1. Scegliere uno degli analizzatori disponibili. 

![Assegnare analizzatori del linguaggio durante la definizione del campoAssign language analyzers during field definition](media/search-language-support/select-analyzer.png "Assegnare analizzatori del linguaggio durante la definizione del campoAssign language analyzers during field definition")

Per impostazione predefinita, tutti i campi ricercabili utilizzano [l'analizzatore Lucene standard,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) indipendente dalla lingua. Per visualizzare l'elenco completo degli analizzatori supportati, vedere [Aggiungere analizzatori del linguaggio a un indice](index-add-language-analyzers.md)di Ricerca cognitiva di Azure.To view the full list of supported analyzers, see Add language analyzers to an Azure Cognitive Search index .

Nel portale, gli analizzatori devono essere utilizzati così come sono. Se è necessaria la personalizzazione o una configurazione specifica di filtri e tokenizer, è necessario [creare un analizzatore personalizzato](index-add-custom-analyzers.md) nel codice. Il portale non supporta la selezione o la configurazione di analizzatori personalizzati.

## <a name="query-language-specific-fields"></a>Campi specifici del linguaggio di query

Dopo aver selezionato l'analizzatore della lingua per un campo, verrà usato per ogni richiesta di ricerca e indicizzazione relativa a quel campo. Quando viene eseguita una query su più campi utilizzando analizzatori diversi, la query verrà elaborata in modo indipendente dagli analizzatori assegnati per ogni campo.

Se la lingua dell'agente che esegue una query è nota, è possibile definire per una richiesta di ricerca un ambito relativo a un campo specifico usando il parametro di query **searchFields** . La query seguente verrà generata solo per la descrizione in polacco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

È possibile eseguire una query sull'indice dal portale, usando [**Esplora ricerche**](search-explorer.md) per incollare una query simile a quella illustrata in precedenza.

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

Se sei uno sviluppatore .NET, tieni presente che puoi configurare gli analizzatori del linguaggio usando [Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) e la proprietà [Analyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 