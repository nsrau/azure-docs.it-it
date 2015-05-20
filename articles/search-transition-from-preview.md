<properties 
	pageTitle="Transizione dalla versione api di anteprima =2014* alla versione api=2015*" 
	description="Informazioni sulle modifiche rilevanti e su come eseguire la migrazione di codice scritto nella versione di anteprima 31/07/2014 o 20/10/2014 in Ricerca di Azure alla versione dell'API 28/02/2015." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#Transizione dalla versione api di anteprima =2014\* alla versione api=2015\*#

Le linee guida seguenti sono indirizzate ai clienti che hanno compilato applicazioni personalizzate nelle versioni di anteprima di Ricerca di Azure e desiderano eseguire la migrazione alla versione disponibile a livello generale del 28/02/2015.

La versione di anteprima utilizzata può essere stata una delle due seguenti:

- [Versione di anteprima del 31/07/2014](search-api-2014-07-31-preview.md)
- [Versione di anteprima del 20/10/2014](search-api-2014-10-20-preview.md)

Ora che Azure è disponibile a livello generale, si invita ad eseguire la transizione a versioni più recenti: la versione del 28/ 02/2015 è la versione API ufficiale della versione disponibile a livello generale di Ricerca di Azure. Questa versione è documentata in [MSDN](https://msdn.microsoft.com/it-it/library/azure/dn798933.aspx).

È in corso anche la distribuzione della prossima versione di anteprima, [28/02/2015-Anteprima](search-api-2015-02-28-preview.md), che introduce funzionalità ancora in fase di sviluppo. Sono graditi commenti e suggerimenti degli utenti nei [forum di Ricerca di Azure](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azuresearch) o nella [pagina dei commenti](http://feedback.azure.com/forums/263029-azure-search).

###Elenco di controllo per la migrazione###

- Esaminare le modifiche di rilievo per determinare se influiscono sulla soluzione.
- Aggiornare la versione API a `2015-02-28` per la versione bloccata. Questa versione è nel contratto di servizio. Se si verificano problemi, è possibile risolverli più rapidamente.
- Compilare, distribuire, testare. È necessario disporre di una parità del 100% in termini di comportamenti di ricerca, ad eccezione delle modifiche di rilievo.
- Distribuire nell'ambiente di produzione.
- Valutare le nuove funzionalità per l'adozione futura. Passare di nuovo alla versione di anteprima 28/02/2015 se si desidera testare i processori del linguaggio naturale di Microsoft o `morelikethis`.

##Modifiche di rilievo nella versione api=2015 \*##

La versione iniziale dell'API includeva una funzionalità di completamento automatico o suggerimenti di digitazione. Anche se utile, tale funzionalità era limitata alla corrispondenza dei prefissi, cercando il primo carattere del termine di ricerca, senza supporto per la corrispondenza di altre parti del campo. L'implementazione era una proprietà booleana denominata `suggestions` che era necessario impostare su `true` se si desiderava abilitare la corrispondenza dei prefissi in un campo specifico.

Questa implementazione originale è diventata obsoleta ed è stata sostituta da un nuovo costrutto `Suggesters` definito nella funzionalità [indice](https://msdn.microsoft.com/it-it/library/azure/dn798941.aspx) che fornisce la corrispondenza di infissi e fuzzy. Come indicato dai nomi, le corrispondenze di infissi e fuzzy forniscono una gamma più ampia di possibilità di corrispondenza. La corrispondenza degli infissi comprende i prefissi in quanto viene ancora cercata la corrispondenza con i caratteri iniziali, ma la ricerca si estende in modo da includere tutta la stringa.

Si è scelto di interrompere l'implementazione precedente, la proprietà booleana, che non sarà più disponibile nelle versioni 2015, senza compatibilità con le versioni precedenti, per evitare la sua adozione accidentale da parte dei clienti che compilano soluzioni più recenti. Se si utilizza `2015-02-28` o `2015-02-28-Preview` occorre utilizzare il nuovo costrutto `Suggesters` per consentire query con suggerimenti di digitazione.

##Trasferimento del codice esistente##

La proprietà di suggerimento è l'unica modifica rilevante. Se non si utilizza questa proprietà è possibile passare la `api-version` da `2014-07-31-Preview` o `2014-10-20-Preview` con `2015-02-28`, quindi ricompilare e ridistribuire. L'applicazione funzionerà come in precedenza.

Per le applicazioni personalizzate che implementavano suggerimenti, effettuare le operazioni seguenti:

1. Aggiornare tutti i pacchetti NuGet.
1. Passare la versione api a `2015-02-28`. Se si utilizza l'esempio di codice riportato di seguito, la versione api è nella classe **AzureSearchHelper**.
1. Eliminare l’attributo `Suggestions={true | false}` dallo schema JSON che definisce l'indice.
1. Aggiungere un costrutto nella parte inferiore dell'indice per `Suggesters` \(come illustrato nella sezione [Dopo](#after) \).
1. Verificare che sia possibile pubblicare il servizio \(potrebbe essere necessario rinominare l'indice per evitare conflitti di denominazione\).
1. Ricompilare la soluzione e distribuire un ambiente di test.
1. Eseguire tutti i test case per garantire che la soluzione si comporti nel modo previsto.
1. Distribuire nell'ambiente di produzione.

L'esempio di codice dall'[esempio Adventure Works su CodePlex](https://azuresearchadventureworksdemo.codeplex.com/) contiene l’implementazione di `Suggestions` originale. È possibile utilizzare questo esempio per esercitarsi con la migrazione del codice nel codice di esempio.

Nella sezione seguente verrà illustrata l’implementazione dei suggerimenti [prima](#before) e [dopo](#after). È possibile sostituire il metodo **CreateCatalogIndex\(\)** con la versione della sezione [Dopo](#after), quindi compilare e distribuire la soluzione per provare le nuove funzionalità.

<a name="before"></a>
###Prima###

Come si può vedere, `Suggestions` è una proprietà booleana impostata su ogni campo. Eliminare tutti gli attributi `Suggestions`.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###Dopo###

Una definizione dello schema migrato omette la proprietà `Suggestions` e aggiunge un costrutto `Suggesters` nella parte inferiore.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##Valutare nuovi approcci e funzionalità##

Dopo aver trasferito la soluzione e verificato che venga eseguita come previsto, è possibile utilizzare questi collegamenti per informazioni sulle nuove funzionalità.

- [Azure Search is generally available \(post di blog\)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Novità dell’ultimo aggiornamento di Ricerca di Azure](../search-latest-updates/)
- [Panoramica di Ricerca di Azure](https://msdn.microsoft.com/it-it/library/azure/dn798933.aspx)

##Ottenere aiuto##

La versione dell'API `2015-02-28` è nel contratto di servizio. Utilizzare le opzioni di supporto e i collegamenti in [questa pagina](http://azure.microsoft.com/support/options/) per creare un ticket di supporto.


<!--HONumber=54-->