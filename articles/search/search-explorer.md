---
title: Esplora ricerche per le query negli indici in Ricerca di Azure | Microsoft Docs
description: Informazioni su come usare Esplora ricerche per eseguire query negli indici in Ricerca di Azure.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 520d9e7b1899c54d922ff6fb77e0901f9609b029
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004134"
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