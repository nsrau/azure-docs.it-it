---
title: Esplora ricerche nel portale di Azure per le query negli indici - Ricerca di Azure
description: Usare gli strumenti del portale di Azure come Esplora ricerche per eseguire query negli indici in Ricerca di Azure. Immettere termini di ricerca o stringhe di ricerca complete con sintassi avanzata.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11f102fcb2a24f9062313f9a3234c29e70a3dfe0
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315666"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Come usare Esplora ricerche per eseguire query negli indici in Ricerca di Azure 

Questo articolo illustra come eseguire query in un indice di Ricerca di Azure esistente usando **Esplora ricerche** nel portale di Azure. È possibile usare Esplora ricerche per inviare stringhe di query Lucene semplici o complete a qualsiasi indice nel servizio.

## <a name="open-the-service-dashboard"></a>Aprire il dashboard del servizio
1. Fare clic su **Tutte le risorse** nell'indice sul lato sinistro del [portale di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Selezionare il servizio Ricerca di Azure.

## <a name="select-an-index"></a>Selezionare un indice

Selezionare l'indice in cui si vuole eseguire la ricerca nel riquadro **Indici**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Aprire Esplora ricerche

Fare clic sul riquadro Esplora ricerche per aprire la barra di ricerca e il riquadro dei risultati.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Avviare la ricerca

Quando si usa Esplora ricerche, per formulare la query è possibile specificare i [parametri di query](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

1. In **Stringa di query** digitare una query e quindi fare clic su **Cerca**. 

   La stringa di query viene analizzata automaticamente in modo da ottenere l'URL di richiesta appropriato per inviare una richiesta HTTP per l'API REST di Ricerca di Azure.   
   
   Per creare la richiesta è possibile usare qualsiasi sintassi di query Lucene semplice o completa valida. Il carattere `*` equivale a una ricerca vuota o non specificata che restituisce tutti i documenti senza un ordine particolare.

2. In **Risultati** vengono presentati i risultati della query in formato JSON non elaborato, identico al payload restituito nel corpo di una risposta HTTP quando si inviano richieste a livello di codice.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Passaggi successivi

Le risorse seguenti offrono altri esempi e informazioni relativi alla sintassi di query.

 + [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Esempi di sintassi di query Lucene](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [Sintassi delle espressioni filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 