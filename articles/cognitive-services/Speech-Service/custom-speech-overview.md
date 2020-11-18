---
title: Panoramica di Riconoscimento vocale personalizzato-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato è un set di strumenti online che consentono di valutare e migliorare l'accuratezza del riconoscimento vocale per le applicazioni, gli strumenti e i prodotti. Per iniziare sono disponibili solo alcuni file audio di test. Per iniziare a creare un'esperienza di riconoscimento vocale personalizzata, seguire i collegamenti seguenti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 6dbe97d615753f0a90c8ba80aa7afa6dafa15eb2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658469"
---
# <a name="what-is-custom-speech"></a>Che cos'è il servizio Riconoscimento vocale personalizzato?

[Riconoscimento vocale personalizzato](https://aka.ms/customspeech) è un set di strumenti basati su interfaccia utente che consentono di valutare e migliorare l'accuratezza del riconoscimento vocale di Microsoft per le applicazioni e i prodotti. Per iniziare sono disponibili solo alcuni file audio di test. Per iniziare a creare un'esperienza di riconoscimento vocale personalizzata, seguire i collegamenti seguenti.

## <a name="whats-in-custom-speech"></a>Quali sono i Riconoscimento vocale personalizzato?

Prima di poter eseguire qualsiasi operazione con Riconoscimento vocale personalizzato, saranno necessari un account Azure e una sottoscrizione al servizio vocale. Una volta ottenuto un account, è possibile preparare i dati, eseguire il training e testare i modelli, controllare la qualità del riconoscimento, valutare l'accuratezza e infine distribuire e usare il modello di riconoscimento vocale personalizzato.

In questo diagramma vengono evidenziati i componenti che costituiscono il [portale di riconoscimento vocale personalizzato](https://aka.ms/customspeech). Usare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

![Vengono evidenziati i diversi componenti che costituiscono il portale di Riconoscimento vocale personalizzato.](./media/custom-speech/custom-speech-overview.png)

1. [Sottoscrivere e creare un progetto](#set-up-your-azure-account) : creare un account Azure e sottoscrivere il servizio di riconoscimento vocale. Questa sottoscrizione unificata consente di accedere a sintesi vocale, sintesi vocale, traduzione vocale e il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Quindi, usando la sottoscrizione al servizio vocale, creare il primo progetto Riconoscimento vocale personalizzato.

1. [Carica i dati di test](how-to-custom-speech-test-data.md) : carica i dati di test (file audio) per valutare l'offerta di sintesi vocale di Microsoft per le applicazioni, gli strumenti e i prodotti.

1. [Controllare la qualità del riconoscimento](how-to-custom-speech-inspect-data.md) : usare il [portale riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) per riprodurre l'audio caricato e controllare la qualità del riconoscimento vocale dei dati di test. Per le misurazioni quantitative, vedere [esaminare i dati](how-to-custom-speech-inspect-data.md).

1. [Valutare e migliorare l'accuratezza](how-to-custom-speech-evaluate-data.md) : valutare e migliorare l'accuratezza del modello di riconoscimento vocale. Il [portale riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) fornirà una *frequenza di errori di parola*, che può essere usata per determinare se è necessario un training aggiuntivo. Se si è soddisfatti dell'accuratezza, è possibile usare direttamente le API del servizio di riconoscimento vocale. Per migliorare l'accuratezza in base a una media relativa del 5%-20%, usare la scheda **Training** nel portale per caricare dati di training aggiuntivi, ad esempio trascrizioni con etichetta umana e testo correlato.

1. Eseguire il [training e distribuire un modello](how-to-custom-speech-train-model.md) : migliorare l'accuratezza del modello di sintesi vocale fornendo trascrizioni scritte (10-1000 ore) e testo correlato (<200 MB) insieme ai dati di test audio. Questi dati consentono di eseguire il training del modello di riconoscimento vocale. Al termine del training, riprovare e, se si è soddisfatti del risultato, è possibile distribuire il modello in un endpoint personalizzato.

## <a name="set-up-your-azure-account"></a>Configurare l'account Azure

Per poter usare il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) per creare un modello personalizzato, è necessario disporre di una sottoscrizione di Azure e del servizio di riconoscimento vocale. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Assicurarsi di creare sottoscrizioni standard (S0), le sottoscrizioni gratuite (F0) non sono supportate.

Dopo aver creato un account Azure e una sottoscrizione al servizio vocale, è necessario accedere al [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) e connettere la sottoscrizione.

1. Accedere al [portale di riconoscimento vocale personalizzato](https://aka.ms/custom-speech).
1. Selezionare la sottoscrizione in cui è necessario lavorare e creare un progetto vocale.
1. Se si vuole modificare la sottoscrizione, usare l'icona a **cremagliera** che si trova nella parte superiore dello spostamento.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuti come dati, modelli, test ed endpoint sono organizzati in **progetti** nel [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Ogni progetto è specifico per un dominio e un paese/lingua. Ad esempio, è possibile creare un progetto per i Call Center che usano la lingua inglese nella Stati Uniti.

Per creare il primo progetto, selezionare la voce vocale **/personalizzata**, quindi fare clic su **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, verranno visualizzate quattro schede: **dati**, **testing**, **Training** e **distribuzione**. Usare i collegamenti forniti nei [passaggi successivi](#next-steps) per informazioni su come usare ogni scheda.

> [!IMPORTANT]
> Il [portale riconoscimento vocale personalizzato](https://aka.ms/custom-speech) è stato aggiornato di recente. Se sono stati creati dati, modelli, test e endpoint pubblicati in precedenza nel portale di CRIS.ai o con le API, è necessario creare un nuovo progetto nel nuovo portale per connettersi a queste entità obsolete.

## <a name="model-lifecycle"></a>Ciclo di vita del modello

Il riconoscimento vocale personalizzato USA sia **modelli di base** che **modelli personalizzati**. Ogni lingua dispone di uno o più **modelli di base**. In genere, quando un nuovo modello di riconoscimento vocale viene rilasciato al servizio di riconoscimento vocale normale, viene anche importato nel servizio Riconoscimento vocale personalizzato come nuovo **modello di base**. Vengono in genere aggiornati ogni 3-6 mesi e i modelli meno recenti diventano meno utili nel corso del tempo, perché il modello più recente ha in genere una maggiore precisione.

Al contrario, i **modelli personalizzati** vengono creati adattando un modello di base scelto a uno scenario specifico del cliente. È possibile utilizzare un modello personalizzato specifico per un periodo di tempo prolungato dopo che è stato raggiunto un modello che soddisfi le proprie esigenze, ma è consigliabile eseguire periodicamente l'aggiornamento al modello di base più recente e ripetere il training con dati aggiuntivi.

Altri termini chiave correlati al ciclo di vita del modello includono:

* **Adattamento**: acquisizione di un modello di base e personalizzazione del proprio dominio/scenario tramite dati di testo e/o dati audio
* **Decodifica**: uso di un modello ed esecuzione del riconoscimento vocale (decodifica dell'audio nel testo)
* **Endpoint**: distribuzione specifica dell'utente di un modello di base o di un modello personalizzato accessibile *solo* da un determinato utente.

### <a name="expiration-timeline"></a>Sequenza temporale scadenza

Poiché nuovi modelli e nuove funzionalità diventano disponibili e meno recenti, i modelli meno accurati vengono ritirati, vedere le sequenze temporali seguenti per la scadenza del modello e dell'endpoint:

**Modelli di base** 

* Adattamento: disponibile per 1 anno. Una volta importato il modello, è disponibile per 1 anno per la creazione di modelli personalizzati. Dopo 1 anno, è necessario creare nuovi modelli personalizzati da una versione più recente del modello di base.  
* Decodifica: disponibile per 2 anni dopo l'importazione. Ciò significa che è possibile creare un endpoint e usare la trascrizione batch per 2 anni con questo modello. 
* Endpoint: disponibile nella stessa sequenza temporale della decodifica

**Modelli personalizzati**

* Decodifica: disponibile per 2 anni dopo la creazione del modello. Ciò significa che è possibile usare il modello personalizzato per 2 anni (batch/tempo reale/test) dopo che è stato creato. Dopo 2 anni, **è necessario ripetere il training del modello**, perché la maggior parte delle volte il modello di base sarà deprecato per l'adattamento.  
* Endpoint: disponibile nella stessa sequenza temporale della decodifica

Quando un modello di base o un modello personalizzato scade, viene sempre eseguito il fallback alla **versione più recente del modello di base**. Pertanto, l'implementazione non verrà mai interrotta, ma potrebbe diventare meno accurata per *i dati specifici se i* modelli personalizzati raggiungono la scadenza. È possibile visualizzare la scadenza per un modello nelle posizioni seguenti nel portale di Riconoscimento vocale personalizzato:

* Riepilogo del training del modello
* Dettagli di training del modello
* Riepilogo della distribuzione
* Dettagli distribuzione

È anche possibile controllare le date di scadenza tramite [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) le [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) API di riconoscimento vocale e personalizzate sotto la `deprecationDates` proprietà nella risposta JSON.

Si noti che è possibile aggiornare il modello in un endpoint di riconoscimento vocale personalizzato senza tempi di inattività cambiando il modello usato dall'endpoint nella sezione relativa alla distribuzione del portale vocale personalizzato oppure tramite l'API riconoscimento vocale personalizzato.

## <a name="next-steps"></a>Passaggi successivi

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare e migliorare l'accuratezza del modello](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training di un modello e distribuirlo](how-to-custom-speech-train-model.md)
