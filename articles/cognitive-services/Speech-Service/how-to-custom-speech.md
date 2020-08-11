---
title: Introduzione al servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato è un set di strumenti online che consentono di valutare e migliorare l'accuratezza del riconoscimento vocale per le applicazioni, gli strumenti e i prodotti. Per iniziare sono disponibili solo alcuni file audio di test. Per iniziare a creare un'esperienza di riconoscimento vocale personalizzata, seguire i collegamenti seguenti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: b9ac473c3864b4be06c978e7d6a3555bb4550d2a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056840"
---
# <a name="what-is-custom-speech"></a>Che cos'è Riconoscimento vocale personalizzato?

[Riconoscimento vocale personalizzato](https://aka.ms/customspeech) è un set di strumenti online che consentono di valutare e migliorare l'accuratezza del riconoscimento vocale di Microsoft per le applicazioni, gli strumenti e i prodotti. Per iniziare sono disponibili solo alcuni file audio di test. Per iniziare a creare un'esperienza di riconoscimento vocale personalizzata, seguire i collegamenti seguenti.

## <a name="whats-in-custom-speech"></a>Quali sono i Riconoscimento vocale personalizzato?

Prima di poter eseguire qualsiasi operazione con Riconoscimento vocale personalizzato, saranno necessari un account Azure e una sottoscrizione al servizio vocale. Una volta ottenuto un account, è possibile preparare i dati, eseguire il training e testare i modelli, controllare la qualità del riconoscimento, valutare l'accuratezza e infine distribuire e usare il modello di riconoscimento vocale personalizzato.

In questo diagramma vengono evidenziati i componenti che costituiscono il [portale di riconoscimento vocale personalizzato](https://aka.ms/customspeech). Usare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

![Vengono evidenziati i diversi componenti che costituiscono il portale di Riconoscimento vocale personalizzato.](./media/custom-speech/custom-speech-overview.png)

1. [Sottoscrivere e creare un progetto](#set-up-your-azure-account) : creare un account Azure e sottoscrivere il servizio di riconoscimento vocale. Questa sottoscrizione unificata consente di accedere a sintesi vocale, sintesi vocale, traduzione vocale e il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Quindi, usando la sottoscrizione al servizio vocale, creare il primo progetto Riconoscimento vocale personalizzato.

2. [Carica i dati di test](how-to-custom-speech-test-data.md) : carica i dati di test (file audio) per valutare l'offerta di sintesi vocale di Microsoft per le applicazioni, gli strumenti e i prodotti.

3. [Controllare la qualità del riconoscimento](how-to-custom-speech-inspect-data.md) : usare il [portale riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) per riprodurre l'audio caricato e controllare la qualità del riconoscimento vocale dei dati di test. Per le misurazioni quantitative, vedere [esaminare i dati](how-to-custom-speech-inspect-data.md).

4. [Valuta accuratezza](how-to-custom-speech-evaluate-data.md) : consente di valutare l'accuratezza del modello di riconoscimento vocale. Il [portale riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) fornirà una *frequenza di errori di parola*, che può essere usata per determinare se è necessario un training aggiuntivo. Se si è soddisfatti dell'accuratezza, è possibile usare direttamente le API del servizio di riconoscimento vocale. Per migliorare l'accuratezza in base a una media relativa del 5%-20%, usare la scheda **Training** nel portale per caricare dati di training aggiuntivi, ad esempio trascrizioni con etichetta umana e testo correlato.

5. [Miglioramento della precisione](how-to-custom-speech-improve-accuracy.md) : è possibile scegliere i dati di training aggiuntivi in modo strategico per migliorare la qualità del modello di riconoscimento vocale basato sullo scenario.

6. Eseguire [il training del modello](how-to-custom-speech-train-model.md) : migliorare l'accuratezza del modello di riconoscimento vocale fornendo trascrizioni scritte (10-1000 ore) e testo correlato (<200 MB) insieme ai dati di test audio. Questi dati consentono di eseguire il training del modello di riconoscimento vocale. Al termine del training, riprovare e, se si è soddisfatti del risultato, è possibile distribuire il modello.

7. [Distribuire il modello](how-to-custom-speech-deploy-model.md) : creare un endpoint personalizzato per il modello di riconoscimento vocale e usarlo in applicazioni, strumenti o prodotti.

## <a name="set-up-your-azure-account"></a>Configurare l'account Azure

Prima di poter usare il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) per creare un modello personalizzato, è necessaria una sottoscrizione al servizio di riconoscimento vocale. Seguire queste istruzioni per creare una sottoscrizione del servizio vocale standard: [creare una sottoscrizione vocale](get-started.md#new-resource).

> [!NOTE]
> Assicurarsi di creare sottoscrizioni standard (S0), le sottoscrizioni gratuite (F0) non sono supportate.

Dopo aver creato un account Azure e una sottoscrizione al servizio vocale, è necessario accedere al [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) e connettere la sottoscrizione.

1. Ottenere la chiave di sottoscrizione del servizio vocale dalla portale di Azure.
2. Accedere al [portale di riconoscimento vocale personalizzato](https://aka.ms/custom-speech).
3. Selezionare la sottoscrizione in cui è necessario lavorare e creare un progetto vocale.
4. Se si vuole modificare la sottoscrizione, usare l'icona a **cremagliera** che si trova nella parte superiore dello spostamento.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuti come dati, modelli, test ed endpoint sono organizzati in **progetti** nel [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Ogni progetto è specifico per un dominio e un paese/lingua. Ad esempio, è possibile creare un progetto per i Call Center che usano la lingua inglese nella Stati Uniti.

Per creare il primo progetto, selezionare la voce vocale **/personalizzata**, quindi fare clic su **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, verranno visualizzate quattro schede: **dati**, **testing**, **Training**e **distribuzione**. Usare i collegamenti forniti nei [passaggi successivi](#next-steps) per informazioni su come usare ogni scheda.

> [!IMPORTANT]
> Il [portale riconoscimento vocale personalizzato](https://aka.ms/custom-speech) è stato aggiornato di recente. Se sono stati creati dati, modelli, test e endpoint pubblicati in precedenza nel portale di CRIS.ai o con le API, è necessario creare un nuovo progetto nel nuovo portale per connettersi a queste entità obsolete.

## <a name="next-steps"></a>Passaggi successivi

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
