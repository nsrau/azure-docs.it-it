---
title: Compilare il primo indice di Ricerca di Azure nel portale | Microsoft Docs
description: "Nel portale di Azure è possibile usare i dati dell&quot;esempio predefinito per generare un indice. È possibile esplorare la ricerca full-text, i filtri, i facet, la ricerca fuzzy, la ricerca geografica e altro ancora."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 02/22/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 7945ee77be8a09dcac9ddd6b338bdd542ec18540
ms.lasthandoff: 04/18/2017


---
# <a name="build-and-query-your-first-azure-search-index-in-the-portal"></a>Compilare il primo indice di Ricerca di Azure ed eseguire query su di esso nel portale

Nel portale di Azure è possibile iniziare con un set di dati di esempio predefinito per generare rapidamente un indice con la procedura guidata **Importa dati**. Con **Esplora ricerche** è possibile esplorare la ricerca full-text, i filtri, i facet, la ricerca fuzzy e la ricerca geografica.  

Questa introduzione senza codice permette di iniziare a usare i dati predefiniti per poter scrivere query interessanti fin da subito. Gli strumenti del portale non sostituiscono il codice, ma sono utili per eseguire queste attività:

+ Apprendere in modo pratico con una preparazione minima.
+ Creare il prototipo di un indice prima di scrivere codice in **Importa dati**.
+ Testare le query e la sintassi del parser in **Esplora ricerche**.
+ Visualizzare un indice esistente pubblicato nel servizio e cercarne gli attributi.

**Tempo stimato:** circa 15 minuti. Di più se è anche necessario eseguire l'iscrizione a un account o un servizio. 

In alternativa, prepararsi usando una [introduzione basata su codice alla programmazione di Ricerca di Azure in .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone la disponibilità di una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e del [servizio Ricerca di Azure](search-create-service-portal.md). 

Se non si vuole eseguire subito il provisioning di un servizio, è possibile guardare una dimostrazione della durata di sei minuti dei passaggi illustrati in questa esercitazione, all'incirca a partire dal terzo minuto di questa [panoramica video su Ricerca di Azure](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="find-your-service"></a>Trovare il servizio
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il dashboard relativo al servizio Ricerca di Azure. Se il riquadro del servizio non è stato aggiunto al dashboard, seguire questa procedura per trovare il servizio: 
   
   * Nell'indice fare clic su **Altri servizi** nella parte inferiore del riquadro di spostamento a sinistra.
   * Nella casella di ricerca digitare *cerca* per ottenere un elenco dei servizi di ricerca per la sottoscrizione. Il servizio verrà visualizzato nell'elenco. 

## <a name="check-for-space"></a>Verificare lo spazio
Molti clienti iniziano con il servizio gratuito. Questa versione è limitata a tre indici, tre origini dati e tre gli indicizzatori. Assicurarsi di avere spazio per gli elementi aggiuntivi prima di iniziare, perché ne verrà creato uno per ogni oggetto. 

> [!TIP] 
> Riquadri del dashboard del servizio mostrano il numero di indici, indicizzatori e origini dati già disponibili. Il riquadro Indicizzatore mostra gli indicatori di esito positivo e negativo. Fare clic sul riquadro per visualizzare il numero di indicizzatori. 
>
> ![Riquadri degli indicizzatori e delle origini dati][1]
>

## <a name="create-index"></a> Creare un indice e caricare i dati
Le query di ricerca scorrono un *indice* contenente dati ricercabili, metadati e costrutti usati per l'ottimizzazione di determinati comportamenti di ricerca.

Per mantenere questa attività basata sul portale, viene usato un set dati di esempio su cui è possibile eseguire una ricerca per indicizzazione con un indicizzatore tramite la procedura guidata **Importa dati**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Passaggio 1: Avviare la procedura guidata Importa dati
1. Nel dashboard del servizio Ricerca di Azure fare clic su **Importa dati** nella barra dei comandi per avviare una procedura guidata che crea e popola un indice.
   
    ![Comando Importa dati][2]

2. Nella procedura guidata, fare clic su **Origine dati** > **Esempi** > **realestate-us-sample**. Questa origine dati è preconfigurata con un nome, un tipo e informazioni di connessione. Dopo la creazione, diventa una "origine dati esistente" che può essere riutilizzata in altre operazioni di importazione.

    ![Selezionare il set di dati di esempio][9]

3. Fare clic su **OK** per usarla.

#### <a name="step-2-define-the-index"></a>Passaggio 2: Definire l'indice
La creazione di un indice è in genere un processo manuale e basato su codice, ma la procedura guidata può generare un indice per qualsiasi origine dati su cui è possibile eseguire ricerche per indicizzazione. Un indice richiede come minimo un nome e una raccolta di campi, con un campo contrassegnato come chiave del documento che lo identifica in modo univoco.

I campi hanno tipi di dati e attributi. Le caselle di controllo nella parte superiore sono *attributi dell'indice* che controllano il modo in cui viene usato il campo. 

* **Recuperabile** indica che viene visualizzato nell'elenco dei risultati della ricerca. Deselezionando questa casella di controllo è possibile contrassegnare i singoli campi perché siano esclusi dai risultati della ricerca, ad esempio quando vengono usati solo nelle espressioni di filtro. 
* **Filtrabile**, **Ordinabile** e **Con facet** determinano se un campo può essere usato in un filtro, un ordinamento o una struttura di esplorazione in base a facet. 
* **Ricercabile** indica che un campo è incluso nella ricerca full-text. Le stringhe sono ricercabili. I campi numerici e i campi booleani sono spesso contrassegnati come non ricercabili. 

Per impostazione predefinita, la procedura guidata analizza l'origine dati alla ricerca di identificatori univoci come base per il campo chiave. Le stringhe vengono attribuite come recuperabili e ricercabili. Gli Integer vengono attribuiti come recuperabili, filtrabili, ordinabili e con facet.

  ![Indice realestate generato][3]

Fare clic su **OK** per creare l'indice.

#### <a name="step-3-define-the-indexer"></a>Passaggio 3: Definire l'indicizzatore
Sempre nella procedura guidata **Importa dati** fare clic su **Indicizzatore** > **Nome** e quindi digitare un nome per l'indicizzatore. 

Questo oggetto definisce un processo eseguibile. È possibile inserirlo nella pianificazione ricorrente. Per il momento, tuttavia, usare l'opzione predefinita per eseguire l'indicizzatore una volta, immediatamente, facendo clic su **OK**.  

  ![Indicizzatore realestate][8]

## <a name="check-progress"></a>Verificare l'avanzamento
Per monitorare l'importazione dei dati, tornare al dashboard del servizio, scorrere verso il basso e fare doppio clic sul riquadro **Indicizzatori** per aprire l'elenco corrispondente. L'indicizzatore appena creato verrà visualizzato nell'elenco con lo stato "in corso" oppure "operazione riuscita", insieme al numero di documenti indicizzati.

   ![Messaggio di stato dell'indicizzatore][4]

## <a name="query-index"></a> Eseguire query sull'indice
Ora è disponibile un indice di ricerca su cui è possibile eseguire query. **Esplora ricerche** è uno strumento di query integrato nel portale. Include una casella di ricerca che permette di verificare se i risultati della ricerca sono quelli previsti. 

> [!TIP]
> La [panoramica video su Ricerca di Azure](https://channel9.msdn.com/Events/Connect/2016/138) illustra la procedura seguente al minuto 6.08 del video.
>

1. Fare clic su **Esplora ricerche** nella barra dei comandi.

   ![Comando di Esplora ricerche][5]

2. Fare clic su **Cambia indice** nella barra dei comandi per passare a *realestate-us-sample*.

   ![Comandi dell'indice e dell'API][6]

3. Fare clic su **Imposta versione API** nella barra dei comandi per verificare quali API REST sono disponibili. Le API di anteprima permettono di accedere a nuove funzionalità non ancora rilasciate a livello generale. Per le query seguenti, usare la versione disponibile a livello generale (2016-09-01), se non diversamente specificato. 

    > [!NOTE]
    > L'[API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/search-documents) e la [libreria .NET](search-howto-dotnet-sdk.md#core-scenarios) sono totalmente equivalenti, ma **Esplora ricerche** può gestire unicamente le chiamate REST. Accetta sia la [sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) che la [sintassi completa del parser di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), oltre a tutti i parametri di ricerca disponibili nelle operazioni di [ricerca nei documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. Nella barra di ricerca immettere le stringhe di query riportate di seguito e fare clic su **Ricerca**.

  ![Esempio di query di ricerca][7]

**`search=seattle`**

+ Il parametro `search` permette di inserire una ricerca per parole chiave per la ricerca full-text. In questo caso la ricerca restituisce elenchi della contea di King, nello stato di Washington, contenenti il termine *Seattle* in qualsiasi campo ricercabile del documento. 

+ **Esplora ricerche** restituisce i risultati in JSON, un formato dettagliato e difficile da leggere se i documenti hanno una struttura densa. A seconda dei documenti, potrebbe essere necessario scrivere codice per la gestione dei risultati della ricerca, allo scopo di estrarre gli elementi importanti. 

+ I documenti sono costituiti da tutti i campi contrassegnati come recuperabili nell'indice. Per visualizzare gli attributi dell'indice nel portale, fare clic su *realestate-us-sample* nel riquadro **Indici**.

**`search=seattle&$count=true&$top=100`**

+ Il simbolo `&` permette di aggiungere parametri di ricerca, che possono essere specificati in qualsiasi ordine. 

+  Il parametro `$count=true` restituisce un conteggio relativo alla somma di tutti i documenti restituiti. È possibile verificare le query filtro monitorando le modifiche segnalate da `$count=true`. 

+ `$top=100` restituisce i 100 documenti con classificazione più alta nel totale. Per impostazione predefinita, Ricerca di Azure restituisce le 50 migliori corrispondenze. Per aumentare o diminuire la quantità è possibile usare `$top`.

**`search=*&facet=city&$top=2`**

+ `search=*` è una ricerca vuota. Le ricerche vuote permettono di eseguire la ricerca su tutti gli elementi. Una query vuota permette di filtrare o esplorare in base a facet il set completo di documenti, ad esempio per ottenere una struttura di esplorazione con facet composta da tutte le città incluse nell'indice.

+  `facet` restituisce una struttura di esplorazione che è possibile passare a un controllo dell'interfaccia utente. Restituisce un conteggio e categorie. In questo caso le categorie sono basate sul numero di città. Ricerca di Azure non prevede alcuna aggregazione, ma è possibile ottenere qualcosa di simile all'aggregazione usando `facet`, che restituisce un conteggio dei documenti in ogni categoria.

+ `$top=2` restituisce due documenti, dimostrando che è possibile usare `top` sia per ridurre che per aumentare il numero di risultati.

**`search=seattle&facet=beds`**

+ Questa query consente l'esplorazione in base a facet e cerca "beds", ovvero "letti", in una ricerca di testo di *seattle*. È possibile specificare `"beds"` come facet perché il campo è contrassegnato come recuperabile, filtrabile e con facet nell'indice e i valori numerici che contiene, da 1 a 5, sono adatti alla categorizzazione degli elenchi in gruppi, ad esempio elenchi con 3 o 4 camere da letto. 

+ Solo i campi filtrabili sono adatti all'esplorazione in base a facet. Solo i campi recuperabili possono essere restituiti nei risultati.

**`search=seattle&$filter=beds gt 3`**

+ Il parametro `filter` restituisce risultati corrispondenti ai criteri immessi. In questo caso, un numero di camere da letto maggiore di 3. 

+ La sintassi del filtro è una costruzione OData. Per altre informazioni, vedere l'articolo relativo alla [sintassi OData per i filtri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

**`search=granite countertops&highlight=description`**

+ L'evidenziazione dei risultati si riferisce alla formattazione del testo corrispondente alla parola chiave, date le corrispondenze trovate in un campo specifico. Se il termine di ricerca si trova all'interno di una descrizione, è possibile aggiungere l'evidenziazione dei risultati per trovarli più facilmente. In questo caso, la frase formattata `"granite countertops"` è più facile da visualizzare nel campo della descrizione.

**`search=mice&highlight=description`**

+ La ricerca full-text permette di trovare forme di termini con una semantica simile. In questo caso, i risultati della ricerca contengono testo evidenziato per "mouse" (topo), per le case con questo tipo di infestazione, in risposta a una ricerca per parole chiave di "mice" (topi). Nei risultati possono essere visualizzate diverse forme dello stesso termine a causa dell'analisi linguistica. 

+ Ricerca di Azure supporta 56 analizzatori, sia Microsoft che Lucene. L'impostazione predefinita di Ricerca di Azure prevede l'uso dell'analizzatore Lucene standard. 

**`search=samamish`**

+ In una ricerca tipica i termini con errori di ortografia, ad esempio "samamish" per l'altopiano di Sammamish nell'area di Seattle, non restituiscono corrispondenze. Per gestire gli errori di ortografia, è possibile usare ricerca fuzzy, descritta nell'esempio seguente.

**`search=samamish~&queryType=full`**

+ La ricerca fuzzy viene abilitata quando si specifica il simbolo `~` e si usa il parser di query completa, che interpreta e analizza correttamente la sintassi `~`. 

+ Per usare la ricerca fuzzy è necessario abilitare in modo esplicito il parser di query completa impostando `queryType=full`. Per altre informazioni sugli scenari di query abilitati dal parser di query completa, vedere [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Sintassi di query Lucene in Ricerca di Azure).

+ Se non si specifica `queryType`, viene usato il parser di query semplice predefinito. Il parser di query semplice è più veloce, ma per la ricerca fuzzy, le espressioni regolari, la ricerca per prossimità o altri tipi di query avanzate, è necessario usare la sintassi completa. 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ La ricerca geospaziale è supportata tramite il [tipo di dati edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) in un campo che contiene coordinate. La ricerca geografica è un tipo di filtro, illustrato nell'articolo relativo alla [sintassi OData per i filtri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

+ La query di esempio filtra tutti i risultati in base a dati posizionali, restituendo i risultati a meno di 5 chilometri da un punto specificato, indicato mediante coordinate di latitudine e longitudine. Aggiungendo `$count` è possibile visualizzare il numero di risultati restituiti se si modifica la distanza o le coordinate. 

+ La ricerca geospaziale risulta utile se l'applicazione di ricerca include una funzionalità di "ricerca nelle vicinanze" o usa l'esplorazione mappa. Non si tratta, tuttavia, di una ricerca full-text. Se i requisiti dell'utente prevedono la ricerca in una città o un paese in base al nome, oltre alle coordinate è necessario aggiungere campi contenenti nomi di città o di paesi.

## <a name="next-steps"></a>Passaggi successivi

+ Modificare uno degli oggetti appena creati. Dopo aver eseguito una volta la procedura guidata, è possibile tornare indietro e visualizzare o modificare i singoli componenti, ovvero indice, indicizzatore oppure origine dati. Alcune modifiche, ad esempio il cambiamento del tipo di dati del campo, non sono consentite nell'indice, ma la maggior parte delle proprietà e delle impostazioni sono modificabili.

  Per visualizzare i singoli componenti, fare clic sul riquadro **Indice**, **Indicizzatore** oppure **Origini dati** nel dashboard per visualizzare un elenco di oggetti esistenti. Per altre informazioni sulle modifiche all'indice che non richiedono la ricompilazione, vedere [Update Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index) (API REST di Ricerca di Azure: aggiornare l'indice).

+ Provare a usare strumenti e procedure con altre origini dati. Il set di dati di esempio, `realestate-us-sample`, proviene da un database SQL di Azure in cui Ricerca di Azure può eseguire ricerche per indicizzazione. Oltre che nel database SQL di Azure, Ricerca di Azure permette di eseguire ricerche per indicizzazione e dedurre un indice da strutture di dati flat nell'archivio tabelle di Azure, nell'archivio BLOB, in SQL Server in una macchina virtuale di Azure e in DocumentDB. Tutte queste origini dati sono supportate nella procedura guidata. Nel codice è possibile popolare facilmente un indice tramite un *indicizzatore*.

+ Tutte le altre origini dati senza indicizzatore sono supportate tramite un modello push, in cui il codice effettua il push di set di righe nuovi e modificati in JSON nell'indice. Per altre informazioni, vedere [Add, update, or delete documents in Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungere, aggiornare o eliminare documenti da Ricerca di Azure).

Per informazioni sulle altre funzionalità descritte in questo articolo, visitare i collegamenti seguenti:

* [Panoramica degli indicizzatori](search-indexer-overview.md)
* [Creare l'indice, include una spiegazione dettagliata degli attributi dell'indice.](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Esplora ricerche](search-explorer.md)
* [Eseguire ricerche nei documenti, include esempi di sintassi di query.](https://docs.microsoft.com/rest/api/searchservice/search-documents)


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
