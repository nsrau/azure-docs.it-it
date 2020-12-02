---
title: Fatture-riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'analisi delle fatture con il modulo API Recognizer-uso e limiti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: eb1be4c78be0eb7fb943700b168fa82ede4d3861
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458392"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Modello di fattura precompilato del modulo di riconoscimento

Il sistema di riconoscimento dei moduli di Azure può analizzare ed estrarre le informazioni dalle fatture di vendita usando i modelli di fattura predefiniti. L'API per la fatturazione consente ai clienti di ottenere fatture in diversi formati e restituire dati strutturati per automatizzare l'elaborazione della fattura. Combina le potenti funzionalità di [riconoscimento ottico dei caratteri (OCR)](../computer-vision/concept-recognizing-text.md) con la comprensione dei modelli di apprendimento avanzato per estrarre le informazioni chiave dalle fatture in inglese. Estrae il testo, le tabelle e le informazioni, ad esempio cliente, fornitore, ID fattura, data di scadenza della fattura, totale, importo della fattura, importo dell'imposta, spedizione, fatturazione e altro ancora. L'API per la fatturazione predefinita è disponibile pubblicamente nel form Recognizer v 2.1 Preview.

## <a name="what-does-the-invoice-service-do"></a>Che cosa fa il servizio di fatturazione?

L'API per la fatturazione estrae i campi chiave dalle fatture e li restituisce in una risposta JSON strutturata organizzata. Le fatture possono provenire da diversi formati e qualità, tra cui immagini acquisite tramite telefono, documenti digitalizzati e PDF digitali. L'API fattura estrae l'output strutturato da tutte le fatture. 

![Esempio di fattura contoso](./media/invoice-example.jpg)

## <a name="try-it-out"></a>Provare questa operazione

Per provare il modulo del servizio di fatturazione di riconoscimento, passare allo strumento dell'interfaccia utente di esempio online:

> [!div class="nextstepaction"]
> [Provare i modelli predefiniti](https://fott-preview.azurewebsites.net/)

È necessaria una sottoscrizione di Azure ([crearne una gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e un modulo e una chiave dell'endpoint della [risorsa riconoscimento](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) per provare il servizio di fatturazione del modulo di riconoscimento. 

![Esempio di fattura analizzata](./media/analyze-invoice.png)


### <a name="input-requirements"></a>Requisiti di input 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>Operazione di analisi della fattura

L'operazione [Analyze fattura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291) accetta un'immagine o un PDF di una fattura come input ed estrae i valori di interesse. La chiamata restituisce un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID risultato da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Operazione Get Analyze result result

Il secondo passaggio consiste nel chiamare l'operazione [Get Analyze result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83) . Questa operazione accetta come input l'ID del risultato creato dall'operazione di analisi della fattura. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il valore **succeeded** . Utilizzare un intervallo da 3 a 5 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

|Campo| Tipo | Valori possibili |
|:-----|:----:|:----|
|status | string | notStarted: l'operazione di analisi non è stata avviata.<br /><br />Running: l'operazione di analisi è in corso.<br /><br />non riuscito: l'operazione di analisi non è riuscita.<br /><br />Succeeded: l'operazione di analisi ha avuto esito positivo.|

Quando il campo **stato** ha il valore **succeeded** , la risposta JSON includerà i risultati di Invoice Understanding, le tabelle estratti e i risultati facoltativi del riconoscimento del testo, se richiesti. Il risultato della comprensione della fattura è organizzato come un dizionario di valori di campo denominato, in cui ogni valore contiene il testo estratto, il valore normalizzato, il rettangolo di delimitazione, la confidenza e gli elementi di parola corrispondenti. Il risultato del riconoscimento del testo è organizzato come una gerarchia di righe e parole, con testo, rettangolo di delimitazione e informazioni sulla confidenza.

### <a name="sample-json-output"></a>Output JSON di esempio

La risposta all'operazione Get Analyze result, sarà la rappresentazione strutturata della fattura con tutte le informazioni estratte. Vedere qui per un [file di fattura di esempio](./media/sample-invoice.jpg) e l' [output della fattura di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)di output strutturato.

L'output JSON è costituito da 3 parti: 
* `"readResults"` il nodo contiene tutti i contrassegni di testo e di selezione riconosciuti. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. 
* `"pageResults"` il nodo contiene le tabelle e le celle estratte con i rispettivi rettangoli di delimitazione, la confidenza e un riferimento alle righe e alle parole in "readResults".
* `"documentResults"` il nodo contiene i valori specifici della fattura individuati dal modello. Qui è possibile trovare tutti i campi della fattura, ad esempio ID fattura, spedizione, fatturazione, cliente, totale e molto altro.

## <a name="example-output"></a>Output di esempio

Il servizio di fatturazione estrae i campi del testo, delle tabelle e dei 26 Invoice. Di seguito sono riportati i campi estratti da una fattura nella risposta di output JSON (l'output seguente usa questa [fattura di esempio](./media/sample-invoice.jpg))  

|Nome| Tipo | Descrizione | Testo | Valore (output standardizzato) |
|:-----|:----|:----|:----| :----|
| CustomerName | string | Cliente che viene fatturato | Microsoft Corp |  |
| CustomerId | string | ID di riferimento per il cliente | CID-12345 |  |
| PurchaseOrder | string | Numero di riferimento dell'ordine di acquisto | PO-3333 | |  |
| InvoiceId | string | ID per la fattura specifica (spesso "numero di fattura") | INV-100 | |  |
| Data fattura | Data | Data di emissione della fattura | 15/11/2019 | 
| DueDate | Data | Il pagamento della data per la fattura è dovuto | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| NomeFornitore | string | Fornitore che ha creato la fattura | CONTOSO LTD. | |
| VendorAddress | string | Indirizzo postale per il fornitore | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | string | Nome associato a VendorAddress | Sede centrale contoso | |
| CustomerAddress | string | Indirizzo postale per il cliente | 123 altra St, Redmond WA, 98052 | |
| CustomerAddressRecipient | string | Nome associato a CustomerAddress | Microsoft Corp | |
| BillingAddress | string | Indirizzo di fatturazione esplicito per il cliente | 123 fattura St, Redmond WA, 98052 | |
| BillingAddressRecipient | string | Nome associato a BillingAddress | Servizi Microsoft | |
| ShippingAddress | string | Indirizzo di spedizione esplicito per il cliente | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | string | Nome associato a ShippingAddress | Microsoft Delivery | |
| SubTotal | Numero | Campo Subtotale identificato in questa fattura | $100,00 | 100 | 
| TotalTax | Numero | Campo fiscale totale identificato sulla fattura | $10,00 | 10 |
| TotaleFattura | Numero | Totale nuovi addebiti associati alla fattura | $110,00 | 110 |
| Controlli ImportoDovuto |  Numero | Importo totale dovuto al fornitore | $610,00 | 610 |
| ServiceAddress | string | Indirizzo di proprietà o indirizzo di servizio esplicito per il cliente | 123 servizio St, Redmond WA, 98052 | |
| ServiceAddressRecipient | string | Nome associato a ServiceAddress | Servizi Microsoft | |
| RemittanceAddress | string | Rimessa esplicita o indirizzo di pagamento per il cliente | 123, New York, NY, 10001 |  |
| RemittanceAddressRecipient | string | Nome associato a RemittanceAddress | Fatturazione di contoso |  |
| ServiceStartDate | Data | Prima data del periodo di servizio (ad esempio, un periodo del servizio di fatturazione dell'utilità) | 14/10/2019 | 2019-10-14 |
| ServiceEndDate | Data | Data di fine del periodo di servizio (ad esempio, un periodo del servizio di fatturazione dell'utilità) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | Numero | Saldo precedentemente non pagato esplicito | $500,00 | 500 |


## <a name="next-steps"></a>Passaggi successivi

- Provare le proprie fatture ed esempi nell' [interfaccia utente di esempio di riconoscimento moduli](https://fott-preview.azurewebsites.net/).
- Completare una [Guida introduttiva alla libreria client di riconoscimento moduli](quickstarts/client-library.md) per iniziare a scrivere un'app per l'elaborazione di fatture con il riconoscimento del modulo nel linguaggio preferito.
- In alternativa, seguire la Guida introduttiva [Estrai dati fattura](./quickstarts/python-invoices.md) per implementare l'estrazione dei dati di fattura tramite Python e l'API REST.
## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)
* [Documentazione di riferimento per l'API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync)
