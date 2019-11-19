---
title: Creare un indice di ricerca in portale di Azure
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare un indice per ricerca cognitiva di Azure usando una finestra di progettazione dell'indice del portale incorporata.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112838"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Creare un indice di ricerca cognitiva di Azure nel portale

Azure ricerca cognitiva include una finestra di progettazione indici incorporata nel portale utile per i prototipi o la creazione di un [indice di ricerca](search-what-is-an-index.md) ospitato nel servizio ricerca cognitiva di Azure. Lo strumento viene usato per la costruzione degli schemi. Quando si salva la definizione, un indice vuoto diventa completamente espresso in ricerca cognitiva di Azure. Il modo in cui viene caricato con contenuto ricercabile dipende dall'utente.

La finestra di progettazione degli indici rappresenta solo uno degli approcci per la creazione di un indice. In alternativa, è possibile creare e caricare un indice usando la [procedura guidata Importa dati](search-get-started-portal.md). La procedura guidata funziona solo con gli indici creati automaticamente. A livello di codice, è possibile creare un indice usando le API [.NET](search-create-index-dotnet.md) o [REST](search-create-index-rest-api.md).

## <a name="start-index-designer"></a>Avviare la finestra di progettazione degli indici

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire il dashboard del servizio. Per cercare i servizi di ricerca esistenti nella sottoscrizione corrente, è possibile fare clic su **Tutti i servizi** nell'indice. 

2. Fare clic sul collegamento **Aggiungi indice** nella barra dei comandi nella parte superiore della pagina.

   ![Aggiungi collegamento Indice nella barra dei comandi](media/search-create-index-portal/add-index.png "Aggiungi collegamento Indice nella barra dei comandi")

3. Assegnare un nome all'indice ricerca cognitiva di Azure. Il riferimento ai nomi di indice è previsto nelle operazioni di indicizzazione e creazione di query. Il nome dell'indice diventa parte dell'URL dell'endpoint usato per le connessioni all'indice e per l'invio di richieste HTTP nell'API REST di Azure ricerca cognitiva.

   * Deve iniziare con una lettera.
   * Usare solo lettere minuscole, numeri o trattini ("-").
   * Il limite per il nome è di 60 caratteri.

## <a name="add-fields"></a>Aggiungere i campi

La composizione dell'indice include un *insieme di campi* che definisce i dati su cui è possibile eseguire ricerche nell'indice. In generale, la raccolta di campi specifica la struttura dei documenti caricati separatamente. La raccolta Campi include i campi obbligatori e facoltativi, denominati e digitati, con gli attributi dell'indice che determinano le modalità d'uso del campo.

1. Aggiungere i campi per specificare tutti i documenti che verranno caricati, impostando un [tipo di dati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) per ognuno. Se ad esempio i documenti includono *ID hotel*, *nome hotel*, *indirizzo*, *città* e *area*, creare un campo corrispondente per ognuno di essi nell'indice. Rivedere le [istruzioni di progettazione nella sezione seguente](#design) per informazioni sull'impostazione degli attributi.

1. Se i dati in ingresso sono di natura gerarchica, nello schema devono essere inclusi [tipi complessi](search-howto-complex-data-types.md) per rappresentare le strutture nidificate. Il set di dati di esempio predefinito, Hotels, illustra i tipi complessi usando un indirizzo (contiene più sottocampi) che presenta una relazione uno-a-uno con ogni hotel e una raccolta complessa di chat, in cui più stanze sono associate a ogni hotel. 

1. Specificare un campo *chiave* di tipo Edm.String. Un campo chiave è obbligatorio per ogni indice di ricerca cognitiva di Azure ed è necessario che sia una stringa. I valori per questo campo devono identificare in modo univoco ogni documento. Per impostazione predefinita, il campo viene denominato *id* ma è possibile rinominarlo, purché la stringa soddisfi le [regole di denominazione](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Se ad esempio la raccolta di campi include *ID hotel*, sceglierlo come chiave. 

1. Impostare gli attributi in ogni campo. La finestra di progettazione degli indici esclude tutti gli attributi non validi per il tipo di dati, ma non suggerisce che cosa includere. Esaminare le indicazioni fornite nella sezione successiva per comprendere la funzione degli attributi.

    La documentazione dell'API di Azure ricerca cognitiva include esempi di codice con un semplice indice degli *Hotel* . Nello screenshot seguente è possibile vedere la definizione dell'indice, che include l'analizzatore di lingua francese specificato durante la definizione dell'indice, che è possibile ricreare come esercitazione nel portale.

    ![Indice demo degli Alberghi](media/search-create-index-portal/field-definitions.png "Indice demo degli Alberghi")

1. Al termine, fare clic su **Crea** per salvare e creare l'indice.

<a name="design"></a>

## <a name="set-attributes"></a>Impostare gli attributi

Sebbene sia possibile aggiungere nuovi campi in qualsiasi momento, le definizioni del campo esistente vengono bloccate per la durata dell'indice. Per questo motivo, gli sviluppatori in genere usano il portale per la creazione di indici semplici, idee di test o usano le pagine del portale per cercare un'impostazione. L'iterazione frequente su una progettazione degli indici è più efficiente se si segue un approccio basato sul codice in modo che sia possibile ricompilare l'indice con facilità.

Gli analizzatori e gli strumenti suggerimenti vengono associati ai campi prima che venga salvato l'indice. Assicurarsi di aggiungere gli analizzatori della lingua o gli strumenti suggerimenti alla definizione dell'indice durante la creazione.

I campi della stringa spesso sono contrassegnati come **Ricercabile** e **Recuperabile**. I campi usati per limitare i risultati della ricerca includono **Classificabile**, **Filtrabile**, e **Con facet**.

Gli attributi del campo determinano le modalità in cui un campo viene usato, ad esempio se viene usato nella ricerca full-text, nella navigazione con facet, nelle operazioni di ordinamento e così via. Nella tabella seguente sono descritti i singoli attributi.

|Attribute|DESCRIZIONE|  
|---------------|-----------------|  
|**searchable**|Ricercabile full-text, soggetto ad analisi lessicali, ad esempio alla scomposizione delle parole durante l'indicizzazione. Se si imposta un campo ricercabile su un valore come "sunny day", questo viene suddiviso internamente nei singoli token "sunny" e "day". Per informazioni vedere [Funzionamento della ricerca full-text](search-lucene-query-architecture.md).|  
|**filterable**|A cui si fa riferimento nelle query **$filter**. I campi filtrabili di tipo `Edm.String` o `Collection(Edm.String)` non sono sottoposti a suddivisione delle parole e quindi i confronti riguardano solo le corrispondenze esatte. Se ad esempio si imposta un campo su "sunny day", `$filter=f eq 'sunny'` non troverà corrispondenze, mentre `$filter=f eq 'sunny day'` ne troverà. |  
|**sortable**|Per impostazione predefinita il sistema ordina i risultati in base al punteggio, ma è possibile configurare l'ordine in base ai campi nei documenti. I campi di tipo `Collection(Edm.String)` non possono essere **ordinabili**. |  
|**facetable**|In genere usato in una presentazione dei risultati della ricerca che include un numero di passaggi per categoria, ad esempio, gli hotel in una specifica città. Questa opzione non può essere usata con i campi di tipo `Edm.GeographyPoint`. I campi di tipo `Edm.String` che sono **filtrabili**, **ordinabili**, o **con facet** possono contenere al massimo 32 kilobyte di lunghezza. Per altri dettagli, vedere [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)(Creare un indice: API REST).|  
|**key**|Identificatore univoco per i documenti all'interno dell'indice. È necessario scegliere un singolo campo come campo chiave e questo deve essere di tipo `Edm.String`.|  
|**retrievable**|Specifica se il campo può essere restituito nel risultato di una ricerca. Questo attributo è utile quando si vuole usare un campo, ad esempio *margine di profitto*, come meccanismo di filtro, ordinamento o punteggio ma si preferisce che il campo non sia visibile all'utente finale. L'attributo deve essere `true` for `key` .|  

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un indice di ricerca cognitiva di Azure, è possibile passare al passaggio successivo: [caricare i dati disponibili per la ricerca nell'indice](search-what-is-data-import.md).

In alternativa, è anche possibile [analizzare in modo approfondito gli indici](search-what-is-an-index.md). Oltre alla raccolta di campi, un indice specifica anche gli analizzatori, gli strumenti suggerimenti, i profili di punteggio e le impostazioni CORS. Il portale offre pagine a schede per definire gli elementi più comuni: i campi, gli analizzatori e gli strumenti di suggerimento. Per creare o modificare altri elementi, è possibile usare l'API REST o .NET SDK.

## <a name="see-also"></a>Vedere anche

 [Funzionamento della ricerca full-text](search-lucene-query-architecture.md)  
 [API REST del servizio Ricerca](https://docs.microsoft.com/rest/api/searchservice/)[ .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

