<properties
	pageTitle="Come cercare dati di StackExchange con Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Informazioni su come eseguire ricerche REST tramite la Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure."
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="liamca"/>

# Come cercare dati di StackExchange con Ricerca di Azure

Questo articolo illustra in dettaglio alcune delle principali funzionalità di ricerca full-text che è possibile usare con [Ricerca di Azure](https://azure.microsoft.com/services/search/). Sfrutta i dati resi [disponibili](https://archive.org/details/stackexchange) da Stack Exchange per l'utilizzo di Creative Commons con l'[attribuzione](http://blog.stackoverflow.com/2009/06/attribution-required/) seguente.

## Introduzione

Per evidenziare alcune di queste funzionalità, è stato creato un indice di Ricerca di Azure da usare come esempio, che contiene i dati di Programmer StackExchange al 14 ottobre 2015. NOTA: più avanti verrà anche illustrato come creare un indice di Ricerca di Azure personalizzato con questi dati.

Si inizierà con una query di ricerca full-text veramente semplice in cui gli utenti possono digitare la parola "azure" per trovare tutti i post di StackExchange relativi ad Azure. Per provare, fare clic su questo collegamento per vedere la ricerca in azione:

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

In questo esempio è sufficiente passare la parola "azure" come parametro di ricerca e visualizzare i risultati restituiti in formato JSON. Ecco alcuni altri esempi di query che è possibile provare.

-	`Faceting`: Quando l'utente esegue la ricerca del set di dati, la possibilità di filtrare i dati è un ottimo modo per spostarsi tra i risultati. A questo scopo, si inizia in genere con un set di categorie (facet) visualizzate all'utente. Di seguito sono riportati alcuni esempi di facet utilizzabili:
  -	**Tag**: molte domande sono associate a tag per consentire agli utenti di esaminare in dettaglio categorie specifiche.
  -	**Date**: un utente potrebbe voler vedere solo le domande sottoposte o le relative risposte in un intervallo di tempo specifico.
  -	**Utente**: si potrebbe voler visualizzare o limitare i risultati da utenti specifici. In questo esempio, si cercherà "azure", ma verrà restituito il numero di facet per i nomi utente tagsCollection e acceptedAnswerDisplayName.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`: Dopo che un utente ha scelto un facet, si potrà eseguire un'altra ricerca, ma sfruttando un filtro per limitare i risultati al valore del facet. Ad esempio, per cercare "Azure" ma limitando i risultati alle voci in cui esiste un tag "architettura" ordinato per viewCount in ordine decrescente:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Spelling Mistakes`: il nuovo supporto (in anteprima) per le [espressioni di query Lucene](https://msdn.microsoft.com/library/mt589323.aspx) consente anche di eseguire alcune query personalizzate, ad esempio corrispondenze fuzzy dei risultati e limitazione della ricerca a campi specifici. In questo esempio si esegue la ricerca della parola "visualizza" nel campo titolo, ma ~ indica una corrispondenza fuzzy e ciò significa che saranno restituiti anche risultati come visualizzare e visualizzazione.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: i[profili di punteggio](https://msdn.microsoft.com/library/azure/dn798928.aspx) sono estremamente utili perché consentono di ottimizzare i risultati restituiti dal servizio di ricerca. Infatti, ora è possibile usare le [espressioni di query Lucene](https://msdn.microsoft.com/library/mt589323.aspx) per applicare immediatamente il punteggio a singoli campi e termini. Ad esempio, per cercare la parola "visualizza" o "grafico" nel campo del titolo, ma conferire un peso maggiore agli elementi contengono la parola "grafico", è possibile procedere in questo modo:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  Esistono molti altri campi nel set di dati che possono essere usati per migliorare i risultati rilevanti per gli utenti. Ad esempio, è possibile usare:

  -	**Grandezza**, punteggio sui campi numerici come answerCount, commentCount, favoriteCount e viewCount per favorire il miglioramento dei risultati della ricerca nel caso di conteggi elevati.
  -	**Aggiornamento**, punteggio sui campi datetime, ad esempio creationDate e lastActivityDate per il miglioramento degli elementi creati o attivi più di recente.
  -	**Pesi dei campi**, per indicare che se il testo di ricerca viene trovato nel corpo della domanda, è considerato più rilevante rispetto a quando viene trovato nella risposta.

Altre operazioni che è possibile provare a eseguire includono:

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx): quando gli utenti digitano nella casella di ricerca, sarà utile usare il completamento automatico per i campi come Titolo, Tag e Nome utente.  

-	`Recommendations`: spesso sono necessari strumenti come Apache Mahout o Azure Machine Learning per creare suggerimenti che consentano di visualizzare domande simili a cui gli utenti potrebbero interessati, ma in questo set di dati ne sono già incluse alcune.

In questa pagina JSFiddle è possibile provare a usare diversi tipi di query. Per altre informazioni sulle modalità di creazione dell'indice, continuare a leggere. È anche possibile contattare l'autore tramite account twitter [@liamca](https://twitter.com/liamca).

## Come è stato creato questo indice di Ricerca di Azure?

Brent Ozar ha già approfondito questo aspetto mostrando come organizzare i dati in un database SQL. Per esaminare in dettaglio il processo di staging dei dati, vedere questa [esercitazione](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/). Diversamente, è possibile sfruttare semplicemente il database SQL di Azure già popolato con alcuni dati il 15 ottobre 2015 oppure provare l'indice di Ricerca di Azure creato. I relativi dettagli sono illustrati nella sezione "Importazione dei dati di staging dal database SQL di Azure" che segue. L'unica cosa aggiunta a quanto già descritto da Brent Ozar è stata la creazione di una vista nel database SQL di Azure che verrà usata dall'[indicizzatore di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx) per effettuare una ricerca per indicizzazione e inserire dati da un gruppo di tabelle a cui si è interessati. Ecco come viene definita in questa vista.

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

Al termine, è possibile usare il [portale Azure classico](https://portal.azure.com) scegliendo "Importa dati" dalla vista SQL di Azure precedente per creare quindi un indice di Ricerca di Azure basato sullo schema dei campi presenti nella vista. Se si vuole usare il database SQL di Azure di staging, ecco la stringa di connessione di sola lettura che è possibile usare:

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=AcomDC_1203_2015-->