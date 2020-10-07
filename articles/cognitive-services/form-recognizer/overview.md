---
title: Informazioni su Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Il servizio Riconoscimento modulo di Azure consente di identificare ed estrarre coppie chiave/valore e dati di tabelle dai documenti del modulo, nonché di estrarre le informazioni principali da ricevute di vendita e biglietti da visita.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: elaborazione dati automatica, elaborazione di documenti, immissione automatica di dati, elaborazione di moduli
ms.openlocfilehash: 5243c170e1f6b5f647057b8cfafbcac9b2fb4db3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318960"
---
# <a name="what-is-form-recognizer"></a>Informazioni su Riconoscimento modulo

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Riconoscimento modulo di Azure è un servizio cognitivo che consente di creare software di elaborazione dati automatica tramite la tecnologia di Machine Learning. Il servizio identifica ed estrae testo, coppie chiave-valore e dati delle tabelle dai documenti dei moduli, quindi restituisce dati strutturati che includono le relazioni nel file originale. È possibile ottenere in modo rapido risultati accurati e su misura per il contenuto specifico, senza alcun intervento manuale impegnativo o competenze approfondite di data science. Usare Riconoscimento modulo per automatizzare l'immissione di dati nelle applicazioni.

Riconoscimento modulo è costituito da modelli di elaborazione di documenti personalizzati, dai modelli predefiniti di ricevuta e biglietto da visita e dall'API Layout. È possibile chiamare i modelli di Riconoscimento modulo usando un'API REST o SDK delle librerie client per ridurre la complessità e integrarli nel flusso di lavoro o nell'applicazione.

Riconoscimento modulo è costituito dai servizi seguenti:
* **[Modelli personalizzati](#custom-models)** : estrarre coppie chiave-valore e dati delle tabelle dai moduli. Questi modelli vengono sottoposti a training con i dati dell'utente, quindi sono personalizzati per i suoi moduli.
* **[Modelli predefiniti](#prebuilt-models)** : estrarre dati da tipi di moduli univoci usando modelli predefiniti. Attualmente sono disponibili modelli predefiniti per ricevute di vendita e biglietti da visita in lingua inglese.
* **[API Layout](#layout-api)** : estrarre le strutture di testo e tabelle, oltre alle coordinate dei relativi rettangoli di selezione, dai documenti.

## <a name="custom-models"></a>Modelli personalizzati

I modelli personalizzati di Riconoscimento modulo eseguono il training dei dati dell'utente e, per iniziare, sono sufficienti cinque moduli di input di esempio. Un modello di elaborazione documenti sottoposto a training può restituire dati strutturati che includono le relazioni nel documento del modulo originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

Quando si esegue il training di modelli personalizzati, sono disponibili le opzioni seguenti: training con dati etichettati e senza dati etichettati.

### <a name="train-without-labels"></a>Training senza etichette

Per impostazione predefinita, Riconoscimento modulo usa l'apprendimento non supervisionato per comprendere il layout e le relazioni tra i campi e le voci dei moduli. Quando si inviano i moduli di input, l'algoritmo li raggruppa in base al tipo, individua quali chiavi e tabelle sono presenti e associa i valori alle chiavi e le voci alle tabelle. Questo metodo non richiede l'etichettatura manuale, né attività intensive di scrittura di codice e manutenzione, ed è consigliabile provarlo per primo.

Per suggerimenti su come raccogliere i documenti di training, vedere [Creare un set di dati di training](./build-training-data-set.md).

### <a name="train-with-labels"></a>Training con etichette

Quando si esegue il training con dati etichettati, il modello esegue l'apprendimento supervisionato per estrarre valori di interesse usando i moduli etichettati forniti. In questo modo si ottengono modelli dalle prestazioni più elevate e anche compatibili con moduli complessi o che contengono valori senza chiavi.

Riconoscimento modulo usa l'[API di layout](#layout-api) per riconoscere le dimensioni e le posizioni previste degli elementi di testo stampati e scritti a mano. Usa quindi le etichette specificate dall'utente per riconoscere le associazioni chiave-valore nei documenti. Per iniziare a eseguire il training di un nuovo modello, è consigliabile usare cinque moduli dello stesso tipo etichettati manualmente e aggiungere altri dati etichettati se necessario per migliorare l'accuratezza del modello.

## <a name="prebuilt-models"></a>Modelli predefiniti

Riconoscimento modulo include anche modelli predefiniti per l'elaborazione dati automatica di tipi di moduli univoci.

### <a name="prebuilt-receipt-model"></a>Modello di ricevuta predefinito

Il modello di ricevuta predefinito viene usato per la lettura di ricevute di vendita in inglese di Australia, Canada, Gran Bretagna, India e Stati Uniti, il tipo usato da ristoranti, distributori di carburante, rivenditori e così via. Questo modello consente di estrarre le informazioni principali, come la data e l'ora della transazione, i dati del fornitore, gli importi delle imposte, gli articoli, i totali e altro ancora. Il training del modello di ricevute predefinito è stato inoltre eseguito in modo che il modello possa riconoscere e restituire tutto il testo di una ricevuta. Per altre informazioni, vedere [Concetti relativi alle ricevute](./concept-receipts.md).

![ricevuta di esempio](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Modello di biglietto da visita predefinito

Il modello di biglietto da visita consente di estrarre informazioni quali il nome della persona, la posizione, la via e numero civico, l'indirizzo di posta elettronica, la società e i numeri di telefono dai biglietti da visita in inglese. Per altre informazioni, vedere [Concetti relativi ai biglietti da visita](./concept-business-cards.md).

![biglietto da visita di esempio](./media/business-card-english.jpg)

## <a name="layout-api"></a>API di layout

Riconoscimento modulo può anche estrarre la struttura di testo e tabelle (il numero di righe e colonne associate al testo) tramite riconoscimento ottico dei caratteri (OCR) in alta definizione.

## <a name="get-started"></a>Introduzione

Seguire un argomento di avvio rapido per iniziare a estrarre i dati dai moduli. È consigliabile usare il servizio gratuito mentre si acquisisce familiarità con questa tecnologia. Tenere presente che il numero di pagine gratuite è limitato a 500 al mese.

* [Guide di avvio rapido sulle librerie client](./quickstarts/client-library.md) (tutti i linguaggi, più scenari)
* Avvii rapidi per l'interfaccia utente Web
  * [Training con etichette - Strumenti di etichettatura campioni](quickstarts/label-tool.md)
* Avvii rapidi per REST
  * Eseguire il training di modelli personalizzati ed estrarre i dati dei moduli
    * [Training senza etichette - cURL](quickstarts/curl-train-extract.md)
    * [Training senza etichette - Python](quickstarts/python-train-extract.md)
    * [Training con etichette - Python](quickstarts/python-labeled-data.md)
  * Estrarre dati dalle ricevute di vendita
    * [Estrarre dati dalle ricevute - cURL](quickstarts/curl-receipts.md)
    * [Estrarre dati dalle ricevute - Python](quickstarts/python-receipts.md)
  * Estrarre dati dai biglietti da visita
    * [Estrarre dati dai biglietti da visita - Python](quickstarts/python-business-cards.md)
  * Estrarre la struttura di testo e tabelle dai moduli
    * [Estrarre i dati del layout - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Esaminare le API REST

Per eseguire il training dei modelli ed estrarre dati strutturati dai moduli, verranno usate le API seguenti.

|Nome |Descrizione |
|---|---|
| **Eseguire il training di un modello personalizzato**| Eseguire il training di un nuovo modulo per analizzare i moduli usando cinque moduli dello stesso tipo. Impostare il parametro _useLabelFile_ su `true` per eseguire il training con dati etichettati manualmente. |
| **Analyze Form** |Analizzare un singolo documento passato come flusso per estrarre testo, coppie chiave-valore e tabelle dal modulo con il modello personalizzato.  |
| **Analyze Receipt** |Analizzare una singola ricevuta per estrarre le informazioni principali e eventuale testo aggiuntivo contenuto nella ricevuta.|
| **Analyze Business Card** |Analizzare un biglietto da lavoro per estrarre le principali informazioni e il testo.|
| **Analyze Layout** |Analizzare il layout di un modulo per estrarre la struttura di testo e tabelle.|

Per altre informazioni, esplorare la [documentazione di riferimento dell'API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm). Se si ha familiarità con una versione precedente dell'API, vedere l'articolo [Novità](./whats-new.md) per informazioni sulle modifiche recenti.

## <a name="input-requirements"></a>Requisiti di input

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come per tutti i servizi cognitivi, gli sviluppatori che usano il servizio di riconoscimento modulo devono conoscere la politica di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Completare una [guida di avvio rapido sulla libreria client](quickstarts/client-library.md) per apprendere come scrivere un'app di elaborazione moduli con Riconoscimento modulo nel linguaggio preferito.