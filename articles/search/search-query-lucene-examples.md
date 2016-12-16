---
title: Esempi di query Lucene per Ricerca di Azure | Documentazione Microsoft
description: "Sintassi di query Lucene per ricerca fuzzy, ricerca per prossimità, l&quot;aumento della priorità dei termini, ricerca con espressioni regolari e ricerca con caratteri jolly."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: Lucene query analyzer syntax
ms.assetid: 147f360d-a5ce-4d7b-a909-c8b65bfb748c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: liamca
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 0f81ac0d19cd0f20c1f2214042eda391a3e68a7d

---

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Esempi di sintassi di query Lucene per la creazione di query in Ricerca di Azure
Quando si creano query per Ricerca di Azure, è possibile usare la [sintassi di query semplice](https://msdn.microsoft.com/library/azure/dn798920.aspx) predefinita o il [parser di query Lucene in Ricerca di Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). Il parser di query Lucene supporta costrutti di query più complessi, ad esempio le query con ambito campo, ricerca fuzzy, ricerca di prossimità, aumento priorità dei termini e ricerca di espressioni regolari.

Questo articolo consente di esaminare esempi che visualizzano la sintassi di query Lucene e i risultati corrispondenti affiancati. Gli esempi vengono eseguiti su un indice di ricerca precaricato in [JSFiddle](https://jsfiddle.net/), un editor di codice online per il test di script e HTML.

Fare clic con il pulsante destro del mouse sugli URL degli esempi di query per aprire JSFiddle in una finestra del browser distinta.

> [!NOTE]
> Gli esempi seguenti sfruttano un indice di ricerca costituito da processi disponibili in base a un set di dati fornito tramite l'iniziativa [City of New York OpenData](https://nycopendata.socrata.com/) . Questi dati non devono essere considerati attuali o completi. L'indice è un servizio sandbox fornito da Microsoft. Non è necessaria una sottoscrizione di Azure o Ricerca di Azure per provare queste query.
>
>

## <a name="viewing-the-examples-in-this-article"></a>Visualizzazione degli esempi in questo articolo
Tutti gli esempi di questo articolo specificano il parser di query Lucene tramite il parametro di ricerca**queryType** . Quando si usa il parser di query Lucene dal codice, il parametro **queryType** va specificato in ogni richiesta.  I valori validi includono **simple**|**full**, dove **simple** è l'impostazione predefinita e **full** corrisponde al parser di query Lucene. Per informazioni dettagliate su come specificare i parametri di query, vedere l'articolo relativo alla [ricerca di documenti (API REST del servizio Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) .

**Esempio 1** : fare clic con il pulsante destro del mouse sul frammento di query seguente per aprirlo in una nuova pagina del browser che carica JSFiddle ed esegue la query:

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Questa query restituisce documenti dall'indice delle offerte di lavoro, caricato in un servizio sandbox.

Nella nuova finestra del browser, il codice di origine Javascript e l'output HTML appaiono affiancati. Lo script fa riferimento a una query specificata negli URL di esempio presenti in questo articolo. Nell' **Esempio 1**la query sottostante è la seguente:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Si noti che la query usa un indice di Ricerca di Azure preconfigurato e denominato nycjobs. Il parametro **searchFields** limita la ricerca al solo campo della qualifica professionale (business_title). **queryType** è impostato su **full**, pertanto Ricerca di Azure usa il parser di query Lucene per eseguire la query.

### <a name="fielded-query-operation"></a>Operazione di query con campo
È possibile modificare gli esempi dell'articolo specificando una costruzione **nomecampo:terminericerca** per definire un'operazione query con campo, dove il campo è una singola parola e anche il termine di ricerca è una singola parola o una frase, che può includere operatori booleani. Ecco alcuni esempi:

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

Assicurarsi di inserire più stringhe racchiuse tra virgolette se si vuole che entrambe le stringhe siano valutate come una singola entità, come in questo caso per la ricerca di due città distinte nel campo "location". Assicurarsi anche che l'operatore sia in lettere maiuscole, come NOT e AND.

Il campo specificato in **nomecampo:terminericerca** deve essere un campo ricercabile. Per informazioni dettagliate sull'uso di attributi dell'indice nelle definizioni campo, vedere [Creare l'indice (API REST del servizio Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) .

## <a name="fuzzy-search"></a>Ricerca fuzzy
Una ricerca fuzzy trova le corrispondenze in termini che hanno una costruzione simile. Secondo la [documentazione di Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), le ricerche fuzzy si basano sulla [distanza di Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Per eseguire una ricerca fuzzy, usare il carattere tilde "~" alla fine di una singola parola con un parametro facoltativo, un valore compreso tra 0 e 2, che specifica il numero minimo di operazioni di modifica o "edit distance". Ad esempio, "blue~" or "blue~1" restituirà blue, blues e glue.

**Esempio 2** : fare clic con il pulsante destro del mouse sul frammento di query seguente per fare una prova. Questa query cerca business_title che contengono il termine senior ma non junior:

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Ricerca per prossimità
Le ricerche per prossimità vengono usate per trovare termini che si trovano vicini in un documento. Inserire un carattere tilde "~" alla fine di una frase seguito dal numero di parole che creano il limite di prossimità. Ad esempio, "hotel airport"~5 troverà i termini hotel e airport entro 5 parole di distanza una dall'altra in un documento.

**Esempio 3** : fare clic con il pulsante destro del mouse sul frammento di query seguente. Questa query cerca i processi con il termine associate, che non è scritto correttamente:

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Esempio 4** : fare clic con il pulsante destro del mouse sulla query. Ricerca processi con il termine "senior analyst" separato da non più di una parola:

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Esempio 5** : ricerca le ricorrenze in cui "senior analyst" non è separato da nessuna parola.

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Aumento priorità dei termini
Questa definizione si riferisce alla termine si riferisce alla classificazione più alta di un documento se contiene il termine con aumento di priorità, rispetto a documenti che non contengono il termine. Questo comportamento è diverso dall'assegnazione di punteggi ai profili, perché questo metodo assegna priorità ad alcuni campi, invece che a termini specifici. L'esempio seguente illustra le differenze.

Considerare un profilo di punteggio che migliora le corrispondenze in un determinato campo, ad esempio **genre** (genere) nell'esempio musicstoreindex. L'aumento di priorità di un termine si usa per assegnare a determinati termini di ricerca una priorità maggiore rispetto ad altri. Ad esempio, "rock^2 electronic" aumenta la priorità nell'indice dei documenti che contengono tali termini di ricerca nel campo **genre** rispetto a quelli con altri campi ricercabili. Inoltre, i documenti che contengono il termine di ricerca "rock" verranno classificati con una priorità superiore rispetto all'altro termine di ricerca "electronic" come risultato il valore di priorità del termine (2).

Per aumentare la priorità di un termine, usare il carattere accento circonflesso "^", con un fattore di aumento di priorità (un numero) alla fine del termine da cercare. Maggiore è il fattore di aumento di priorità, più rilevante sarà il termine rispetto ad altri termini di ricerca. Per impostazione predefinita, il fattore di aumento di priorità è 1. Anche se il fattore di aumento di priorità deve essere positivo, può essere minore di 1 (ad esempio 0,2).

**Esempio 6**: fare clic con il pulsante destro del mouse sulla query. Cercare processi con il termine "computer analyst" per cui non sono presenti risultati con entrambe le parole computer e analyst, anche se all'inizio dei risultati sono presenti processi che contengono analyst.

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Esempio 7**: provare di nuovo, questa volta aumentando la priorità dei risultati con il termine computer rispetto al termine analyst, se non esiste nessuna delle due parole.

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Espressione regolare
Una ricerca con espressione regolare trova una corrispondenza in base al contenuto incluso tra le barre "/", come indicato nella [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Esempio 8** : fare clic con il pulsante destro del mouse sulla query. Cercare i processi con il termine Senior o Junior.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

L'URL per questo esempio non visualizza correttamente la pagina. Come soluzione alternativa, copiare l'URL seguente e incollarlo nell'indirizzo URL del browser:     `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search"></a>Ricerca con caratteri jolly
È possibile usare una sintassi generalmente riconosciuta per ricerche con caratteri jolly per trovare più caratteri (\*) o un singolo carattere (?). Si noti che il parser di query Lucene supporta l'utilizzo di questi simboli con un singolo termine, non una frase.

**Esempio 9**: fare clic con il pulsante destro del mouse sulla query. Cercare processi contenenti il prefisso 'prog' che includerà business_title con i termini programming e programmer.

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

Non è possibile usare un carattere * o ? come primo carattere di una ricerca.

## <a name="next-steps"></a>Passaggi successivi
Provare a specificare il parser di query Lucene nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca per .NET e l'API REST. I collegamenti usano la sintassi semplice predefinita, quindi è necessario applicare quanto appreso in questo articolo per specificare il parametro **queryType**.

* [Eseguire query su un indice di Ricerca di Azure con .NET SDK](search-query-dotnet.md)
* [Eseguire query su un indice di Ricerca di Azure con l'API REST](search-query-rest-api.md)



<!--HONumber=Nov16_HO3-->


