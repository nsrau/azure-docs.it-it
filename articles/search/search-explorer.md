---
title: Eseguire query nell&quot;indice di Ricerca di Azure usando il portale di Azure| Documentazione Microsoft
description: Inviare una query di ricerca in Esplora ricerche del portale di Azure.
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Eseguire query nell'indice di Ricerca di Azure usando il portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-query-overview.md)
> * [Portale](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Questa guida illustra come eseguire query nell'indice di Ricerca di Azure nel portale di Azure.

Prima di iniziare questa procedura dettagliata, è necessario avere [creato un indice di Ricerca di Azure](search-what-is-an-index.md) e [averlo popolato con dati](search-what-is-data-import.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Passaggio al pannello del servizio Ricerca di Azure
1. Scegliere "Tutte le risorse" dal menu nel lato sinistro del [portale di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selezionare il servizio Ricerca di Azure

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. Selezionare l'indice in cui eseguire la ricerca
1. Selezionare l'indice in cui eseguire la ricerca dal riquadro "Indici".

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. Fare clic sul riquadro "Esplora ricerche"
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. Avviare la ricerca
1. Per eseguire ricerche nell'indice di Ricerca di Azure, iniziare a digitare il testo da cercare nel campo "*Stringa di query*", quindi premere "**Cerca**".
   
   * Quando si usa Esplora ricerche, è possibile specificare uno dei [parametri di query](https://msdn.microsoft.com/library/dn798927.aspx)
2. Nella sezione "*Risultati*" vengono presentati i risultati della query in formato JSON non elaborato analogo a quello che si riceve in un corpo di risposta HTTP quando si invia una richiesta di ricerca per l'API REST di Ricerca di Azure.
3. La stringa di query viene analizzata automaticamente nel'URL di richiesta appropriato per inviare una richiesta HTTP per l'API REST di Ricerca di Azure.

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->


