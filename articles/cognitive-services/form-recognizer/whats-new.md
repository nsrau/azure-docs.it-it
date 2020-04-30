---
title: Novità di Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Comprendere le ultime modifiche apportate all'API di riconoscimento del modulo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531083"
---
# <a name="whats-new-in-form-recognizer"></a>Novità di Riconoscimento modulo

Il servizio di riconoscimento form viene aggiornato in modo continuo. Usare questo articolo per rimanere sempre aggiornati sui miglioramenti delle funzionalità, sulle correzioni e sugli aggiornamenti della documentazione.

> [!NOTE]
> Le guide introduttive e le guide per il riconoscimento moduli utilizzano sempre la versione più recente dell'API, a meno che non sia specificato.

## <a name="march-2020"></a>Marzo 2020 

### <a name="new-features"></a>Nuove funzionalità

* **Tipi di valore per l'assegnazione di etichette** È ora possibile specificare i tipi di valori di cui si sta eseguendo l'assegnazione di etichette con lo strumento di assegnazione di etichette di esempio di form Recognizer. Sono attualmente supportati i tipi di valore e le varianti seguenti:
  * `string`
    * predefinito, `no-whitespaces`, `alphanumeric`
  * `number`
    * predefinito, `currency`
  * `date` 
    * predefinito, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Per informazioni sull'uso di questa funzionalità, vedere la guida agli strumenti per l' [assegnazione di etichette di esempio](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Visualizzazione tabella** Lo strumento di assegnazione di etichette di esempio ora Visualizza le tabelle riconosciute nel documento. In questo modo è possibile visualizzare le tabelle riconosciute ed estratte dal documento, prima dell'assegnazione di etichette e dell'analisi. Questa funzionalità può essere attivata/disattivata tramite l'opzione livelli.

  Questo è un esempio di come vengono riconosciute ed estratte le tabelle:

  > [!div class="mx-imgBorder"]
  > ![Visualizzazione delle tabelle mediante lo strumento di assegnazione di etichette di esempio](./media/whats-new/formre-table-viz.png)

    Le tabelle estratte sono disponibili nell'output JSON sotto `"pageResults"`.

  > [!IMPORTANT]
  > L'assegnazione di etichette alle tabelle non è supportata. Se le tabelle non vengono riconosciute e extrated automaticamente, è possibile contrassegnarle solo come coppie chiave/valore. Quando si etichettano le tabelle come coppie chiave/valore, etichettare ogni cella come valore univoco.

### <a name="extraction-enhancements"></a>Miglioramenti dell'estrazione

Questa versione include miglioramenti dell'estrazione e miglioramenti dell'accuratezza, in particolare la funzionalità per etichettare ed estrarre più coppie chiave/valore nella stessa riga di testo. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Lo strumento di assegnazione di etichette di esempio è ora Open Source

Lo strumento per l'assegnazione di etichette di esempio di form Recognizer è ora disponibile come progetto open source. Puoi integrarlo nelle tue soluzioni e apportare modifiche specifiche per i clienti in base alle tue esigenze.

Per altre informazioni sullo strumento per l'assegnazione di etichette di esempio di form Recognizer, vedere la documentazione disponibile su [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposizione di TLS 1.2

TLS 1,2 viene ora applicato per tutte le richieste HTTP a questo servizio. Per altre informazioni, vedere [sicurezza dei servizi cognitivi di Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Gennaio 2020

Questa versione introduce il modulo Recognizer 2,0 (Preview). Nelle sezioni seguenti sono disponibili altre informazioni su nuove funzionalità, miglioramenti e modifiche. 

### <a name="new-features"></a>Nuove funzionalità

* **Modello personalizzato**
  * Eseguire il **training con etichette** È ora possibile eseguire il training di un modello personalizzato con dati con etichetta manuale. In questo modo si ottengono modelli dalle prestazioni più elevate e anche compatibili con moduli complessi o che contengono valori senza chiavi.
  * **API asincrona** È possibile usare le chiamate API asincrone per eseguire il training e analizzare i set di dati e i file di grandi dimensioni.
  * **Supporto file TIFF** È ora possibile eseguire il training con ed estrarre i dati dai documenti TIFF.
  * **Miglioramenti dell'accuratezza dell'estrazione**

* **Modello di ricevute predefinito**
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