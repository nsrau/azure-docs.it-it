---
title: Ricevute-riconoscimento form
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'analisi della ricezione con il modulo API Recognizer-uso e limiti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724983"
---
# <a name="receipt-concepts"></a>Concetti di ricezione

Il riconoscimento AzureForm può analizzare le ricevute usando uno dei modelli predefiniti. L'API di ricezione estrae le informazioni chiave dalle ricevute di vendita in inglese, ad esempio nome Merchant, data transazione, totale transazione, voci e altro ancora. 

## <a name="understanding-receipts"></a>Informazioni sulle ricevute 

Molte aziende e persone si basano ancora sull'estrazione manuale dei dati dalle ricevute di vendita, sia per le segnalazioni spese aziendali, i rimborsi, le imposte, il budget o altri scopi. Spesso in questi scenari, le immagini della ricevuta fisica sono necessarie ai fini della convalida.  

L'estrazione automatica dei dati da queste ricevute può essere complessa. Le ricevute potrebbero essere sgualcite e difficili da leggere e le immagini smartphone delle ricevute potrebbero essere di bassa qualità. Inoltre, i modelli e i campi di ricezione possono variare significativamente in base al mercato, all'area e al Merchant. Queste problematiche nell'estrazione dei dati e nel rilevamento dei campi rendono la ricezione un problema univoco.  

Usando il riconoscimento ottico dei caratteri (OCR) e il modello di ricezione predefinito, l'API di ricezione Abilita questi scenari di elaborazione della ricezione. Poiché il modello è già sottoposto a training sui dati, è possibile analizzare facilmente le ricevute in un passaggio &mdash; non è necessario il training del modello o l'assegnazione di etichette.

![ricezione di esempio](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Che cosa fa l'API di ricezione? 

L'API di ricezione precompilata estrae il contenuto delle ricevute di vendita &mdash; il tipo di ricezione che si otterrebbe normalmente presso un ristorante, un rivenditore o un negozio di alimentari.

### <a name="fields-extracted"></a>Campi estratti

* Nome esercente 
* Indirizzo esercente 
* Numero di telefono esercente 
* Data della transazione 
* Tempo transazione 
* Subtotale 
* Imposta 
* Totale 
* Suggerimento 
* Estrazione di elementi riga (ad esempio, quantità di elementi, prezzo dell'elemento, nome dell'elemento)

### <a name="additional-features"></a>Funzionalità aggiuntive

L'API di ricezione restituisce anche le informazioni seguenti:

* Tipo di ricezione (ad esempio, voce, carta di credito e così via)
* Livello di confidenza del campo (ogni campo restituisce un valore di attendibilità associato)
* Testo non elaborato OCR (output di testo estratto dall'OCR per l'intera ricezione)

## <a name="input-requirements"></a>Requisiti di input

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Impostazioni locali supportate 

* La **ricezione precompilata v 2.0** (GA) supporta le ricevute di vendita nelle impostazioni locali en-US
* **Ricezione precompilata v 2.1-Preview. 1** (anteprima pubblica) aggiunge ulteriore supporto per le impostazioni locali di ricezione seguenti: 
  * IT-AU 
  * EN-CA 
  * IT-GB 
  * IT-IN 

  > [!NOTE]
  > Input della lingua 
  >
  > La ricezione precompilata v 2.1-Preview. 1 include un parametro request facoltativo per specificare le impostazioni locali di ricezione da altri mercati in lingua inglese. Per le ricevute di vendita in inglese da Australia (EN-AU), Canada (EN-CA), Gran Bretagna (EN-GB) e India (EN-IN), è possibile specificare le impostazioni locali per ottenere risultati migliori. Se non è specificata alcuna impostazione locale in v 2.1-Preview. 1, per impostazione predefinita il modello verrà impostato sul modello EN-US.

## <a name="customer-scenarios"></a>Scenari dei clienti  

I dati estratti con l'API di ricezione possono essere usati per eseguire varie attività. Di seguito sono riportati alcuni esempi di ciò che i clienti hanno ottenuto con l'API di ricezione. 

### <a name="business-expense-reporting"></a>Report spese aziendali  

L'archiviazione delle spese aziendali comporta spesso un tempo di spesa per l'immissione manuale dei dati dalle immagini delle ricevute. Con l'API di ricezione, è possibile usare i campi estratti per automatizzare parzialmente questo processo e analizzare rapidamente le ricevute.  

Poiché l'API di ricezione ha un semplice output JSON, è possibile usare i valori dei campi estratti in diversi modi. Eseguire l'integrazione con le applicazioni di spesa interne per popolare i report spese. Per ulteriori informazioni su questo scenario, vedere la pagina relativa alla modalità di utilizzo dell'API di ricezione da Acumatica per [la segnalazione di un processo meno doloroso](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Controllo e contabilità 

L'output dell'API di ricezione può essere usato anche per eseguire analisi su un numero elevato di spese in diversi punti del processo di segnalazione spese e rimborso. È possibile elaborare le ricevute per la valutazione per il controllo manuale o per le approvazioni rapide.  

L'output della ricevuta è utile anche per la contabilità generale per l'uso aziendale o personale. Usare l'API di ricezione per trasformare i dati di un'immagine di ricezione non elaborata/PDF in un output digitale azionabile.

### <a name="consumer-behavior"></a>Comportamento del consumer 

Le ricevute contengono dati utili che è possibile usare per analizzare il comportamento degli utenti e le tendenze degli acquisti.

