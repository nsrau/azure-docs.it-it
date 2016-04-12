<properties
    pageTitle="Esempi di query Lucene per Ricerca di Azure | Ricerca di Microsoft Azure"
    description="Sintassi di query Lucene per ricerca fuzzy, ricerca per prossimità, l'aumento della priorità dei termini, ricerca con espressioni regolari e ricerca con caratteri jolly."
    services="search"
    documentationCenter=""
	authors="LiamCa"
	manager="pablocas"
	editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="03/25/2016"
    ms.author="liamca"
/>

# Esempi di sintassi di query Lucene per la creazione di query in Ricerca di Azure

Un'alternativa alla [sintassi di query semplice](https://msdn.microsoft.com/library/azure/dn798920.aspx) predefinita consiste nell'usare il [parser di query Lucene in Ricerca di Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). Il parser di query Lucene supporta costrutti di query più complessi, ad esempio le query con ambito campo, ricerca fuzzy, ricerca di prossimità, aumento priorità dei termini e ricerca di espressioni regolari.

Questo articolo consente di esaminare gli esempi che visualizzano le sintassi di query e i risultati affiancati. Gli esempi vengono eseguiti su un indice di ricerca precaricato in [JSFiddle](https://jsfiddle.net/), un editor di codice online per il test di script e HTML.

Fare clic con il pulsante destro del mouse sugli URL degli esempi di query per aprire JSFiddle in una finestra del browser distinta.

> [AZURE.NOTE] Gli esempi seguenti sfruttano un indice di ricerca costituito da processi disponibili in base a un set di dati fornito tramite l'iniziativa [City of New York OpenData](https://nycopendata.socrata.com/). Questi dati non devono essere considerati attuali o completi. L'indice è un servizio sandbox fornito da Microsoft. Non è necessaria una sottoscrizione di Azure o Ricerca di Azure per provare queste query.

## Impostare il parser di query Lucene nella richiesta di ricerca

Per usare il parser di query Lucene, impostare il parametro di ricerca **queryType** per specificare quali parser usare. Si specificherà il parametro **queryType** a ogni richiesta. I valori validi includono **simple**|**full**, dove **simple** è l'impostazione predefinita. Per informazioni dettagliate su come specificare i parametri di query, vedere l'articolo relativo alla[ricerca di documenti (API REST del servizio Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Esempio 1**: fare clic con il pulsante destro del mouse sul frammento di query seguente per aprirlo in una nuova pagina del browser che carica JSFiddle ed esegue la query. Questa query restituisce i documenti dall'indice Jobs, caricato in un servizio sandbox:
- [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

## Operazione di query con campo

Negli esempi che seguono si specificherà una costruzione **nomecampo:terminericerca** costruzione per definire un'operazione di query con campo, dove il campo è una singola parola e anche il termine di ricerca è una singola parola o una frase, usando facoltativamente operatori booleani. Ecco alcuni esempi:

- business\_title:senior NOT junior
- state:"New York" AND "New Jersey"

Assicurarsi di inserire più stringhe racchiuse tra virgolette se si vuole che entrambe le stringhe siano valutate come una singola entità, come in questo caso per la ricerca di due città distinte nel campo "location". Assicurarsi anche che l'operatore sia in lettere maiuscole, come NOT e AND.

Il campo specificato **nomecampo:terminericerca** deve essere un campo ricercabile. Per informazioni dettagliate sull'uso di attributi dell'indice nelle definizioni di campo, vedere l'articolo relativo alla [creazione dell'indice (API REST del servizio Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

## Ricerca fuzzy

Una ricerca fuzzy trova le corrispondenze in termini che hanno una costruzione simile. Secondo la [documentazione su Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), le ricerche fuzzy si basano su [Damerau-Levenshtein Distance](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Per eseguire una ricerca fuzzy, usare il carattere tilde "~" alla fine di una singola parola con un parametro facoltativo, un valore compreso tra 0 e 2, che specifica il numero minimo di operazioni di modifica o "edit distance". Ad esempio, "blue~" or "blue~1" restituirà blue, blues e glue.

**Esempio 2**: fare clic con il pulsante del mouse sul frammento di query seguente per fare una prova. Questa query cerca business\_title che contengono il termine senior ma non junior:

- [&queryType=full&search= business\_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## Ricerca per prossimità

Le ricerche per prossimità vengono usate per trovare termini che si trovano vicini in un documento. Inserire un carattere tilde "~" alla fine di una frase seguito dal numero di parole che creano il limite di prossimità. Ad esempio, "hotel airport"~5 troverà i termini hotel e airport entro 5 parole di distanza una dall'altra in un documento.

**Esempio 3**: fare clic con il pulsante destro del mouse sul frammento di query seguente. Questa query cerca i processi con il termine associate, che non è scritto correttamente:

- [&queryType=full&search= business\_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Esempio 4**: fare clic con il pulsante destro del mouse sulla query. Ricerca processi con il termine "senior analyst" separato da non più di una parola:

- [&queryType=full&search=business\_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Esempio 5**: provare di nuovo a rimuovere le parole tra il termine "senior analyst".

- [&queryType=full&search=business\_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## Aumento priorità dei termini

Questa definizione si riferisce alla termine si riferisce alla classificazione più alta di un documento se contiene il termine con aumento di priorità, rispetto a documenti che non contengono il termine. Questo comportamento è diverso dall'assegnazione di punteggi ai profili, perché questo metodo assegna priorità ad alcuni campi, invece che a termini specifici. L'esempio seguente illustra le differenze.

Si consideri un profilo di punteggio che migliora le corrispondenze in un determinato campo, ad esempio **genre** nell'esempio musicstoreindex. L'aumento di priorità di un termine si usa per assegnare a determinati termini di ricerca una priorità maggiore rispetto ad altri. Ad esempio, "rock^2 electronic" aumenta la priorità dei documenti che contengono termini di ricerca nel campo **genre** con una priorità maggiore rispetto ad altri campi ricercabili nell'indice. Inoltre, i documenti che contengono il termine di ricerca "rock" verranno classificati con una priorità superiore rispetto all'altro termine di ricerca "electronic" come risultato il valore di priorità del termine (2).

Per aumentare la priorità di un termine, usare il carattere accento circonflesso "^", con un fattore di aumento di priorità (un numero) alla fine del termine da cercare. Maggiore è il fattore di aumento di priorità, più rilevante sarà il termine rispetto ad altri termini di ricerca. Per impostazione predefinita, il fattore di aumento di priorità è 1. Anche se il fattore di aumento di priorità deve essere positivo, può essere minore di 1 (ad esempio 0,2).

**Esempio 6**: fare clic con il pulsante destro del mouse sulla query. Cercare processi con il termine "computer analyst" per cui non sono presenti risultati con entrambe le parole computer e analyst, anche se all'inizio dei risultati sono presenti processi che contengono analyst.

- [&queryType=full&search=business\_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Esempio 7**: provare di nuovo, questa volta aumentando la priorità dei risultati con il termine computer rispetto al termine analyst, se non sono presenti entrambe le parole.

- [&queryType=full&search=business\_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## Espressione regolare

Una ricerca con espressione regolare trova una corrispondenza in base al contenuto tra le barre "/", come indicato nella [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Esempio 8**: fare clic con il pulsante destro del mouse sulla query. Cercare i processi con il termine Senior o Junior.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

L'URL per questo esempio non visualizza correttamente la pagina. In alternativa, copiare l'URL seguente e incollarlo l'indirizzo URL del browser: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## Ricerca con caratteri jolly

È possibile usare una sintassi generalmente riconosciuta per ricerche con caratteri jolly per trovare più caratteri (*) o un singolo carattere (?). Si noti che il parser di query Lucene supporta l'utilizzo di questi simboli con un singolo termine, non una frase.

**Esempio 9**: fare clic con il pulsante destro del mouse sulla query. Cercare processi contenenti il prefisso 'prog' che includerà business\_title con i termini programming e programmer.

- [&queryType=full&$select=business\_title&search=business\_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Non è possibile usare un carattere * o ? come primo carattere di una ricerca.


## Passaggi successivi

Provare a specificare il parser di query Lucene nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca per .NET e l'API REST. I collegamenti usano la sintassi semplice predefinita, quindi sarà necessario applicare quanto appreso in questo articolo per specificare il parametro **queryType**.

- [Eseguire query su un indice di Ricerca di Azure con .NET SDK](search-query-dotnet.md)
- [Eseguire query su un indice di Ricerca di Azure con l'API REST](search-query-rest-api.md)


 

<!---HONumber=AcomDC_0330_2016-->