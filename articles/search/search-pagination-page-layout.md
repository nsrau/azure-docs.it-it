<properties 
	pageTitle="Come impaginare i risultati della ricerca in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
	description="L’impaginazione in Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="08/29/2016" 
	ms.author="heidist"/>  

#Come impaginare i risultati della ricerca in Ricerca di Azure#

In questo articolo vengono fornite indicazioni su come utilizzare l'API REST del servizio Ricerca di Azure per implementare elementi standard di una pagina di risultati di ricerca, ad esempio i conteggi totali, il recupero di documenti, i criteri di ordinamento e la navigazione.
 
In ogni caso citato di seguito, le opzioni relative alla pagina che forniscono dati o informazioni per la pagina dei risultati della ricerca vengono specificati tramite richieste [Cerca nel documento](http://msdn.microsoft.com/library/azure/dn798927.aspx) inviate al servizio Ricerca di Azure. Le richieste includono comando GET, percorso e parametri di query che indicano al servizio quali elementi sono richiesti e come formulare la risposta.

> [AZURE.NOTE] Una richiesta valida include diversi elementi, ad esempio URL e percorso del servizio, verbo HTTP, `api-version`, e così via. Per brevità, gli esempi sono stati tagliati in modo da evidenziare solo la sintassi rilevante per l'impaginazione. Vedere la documentazione dell'[API REST del servizio Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) per informazioni dettagliate sulla sintassi della richiesta.

## Corrispondenze totali e conteggi delle pagine ##

La visualizzazione del numero totale di risultati ottenuti da una query e la restituzione di tali risultati in blocchi più piccoli è fondamentale per tutte le pagine di ricerca.

![][1]
 
In Ricerca di Azure è possibile utilizzare i parametri `$count`, `$top`, e `$skip` per restituire questi valori. Nell'esempio seguente viene illustrata una richiesta di esempio per le corrispondenze totali, restituite come `@OData.count`:

    	GET /indexes/onlineCatalog/docs?$count=true

Recuperare i documenti in gruppi di 15 e visualizzare le corrispondenze totali, a partire dalla prima pagina:

		GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

L'impaginazione dei risultati richiede sia `$top` che `$skip`, dove `$top` specifica il numero di elementi da restituire in un batch e `$skip` specifica il numero di elementi da ignorare. Nell'esempio seguente, in ogni pagina vengono visualizzati 15 elementi, indicati da salti incrementali nel parametro `$skip`.

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## Layout  ##

In una pagina di risultati della ricerca, è possibile visualizzare un'immagine di anteprima, un sottoinsieme dei campi e un collegamento a una pagina di prodotto completa.

 ![][2]
 
In Ricerca di Azure è preferibile utilizzare `$select` e un comando di ricerca per implementare questa esperienza.

Per restituire un sottoinsieme di campi per un layout affiancato:

    	GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Non è possibile eseguire ricerche direttamente in immagini e file multimediali, che devono essere archiviati in un'altra piattaforma di archiviazione, ad esempio la risorsa di archiviazione Blob di Azure, per ridurre i costi. Nell'indice e nei documenti, definire un campo che contiene l'indirizzo URL del contenuto esterno. È quindi possibile utilizzare il campo come riferimento a un'immagine. L'URL dell'immagine deve essere nel documento.

Per recuperare una pagina di descrizione del prodotto per un evento **onClick**, utilizzare [Documento ricerca](http://msdn.microsoft.com/library/azure/dn798929.aspx) per passare la chiave del documento da recuperare. Il tipo di dati della chiave è `Edm.String`. In questo esempio è *246810*.
   
    	GET /indexes/onlineCatalog/docs/246810

## Ordinare per pertinenza, classificazione o prezzo ##

In genere per impostazione predefinita i risultati vengono ordinati per pertinenza, ma è comune offrire alternative in modo che i clienti possano ridisporre rapidamente i risultati in un ordine di priorità diverso.

 ![][3]

In Ricerca di Azure, l'ordinamento è basato sull’espressione `$orderby` per tutti i campi indicizzati come `"Sortable": true.`

La pertinenza è fortemente associata ai profili di punteggio. È possibile utilizzare il punteggio predefinito, che si basa sull'analisi del testo e sulle statistiche, per ordinare tutti i risultati, con punteggi più alti ai documenti che contengono più corrispondenze o corrispondenze migliori con un termine di ricerca.

Gli ordinamenti alternativi sono in genere associati agli eventi **onClick** che richiamano il metodo che genera il criterio di ordinamento. Si consideri, ad esempio, questo elemento di pagina:

 ![][4]

Creare un metodo che accetta l'opzione di ordinamento selezionata come input e restituisce un elenco ordinato per i criteri associati a tale opzione.

 ![][5]
 
> [AZURE.NOTE] Mentre l'assegnazione del punteggio predefinita è sufficiente per molti scenari, è consigliabile invece basare la pertinenza su un profilo di punteggio personalizzato. Un profilo di punteggio personalizzato offre un modo per favorire gli elementi più vantaggiosi per l'azienda. Vedere [Aggiungere un profilo di punteggio](http://msdn.microsoft.com/library/azure/dn798928.aspx) per ulteriori informazioni.

## Esplorazione in base a facet ##

L’esplorazione di ricerca è comune in una pagina di risultati, che spesso si trova all'inizio di una pagina o sul lato. In Ricerca di Azure, l’esplorazione basata su facet fornisce una ricerca autoindirizzata in base a filtri predefiniti. Per maggiori informazioni vendere [Esplorazione basata su facet in Ricerca di Azure](search-faceted-navigation.md).

## Filtri a livello di pagina ##

Se la progettazione della soluzione include pagine di ricerca dedicate per specifici tipi di contenuto (ad esempio un'applicazione di vendita in linea con servizi elencati nella parte superiore della pagina), è possibile inserire un'espressione di filtro insieme a un evento **onClick** per aprire una pagina in uno stato prefiltrato.

È possibile inviare un filtro con o senza espressione di ricerca. Ad esempio, la seguente richiesta filtrerà la marca, restituendo solo i documenti ad essa corrispondenti.

    	GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Vedere [Ricerca nei documenti (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) per ulteriori informazioni sulle espressioni `$filter`.

## Vedere anche ##

- [API REST per il servizio Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx)
- [Operazioni sugli indici](http://msdn.microsoft.com/library/azure/dn798918.aspx)
- [Operazioni sui documenti](http://msdn.microsoft.com/library/azure/dn800962.aspx)
- [Video ed esercitazioni su Ricerca di Azure](search-video-demo-tutorial-list.md)
- [Esplorazione in base a facet in Ricerca di Azure](search-faceted-navigation.md)


<!--Image references-->  
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png

<!---HONumber=AcomDC_0907_2016-->