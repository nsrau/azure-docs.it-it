---
title: Introduzione al riconoscimento vocale personalizzato - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il riconoscimento personalizzato è un insieme di strumenti online che consentono di valutare e migliorare la precisione del testo per le applicazioni, gli strumenti e i prodotti. Tutto ciò che serve per iniziare sono una manciata di file audio di prova. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di sintesi vocale personalizzata.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: e30587ed4d09ba792516ebc7b16529a6ee5ac57c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918955"
---
# <a name="what-is-custom-speech"></a>Che cos'è il riconoscimento vocale personalizzato?

[Il riconoscimento vocale personalizzato](https://aka.ms/customspeech) è un insieme di strumenti online che consentono di valutare e migliorare l'accuratezza del riconoscimento vocale di Microsoft per applicazioni, strumenti e prodotti. Tutto ciò che serve per iniziare sono una manciata di file audio di prova. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di sintesi vocale personalizzata.

## <a name="whats-in-custom-speech"></a>Cosa c'è in Custom Speech?

Prima di poter eseguire qualsiasi operazione con il riconoscimento vocale personalizzato, sono necessari un account Azure e una sottoscrizione al servizio di riconoscimento vocale. Una volta ottenuto un account, è possibile preparare i dati, eseguire il training e testare i modelli, controllare la qualità del riconoscimento, valutare l'accuratezza e infine distribuire e utilizzare il modello di sintesi vocale personalizzato.

Questo diagramma evidenzia le parti che costituiscono il [portale di riconoscimento vocale personalizzato](https://aka.ms/customspeech). Utilizzare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

![Evidenzia i diversi componenti che costituiscono il portale di riconoscimento vocale personalizzato.](./media/custom-speech/custom-speech-overview.png)

1. [Sottoscrivere e creare un progetto:](#set-up-your-azure-account) creare un account Azure e sottoscrivere il servizio di riconoscimento vocale. Questa sottoscrizione unificata consente di accedere alla sintesi vocale, alla sintesi vocale, alla traduzione vocale e al [portale di riconoscimento vocale personalizzato.](https://speech.microsoft.com/customspeech) Quindi, usando la sottoscrizione al servizio di riconoscimento vocale, crea il primo progetto di riconoscimento vocale personalizzato.

2. [Caricare i dati](how-to-custom-speech-test-data.md) di test: caricare i dati di test (file audio) per valutare l'offerta di sintesi vocale di Microsoft per applicazioni, strumenti e prodotti.

3. [Esaminare la qualità del riconoscimento:](how-to-custom-speech-inspect-data.md) utilizzare il portale [di riconoscimento personalizzato](https://speech.microsoft.com/customspeech) per riprodurre l'audio caricato e controllare la qualità del riconoscimento vocale dei dati di test. Per le misurazioni quantitative, vedere [Esaminare i dati](how-to-custom-speech-inspect-data.md).

4. [Valutare l'accuratezza](how-to-custom-speech-evaluate-data.md) - Valutare l'accuratezza del modello di sintesi vocale. Il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) fornirà una *frequenza*di errore di Word , che può essere utilizzata per determinare se è necessaria una formazione aggiuntiva. Se si è soddisfatti della precisione, è possibile utilizzare direttamente le API del servizio di riconoscimento vocale. Se desideri migliorare la precisione di una media relativa tra il 5% e il 20%, usa la scheda **Formazione** nel portale per caricare dati di training aggiuntivi, ad esempio trascrizioni con etichetta umana e testo correlato.

5. [Eseguire il training del modello](how-to-custom-speech-train-model.md) - Migliorare l'accuratezza del modello di sintesi vocale fornendo trascrizioni scritte (10-1.000 ore) e testo correlato (<200 MB) insieme ai dati di test audio. Questi dati consentono di eseguire il training del modello di sintesi vocale. Dopo il training, il test e se si è soddisfatti del risultato, è possibile distribuire il modello.

6. [Distribuire il modello:](how-to-custom-speech-deploy-model.md) creare un endpoint personalizzato per il modello di sintesi vocale e utilizzarlo nelle applicazioni, negli strumenti o nei prodotti.

## <a name="set-up-your-azure-account"></a>Configurare l'account AzureSet up your Azure account

È necessaria una sottoscrizione al servizio di riconoscimento vocale prima di poter usare il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) per creare un modello personalizzato. Seguire queste istruzioni per creare una sottoscrizione al servizio di riconoscimento vocale standard: Creare una sottoscrizione vocale di [riconoscimento vocale.](get-started.md#new-resource)

> [!NOTE]
> Assicurarsi di creare abbonamenti standard (S0), gli abbonamenti di prova gratuita (F0) non sono supportati.

Dopo aver creato un account Azure e una sottoscrizione al servizio di riconoscimento vocale, è necessario accedere al [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) e connettere la sottoscrizione.

1. Ottenere la chiave di sottoscrizione del servizio di riconoscimento vocale dal portale di Azure.Get your Speech service subscription key from the Azure portal.
2. Accedere al [portale di riconoscimento vocale personalizzato](https://aka.ms/custom-speech).
3. Selezionare la sottoscrizione su cui lavorare e creare un progetto di riconoscimento vocale.
4. Se si vuole modificare l'abbonamento, usare l'icona a forma di **ingranaggio** nella barra di spostamento superiore.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuto come dati, modelli, test ed endpoint sono organizzati in **progetti** nel [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Ogni progetto è specifico di un dominio e di un paese/lingua. Ad esempio, è possibile creare un progetto per i servizi clienti che utilizzano l'inglese negli Stati Uniti.

Per creare il primo progetto, selezionare la funzionalità **sintesi vocale/sintesi vocale personalizzata**, quindi fare clic su **Nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, dovrebbero essere visualizzate quattro schede: **Dati**, **Testing**, **Training**e **Deployment**. Utilizzare i collegamenti forniti in [Passaggi successivi](#next-steps) per informazioni su come utilizzare ogni scheda.

> [!IMPORTANT]
> Il [portale di riconoscimento vocale personalizzato](https://aka.ms/custom-speech) è stato aggiornato di recente! Se sono stati creati dati, modelli, test ed endpoint pubblicati precedenti nel portale CRIS.ai o con le API, è necessario creare un nuovo progetto nel nuovo portale per connettersi a queste entità precedenti.

## <a name="next-steps"></a>Passaggi successivi

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Ispezionare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
