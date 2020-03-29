---
title: Novità di Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Comprendere le ultime modifiche apportate all'API di riconoscimento dei moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 7f20244906581dd2869bbc7fcd997d5245540eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155172"
---
# <a name="whats-new-in-form-recognizer"></a>Novità di Riconoscimento modulo

Il servizio Di riconoscimento moduli viene aggiornato in modo continuativo. Utilizzare questo articolo per rimanere aggiornati con i miglioramenti delle funzionalità, correzioni e aggiornamenti della documentazione.

> [!NOTE]
> Le guide introduttive e le guide per il sistema di riconoscimento dei moduli utilizzano sempre la versione più recente dell'API, a meno che non venga specificato.

## <a name="march-2020"></a>Marzo 2020 

### <a name="extraction-enhancements"></a>Miglioramenti dell'estrazione

Questa versione include miglioramenti dell'estrazione e della precisione, in particolare la capacità di etichettare ed estrarre più coppie chiave/valore nella stessa riga di testo. 
 
### <a name="form-recognizer-sample-labeling-tool-is-now-open-source"></a>Lo strumento di etichettatura dei campioni di riconoscimento dei moduli è ora open source

Lo strumento di etichettatura dei campioni di riconoscimento dei moduli è ora disponibile come progetto open source. È possibile integrarlo all'interno delle soluzioni e apportare modifiche specifiche del cliente per soddisfare le proprie esigenze.

Per ulteriori informazioni sullo strumento Form Recognizer Sample Labeling Tool, consultare la documentazione disponibile in [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="labeling-value-types"></a>Tipi di valore di etichettatura

I tipi di valore sono ora disponibili per l'utilizzo con lo strumento di etichettatura dei campioni di riconoscimento dei moduli. Questi tipi di valore sono attualmente supportati:These value types are currently supported: 

* string
* Number 
* Integer
* Data 
* Tempo

Questa immagine mostra l'aspetto della selezione del tipo di valore all'interno dello strumento di etichettatura dei campioni di riconoscimento dei moduli:This image shows what value type selection looks like within the Form Recognizer Sample Labeling Tool:

> [!div class="mx-imgBorder"]
> ![Selezione del tipo di valore con lo strumento di etichettatura campione](./media/whats-new/formre-value-type.png)

La tabella estratta è disponibile `pageResults`nell'output JSON in .

### <a name="table-visualization"></a>Visualizzazione tabella 

Lo strumento di etichettatura del sistema di riconoscimento dei moduli ora visualizza le tabelle riconosciute nel documento. Ciò consente di visualizzare le tabelle riconosciute ed estratte dal documento, prima di etichettare e analizzare con lo strumento di etichettatura dei campioni di riconoscimento dei moduli. Questa funzione può essere attivata/disattivata utilizzando l'opzione Layer. 

Di seguito è illustrato un esempio di come vengono riconosciute ed estratte le tabelle:This is an example of how tables are recognized and extracted:

> [!div class="mx-imgBorder"]
> ![Visualizzazione tabella mediante lo strumento di etichettatura di esempio](./media/whats-new/formre-table-viz.png)

> [!IMPORTANT]
> L'etichettatura delle tabelle non è supportata. Se le tabelle non vengono riconosciute e vengono esunte automaticamente, è possibile etichettarle solo come coppie chiave/valore. Quando si etichettano le tabelle come coppie chiave/valore, etichettare ogni cella come valore.

### <a name="tls-12-enforcement"></a>Imposizione di TLS 1.2

* TLS 1.2 è ora applicato per tutte le richieste HTTP a questo servizio. Per altre informazioni, vedere [Sicurezza dei Servizi cognitivi](../cognitive-services-security.md)di Azure .For more information, see Azure Cognitive Services security .

## <a name="january-2020"></a>Gennaio 2020

Questa versione introduce il Form Recognizer 2.0 (anteprima). Nelle sezioni seguenti sono disponibili ulteriori informazioni su nuove funzionalità, miglioramenti e modifiche. 

### <a name="new-features"></a>Nuove funzionalità

* **Modello personalizzato**
  * **Treno con etichette** È ora possibile eseguire il training di un modello personalizzato con dati etichettati manualmente. In questo modo si ottengono modelli dalle prestazioni più elevate e anche compatibili con moduli complessi o che contengono valori senza chiavi.
  * **API asincrona** È possibile usare le chiamate API asincrone per eseguire il training e analizzare file e set di dati di grandi dimensioni.
  * **Supporto dei file TIFF** È ora possibile eseguire il training ed estrarre i dati dai documenti TIFF.
  * **Miglioramenti della precisione di estrazione**

* **Modello di ricevimento predefinito**
  * **Importi delle mance** È ora possibile estrarre quantità di mance e altri valori scritti a mano.
  * **Estrazione di elementi pubblicitari** Puoi estrarre i valori degli elementi pubblicitari dalle ricevute.
  * **Valori di confidenza** È possibile visualizzare l'attendibilità del modello per ogni valore estratto.
  * **Miglioramenti della precisione di estrazione**

* **Estrazione del layout** È ora possibile utilizzare l'API layout per estrarre dati di testo e dati di tabella dai moduli.

### <a name="custom-model-api-changes"></a>Modifiche all'API del modello personalizzatoCustom model API changes

Tutte le API per il training e l'uso di modelli personalizzati sono state rinominate e alcuni metodi sincroni sono ora asincroni. Di seguito sono riportate le modifiche principali:

* Il processo di training di un modello è ora asincrono. Si inizia il training tramite la chiamata all'API **/custom/models.** Questa chiamata restituisce un ID operazione, che è possibile passare **in custom/models/'modelID'** per restituire i risultati del training.
* L'estrazione di chiave/valore viene ora avviata dalla chiamata all'API **/custom/models/'modelID'/analyze.** Questa chiamata restituisce un ID operazione, che è possibile passare **in custom/models/'modelID'/analyzeResults/'resultID'** per restituire i risultati dell'estrazione.
* Gli ID operazione per l'operazione Train sono ora disponibili nell'intestazione **Location** delle risposte HTTP, non nell'intestazione **Operation-Location.**

### <a name="receipt-api-changes"></a>Modifiche all'API di ricevimento

Le API per la lettura delle ricevute di vendita sono state rinominate.

* L'estrazione dei dati di ricevimento viene ora avviata dalla chiamata API **/prebuilt/receipt/analyze.** Questa chiamata restituisce un ID operazione, che è possibile passare in **/prebuilt/receipt/analyzeResults/'resultID'** per restituire i risultati dell'estrazione.

### <a name="output-format-changes"></a>Modifiche al formato di output

Le risposte JSON per tutte le chiamate API hanno nuovi formati. Alcune chiavi e valori sono stati aggiunti, rimossi o rinominati. Vedere le guide introduttive per esempi dei formati JSON correnti.

## <a name="next-steps"></a>Passaggi successivi

Completare un argomento di [avvio rapido](quickstarts/curl-train-extract.md) per iniziare a usare le [API di riconoscimento modulo](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).