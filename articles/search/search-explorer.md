---
title: 'Eseguire query su un indice: portale e Ricerca di Azure | Microsoft Docs'
description: Inviare una query di ricerca in Esplora ricerche del portale di Azure.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: heidist
ms.openlocfilehash: a3592bd0c304dfb78374eeba432c0d28203980c9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790509"
---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Eseguire query su un indice di Ricerca di Azure con Esplora ricerche nel portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-query-overview.md)
> * [Portale](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Questo articolo illustra come eseguire query su un indice di Ricerca di Azure con **Esplora ricerche** nel portale di Azure. È possibile usare Esplora ricerche per inviare stringhe di query Lucene semplici o complete a qualsiasi indice nel servizio.

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

Quando si usa Esplora ricerche, per formulare la query è possibile specificare [parametri di query](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

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