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
ms.openlocfilehash: df3db534550e709e40cc94d5f951056d93a1003e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025430"
---
# <a name="what-is-form-recognizer"></a>Informazioni su Riconoscimento modulo

Riconoscimento modulo di Azure è un servizio cognitivo che usa tecnologia di apprendimento automatico per identificare ed estrarre coppie chiave-valore e dati di tabella da documenti modulo. Restituisce quindi dati strutturati che includono le relazioni nel file originale. È possibile chiamare il modello Riconoscimento modulo personalizzato usando una semplice API REST per ridurre la complessità e integrarlo facilmente nel flusso di lavoro o in un'altra applicazione. Per iniziare sono necessari solo cinque documenti modulo o un modulo vuoto dello stesso tipo come materiale di input. È possibile ottenere risultati in modo rapido, accurato e su misura per il contenuto specifico, senza la necessità di alcun intervento manuale impegnativo né competenze approfondite di data science.

## <a name="request-access"></a>Richiedere l'accesso
Riconoscimento modulo è disponibile come anteprima ad accesso limitato. Per ottenere accesso all'anteprima, compilare e inviare il modulo di [richiesta di accesso a Riconoscimento modulo di Servizi cognitivi](https://aka.ms/FormRecognizerRequestAccess). Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà Riconoscimento modulo. Se la richiesta viene approvata dal team di Servizi cognitivi di Azure, si riceverà un messaggio di posta elettronica con le istruzioni su come accedere al servizio.

## <a name="what-it-does"></a>Risultato

Quando si inviano i dati di input, esegue il training dell'algoritmo, quindi il clustering dei moduli per tipi, individua quali tabelle e le chiavi sono presenti e apprende ad associare i valori alle chiavi e le voci alle tabelle. L'apprendimento non supervisionato consente al modello di riconoscere il layout e le relazioni tra campi e voci senza assegnazione manuale di etichette dati o impegnative attività di creazione di codice e manutenzione. Al contrario, i modelli di Machine Learning con training preliminare richiedono dati standardizzati e sono meno accurati con il materiale di input che si discosta dai formati tradizionali, come i moduli specifici del settore.

Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

## <a name="what-it-includes"></a>Cosa include

Riconoscimento modulo è disponibile come API REST. È possibile creare, eseguire il training e assegnare punteggi a un modello tramite la chiamata dell'API e, facoltativamente, è possibile eseguire il modello in un contenitore Docker locale.

## <a name="input-requirements"></a>Requisiti di input

Riconoscimento modulo funziona su documenti di input che soddisfano i requisiti seguenti:

* Formato PDF, PNG o JPG (testo o digitalizzato). I documenti PDF con testo incorporato sono preferibili perché non vi è alcuna possibilità di errore nell'estrazione e individuazione dei caratteri.
* Le dimensioni del file devono essere minori di 4 MB
* Per le immagini, le dimensioni devono essere comprese tra 50x50 e 4200x4200 pixel
* Se digitalizzati da documenti cartacei, i moduli devono essere scansioni di alta qualità
* Devono usare l'alfabeto latino (caratteri in lingua inglese)
* Dati stampati (non scritti a mano)
* Devono contenere chiavi e valori
* Le chiavi possono essere visualizzate sopra o a sinistra dei valori, ma non sotto o a destra.

In più, Riconoscimento modulo non supporta ancora i tipi di dati di input seguenti:

* Tabelle complesse (tabelle nidificate, intestazioni unite o celle e così via) 
* Caselle di controllo o pulsanti di opzione
* Documenti PDF con più di 50 pagine

## <a name="where-do-i-start"></a>Dove iniziare?

**Passaggio 1:** Creare una risorsa Riconoscimento modulo nel portale di Azure.

**Passaggio 2:** Provare una guida di avvio rapido per acquisire esperienza pratica:
* [Guida introduttiva: Eseguire il training di un modello di Riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con cURL](quickstarts/curl-train-extract.md)
* [Guida introduttiva: Eseguire il training di un modello di Riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con Python](quickstarts/python-train-extract.md)

Il servizio gratuito è consigliabile a scopi formativi, ma tenere presente che il numero di pagine gratuite è limitato a 500 al mese.

**Passaggio 3:** Esaminare l'API REST. Usare le API seguenti per eseguire il training ed estrarre i dati strutturati dai moduli.

| API REST | DESCRIZIONE |
|-----|-------------|
| Eseguire il training | Eseguire il training di un nuovo modulo per analizzare i moduli usando 5 moduli dello stesso tipo o un modulo vuoto.  |
| Analizzare  |Analizzare un singolo documento passato come flusso per estrarre le coppie chiave-valore e le tabelle dal modulo con il proprio modello personalizzato.  |

Esplorare il [documento Informazioni di riferimento sulle API del servizio REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Il servizio viene offerto come [anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) di un servizio di Azure in base alle [condizioni dei Servizi online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Si manterrà la proprietà dei dati che verranno usati solo per fornire i Servizi online come illustrato nel contratto:

### <a name="processing-of-customer-data-ownership"></a>Elaborazione dei dati dei clienti; proprietà

I Dati del Cliente verranno usati o altrimenti elaborati solo per fornire i Servizi online al cliente, incluse le finalità compatibili con la fornitura di tali servizi. Microsoft non userà o elaborerà altrimenti i Dati del Cliente e non deriverà informazioni da tali dati per finalità promozionali o finalità analoghe di tipo commerciale. Tra le parti, il Cliente conserva ogni diritto, titolo e interesse in e per i Dati del Cliente. Microsoft non acquisisce alcun diritto sui Dati del Cliente, diverso dai diritti che il Cliente concede a Microsoft per fornire i Servizi online al Cliente.

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Riconoscimento modulo devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Vedere la [guida introduttiva](quickstarts/curl-train-extract.md) per iniziare a usare l'[API Riconoscimento modulo](https://aka.ms/form-recognizer/api).
