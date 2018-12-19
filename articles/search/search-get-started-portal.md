---
title: Esercitazione su indicizzazione, query e filtri nel portale di Azure - Ricerca di Azure
description: In questa esercitazione si useranno il portale di Azure e i dati di esempio predefiniti per generare un indice in Ricerca di Azure. È possibile esplorare la ricerca full-text, i filtri, i facet, la ricerca fuzzy, la ricerca geografica e altro ancora.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d8b95aaab99fc4f0aa5df21374d6ec023f869b7d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314024"
---
# <a name="tutorial-use-built-in-portal-tools-for-azure-search-indexing-and-queries"></a>Esercitazione: Usare gli strumenti predefiniti del portale per indicizzazione e query in Ricerca di Azure

Per un rapido esame e una migliore comprensione dei concetti relativi a Ricerca di Azure, è possibile usare gli strumenti predefiniti forniti nella pagina del servizio Ricerca di Azure nel portale di Azure. Questi strumenti possono non offrire la funzionalità completa delle API .NET e REST. Tuttavia, le procedure guidate e gli editor offrono un'introduzione senza codice a Ricerca di Azure, per poter scrivere query interessanti direttamente su un set di dati di esempio.

> [!div class="checklist"]
> * Iniziare con i dati di esempio pubblici e generare automaticamente un indice di Ricerca di Azure tramite la procedura guidata **Importa dati**.
> * Visualizzare lo schema dell'indice e gli attributi per qualsiasi indice pubblicato in Ricerca di Azure.
> * Con **Esplora ricerche** è possibile esplorare la ricerca full-text, i filtri, i facet, la ricerca fuzzy e la ricerca geografica.  

Se gli strumenti sono troppo vincolanti, è possibile esaminare un'[introduzione basata su codice alla programmazione di Ricerca di Azure in .NET](search-howto-dotnet-sdk.md) oppure provare a usare gli [strumenti di test Web per effettuare chiamate API REST](search-fiddler.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. È anche possibile guardare una dimostrazione della durata di sei minuti dei passaggi illustrati in questa esercitazione, partendo circa dal terzo minuto di questa [panoramica video su Ricerca di Azure](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Prerequisiti

[Creare un servizio Ricerca di Azure](search-create-service-portal.md) o trovare un servizio esistente nella sottoscrizione corrente.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il dashboard relativo al servizio Ricerca di Azure. Se il riquadro del servizio non è stato aggiunto al dashboard, seguire questa procedura per trovare il servizio:

   * Nell'indice fare clic su **Tutti i servizi** nel riquadro di spostamento a sinistra.
   * Nella casella di ricerca digitare *ricerca* per ottenere un elenco dei servizi correlati alla ricerca per la sottoscrizione. Fare clic su **Servizi di ricerca**. Il servizio verrà visualizzato nell'elenco.

### <a name="check-for-space"></a>Verificare lo spazio

Molti clienti iniziano con il servizio gratuito. Questa versione è limitata a tre indici, tre origini dati e tre gli indicizzatori. Assicurarsi di avere spazio per gli elementi aggiuntivi prima di iniziare, perché ne verrà creato uno per ogni oggetto.

> [!TIP]
> Riquadri del dashboard del servizio mostrano il numero di indici, indicizzatori e origini dati già disponibili. Il riquadro Indicizzatore mostra gli indicatori di esito positivo e negativo. Fare clic sul riquadro per visualizzare il numero di indicizzatori.
>
> ![Riquadri degli indicizzatori e delle origini dati][1]
>

## <a name="create-index"></a> Creare un indice e caricare i dati

Le query di ricerca eseguono iterazioni su un [*indice*](search-what-is-an-index.md) contenente dati ricercabili, metadati e costrutti aggiuntivi usati per l'ottimizzazione di determinati comportamenti di ricerca.

Ai fini di questa esercitazione, viene usato un set di dati di esempio predefinito su cui è possibile eseguire una ricerca per indicizzazione con un [*indicizzatore*](search-indexer-overview.md) tramite la procedura guidata **Importa dati**. Un indicizzatore è un crawler specifico dell'origine in grado di leggere i metadati e il contenuto da origini dati di Azure supportate. Gli indicizzatori sono visibili nel portale tramite la procedura guidata **Importa dati**. Successivamente, è possibile creare indicizzatori a livello di codice e gestirli come risorse indipendenti.

### <a name="step-1-start-the-import-data-wizard"></a>Passaggio 1: Avviare la procedura guidata Importa dati

1. Dal dashboard del servizio Ricerca di Azure fare clic su **Importa dati** sulla barra dei comandi per avviare la procedura guidata. Questa procedura guidata semplifica la creazione e il completamento di un indice di ricerca.

    ![Comando Importa dati][2]

2. Nella procedura guidata fare clic su **Definisci la connessione ai dati** > **Esempi** > **realestate-us-sample**. Questa origine dati è preconfigurata con un nome, un tipo e informazioni di connessione. Dopo la creazione, diventa una "origine dati esistente" che può essere riutilizzata in altre operazioni di importazione.

    ![Selezionare il set di dati di esempio][9]

3. Fare clic su **OK** per usarla.

### <a name="skip-cognitive-skills"></a>Ignorare le competenze cognitive

La procedura guidata **Importa dati** include un passaggio facoltativo per le competenze cognitive che permette di aggiungere algoritmi di intelligenza artificiale personalizzati all'indicizzazione. Ignorare questo passaggio per il momento e passare a **Personalizza indice di destinazione**.

> [!TIP]
> È possibile provare a eseguire la nuova funzionalità di anteprima di ricerca cognitiva per Ricerca di Azure presentata nella [guida introduttiva alla ricerca cognitiva](cognitive-search-quickstart-blob.md) oppure in questa [esercitazione](cognitive-search-tutorial-blob.md).

   ![Ignorare il passaggio delle competenze cognitive][11]

### <a name="step-2-define-the-index"></a>Passaggio 2: Definire l'indice

In genere, la creazione dell'indice è un esercizio manuale svolto tramite codice. Ai fini di questa esercitazione, la procedura guidata può generare un indice per qualsiasi origine dati su cui è possibile eseguire ricerche per indicizzazione. Come minimo, un indice richiede un nome e una raccolta di campi, con un campo contrassegnato come chiave del documento per identificare in modo univoco ogni documento.

I campi hanno tipi di dati e attributi. Le caselle di controllo nella parte superiore sono *attributi dell'indice* che controllano il modo in cui viene usato il campo.

* **Recuperabile** indica che viene visualizzato nell'elenco dei risultati della ricerca. Deselezionando questa casella di controllo è possibile contrassegnare i singoli campi perché siano esclusi dai risultati della ricerca, ad esempio quando vengono usati solo nelle espressioni di filtro.
* **Filtrabile**, **Ordinabile** e **Con facet** determinano se un campo può essere usato in un filtro, un ordinamento o una struttura di esplorazione in base a facet.
* **Ricercabile** indica che un campo è incluso nella ricerca full-text. Le stringhe sono ricercabili. I campi numerici e i campi booleani sono spesso contrassegnati come non ricercabili.

Per impostazione predefinita, la procedura guidata analizza l'origine dati alla ricerca di identificatori univoci come base per il campo chiave. Le stringhe vengono attribuite come recuperabili e ricercabili. Gli Integer vengono attribuiti come recuperabili, filtrabili, ordinabili e con facet.

  ![Indice realestate generato][3]

Fare clic su **OK** per creare l'indice.

### <a name="step-3-define-the-indexer"></a>Passaggio 3: Definire l'indicizzatore

Sempre nella procedura guidata **Importa dati** fare clic su **Indicizzatore** > **Nome** e quindi digitare un nome per l'indicizzatore.

Questo oggetto definisce un processo eseguibile. È possibile inserirlo nella pianificazione ricorrente. Per il momento, tuttavia, usare l'opzione predefinita per eseguire l'indicizzatore una volta, immediatamente, facendo clic su **OK**.  

  ![Indicizzatore realestate][8]

### <a name="check-progress"></a>Verificare l'avanzamento

Per monitorare l'importazione dei dati, tornare al dashboard del servizio, scorrere verso il basso e fare doppio clic sul riquadro **Indicizzatori** per aprire l'elenco corrispondente. L'indicizzatore appena creato verrà visualizzato nell'elenco con lo stato "in corso" oppure "operazione riuscita", insieme al numero di documenti indicizzati.

   ![Messaggio di stato dell'indicizzatore][4]

### <a name="step-4-view-the-index"></a>Passaggio 4: Visualizzare l'indice

I riquadri nel dashboard del servizio offrono sia informazioni di riepilogo sui diversi oggetti in una risorsa sia l'accesso a informazioni dettagliate. Il riquadro **Indici** elenca gli indici esistenti, incluso l'indice*realestate-us-sample* appena creato nel passaggio precedente.

Fare ora clic sull'indice *realestate-us-sample* per visualizzare le opzioni del portale per la definizione dell'indice. L'opzione **Aggiungi/modifica campi** consente di creare nuovi campi e definire attributi completi per tali campi. I campi esistenti hanno una rappresentazione fisica in Ricerca di Azure e pertanto non sono modificabili, nemmeno nel codice. Per modificare in modo sostanziale un campo esistente, crearne uno nuovo ed eliminare l'originale.

   ![definizione di indice di esempio][10]

Altri costrutti, ad esempio i profili di punteggio e le opzioni CORS, possono essere aggiunti in qualsiasi momento.

Per capire chiaramente cosa è possibile o meno modificare durante la progettazione di indici, prendersi un po' di tempo per controllare le opzioni di definizione dell'indice. Le opzioni di grigio indicano che un valore non può essere modificato o eliminato. Analogamente, ignorare le caselle di controllo Analizzatore e Strumento suggerimenti per il momento.

## <a name="query-index"></a> Eseguire query sull'indice

A questo punto dovrebbe essere disponibile un indice di ricerca pronto per le query tramite la pagina predefinita per le query [**Esplora ricerche**](search-explorer.md). In questa pagina è disponibile una casella di ricerca che consente di testare stringhe di query arbitrarie.

> [!TIP]
> I passaggi seguenti vengono presentati al minuto 6.08 del [video della panoramica di Ricerca di Azure](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Fare clic su **Esplora ricerche** nella barra dei comandi.

   ![Comando di Esplora ricerche][5]

2. Fare clic su **Cambia indice** nella barra dei comandi per passare a *realestate-us-sample*. Fare clic su **Imposta versione API** nella barra dei comandi per verificare quali API REST sono disponibili. Per le query seguenti, usare la versione disponibile a livello generale (2017-11-11).

   ![Comandi dell'indice e dell'API][6]

3. Nella barra di ricerca immettere le stringhe di query riportate di seguito e fare clic su **Ricerca**.

    > [!NOTE]
    > **Esplora ricerche** è progettato per gestire unicamente [richieste all'API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Accetta sia la [sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) che la [sintassi completa del parser di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), oltre a tutti i parametri di ricerca disponibili nelle operazioni di [ricerca nei documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    >

### <a name="simple-query-with-top-n-results"></a>Query semplice con i primi N risultati

#### <a name="example-string-searchseattle"></a>Esempio (di stringa): `search=seattle`

* Il parametro **search** permette di inserire una ricerca per parole chiave per la ricerca full-text. In questo caso la ricerca restituisce voci nella contea di King, nello stato di Washington, contenenti il termine *Seattle* in qualsiasi campo ricercabile del documento.

* **Esplora ricerche** restituisce i risultati in JSON, un formato dettagliato e difficile da leggere se i documenti hanno una struttura densa. Si tratta di una scelta intenzionale. La visibilità dell'intero documento è importante per finalità di sviluppo, in particolare durante i test. Per migliorare l'esperienza utente, è necessario scrivere codice che [gestisce i risultati della ricerca](search-pagination-page-layout.md) per mettere in evidenza gli elementi importanti.

* I documenti sono costituiti da tutti i campi contrassegnati come recuperabili nell'indice. Per visualizzare gli attributi dell'indice nel portale, fare clic su *realestate-us-sample* nel riquadro **Indici**.

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>Esempio (con parametri): `search=seattle&$count=true&$top=100`

* Il simbolo **&** permette di aggiungere parametri di ricerca, che possono essere specificati in qualsiasi ordine.

* Il parametro **$count=true** restituisce il numero totale dei documenti trovati. Questo valore viene visualizzato nella parte superiore dei risultati della ricerca. È possibile verificare le query filtro monitorando le modifiche segnalate da **$count=true**. La riduzione dei risultati indica che il filtro funziona.

* **$top=100** restituisce i 100 documenti con classificazione più alta nel totale. Per impostazione predefinita, Ricerca di Azure restituisce le 50 migliori corrispondenze. Per aumentare o diminuire la quantità è possibile usare **$top**.

### <a name="filter-query"></a> Filtrare la query

Quando si aggiunge il parametro **$filter**, vengono inclusi filtri nelle richieste di ricerca. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Esempio (con filtri): `search=seattle&$filter=beds gt 3`

* Il parametro **$filter** restituisce risultati corrispondenti ai criteri immessi. In questo caso, un numero di camere da letto maggiore di 3.

* La sintassi del filtro è una costruzione OData. Per altre informazioni, vedere l'articolo relativo alla [sintassi OData per i filtri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> Applicare un facet alla query

Nelle richieste di ricerca vengono inclusi filtri facet. È possibile usare il parametro facet per restituire un conteggio aggregato dei documenti che corrispondono a un valore facet specificato.

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Esempio (con facet e riduzione dell'ambito): `search=*&facet=city&$top=2`

* **search=*** è una ricerca vuota. Le ricerche vuote permettono di eseguire la ricerca su tutti gli elementi. Una query vuota permette di filtrare o esplorare in base a facet il set completo di documenti, ad esempio per ottenere una struttura di esplorazione con facet composta da tutte le città incluse nell'indice.

* **facet** restituisce una struttura di esplorazione che è possibile passare a un controllo dell'interfaccia utente. Restituisce un conteggio e categorie. In questo caso le categorie sono basate sul numero di città. Ricerca di Azure non prevede alcuna aggregazione, ma è possibile ottenere qualcosa di simile all'aggregazione usando `facet`, che restituisce un conteggio dei documenti in ogni categoria.

* **$top=2** restituisce due documenti, dimostrando che è possibile usare `top` sia per ridurre che per aumentare il numero di risultati.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Esempio (con facet su valori numerici): `search=seattle&facet=beds`**

* Questa query consente l'esplorazione in base a facet e cerca "beds", ovvero "letti", in una ricerca di testo di *seattle*. Il termine *beds* può essere specificato come facet perché il campo è contrassegnato come recuperabile, filtrabile e con facet nell'indice e i valori numerici che contiene, da 1 a 5, sono adatti alla categorizzazione degli elenchi in gruppi, ad esempio elenchi con 3 o 4 camere da letto.

* Solo i campi filtrabili sono adatti all'esplorazione in base a facet. Solo i campi recuperabili possono essere restituiti nei risultati.

### <a name="highlight-query"></a> Evidenziare i risultati della ricerca

L'evidenziazione dei risultati si riferisce alla formattazione del testo corrispondente alla parola chiave, date le corrispondenze trovate in un campo specifico. Se il termine di ricerca si trova all'interno di una descrizione, è possibile aggiungere l'evidenziazione dei risultati per trovarli più facilmente.

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Esempio (con evidenziazione): `search=granite countertops&highlight=description`

* In questo esempio la frase formattata *granite countertops* è più facile da trovare nel campo della descrizione.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Esempio (con analisi linguistica): `search=mice&highlight=description`

* La ricerca full-text permette di trovare forme di termini con una semantica simile. In questo caso, i risultati della ricerca contengono testo evidenziato per "mouse" (topo), per le case con questo tipo di infestazione, in risposta a una ricerca per parole chiave di "mice" (topi). Nei risultati possono essere visualizzate diverse forme dello stesso termine a causa dell'analisi linguistica.

* Ricerca di Azure supporta 56 analizzatori, sia Microsoft che Lucene. L'impostazione predefinita di Ricerca di Azure prevede l'uso dell'analizzatore Lucene standard.

### <a name="fuzzy-search"></a> Provare la ricerca fuzzy

Per impostazione predefinita, in una ricerca tipica i termini di query con errori di ortografia, ad esempio *samamish* per l'altopiano di Sammamish nell'area di Seattle, non restituiscono corrispondenze. L'esempio seguente non restituisce alcun risultato.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Esempio (termine con errori di ortografia, non gestito): `search=samamish`

Per gestire gli errori di ortografia, è possibile usare la ricerca fuzzy. La ricerca fuzzy viene abilitata quando si usa la sintassi di query Lucene completa e ciò avviene in due modi, impostando **queryType = full** nella query e aggiungendo **~** alla stringa di ricerca.

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Esempio (termine con errori di ortografia, gestito): `search=samamish~&queryType=full`

Questo esempio restituisce ora i documenti che includono corrispondenze per "Sammamish".

Se non si specifica **queryType**, viene usato il parser di query semplice predefinito. Il parser di query semplice è più veloce, ma per la ricerca fuzzy, le espressioni regolari, la ricerca per prossimità o altri tipi di query avanzate, è necessario usare la sintassi completa.

La ricerca fuzzy e la ricerca con caratteri jolly hanno implicazioni per l'output della ricerca. L'analisi linguistica non viene eseguita su questi formati di query. Prima di usare la ricerca fuzzy e la ricerca con caratteri jolly, vedere [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md#stage-2-lexical-analysis) e cercare la sezione sulle eccezioni per l'analisi lessicale.

Per altre informazioni sugli scenari di query abilitati dal parser di query completa, vedere [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Sintassi di query Lucene in Ricerca di Azure).

### <a name="geo-search"></a> Provare la ricerca geospaziale

La ricerca geospaziale è supportata tramite il [tipo di dati edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) in un campo che contiene coordinate. La ricerca geografica è un tipo di filtro, illustrato nell'articolo relativo alla [sintassi OData per i filtri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Esempio (filtri con coordinate geografiche): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

La query di esempio filtra tutti i risultati in base a dati posizionali, restituendo i risultati a meno di 5 chilometri da un punto specificato, indicato mediante coordinate di latitudine e longitudine. Aggiungendo **$count** è possibile visualizzare il numero di risultati restituiti se si modifica la distanza o le coordinate.

La ricerca geospaziale risulta utile se l'applicazione di ricerca include una funzionalità di "ricerca nelle vicinanze" o usa l'esplorazione mappa. Non si tratta, tuttavia, di una ricerca full-text. Se i requisiti dell'utente prevedono la ricerca in una città o un paese in base al nome, oltre alle coordinate è necessario aggiungere campi contenenti nomi di città o di paesi.

## <a name="takeaways"></a>Risultati

Questa esercitazione ha presentato una breve introduzione all'uso di Ricerca di Azure dal portale di Azure.

Si è appreso come creare un indice di ricerca tramite la procedura guidata **Importa dati**. È stata acquisita familiarità con gli [indicizzatori](search-indexer-overview.md), nonché con il flusso di lavoro di base per la progettazione di indici, tra cui le [modifiche supportate a un indice pubblicato](https://docs.microsoft.com/rest/api/searchservice/update-index).

Usando **Esplora ricerche** nel portale di Azure, è stata acquisita la sintassi di query di base tramite esempi pratici che hanno mostrato le principali funzionalità, come filtri, evidenziazione dei risultati, ricerca fuzzy e ricerca geografica.

Si è inoltre appreso come usare i riquadri nel dashboard del portale per l'indice di ricerca, l'indicizzatore e le origini dati. Considerando qualsiasi nuova origine dati in futuro, è possibile usare il portale per controllarne rapidamente le definizioni o le raccolte di campi in modo semplice.

## <a name="clean-up"></a>Eseguire la pulizia

Se il servizio Ricerca di Azure è stato usato per la prima volta con questa esercitazione, eliminare il gruppo di risorse contenente il servizio Ricerca di Azure. In caso contrario, cercare il nome del gruppo di risorse corretto nell'elenco di servizi ed eliminare quello appropriato.

## <a name="next-steps"></a>Passaggi successivi

È possibile esplorare ancora Ricerca di Azure usando gli strumenti a livello di codice:

* [Creazione di un indice tramite .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Creazione di un indice tramite API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Strumenti di test Web come Postman o Fiddler per chiamare le API REST di Ricerca di Azure](search-fiddler.md)

<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png