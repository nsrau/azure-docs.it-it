---
title: Layout-riconoscimento form
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'analisi del layout con il modulo API Recognizer-uso e limiti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 60ef490bbba4dca4f01b82967ad6ea8636e7bd4e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353698"
---
# <a name="form-recognizer-layout-service"></a>Servizio di layout riconoscimento moduli

Il riconoscitore di form di Azure può estrarre testo, tabelle, contrassegni di selezione e informazioni sulla struttura dai documenti usando il servizio di layout. L'API di layout consente ai clienti di prendere documenti in un'ampia gamma di formati e restituire dati strutturati e rappresentazione del documento. Combina le potenti funzionalità di [riconoscimento ottico dei caratteri (OCR)](../computer-vision/concept-recognizing-text.md) con i documenti relativi ai modelli di apprendimento avanzato per estrarre testo, tabelle, contrassegni di selezione e struttura di documenti. 

## <a name="what-does-the-layout-service-do"></a>Cosa funziona il servizio di layout?

L'API di layout estrae testo, tabelle, contrassegni di selezione e informazioni di struttura da documenti con precisione eccezionale e li restituisce in una risposta JSON strutturata organizzata. I documenti possono provenire da diversi formati e qualità, tra cui immagini acquisite tramite telefono, documenti digitalizzati e PDF digitali. L'API di layout estrae l'output strutturato da tutti i documenti.

![Esempio di layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Provare questa operazione

Per provare il servizio di layout riconoscimento moduli, passare allo strumento dell'interfaccia utente di esempio online:

> [!div class="nextstepaction"]
> [Interfaccia utente di esempio](https://fott-preview.azurewebsites.net/)

È necessaria una sottoscrizione di Azure ([crearne una gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e un endpoint e una chiave del sistema di [riconoscimento del modulo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) per provare l'API del layout di riconoscimento del modulo. 

![Schermata dell'interfaccia utente di esempio; il testo, le tabelle e i contrassegni di selezione di un documento vengono analizzati](./media/analyze-layout.png)

### <a name="input-requirements"></a>Requisiti di input 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Operazione di analisi del layout

L'operazione [Analyze layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) accetta un documento (file di immagine, TIFF o PDF) come input ed estrae il testo, le tabelle, i contrassegni di selezione e la struttura del documento. La chiamata restituisce un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID risultato da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>Operazione Get Analyze result layout

Il secondo passaggio consiste nel chiamare l'operazione [Get Analyze result layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) . Questa operazione accetta come input l'ID del risultato creato dall'operazione di analisi del layout. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. 

|Campo| Tipo | Valori possibili |
|:-----|:----:|:----|
|status | string | `notStarted`: L'operazione di analisi non è stata avviata.<br /><br />`running`: È in corso l'operazione di analisi.<br /><br />`failed`: L'operazione di analisi non è riuscita.<br /><br />`succeeded`: L'operazione di analisi ha avuto esito positivo.|

Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il `succeeded` valore. Utilizzare un intervallo da 3 a 5 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

Quando il campo **stato** presenta il `succeeded` valore, la risposta JSON includerà i risultati dell'estrazione del layout, il testo, le tabelle e i contrassegni di selezione estratti. I dati estratti contengono le righe e le parole estratte del testo, il rettangolo di selezione, l'aspetto del testo scritto a mano, le tabelle e i contrassegni di selezione con un'indicazione di selezionato/deselezionato. 

### <a name="sample-json-output"></a>Output JSON di esempio

La risposta all'operazione Get Analyze layout result sarà la rappresentazione strutturata del documento con tutte le informazioni estratte. Vedere qui per un [file di documento di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) e il relativo [output di layout di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)di output strutturato.

L'output JSON è costituito da due parti: 
* `"readResults"` il nodo contiene tutti i contrassegni di testo e di selezione riconosciuti. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. 
* `"pageResults"` il nodo contiene le tabelle e le celle estratte con i rispettivi rettangoli di delimitazione, la confidenza e un riferimento alle righe e alle parole in "readResults".

## <a name="example-output"></a>Output di esempio

### <a name="text"></a>Testo

Il layout estrae testo da documenti (PDF, TIFF) e immagini (jpg, PNG, BMP) con diversi angoli di testo, colori, angoli, foto di documenti, fax, stampati, manoscritti (solo in inglese) e modalità mista. Il testo viene estratto con le informazioni su righe, parole, caselle di delimitazione, punteggi di confidenza e stile (scritti a mano). Tutte le informazioni sul testo sono incluse nella `"readResults"` sezione dell'output JSON. 

### <a name="tables"></a>Tabelle

Il layout estrae le tabelle da documenti (PDF, TIFF) e immagini (jpg, PNG, BMP). È possibile analizzare, fotografare o digitalizzare documenti. Le tabelle possono essere tabelle complesse con celle o colonne unite, con o senza bordi e con angoli dispari. Le tabelle estratte includono il numero di colonne e righe, l'intervallo di righe e l'intervallo di colonne. Ogni cella viene estratta con il relativo rettangolo di delimitazione e fa riferimento al testo estratto nella `"readResults"` sezione. Le informazioni sulla tabella si trovano nella `"pageResults"` sezione dell'output JSON. 

![Esempio di tabelle](./media/tables-example.jpg)

### <a name="selection-marks"></a>Contrassegni di selezione

Il layout estrae anche i contrassegni di selezione dai documenti. I contrassegni di selezione estratti includono il rettangolo di selezione, la confidenza e lo stato (selezionato/deselezionato). Le informazioni sul contrassegno di selezione vengono estratte nella `"readResults"` sezione dell'output JSON. 

## <a name="next-steps"></a>Passaggi successivi

- Provare l'estrazione del layout usando l' [interfaccia utente di esempio di riconoscimento form](https://fott-preview.azurewebsites.net/)
- Completare una [Guida introduttiva alla libreria client di riconoscimento moduli](quickstarts/client-library.md) per iniziare a estrarre i layout nel linguaggio preferito.
- In alternativa, seguire la Guida introduttiva [estrarre i dati di layout](./QuickStarts/python-layout.md) per implementare l'estrazione dei dati di layout con Python e l'API REST.

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)
* [Documentazione di riferimento per l'API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)