---
title: Iniziare con il riconoscimento vocale personalizzato - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato è un set di strumenti online che consentono di valutare e migliorare l'accuratezza per il riconoscimento vocale di Microsoft per applicazioni, strumenti e prodotti. Tutto ciò che serve per iniziare sono un numero limitato di file audio di test. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di riconoscimento vocale personalizzata.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ab33feb1ffdbced193afaba8f52719b3c215652f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511131"
---
# <a name="what-is-custom-speech"></a>Che cos'è riconoscimento vocale personalizzato?

[Riconoscimento vocale personalizzato](https://aka.ms/custom-speech) è un set di strumenti online che consentono di valutare e migliorare l'accuratezza per il riconoscimento vocale di Microsoft per applicazioni, strumenti e prodotti. Tutto ciò che serve per iniziare sono un numero limitato di file audio di test. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di riconoscimento vocale personalizzata.

## <a name="whats-in-custom-speech"></a>Che cos'è riconoscimento vocale personalizzato?

Prima di eseguire qualsiasi operazione con il riconoscimento vocale personalizzato, è necessario un account Azure e una sottoscrizione di servizi di riconoscimento vocale. Dopo che hai un account, è possibile prepara i dati, eseguire il training e testare i modelli, controllare la qualità del riconoscimento, valutare accuratezza e infine distribuire e usare il modello di riconoscimento vocale personalizzato.

Il diagramma evidenzia le parti che compongono il portale di riconoscimento vocale personalizzato. Usare i collegamenti seguenti per altre informazioni su ogni passaggio.

![Evidenzia i diversi componenti che costituiscono il portale di riconoscimento vocale personalizzato.](./media/custom-speech/custom-speech-overview.png)

1. [Iscriviti e crea un progetto](#set-up-your-azure-account) : creare un account Azure e sottoscrivere i servizi di riconoscimento vocale. Questa sottoscrizione unificata consente di accedere a per il riconoscimento vocale, sintesi vocale, traduzione vocale e il portale di riconoscimento vocale personalizzato. Quindi, usando la sottoscrizione di servizi di riconoscimento vocale, creare il primo progetto di riconoscimento vocale personalizzato.

2. [Caricare dati di test](how-to-custom-speech-test-data.md) -caricare dati di test (file audio) per la valutazione per il riconoscimento di Microsoft vocale che offre alle applicazioni, strumenti e prodotti.

3. [Controllare la qualità del riconoscimento](how-to-custom-speech-inspect-data.md) -usare il portale di riconoscimento vocale personalizzato a riprodurre l'audio caricato e analizzare la qualità di riconoscimento vocale dei dati di test. Per le misure quantitative, vedere [analizzare dati](how-to-custom-speech-inspect-data.md).

4. [Valutare l'accuratezza](how-to-custom-speech-evaluate-data.md) -valutare l'accuratezza del modello per il riconoscimento vocale. Il portale di riconoscimento vocale personalizzato fornirà una *frequenza degli errori di Word*, che consente di determinare se sia necessario training aggiuntivi. Se si è soddisfatti con la precisione, è possibile usare direttamente le API del servizio riconoscimento vocale. Se si vuole migliorare l'accuratezza per una relativa media del 5% - 20%, usare il **Training** scheda nel portale per caricare i dati di training aggiuntivi, ad esempio un'etichetta umane trascrizioni e testo correlato.

5. [Il training del modello](how-to-custom-speech-train-model.md) : migliorare l'accuratezza del modello di riconoscimento vocale, fornendo le trascrizioni scritte (10-1.000 ore) e testo relativo (< 200 MB) e l'audio di dati di test. Questi dati consentono di training del modello di riconoscimento vocale. Dopo il training, rieseguire il test, e se si è soddisfatti del risultato, è possibile distribuire il modello.

6. [Distribuire il modello](how-to-custom-speech-deploy-model.md) : creare un endpoint personalizzato per il modello di riconoscimento vocale e usarlo nelle applicazioni, strumenti o prodotti.

## <a name="set-up-your-azure-account"></a>Configurare l'account di Azure

Prima di poter usare il portale di riconoscimento vocale personalizzato per creare un modello personalizzato, è necessaria una sottoscrizione di servizi di riconoscimento vocale. Seguire queste istruzioni per creare una sottoscrizione standard di servizi di riconoscimento vocale: [Creare una sottoscrizione di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Assicurarsi di creare le sottoscrizioni standard (S0), le sottoscrizioni di valutazione gratuita (F0) non sono supportate.

Dopo aver creato un account Azure e una sottoscrizione di servizi di riconoscimento vocale, è necessario accedere al portale di riconoscimento vocale personalizzato e connettere la sottoscrizione.

1. Ottenere la chiave di sottoscrizione di servizi di riconoscimento vocale dal portale di Azure.
2. Accedi per il [portale di riconoscimento vocale personalizzato](https://aka.ms/custom-speech).
3. Selezionare la sottoscrizione che si desidera utilizzare e per creare un progetto di riconoscimento vocale.
4. Se si desidera modificare la sottoscrizione, usare il **rappresenta dalla ruota dentata** icona che si trova nel riquadro di spostamento superiore.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuto, ad esempio i dati, modelli, i test e gli endpoint sono organizzati in **progetti** nel portale di riconoscimento vocale personalizzato. Ogni progetto è specifico di un paese/linguaggio e il dominio. Ad esempio, è possibile creare un progetto per i call center che usano l'inglese parlato negli Stati Uniti.

Per creare il primo progetto, selezionare la **riconoscimento vocale da testo/personalizzato**, quindi fare clic su **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, sono presenti quattro schede: **I dati**, **Testing**, **Training**, e **distribuzione**. Usare i collegamenti disponibili nella [passaggi successivi](#next-steps) per imparare a utilizzare ogni scheda.

## <a name="next-steps"></a>Passaggi successivi

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
