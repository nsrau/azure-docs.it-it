<properties
	pageTitle="Eseguire query in un indice di Ricerca di Azure con Esplora ricerche nel portale di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Esplora ricerche offre un approccio senza codice per l'esecuzione di query in un indice di Ricerca di Azure nel portale di Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Eseguire query in un indice di Ricerca di Azure con Esplora ricerche nel portale di Azure
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

**Esplora ricerche** è uno strumento di query incorporato nel portale di Azure che permette di eseguire query senza codice in un indice di Ricerca di Azure. Si connette a qualsiasi indice nel servizio e presenta una casella di ricerca per l'immissione di espressioni e stringhe di ricerca. La sintassi di query valida viene generata in base all'input che viene fornito. I risultati vengono visualizzati nella pagina del portale.

Esplora ricerche è lo strumento ideale per conoscere la sintassi delle query, eseguire query ad hoc o perfezionare un'espressione di query prima di inserirla nel codice. Per usare Esplora ricerche è necessario un servizio di Ricerca di Azure e un indice. Per assistenza su queste attività, vedere [Creare un servizio di Ricerca di Azure nel portale di Azure classico](search-create-service-portal.md) e [Importare dati in Ricerca di Azure tramite il portale](search-import-data-portal.md).

## Aprire Esplora ricerche

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire il dashboard relativo al servizio Ricerca di Azure. Di seguito alcuni modi per trovare il dashboard.
	- Nell'indice, fare clic su **Home page**. La home page dispone di sezioni per ogni servizio incluso nella sottoscrizione. Fare clic sull'icona per aprire il dashboard del servizio.
	- Nell'indice fare clic su **Esplora tutto** > **Filtra per** > **Servizi di ricerca** per trovare il servizio di ricerca nell'elenco.

3. Nel dashboard del servizio verrà visualizzata una barra dei comandi nella parte superiore che include il comando **Esplora ricerche**.

  	![][1]

4. Fare clic su **Esplora ricerche** per aprire il pannello corrispondente.
5. Impostare l'indice e la versione dell'API. Esplora ricerche si connette automaticamente al primo indice nell'elenco di indici, ma è possibile fare clic su **Cambia indice** per passare a un altro. **Imposta versione API** consente di specificare la versione di anteprima o di disponibilità generale. Una parte della sintassi di query è solo in anteprima.
6. Se sono state seguite le indicazioni in [Introduzione a Ricerca di Azure](search-get-started-portal.md) per creare e popolare un indice in base ai dati dello United States Geological Survey (USGS) per il Rhode Island, è possibile usare questo termine di ricerca per verificare che gli stessi 3 risultati vengano restituiti in Esplora ricerche: `roger williams +school -building`

Si noti la sintassi di query generata automaticamente in risposta all'input del termine di ricerca.

![][2]

## Passaggi successivi

Altre informazioni ed esempi sulla sintassi di query sono disponibili nell'articolo relativo alla [ricerca di documenti](https://msdn.microsoft.com/library/azure/dn798927.aspx) in MSDN.

Visitare i collegamenti seguenti per altri approcci senza codice alla creazione o alla gestione di un indice o un servizio di ricerca:

- [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md)
- [Importare dati in Ricerca di Azure tramite il portale](search-import-data-portal.md)
- [Gestire il servizio di ricerca in Azure](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->