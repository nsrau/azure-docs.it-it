---
title: Informazioni su Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare Riconoscimento modulo per analizzare dati di modulo e tabella.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: e064faf3017b95cb3a5f3d9b89f178fb7f846766
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592613"
---
# <a name="what-is-form-recognizer"></a>Informazioni su Riconoscimento modulo

Riconoscimento modulo di Azure è un servizio cognitivo che usa tecnologia di Machine Learning per identificare ed estrarre coppie chiave-valore e dati di tabella da documenti modulo. Restituisce quindi dati strutturati che includono le relazioni nel file originale. È possibile chiamare il modello di riconoscimento modulo personalizzato usando una semplice API REST per ridurre la complessità e integrarlo facilmente nel flusso di lavoro o nell'applicazione. Per iniziare sono necessari solo cinque documenti modulo compilati oppure due moduli compilati più un modulo vuoto dello stesso tipo del materiale di input. È possibile ottenere in modo rapido risultati accurati e su misura per il contenuto specifico, senza alcun intervento manuale impegnativo o competenze approfondite di data science.

## <a name="custom-models"></a>Modelli personalizzati

Il modello personalizzato di Riconoscimento modulo esegue il training in base ai dati personali e, per iniziare, sono necessari solo cinque moduli di input di esempio. Quando si inviano i dati di input, l'algoritmo esegue il clustering dei moduli per tipo, individua le tabelle e le chiavi presenti e associa i valori alle chiavi e le voci alle tabelle. Restituisce quindi dati strutturati che includono le relazioni nel file originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

L'apprendimento non supervisionato consente al modello di riconoscere il layout e le relazioni tra campi e voci senza assegnazione manuale di etichette dati o impegnative attività di creazione di codice e manutenzione. Al contrario, i modelli di Machine Learning con training preliminare richiedono dati standardizzati sono meno accurati se usati con materiale di input che si discosta dai formati tradizionali, come i moduli specifici del settore.

## <a name="pre-built-receipt-model"></a>Modello di ricevuta predefinito

Riconoscimento modulo include anche un modello per la lettura delle ricevute di vendita. Questo modello consente di estrarre le informazioni principali, come la data e l'ora della transazione, i dati del rivenditore, gli importi di imposte e totali e altro ancora. Il training del modello di ricevute predefinito è stato inoltre eseguito in modo che il modello possa riconoscere e restituire tutto il testo di una ricevuta.

## <a name="what-it-includes"></a>Cosa include

Riconoscimento modulo è disponibile come API REST. È possibile creare, eseguire il training e assegnare un punteggio a un modello personalizzato oppure accedere al modello predefinito richiamando queste API. Se necessario, è possibile eseguire il training e avviare modelli personalizzati anche in un contenitore Docker locale.

## <a name="input-requirements-custom-model"></a>Requisiti di input (modello personalizzato)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>Richiedere l'accesso

Il riconoscimento modulo è disponibile in anteprima ad accesso limitato. Per avere accesso all'anteprima, completare e inviare il modulo di [richiesta di accesso al riconoscimento modulo](https://aka.ms/FormRecognizerRequestAccess). Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà Riconoscimento modulo. Se la richiesta viene approvata dal team di Servizi cognitivi di Azure, si riceverà un messaggio di posta elettronica con le istruzioni per l'accesso al servizio.

## <a name="where-do-i-start"></a>Dove iniziare?

**Passaggio 1:** Creare una risorsa Riconoscimento modulo nel portale di Azure.

**Passaggio 2:** Seguire uno dei seguenti argomenti di avvio rapido per usare l'API REST:
* [Guida introduttiva: Eseguire il training di un modello di riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con cURL](quickstarts/curl-train-extract.md)
* [Guida introduttiva: Eseguire il training di un modello di riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con Python](quickstarts/python-train-extract.md)
* [Guida introduttiva: Estrarre i dati delle ricevute usando cURL](quickstarts/curl-receipts.md)
* [Guida introduttiva: Estrarre i dati delle ricevute usando Python](quickstarts/python-receipts.md)

È consigliabile usare il servizio gratuito mentre si acquisisce familiarità con questa tecnologia. Tenere presente che il numero di pagine gratuite è limitato a 500 al mese.

**Passaggio 3:** Esaminare le API REST

Usare le API seguenti per eseguire il training ed estrarre dati strutturati dai moduli.

|||
|---|---|
| Eseguire il training del modello| Eseguire il training di un nuovo modulo per analizzare i moduli usando cinque moduli dello stesso tipo. In alternativa, eseguire il training di un modulo vuoto e di due moduli compilati.  |
| Analyze Form (Analisi modulo) |Analizzare un singolo documento passato come flusso per estrarre le coppie chiave-valore e le tabelle dal modulo con il proprio modello personalizzato.  |
| Analyze Receipt (Analisi ricevuta) |Analizzare una singola ricevuta per estrarre le informazioni principali e eventuale testo aggiuntivo contenuto nella ricevuta.|

Per altre informazioni, esplorare la [documentazione di riferimento dell'API REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Questo servizio viene offerto come [anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) di un servizio di Azure in base alle [condizioni dei Servizi online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Come per tutti i servizi cognitivi, gli sviluppatori che usano il servizio di riconoscimento modulo devono conoscere la politica di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Completare un argomento di [avvio rapido](quickstarts/curl-train-extract.md) per iniziare a usare le [API di riconoscimento modulo](https://aka.ms/form-recognizer/api).
