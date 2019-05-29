---
title: Informazioni su Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare Riconoscimento modulo per analizzare dati di modulo e tabella.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601631"
---
# <a name="what-is-form-recognizer"></a>Informazioni su Riconoscimento modulo

Riconoscimento modulo di Azure è un servizio cognitivo che usa tecnologia di apprendimento automatico per identificare ed estrarre coppie chiave-valore e dati di tabella da documenti modulo. Restituisce quindi dati strutturati che includono le relazioni nel file originale. È possibile chiamare il modello di riconoscimento modulo personalizzato usando una semplice API REST per ridurre la complessità e integrarlo facilmente nel flusso di lavoro o nell'applicazione. Per iniziare sono necessari solo cinque documenti modulo o un modulo vuoto dello stesso tipo del materiale di input. È possibile ottenere in modo rapido risultati accurati e su misura per il contenuto specifico, senza alcun intervento manuale impegnativo o competenze approfondite di data science.

## <a name="request-access"></a>Richiedere l'accesso
Il riconoscimento modulo è disponibile in anteprima ad accesso limitato. Per avere accesso all'anteprima, completare e inviare il modulo di [richiesta di accesso al riconoscimento modulo](https://aka.ms/FormRecognizerRequestAccess). Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà Riconoscimento modulo. Se la richiesta viene approvata dal team di Servizi cognitivi di Azure, si riceverà un messaggio di posta elettronica con le istruzioni per l'accesso al servizio.

## <a name="what-it-does"></a>Risultato

Quando si inviano i dati di input, esegue il training dell'algoritmo, quindi il clustering dei moduli per tipi, individua quali tabelle e le chiavi sono presenti e apprende ad associare i valori alle chiavi e le voci alle tabelle. L'apprendimento non supervisionato consente al modello di riconoscere il layout e le relazioni tra campi e voci senza assegnazione manuale di etichette dati o impegnative attività di creazione di codice e manutenzione. Al contrario, i modelli di Machine Learning con training preliminare richiedono dati standardizzati e sono meno accurati se usati con materiale di input che si discosta dai formati tradizionali, come i moduli specifici del settore.

Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

## <a name="what-it-includes"></a>Cosa include

Riconoscimento modulo è disponibile come API REST. È possibile creare, eseguire il training e assegnare punteggi a un modello richiamando l'API. È eventualmente possibile eseguire il modello in un contenitore Docker locale.

## <a name="input-requirements"></a>Requisiti di input

Il riconoscimento modulo funziona su documenti di input che soddisfano questi requisiti:

* Il formato deve essere JPG, PNG o PDF (testo o digitalizzato). I documenti PDF con testo incorporato sono i più adatti perché non vi è alcuna possibilità di errore nell'estrazione e individuazione dei caratteri.
* Le dimensioni del file devono essere inferiori a 4 megabyte (MB).
* Per le immagini, le dimensioni devono essere comprese tra 50 x 50 pixel e 4200 x 4200 pixel.
* Se digitalizzati da documenti cartacei, i moduli devono essere scansioni di alta qualità.
* Per il testo deve essere usato l'alfabeto latino (caratteri in lingua inglese).
* I dati devono essere stampati (non scritti a mano).
* I dati devono contenere chiavi e valori.
* Le chiavi possono essere visualizzate sopra o a sinistra dei valori, ma non sotto o a destra.

Il riconoscimento modulo attualmente non supporta questi tipi di dati di input:

* Tabelle complesse (tabelle annidate, intestazioni o celle unite e così via).
* Caselle di controllo o pulsanti di opzione.
* Documenti PDF con più di 50 pagine.

## <a name="where-do-i-start"></a>Dove iniziare?

**Passaggio 1:** Creare una risorsa Riconoscimento modulo nel portale di Azure.

**Passaggio 2:** Provare una guida di avvio rapido per acquisire esperienza pratica:
* [Guida introduttiva: Eseguire il training di un modello di riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con cURL](quickstarts/curl-train-extract.md)
* [Guida introduttiva: Eseguire il training di un modello di riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con Python](quickstarts/python-train-extract.md)

In fase di apprendimento della tecnologia è consigliabile usare il servizio gratuito, ma tenere presente che il numero di pagine gratuite è limitato a 500 pagine al mese.

**Passaggio 3:** Esaminare le API REST

Usare le API seguenti per eseguire il training ed estrarre dati strutturati dai moduli.

| API REST | DESCRIZIONE |
|-----|-------------|
| Eseguire il training | Eseguire il training di un nuovo modulo per analizzare i moduli usando cinque moduli dello stesso tipo o un modulo vuoto.  |
| Analizzare  |Analizzare un singolo documento passato come flusso per estrarre le coppie chiave-valore e le tabelle dal modulo con il proprio modello personalizzato.  |

Esplorare il [documento Informazioni di riferimento sulle API del servizio REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Questo servizio viene offerto come [anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) di un servizio di Azure in base alle [condizioni dei Servizi online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Come per tutti i servizi cognitivi, gli sviluppatori che usano il servizio di riconoscimento modulo devono conoscere la politica di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Completare un argomento di [avvio rapido](quickstarts/curl-train-extract.md) per iniziare a usare le [API di riconoscimento modulo](https://aka.ms/form-recognizer/api).
