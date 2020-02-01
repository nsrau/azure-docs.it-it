---
title: Novità di Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Comprendere le ultime modifiche apportate all'API di riconoscimento del modulo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: 2109d25d3962063c711dcab491855d9ebf1cf694
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901876"
---
# <a name="whats-new-in-form-recognizer"></a>Novità di Riconoscimento modulo

Questo articolo evidenzia le principali modifiche apportate con le nuove versioni dell'API di riconoscimento del modulo.

> [!NOTE]
> Le guide introduttive e le guide in questo set di documenti utilizzano sempre la versione più recente dell'API, a meno che non specifichino diversamente.

## <a name="form-recognizer-20-preview"></a>Riconoscimento form 2,0 (anteprima)

### <a name="new-features"></a>Nuove funzionalità

* **Modello personalizzato**
  * Eseguire il **training con etichette** È ora possibile eseguire il training di un modello personalizzato con dati con etichetta manuale. In questo modo si ottengono modelli dalle prestazioni più elevate e anche compatibili con moduli complessi o che contengono valori senza chiavi.
  * **API asincrona** È possibile usare le chiamate API asincrone per eseguire il training e analizzare i set di dati e i file di grandi dimensioni.
  * **Supporto file TIFF** È ora possibile eseguire il training con ed estrarre i dati dai documenti TIFF.
  * **Miglioramenti dell'accuratezza dell'estrazione**

* **Modello di ricezione predefinito**
  * **Importi delle mance** È ora possibile estrarre gli importi delle mance e altri valori scritti a mano.
  * **Estrazione di elementi linea** È possibile estrarre i valori degli elementi linea dalle ricevute.
  * **Valori di confidenza** È possibile visualizzare la confidenza del modello per ogni valore estratto.
  * **Miglioramenti dell'accuratezza dell'estrazione**

* **Estrazione del layout** È ora possibile usare l'API layout per estrarre dati di testo e dati della tabella dai form.

### <a name="custom-model-api-changes"></a>Modifiche all'API del modello personalizzato

Tutte le API per il training e l'utilizzo di modelli personalizzati sono state rinominate e alcuni metodi sincroni sono ora asincroni. Di seguito sono riportate le modifiche principali:

* Il processo di training di un modello è ora asincrono. Si avvia il training tramite la chiamata API **/Custom/Models** . Questa chiamata restituisce un ID operazione, che può essere passato in **Custom/Models/{ModelId}** per restituire i risultati di training.
* L'estrazione chiave/valore è ora iniziata dalla chiamata API **/Custom/Models/{ModelId}/Analyze** . Questa chiamata restituisce un ID operazione, che può essere passato in **Custom/Models/{ModelId}/analyzeResults/{resultID}** per restituire i risultati dell'estrazione.
* Gli ID operazione per l'operazione di training sono ora disponibili nell'intestazione **location** delle risposte http, non nell'intestazione **Operation-location** .

### <a name="receipt-api-changes"></a>Modifiche all'API di ricezione

Le API per la lettura delle ricevute di vendita sono state rinominate.

* L'estrazione dei dati della ricevuta è stata avviata dalla chiamata dell'API **/prebuilt/Receipt/Analyze** . Questa chiamata restituisce un ID operazione, che può essere passato in **/prebuilt/Receipt/analyzeResults/{resultID}** per restituire i risultati dell'estrazione.

### <a name="output-format-changes"></a>Modifiche al formato di output

Le risposte JSON per tutte le chiamate API hanno nuovi formati. Alcune chiavi e valori sono stati aggiunti, rimossi o rinominati. Vedere le guide introduttive per esempi dei formati JSON correnti.

## <a name="next-steps"></a>Passaggi successivi

Completare un argomento di [avvio rapido](quickstarts/curl-train-extract.md) per iniziare a usare le [API di riconoscimento modulo](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).