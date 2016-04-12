<properties
    pageTitle="Eseguire query nell'indice di Ricerca di Azure usando il portale di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Inviare una query di ricerca in Esplora ricerche del portale di Azure."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="ashmaka"
/>
# Eseguire query nell'indice di Ricerca di Azure usando il portale di Azure
> [AZURE.SELECTOR]
- [Panoramica](search-query-overview.md)
- [Portale](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Questa guida illustra come eseguire query nell'indice di Ricerca di Azure nel portale di Azure.

Prima di iniziare questa procedura dettagliata, è necessario avere [creato un indice di Ricerca di Azure](search-what-is-an-index.md) e [averlo popolato con dati](search-what-is-data-import.md).

## I. Passare al pannello del servizio Ricerca di Azure
1. Scegliere "Tutte le risorse" dal menu nel lato sinistro del [portale di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selezionare il servizio Ricerca di Azure

## II. Selezionare l'indice in cui eseguire la ricerca
1. Selezionare l'indice in cui eseguire la ricerca dal riquadro "Indici".

![](./media/search-explorer/pick-index.png)

## III. Fare clic sul riquadro "Esplora ricerche"
![](./media/search-explorer/search-explorer-tile.png)

## III. Avviare la ricerca
1. Per eseguire ricerche nell'indice di Ricerca di Azure, iniziare a digitare il testo da cercare nel campo "*Stringa di query*", quindi premere "**Cerca**".
 * Quando si usa Esplora ricerche, è possibile specificare uno dei [parametri di query](https://msdn.microsoft.com/library/dn798927.aspx).

2. Nella sezione "*Risultati*" vengono presentati i risultati della query in formato JSON non elaborato analogo a quello che si riceve in un corpo di risposta HTTP quando si invia una richiesta di ricerca per l'API REST di Ricerca di Azure.
3. La stringa di query viene analizzata automaticamente nel'URL di richiesta appropriato per inviare una richiesta HTTP per l'API REST di Ricerca di Azure.

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0316_2016-->