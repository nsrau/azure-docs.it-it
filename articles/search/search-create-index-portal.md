---
title: 'Creare un indice: portale e Ricerca di Azure | Microsoft Docs'
description: Creare un indice nel portale di Azure.
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Creare un indice di Ricerca di Azure nel portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-what-is-an-index.md)
> * [Portale](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Usare la finestra di progettazione per l'indice predefinita nel portale di Azure per creare un prototipo un [indice di ricerca](search-what-is-an-index.md) da eseguire sul servizio Ricerca di Azure. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone la disponibilità di una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e del [servizio Ricerca di Azure](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Trovare il servizio di ricerca
1. Accedere alla pagina del portale di Azure ed esaminare i [servizi di ricerca per la sottoscrizione](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selezionare il servizio Ricerca di Azure.

## <a name="name-the-index"></a>Assegnare un nome all'indice

1. Fare clic sul pulsante **Aggiungi indice** nella barra dei comandi nella parte superiore della pagina.
2. Assegnare un nome all'indice di Ricerca di Azure. 
   * Deve iniziare con una lettera.
   * Usare solo lettere minuscole, numeri o trattini ("-").
   * Il limite per il nome è di 60 caratteri.

  Il nome dell'indice diventa parte dell'URL dell'endpoint usato per le connessioni all'indice e per l'invio di richieste HTTP nell'API REST di Ricerca di Azure.

## <a name="define-the-fields-of-your-index"></a>Definire i campi dell'indice

La composizione dell'indice include un *insieme di campi* che definisce i dati su cui è possibile eseguire ricerche nell'indice. In particolare, specifica la struttura dei documenti caricati separatamente. La raccolta campi include i campi obbligatori e facoltativi, denominati e digitati, con gli attributi dell'indice per determinare le modalità d'uso del campo.

1. Nel pannello **Aggiungi indice** fare clic su **Campi >** per aprire con effetto di scorrimento il pannello di definizione del campo. 

2. Accettare il campo *chiave* generato di tipo Edm.String. Per impostazione predefinita, il campo viene denominato *id* ma è possibile rinominarlo, purché la stringa soddisfi le [regole di denominazione](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Per ogni indice di Ricerca di Azure è obbligatorio un campo chiave che deve essere una stringa.

3. Aggiungere i campi per specificare tutti i documenti che verranno caricati. Se i documenti includono *ID*, *nome hotel*, *indirizzo*, *città* e *area*, creare un campo corrispondente per ciascuno di essi nell'indice. Rivedere le [istruzioni di progettazione nella sezione seguente](#design) per informazioni sugli attributi di impostazione.

4. Facoltativamente, aggiungere tutti i campi che vengono usati internamente nelle espressioni di filtro. È possibile impostare gli attributi nel campo per escludere i campi dalle operazioni di ricerca.

5. Al termine, fare clic su **OK** per salvare e creare l'indice.

## <a name="tips-for-adding-fields"></a>Suggerimenti per l'aggiunta di campi

La creazione di un indice nel portale prevede l'uso quasi esclusivo della tastiera. Abbreviare la procedura seguendo questo flusso di lavoro:

1. Innanzitutto, compilare l'elenco dei campi immettendo i nomi impostando i tipi di dati.

2. Successivamente, usare le caselle di controllo nella parte superiore di ciascun attributo abilitare in blocco l'impostazione per tutti i campi e quindi cancellare selettivamente le caselle di alcuni campi non necessarie. Ad esempio, nei i campi della stringa in genere è possibile eseguire ricerche. Di conseguenza, è possibile fare clic su **Recuperabile** e **Ricercabile** sia per restituire i valori del campo nei risultati della ricerca, che per consentire la ricerca full-text nel campo. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Indicazioni di progettazione per impostare gli attributi

Sebbene sia possibile aggiungere nuovi campi in qualsiasi momento, le definizioni del campo esistente vengono bloccate per la durata dell'indice. Per questo motivo, gli sviluppatori in genere usano il portale per la creazione di indici semplici, idee di test o usano le pagine del portale per cercare un'impostazione. L'iterazione frequente su una progettazione degli indici è più efficiente se si segue un approccio basato sul codice in modo che sia possibile ricompilare l'indice con facilità.

Gli analizzatori e gli strumenti suggerimenti vengono associati ai campi prima che venga salvato l'indice. Assicurarsi di fare clic su ogni pagina a schede per aggiungere gli analizzatori del linguaggio o gli strumenti suggerimenti alla definizione dell'indice.

I campi della stringa spesso sono contrassegnati come **Ricercabile** e **Recuperabile**.

I campi usati per limitare i risultati della ricerca includono **Classificabile**, **Filtrabile**, e **Con facet**.

Gli attributi del campo determinano le modalità in cui un campo viene usato, ad esempio se viene usato nella ricerca full-text, nella navigazione con facet, nelle operazioni di ordinamento e così via. Nella tabella seguente sono descritti i singoli attributi.

|Attributo|DESCRIZIONE|  
|---------------|-----------------|  
|**searchable**|Ricercabile full-text, soggetto ad analisi lessicali, ad esempio alla scomposizione delle parole durante l'indicizzazione. Se si imposta un campo ricercabile su un valore come "sunny day", questo viene suddiviso internamente nei singoli token "sunny" e "day". Per informazioni vedere [Funzionamento della ricerca full-text](search-lucene-query-architecture.md).|  
|**filterable**|A cui si fa riferimento nelle query **$filter**. I campi filtrabili di tipo `Edm.String` o `Collection(Edm.String)` non sono sottoposti a suddivisione delle parole e quindi i confronti riguardano solo le corrispondenze esatte. Se ad esempio si imposta un campo su "sunny day", `$filter=f eq 'sunny'` non troverà corrispondenze, mentre `$filter=f eq 'sunny day'` ne troverà. |  
|**sortable**|Per impostazione predefinita il sistema ordina i risultati in base al punteggio, ma è possibile configurare l'ordine in base ai campi nei documenti. I campi di tipo `Collection(Edm.String)` non possono essere **ordinabili**. |  
|**facetable**|In genere usato in una presentazione dei risultati della ricerca che include un numero di passaggi per categoria, ad esempio, gli hotel in una specifica città. Questa opzione non può essere usata con i campi di tipo `Edm.GeographyPoint`. I campi di tipo `Edm.String` che sono **filtrabili**, **ordinabili**, o **con facet** possono contenere al massimo 32 kilobyte di lunghezza. Per altri dettagli, vedere [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)(Creare un indice: API REST).|  
|**key**|Identificatore univoco per i documenti all'interno dell'indice. È necessario scegliere un singolo campo come campo chiave e questo deve essere di tipo `Edm.String`.|  
|**retrievable**|Specifica se il campo può essere restituito nel risultato di una ricerca. Questo attributo è utile quando si vuole usare un campo, ad esempio *margine di profitto*, come meccanismo di filtro, ordinamento o punteggio ma si preferisce che il campo non sia visibile all'utente finale. L'attributo deve essere `true` for `key` .|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Creare l'indice degli hotel usato nelle sezioni API di esempio

La documentazione API di Ricerca di Azure include esempi di codice che presentano un semplice indice *hotel*. Nelle schermate riportate di seguito, è possibile visualizzare la definizione dell'indice, che include l'analizzatore di lingua francese specificato durante la definizione dell'indice, che è possibile ricreare come esercitazione nel portale.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un indice di Ricerca di Azure, è possibile passare al passaggio successivo: [caricare i dati ricercabili nell'indice](search-what-is-data-import.md).

In alternativa, è anche possibile analizzare in modo approfondito gli indici. Oltre alla raccolta di campi, un indice specifica anche gli analizzatori, gli strumenti suggerimenti, i profili di punteggio e le impostazioni CORS. Il portale offre pagine a schede per definire gli elementi più comuni: i campi, gli analizzatori e gli strumenti di suggerimento. Per creare o modificare altri elementi, è possibile usare l'API REST o .NET SDK.

## <a name="see-also"></a>Vedere anche 

 [Funzionamento della ricerca full-text](search-lucene-query-architecture.md)  
 [API REST del servizio Ricerca](https://docs.microsoft.com/rest/api/searchservice/)[ .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

