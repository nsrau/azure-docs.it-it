---
title: Informazioni su Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Il servizio Riconoscimento modulo di Azure consente di identificare ed estrarre coppie chiave/valore e dati di tabelle dai documenti del modulo, nonché di estrarre le informazioni principali da ricevute di vendita e biglietti da visita.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: elaborazione dati automatica, elaborazione di documenti, immissione automatica di dati, elaborazione di moduli
ms.openlocfilehash: 05024b8d7f4cf76024d71a79cb1fce489d58ee70
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519524"
---
# <a name="what-is-form-recognizer"></a>Informazioni su Riconoscimento modulo

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Riconoscimento modulo di Azure è un servizio cognitivo che consente di creare software di elaborazione dati automatica tramite la tecnologia di Machine Learning. Il servizio identifica ed estrae testo, coppie chiave-valore, contrassegni di selezione, tabelle e strutture dai documenti, quindi restituisce dati strutturati che includono le relazioni nel file originale, rettangoli delimitatori, attendibilità e altro ancora. È possibile ottenere in modo rapido risultati accurati e su misura per il contenuto specifico, senza alcun intervento manuale impegnativo o competenze approfondite di data science. Usare Riconoscimento modulo per automatizzare l'immissione di dati nelle applicazioni e arricchire le funzionalità di ricerca di documenti.

Riconoscimento modulo è costituito da modelli di elaborazione di documenti personalizzati, da modelli predefiniti di fatture, ricevute e biglietti da visita e dal modello di layout. È possibile chiamare i modelli di Riconoscimento modulo usando un'API REST o SDK delle librerie client per ridurre la complessità e integrarli nel flusso di lavoro o nell'applicazione.

Riconoscimento modulo è costituito dai servizi seguenti:
* **[API Layout](#layout-api)** : estrarre testo, contrassegni di selezione e strutture di tabelle, oltre alle relative coordinate dei rettangoli delimitatori, dai documenti.
* **[Modelli personalizzati](#custom-models)** : estrarre testo, coppie chiave-valore, contrassegni di selezione e dati di tabelle dai moduli. Questi modelli vengono sottoposti a training con i dati dell'utente, quindi sono personalizzati per i suoi moduli.
* **[Modelli predefiniti](#prebuilt-models)** : estrarre dati da tipi di moduli univoci usando modelli predefiniti. Attualmente sono disponibili i modelli predefiniti seguenti
    * [Fatture](./concept-invoices.md)
    * [Scontrini](./concept-receipts.md)
    * [Biglietti da visita](./concept-business-cards.md)


## <a name="try-it-out"></a>Provare questa operazione

Per provare il servizio Riconoscimento modulo, passare allo strumento dell'interfaccia utente di esempio online:


# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Provare i modelli predefiniti](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)
> [!div class="nextstepaction"]
> [Provare i modelli predefiniti](https://fott-preview.azurewebsites.net/)

---

Per provare il servizio Riconoscimento modulo, è necessario avere una sottoscrizione di Azure ([crearne una gratuita](https://azure.microsoft.com/free/cognitive-services)), oltre a un endpoint e a una chiave della [risorsa Riconoscimento modulo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer).

## <a name="layout-api"></a>API di layout

Riconoscimento modulo consente di estrarre testo, contrassegni di selezione e strutture di tabelle (i numeri di righe e colonne associate al testo) dai documenti tramite riconoscimento ottico dei caratteri (OCR) in alta definizione e con un modello di Deep Learning avanzato. Per altre informazioni, vedere la guida concettuale sul [layout](./concept-layout.md).

:::image type="content" source="./media/tables-example.jpg" alt-text="Esempi di tabelle" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modelli personalizzati

I modelli personalizzati di Riconoscimento modulo eseguono il training dei dati dell'utente e, per iniziare, sono sufficienti cinque moduli di input di esempio. Un modello di elaborazione documenti sottoposto a training può restituire dati strutturati che includono le relazioni nel documento del modulo originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

Quando si esegue il training di modelli personalizzati, sono disponibili le opzioni seguenti: training con dati etichettati e senza dati etichettati.

### <a name="train-without-labels"></a>Training senza etichette

Per impostazione predefinita, Riconoscimento modulo usa l'apprendimento non supervisionato per comprendere il layout e le relazioni tra i campi e le voci dei moduli. Quando si inviano i moduli di input, l'algoritmo li raggruppa in base al tipo, individua quali chiavi e tabelle sono presenti e associa i valori alle chiavi e le voci alle tabelle. Questo metodo non richiede l'etichettatura manuale, né attività intensive di scrittura di codice e manutenzione, ed è consigliabile provarlo per primo.

Per suggerimenti su come raccogliere i documenti di training, vedere [Creare un set di dati di training](./build-training-data-set.md).

### <a name="train-with-labels"></a>Training con etichette

Quando si esegue il training con dati etichettati, il modello esegue l'apprendimento supervisionato per estrarre valori di interesse usando i moduli etichettati forniti. In questo modo si ottengono modelli dalle prestazioni più elevate e anche compatibili con moduli complessi o che contengono valori senza chiavi.

Riconoscimento modulo usa l'[API di layout](#layout-api) per riconoscere le dimensioni e le posizioni previste degli elementi di testo stampati e scritti a mano. Usa quindi le etichette specificate dall'utente per riconoscere le associazioni chiave-valore nei documenti. Per iniziare a eseguire il training di un nuovo modello, è consigliabile usare cinque moduli dello stesso tipo (stessa struttura) etichettati manualmente e aggiungere altri dati etichettati se necessario per migliorare l'accuratezza.

[Introduzione al training con le etichette](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>Modelli predefiniti

Riconoscimento modulo include anche modelli predefiniti per l'elaborazione dati automatica di tipi di moduli univoci.

### <a name="prebuilt-invoice-model"></a>Modello predefinito di fattura
Il modello predefinito di fattura estrae dati dalle fatture in un'ampia varietà di formati e restituisce dati strutturati. Questo modello estrae informazioni chiave come l'ID fattura, i dati del cliente e del fornitore, l'indirizzo di spedizione e di fatturazione, l'importo totale, le imposte, il subtotale e altro ancora. Il training del modello predefinito di fattura è stato inoltre eseguito in modo che possa riconoscere e restituire tutto il testo e le tabelle della fattura. Per altre informazioni, vedere la guida concettuale sulle [fatture](./concept-invoices.md).

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Esempio di fattura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Modello di ricevuta predefinito

Il modello di ricevuta predefinito viene usato per la lettura di ricevute di vendita in inglese di Australia, Canada, Gran Bretagna, India e Stati Uniti, il tipo usato da ristoranti, distributori di carburante, rivenditori e così via. Questo modello consente di estrarre le informazioni principali, come la data e l'ora della transazione, i dati del fornitore, gli importi delle imposte, gli articoli, i totali e altro ancora. Il training del modello di ricevute predefinito è stato inoltre eseguito in modo che il modello possa riconoscere e restituire tutto il testo di una ricevuta. Per altre informazioni, vedere [Concetti relativi alle ricevute](./concept-receipts.md).

:::image type="content" source="./media/overview-receipt.jpg" alt-text="ricevuta di esempio" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>Modello di biglietto da visita predefinito

Il modello di biglietto da visita consente di estrarre informazioni quali il nome della persona, la posizione, la via e numero civico, l'indirizzo di posta elettronica, la società e i numeri di telefono dai biglietti da visita in inglese. Per altre informazioni, vedere [Concetti relativi ai biglietti da visita](./concept-business-cards.md).

:::image type="content" source="./media/overview-business-card.jpg" alt-text="biglietto da visita di esempio" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>Introduzione

Per iniziare a estrarre dati dai moduli, usare lo [strumento Riconoscimento modulo di esempio](https://fott.azurewebsites.net/) o seguire una guida di avvio rapido. È consigliabile usare il servizio gratuito mentre si acquisisce familiarità con questa tecnologia. Tenere presente che il numero di pagine gratuite è limitato a 500 al mese.

* [Guide di avvio rapido sulle librerie client](./quickstarts/client-library.md) (tutti i linguaggi, più scenari)
* Avvii rapidi per l'interfaccia utente Web
  * [Training con etichette - Strumenti di etichettatura campioni](quickstarts/label-tool.md)
* Avvii rapidi per REST
 * Estrarre testo, contrassegni di selezione e strutture di tabelle dai documenti
    * [Estrarre i dati del layout - Python](quickstarts/python-layout.md)
  * Eseguire il training di modelli personalizzati ed estrarre i dati dei moduli
    * [Training senza etichette - cURL](quickstarts/curl-train-extract.md)
    * [Training senza etichette - Python](quickstarts/python-train-extract.md)
    * [Training con etichette - Python](quickstarts/python-labeled-data.md)
  * Estrarre dati dalle fatture
    * [Estrarre dati dalle fatture - Python](quickstarts/python-invoices.md)
  * Estrarre dati dalle ricevute di vendita
    * [Estrarre dati dalle ricevute - cURL](quickstarts/curl-receipts.md)
    * [Estrarre dati dalle ricevute - Python](quickstarts/python-receipts.md)
  * Estrarre dati dai biglietti da visita
    * [Estrarre dati dai biglietti da visita - Python](quickstarts/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Esaminare le API REST

Per eseguire il training dei modelli ed estrarre dati strutturati dai moduli, verranno usate le API seguenti.

|Nome |Descrizione |
|---|---|
| **Analyze Layout** | Analizzare un documento passato come flusso per estrarre testo, contrassegni di selezione, tabelle e strutture dal documento |
| **Eseguire il training di un modello personalizzato**| Eseguire il training di un nuovo modulo per analizzare i moduli usando cinque moduli dello stesso tipo. Impostare il parametro _useLabelFile_ su `true` per eseguire il training con dati etichettati manualmente. |
| **Analyze Form** |Analizzare un modulo passato come flusso per estrarre testo, coppie chiave-valore e tabelle dal modulo con un modello personalizzato.  |
| **Analyze Invoice** | Analizzare una fattura per estrarre informazioni chiave, tabelle e altro testo.|
| **Analyze Receipt** | Analizzare una ricevuta per estrarre informazioni chiave e altro testo.|
| **Analyze Business Card** | Analizzare un biglietto da lavoro per estrarre le principali informazioni e il testo.|


# <a name="v20"></a>[v2.0](#tab/v2-0)
Per altre informazioni, esplorare la [documentazione di riferimento dell'API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm). Se si ha familiarità con una versione precedente dell'API, vedere l'articolo [Novità](./whats-new.md) per informazioni sulle modifiche recenti.

# <a name="v21"></a>[v2.1](#tab/v2-1)
Per altre informazioni, esplorare la [documentazione di riferimento dell'API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm). Se si ha familiarità con una versione precedente dell'API, vedere l'articolo [Novità](./whats-new.md) per informazioni sulle modifiche recenti.

---

## <a name="input-requirements"></a>Requisiti di input

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuire in locale con i contenitori Docker

[Usare i contenitori di Riconoscimento modulo (anteprima)](form-recognizer-container-howto.md) per distribuire le funzionalità dell'API in locale. Questo contenitore Docker consente di avvicinare il servizio ai dati per motivi di conformità, sicurezza o di altro tipo.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come per tutti i servizi cognitivi, gli sviluppatori che usano il servizio di riconoscimento modulo devono conoscere la politica di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Completare una [guida di avvio rapido sulla libreria client](quickstarts/client-library.md) per apprendere come scrivere un'app di elaborazione moduli con Riconoscimento modulo nel linguaggio preferito.
