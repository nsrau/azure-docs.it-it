---
title: Informazioni su Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Riconoscimento modulo di Servizi cognitivi Microsoft consente di identificare ed estrarre coppie chiave-valore e dati di tabella da documenti modulo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 2193bd85265103957c5cafad10a243b266f7e5bc
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026883"
---
# <a name="what-is-form-recognizer"></a>Informazioni su Riconoscimento modulo

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Riconoscimento modulo di Azure è un servizio cognitivo che usa la tecnologia di Machine Learning per identificare ed estrarre coppie chiave-valore e dati di tabelle da documenti modulo. Inserisce il testo dai moduli e restituisce dati strutturati che includono le relazioni del file originale. È possibile ottenere in modo rapido risultati accurati e su misura per il contenuto specifico, senza alcun intervento manuale impegnativo o competenze approfondite di data science. Riconoscimento modulo è costituito da modelli personalizzati, dal modello predefinito di ricevuta e dall'API di layout. È possibile chiamare i modelli di Riconoscimento modulo usando un'API REST per ridurre la complessità e integrarli nel flusso di lavoro o nell'applicazione.

Riconoscimento modulo è costituito dai servizi seguenti:
* **Modelli personalizzati**: per estrarre le coppie chiave-valore e i dati delle tabelle dai moduli. Questi modelli vengono sottoposti a training con i dati dell'utente, quindi sono personalizzati per i suoi moduli.
* **Modello predefinito di ricevuta**: per estrarre i dati dalle ricevute di vendita USA usando un modello predefinito.
* **API di layout**: per estrarre le strutture di testo e tabelle, oltre alle coordinate dei relativi rettangoli di selezione, dai documenti.

<!-- add diagram -->

## <a name="custom-models"></a>Modelli personalizzati

I modelli personalizzati di Riconoscimento modulo eseguono il training dei dati dell'utente e, per iniziare, sono sufficienti cinque moduli di input di esempio. Un modello sottoposto a training restituisce dati strutturati che includono le relazioni del file originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

Quando si esegue il training di modelli personalizzati, sono disponibili le opzioni seguenti: training con dati etichettati e senza dati etichettati.

### <a name="train-without-labels"></a>Training senza etichette

Per impostazione predefinita, Riconoscimento modulo usa l'apprendimento non supervisionato per comprendere il layout e le relazioni tra i campi e le voci dei moduli. Quando si inviano i moduli di input, l'algoritmo li raggruppa in base al tipo, individua quali chiavi e tabelle sono presenti e associa i valori alle chiavi e le voci alle tabelle. Questo metodo non richiede l'etichettatura manuale, né attività intensive di scrittura di codice e manutenzione, ed è consigliabile provarlo per primo.

### <a name="train-with-labels"></a>Training con etichette

Quando si esegue il training con dati etichettati, il modello esegue l'apprendimento supervisionato per estrarre valori di interesse usando i moduli etichettati forniti. In questo modo si ottengono modelli dalle prestazioni più elevate e anche compatibili con moduli complessi o che contengono valori senza chiavi.

Riconoscimento modulo usa l'[API di layout](#layout-api) per riconoscere le dimensioni e le posizioni previste degli elementi di testo stampati e scritti a mano. Usa quindi le etichette specificate dall'utente per riconoscere le associazioni chiave-valore nei documenti. Per iniziare a eseguire il training di un nuovo modello, è consigliabile usare cinque moduli dello stesso tipo etichettati manualmente e aggiungere altri dati etichettati se necessario per migliorare l'accuratezza del modello.

## <a name="prebuilt-receipt-model"></a>Modello di ricevute predefinito

Riconoscimento modulo include anche un modello per la lettura di ricevute di vendita in inglese degli Stati Uniti, il tipo usato da ristoranti, distributori di carburante, rivenditori e così via ([ricevuta di esempio](./media/contoso-receipt-small.png)). Questo modello consente di estrarre le informazioni principali, come la data e l'ora della transazione, i dati del rivenditore, gli importi di imposte e totali e altro ancora. Il training del modello di ricevute predefinito è stato inoltre eseguito in modo che il modello possa riconoscere e restituire tutto il testo di una ricevuta.

## <a name="layout-api"></a>API di layout

Riconoscimento modulo può anche estrarre la struttura di testo e tabelle (il numero di righe e colonne associate al testo) tramite riconoscimento ottico dei caratteri (OCR) in alta definizione.

## <a name="get-started"></a>Introduzione

Seguire un argomento di avvio rapido per iniziare a estrarre i dati dai moduli. È consigliabile usare il servizio gratuito mentre si acquisisce familiarità con questa tecnologia. Tenere presente che il numero di pagine gratuite è limitato a 500 al mese.

* [Avvio rapido per la libreria client](./quickstarts/client-library.md) (tutti i linguaggi, più scenari)
* Avvii rapidi per l'interfaccia utente Web
  * [Training con etichette - Strumenti di etichettatura campioni](quickstarts/label-tool.md)
* Avvii rapidi per REST
  * Eseguire il training di modelli personalizzati ed estrarre i dati dei moduli
    * [Training senza etichette - cURL](quickstarts/curl-train-extract.md)
    * [Training senza etichette - Python](quickstarts/python-train-extract.md)
    * [Training con etichette - Python](quickstarts/python-labeled-data.md)
  * Estrarre dati dalle ricevute di vendita USA
    * [Estrarre dati dalle ricevute - cURL](quickstarts/curl-receipts.md)
    * [Estrarre dati dalle ricevute - Python](quickstarts/python-receipts.md)
  * Estrarre la struttura di testo e tabelle dai moduli
    * [Estrarre i dati del layout - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Esaminare le API REST

Per eseguire il training dei modelli ed estrarre dati strutturati dai moduli, verranno usate le API seguenti.

|Nome |Descrizione |
|---|---|
| **Eseguire il training di un modello personalizzato**| Eseguire il training di un nuovo modulo per analizzare i moduli usando cinque moduli dello stesso tipo. Impostare il parametro _useLabelFile_ su `true` per eseguire il training con dati etichettati manualmente. |
| **Analyze Form** |Analizzare un singolo documento passato come flusso per estrarre testo, coppie chiave-valore e tabelle dal modulo con il modello personalizzato.  |
| **Analyze Receipt** |Analizzare una singola ricevuta per estrarre le informazioni principali e eventuale testo aggiuntivo contenuto nella ricevuta.|
| **Analyze Layout** |Analizzare il layout di un modulo per estrarre la struttura di testo e tabelle.|

Per altre informazioni, esplorare la [documentazione di riferimento dell'API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm). Se si ha familiarità con una versione precedente dell'API, vedere l'articolo [Novità](./whats-new.md) per informazioni sulle modifiche recenti.

## <a name="input-requirements"></a>Requisiti di input
### <a name="custom-model"></a>Modello personalizzato

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Modello di ricevute predefinito

I requisiti di input per il modello di ricevuta sono leggermente diversi.

* Il formato deve essere JPEG, PNG, PDF (testo o digitalizzato) o TIFF.
* Le dimensioni dei file devono essere minori di 20 MB.
* Per le immagini, le dimensioni devono essere comprese tra 50 x 50 pixel e 10000 x 10000 pixel.
* Le dimensioni dei PDF devono essere al massimo di 17 x 17 pollici, corrispondenti a formati della carta Legal o A3 e inferiori.
* Per PDF e TIFF, vengono elaborate solo le prime 200 pagine (con una sottoscrizione di livello gratuito, vengono elaborate solo le prime due pagine).

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come per tutti i servizi cognitivi, gli sviluppatori che usano il servizio di riconoscimento modulo devono conoscere la politica di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Completare un argomento di [avvio rapido](quickstarts/curl-train-extract.md) per iniziare a usare le [API di riconoscimento modulo](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).
