---
title: 'Esercitazione: Integrare Power BI con il servizio cognitivo Analisi del testo'
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare Analisi del testo per estrarre frasi chiave dal testo archiviato in Power BI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 705e637235eb81be29a2ea0d7d68ccd000ea0470
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626025"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Esercitazione: Integrare Power BI con il servizio cognitivo Analisi del testo

Microsoft Power BI Desktop è un'applicazione gratuita che consente di connettersi ai dati e di trasformarli e visualizzarli. Il servizio Analisi del testo, incluso nei Servizi cognitivi di Microsoft Azure, è un servizio di elaborazione del linguaggio naturale. In presenza di testo non elaborato e non strutturato, è in grado di estrarre le frasi più importanti, analizzare il sentiment e identificare le entità note, come ad esempio i marchi. Se combinati, questi strumenti permettono di visualizzare rapidamente ciò di cui parlano i clienti e che cosa ne pensano.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Power BI Desktop per importare e trasformare i dati
> * Creare una funzione personalizzata in Power BI Desktop
> * Integrare Power BI Desktop con l'API Frasi chiave di Analisi del testo
> * Usare l'API Frasi chiave di Analisi del testo per estrarre le frasi più importanti dal feedback dei clienti
> * Creare una nuvola di parole dal feedback dei clienti

## <a name="prerequisites"></a>Prerequisiti
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Download gratuito](https://powerbi.microsoft.com/get-started/).
- Un account Microsoft Azure. [Avviare una versione di valutazione gratuita](https://azure.microsoft.com/free/) o [eseguire l'accesso](https://portal.azure.com/).
- Un account API Servizi cognitivi con l'API Analisi del testo. Se non se ne ha uno, è possibile [iscriversi](../../cognitive-services-apis-create-account.md) e usare il livello gratuito per 5.000 transazioni al mese (vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)) per completare questa esercitazione.
- La [chiave di accesso di Analisi del testo](../how-tos/text-analytics-how-to-access-key.md) generata durante l'iscrizione.
- Commenti dei clienti. È possibile usare i [nostri dati di esempio](https://aka.ms/cogsvc/ta) o i propri dati. Questa esercitazione presuppone che si usino i nostri dati di esempio.

## <a name="load-customer-data"></a>Caricare i dati dei clienti
<a name="LoadingData"></a>

Per iniziare, aprire Power BI Desktop e caricare il file con valori delimitati da virgole (CSV) `FabrikamComments.csv` scaricato in [Prerequisiti](#Prerequisites). Questo file rappresenta un giorno di attività ipotetica nel forum di supporto di una piccola azienda fittizia.

> [!NOTE]
> Power BI può usare i dati da un'ampia gamma di origini, ad esempio Facebook o un database SQL. Per altre informazioni, vedere [Integrazione di Facebook con Power BI](https://powerbi.microsoft.com/integrations/facebook/) e [Integrazione di SQL Server con Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

Nella finestra principale di Power BI Desktop selezionare **Home** sulla barra multifunzione. Nel gruppo **Dati esterni** sulla barra multifunzione aprire il menu a discesa **Dati** e selezionare **Testo/CSV**.

![[Pulsante Dati]](../media/tutorials/power-bi/get-data-button.png)

Verrà visualizzata la finestra di dialogo Apri. Passare alla cartella Download o alla cartella in cui è stato scaricato il file `FabrikamComments.csv`. Fare clic su `FabrikamComments.csv` e quindi sul pulsante **Apri**. Verrà visualizzata la finestra di dialogo di importazione del file CSV.

![[Finestra di dialogo di importazione del file CSV]](../media/tutorials/power-bi/csv-import.png)

La finestra di dialogo di importazione del file CSV consente di verificare che Power BI Desktop abbia rilevato correttamente il set di caratteri, il delimitatore, le intestazioni di riga e i tipi di colonne. Le informazioni sono tutte corrette, quindi fare clic su **Carica**.

Per visualizzare i dati caricati, fare clic sul pulsante **Vista dati** sul bordo sinistro dell'area di lavoro di Power BI. Verrà visualizzata una tabella contenente i dati, come in Microsoft Excel.

![[Visualizzazione iniziale dei dati importati]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Preparare i dati
<a name="PreparingData"></a>

Potrebbe essere necessario trasformare i dati in Power BI Desktop prima che siano pronti per essere elaborati dall'API Frasi chiave del servizio Analisi del testo.

I dati di esempio contengono una colonna `subject` e una colonna `comment`. Usando la funzione Merge di colonne di Power BI Desktop è possibile estrarre le frasi chiave dai dati in entrambe queste colonne, invece che solo dalla colonna `comment`.

In Power BI Desktop selezionare **Home** sulla barra multifunzione. Nel gruppo **Dati esterni** fare clic su **Modifica query**.

![[Gruppo Dati esterni nella scheda Home della barra multifunzione]](../media/tutorials/power-bi/edit-queries.png)

Selezionare `FabrikamComments` nell'elenco **Query** sul lato sinistro della finestra, se non è già selezionato.

Ora selezionare entrambe le colonne `subject` e `comment` nella tabella. Potrebbe essere necessario scorrere orizzontalmente per visualizzare le colonne. Fare prima clic sull'intestazione di colonna `subject`, quindi tenere premuto il tasto CTRL e fare clic sull'intestazione di colonna `comment`.

![[Selezione dei campi da unire]](../media/tutorials/power-bi/select-columns.png)

Selezionare **Trasforma** sulla barra multifunzione. Nel gruppo **Colonne di testo** sulla barra multifunzione fare clic su **Merge di colonne**. Verrà visualizzata la finestra di dialogo Merge di colonne.

![[Merge di campi con la finestra di dialogo Merge di colonne]](../media/tutorials/power-bi/merge-columns.png)

Nella finestra di dialogo Merge di colonne scegliere `Tab` come separatore e quindi fare clic su **OK**.

È anche possibile escludere i messaggi vuoti usando il filtro Rimuovi vuoti oppure rimuovere i caratteri non stampabili tramite la trasformazione Pulisci. Se i dati contengono una colonna come la colonna `spamscore` del file di esempio, è possibile ignorare i commenti "indesiderati" usando un filtro Numero.

## <a name="understand-the-api"></a>Informazioni sull'API
<a name="UnderstandingAPI"></a>

L'[API Frasi chiave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) del servizio Analisi del testo può elaborare fino a un migliaio di documenti di testo per richiesta HTTP. Power BI preferisce gestire i record uno alla volta, quindi in questa esercitazione le chiamate all'API conterranno ognuna un solo documento. L'API Frasi chiave richiede i campi seguenti per l'elaborazione di ogni documento.

| | |
| - | - |
| `id`  | Identificatore univoco per il documento nella richiesta. Anche la risposta contiene questo campo. In questo modo, se si elaborano più documenti, è possibile associare facilmente le frasi chiave estratte al documento da cui provengono. Dato che in questa esercitazione si sta elaborando un solo documento per richiesta, è possibile impostare come hardcoded il valore di `id` in modo che sia lo stesso per ogni richiesta.|
| `text`  | Testo da elaborare. Il valore di questo campo proviene dalla colonna `Merged` creata nella [sezione precedente](#PreparingData), che contiene la riga dell'oggetto combinata e il testo del commento. L'API Frasi chiave richiede che la lunghezza dei dati non superi 5.120 caratteri.|
| `language` | Codice del linguaggio naturale in cui è scritto il documento. Tutti i messaggi nei dati di esempio sono in inglese, quindi è possibile impostare come hardcoded il valore `en` per questo campo.|

## <a name="create-a-custom-function"></a>Creare una funzione personalizzata
<a name="CreateCustomFunction"></a>

A questo punto è possibile creare la funzione personalizzata che integrerà Power BI e Analisi del testo. La funzione riceve il testo da elaborare come parametro. Converte i dati nel e dal formato JSON necessario e invia la richiesta HTTP all'API Frasi chiave. La funzione analizza quindi la risposta dell'API e restituisce una stringa che contiene un elenco delimitato da virgole delle frasi chiave estratte.

> [!NOTE]
> Le funzioni personalizzate di Power BI Desktop sono scritte nella [lingua della formula di Power Query M](https://docs.microsoft.com/powerquery-m/power-query-m-reference), in breve semplicemente "M". M è un linguaggio di programmazione funzionale basato su [F#](https://docs.microsoft.com/dotnet/fsharp/). Non è necessario essere un programmatore per completare questa esercitazione. Tutto il codice necessario è riportato sotto.

In Power BI Desktop verificare che sia ancora aperta la finestra 	Editor di query. Se non lo è, selezionare la scheda **Home** sulla barra multifunzione, quindi nel gruppo **Dati esterni** fare clic su **Modifica query**.

Sempre nella scheda **Home** sulla barra multifunzione, nel gruppo **Nuova query** aprire il menu a discesa **Nuova origine** e selezionare **Query vuota**. 

Verrà visualizzata una nuova query, inizialmente denominata `Query1`, nell'elenco Query. Fare doppio clic su questa voce e assegnarle il nome `KeyPhrases`.

Ancora nella scheda **Home** sulla barra multifunzione, nel gruppo **Query** fare clic su **Editor avanzato** per aprire la finestra Editor avanzato. Eliminare il codice già presente nella finestra e incollare il codice seguente. 

> [!NOTE]
> Gli esempi seguenti presuppongono che l'endpoint dell'API Analisi del testo inizi con `https://westus.api.cognitive.microsoft.com`. Analisi del testo consente di creare una sottoscrizione in 13 aree diverse. Se l'iscrizione al servizio è stata effettuata in un'altra area, assicurarsi di usare l'endpoint corretto per l'area selezionata. Per trovare questo endpoint, accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/), selezionare la propria sottoscrizione di Analisi del testo e quindi selezionare la pagina Panoramica.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Sostituire `YOUR_API_KEY_HERE` con la chiave di accesso di Analisi del testo. Anche per trovare questa chiave è possibile accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/), selezionare la propria sottoscrizione di Analisi del testo e quindi selezionare la pagina Panoramica. Assicurarsi di lasciare le virgolette prima e dopo la chiave. Fare quindi clic su **Fine.**

## <a name="use-the-custom-function"></a>Usare la funzione personalizzata
<a name="UseCustomFunction"></a>

Ora è possibile usare la funzione personalizzata per estrarre le frasi chiave da ogni commento dei clienti e archiviarle in una nuova colonna nella tabella. 

Nella finestra Editor di query di Power BI Desktop tornare alla query `FabrikamComments`. Selezionare **Aggiungi colonna** sulla barra multifunzione. Nel gruppo **Generale** fare clic su **Richiama funzione personalizzata**.

![[Pulsante Richiama funzione personalizzata]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Viene visualizzata la finestra di dialogo Richiama funzione personalizzata. In **Nome nuova colonna** immettere `keyphrases`. In **Query della funzione** selezionare la funzione personalizzata creata, `KeyPhrases`.

Nella finestra di dialogo viene visualizzato un nuovo campo, **testo (facoltativo)** . In questo campo occorre specificare la colonna che si vuole usare per fornire i valori per il parametro `text` dell'API Frasi chiave. Tenere presente che i valori per i parametri `language` e `id` sono già stati impostati come hardcoded. Selezionare `Merged` (la colonna creata [in precedenza](#PreparingData) tramite il merge dei campi dell'oggetto e del messaggio) dal menu a discesa.

![[Richiamo di una funzione personalizzata]](../media/tutorials/power-bi/invoke-custom-function.png)

Infine fare clic su **OK.**

Se è tutto corretto, Power BI chiama la funzione personalizzata una volta per ogni riga della tabella. Invia le query all'API Frasi chiave e aggiunge alla tabella una nuova colonna in cui archiviare i risultati. Prima di questa fase, potrebbe essere necessario specificare le impostazioni di autenticazione e privacy.

## <a name="authentication-and-privacy"></a>Autenticazione e privacy
<a name="Authentication"></a>

Dopo aver chiuso la finestra di dialogo Richiama funzione personalizzata, potrebbe essere visualizzato un banner in cui viene chiesto di specificare come connettersi all'API Frasi chiave.

![[banner credenziali]](../media/tutorials/power-bi/credentials-banner.png)

Fare clic su **Modifica credenziali**, assicurarsi che l'opzione `Anonymous` sia selezionata nella finestra di dialogo e quindi fare clic su **Connetti**. 

> [!NOTE]
> Occorre selezionare `Anonymous` in quanto il servizio Analisi del testo autentica l'utente tramite la sua chiave di accesso, in modo che Power BI non debba specificare le credenziali per la richiesta HTTP stessa.

![[impostazione dell'autenticazione su anonima]](../media/tutorials/power-bi/access-web-content.png)

Se viene visualizzato il banner Modifica credenziali anche dopo aver scelto l'accesso anonimo, è possibile che si sia dimenticato di incollare la chiave di accesso di Analisi del testo nel codice nella [funzione personalizzata](#CreateCustomFunction) `KeyPhrases`.

Successivamente, potrebbe essere visualizzato un banner che chiede di fornire informazioni sul livello di privacy delle origini dati. 

![[banner privacy]](../media/tutorials/power-bi/privacy-banner.png)

Fare clic su **Continua** e scegliere `Public` per ogni origine dati visualizzata nella finestra di dialogo. Fare quindi clic su **Salva.**

![[impostazione della privacy dell'origine dati]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Creare la nuvola di parole
<a name="WordCloud"></a>

Dopo aver gestito gli eventuali banner visualizzati, fare clic su **Chiudi e applica** nella scheda Home della barra multifunzione per chiudere l'Editor di query.

Power BI Desktop impiega alcuni istanti per effettuare le richieste HTTP necessarie. Per ogni riga nella tabella, la nuova colonna `keyphrases` contiene le frasi chiave rilevate nel testo dall'API Frasi chiave. 

Questa colonna verrà ora usata per generare una nuvola di parole. Per iniziare, fare clic sul pulsante **Report** nella finestra principale di Power BI Desktop nella parte sinistra dell'area di lavoro.

> [!NOTE]
> Perché usare le frasi chiave estratte per generare una nuvola di parole anziché usare il testo completo di ogni commento? Le frasi chiave forniscono le parole *importanti* dei commenti del cliente, non solo le *parole più comuni*. Inoltre, le dimensioni delle parole nella nuvola risultante non sono distorte dall'uso frequente di una parola in un numero relativamente piccolo di commenti.

Se l'oggetto visivo personalizzato Word Cloud non è già installato, installarlo. Nel pannello Visualizzazioni nella parte destra dell'area di lavoro, fare clic sui puntini di sospensione ( **...** ) e scegliere **Importa dall'archivio**. Cercare "cloud" e fare clic sul pulsante **Aggiungi** accanto all'oggetto visivo Word Cloud. Power BI installa l'oggetto visivo Word Cloud e notifica l'avvenuta installazione.

![[aggiunta di un oggetto visivo personalizzato]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Fare clic sull'icona Word Cloud nel pannello Visualizzazioni.

![[icona di Word Cloud nel pannello Visualizzazioni]](../media/tutorials/power-bi/visualizations-panel.png)

Verrà visualizzato un nuovo report nell'area di lavoro. Trascinare il campo `keyphrases` dal pannello Campi nel campo Categoria nel pannello Visualizzazioni. La nuvola di parole verrà visualizzata all'interno del report.

Passare quindi alla pagina Formato del pannello Visualizzazioni. Nella categoria Parole non significative, attivare **Parole non significative predefinite** per eliminare le parole brevi e comuni, ad esempio articoli e preposizioni, dalla nuvola. 

![[attivazione delle parole non significative predefinite]](../media/tutorials/power-bi/default-stop-words.png)

Poco più in basso in questo pannello, disattivare **Ruota il testo** e **Titolo**.

![[attivare la modalità messa a fuoco]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Fare clic sullo strumento Modalità messa a fuoco nel report per osservare più attentamente la nuvola di parole. Lo strumento espande la nuvola adattandola all'intera area di lavoro, come mostrato sotto.

![[nuvola di parole]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Altri servizi di Analisi del testo
<a name="MoreServices"></a>

Il servizio Analisi del testo, uno dei servizi cognitivi offerti da Microsoft Azure, fornisce inoltre l'analisi del sentiment e il rilevamento della lingua. Il rilevamento della lingua è particolarmente utile se i commenti dei clienti non sono tutti in lingua inglese.

Entrambe queste altre API sono simili all'API Frasi chiave. Questo significa che è possibile integrarle con Power BI Desktop usando funzioni personalizzate quasi identiche a quella creata in questa esercitazione. È sufficiente creare una query vuota e incollare il codice seguente appropriato nell'Editor avanzato, esattamente come in uno dei passaggi precedenti. (Non dimenticare la chiave di accesso) Quindi, come nella procedura illustrata in questa esercitazione, usare la funzione per aggiungere una nuova colonna alla tabella.

La funzione Analisi del sentiment seguente restituisce un punteggio che indica il livello di positività del sentiment espresso nel testo.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Di seguito sono presentate due versioni di una funzione di Rilevamento lingua. La prima restituisce il codice lingua ISO, ad esempio `en` per la lingua inglese, mentre la seconda restituisce il nome "descrittivo", ad esempio `English`. È possibile notare che nelle due versioni varia solo l'ultima riga del corpo.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Infine, ecco una variante della funzione Frasi chiave già presentata che restituisce le frasi come un oggetto elenco, anziché come una singola stringa di frasi delimitate da virgole. 

> [!NOTE]
> La restituzione di una singola stringa risultava più facile ai fini dell'esempio della nuvola di parole. Un elenco, tuttavia, è un formato più flessibile da usare con le frasi restituite in Power BI. È possibile modificare gli oggetti elenco in Power BI Desktop tramite il gruppo Colonna strutturata nella barra multifunzione Trasforma dell'Editor di query.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Passaggi successivi
<a name="NextSteps"></a>

Altre informazioni sul servizio Analisi del testo,sulla lingua della formula M di Power Query o su Power BI.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API Analisi del testo](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulla lingua della formula M di Power Query](https://docs.microsoft.com/powerquery-m/power-query-m-reference)

> [!div class="nextstepaction"]
> [Documentazione di Power BI](https://powerbi.microsoft.com/documentation/powerbi-landing-page/)
