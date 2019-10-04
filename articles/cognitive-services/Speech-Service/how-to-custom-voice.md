---
title: Che cos'è una voce personalizzata? -Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Custom Voice è un set di strumenti online che ti permettono di creare una voce unica e riconoscibile per il tuo marchio. Per iniziare sono disponibili solo alcuni file audio e le trascrizioni associate. Per iniziare a creare un'esperienza di riconoscimento vocale personalizzata, seguire i collegamenti seguenti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 636c397001a3130c60fc2bf498339ad058aca374
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959403"
---
# <a name="get-started-with-custom-voice"></a>Introduzione a Voce personalizzata

[Custom Voice](https://aka.ms/customvoice) è un set di strumenti online che ti permettono di creare una voce unica e riconoscibile per il tuo marchio. Per iniziare sono disponibili solo alcuni file audio e le trascrizioni associate. Per iniziare a creare un'esperienza di sintesi vocale personalizzata, seguire i collegamenti seguenti.

## <a name="whats-in-custom-voice"></a>Che cos'è la voce personalizzata?

Prima di iniziare con la voce personalizzata, sono necessari un account Azure e una sottoscrizione di servizi vocali. Dopo aver creato un account, è possibile preparare i dati, eseguire il training e testare i modelli, valutare la qualità vocale e infine distribuire il modello Voice personalizzato.

Il diagramma seguente illustra i passaggi per creare un modello Voice personalizzato usando il [portale vocale personalizzato](https://aka.ms/customvoice). Usare i collegamenti per altre informazioni.

![Diagramma dell'architettura vocale personalizzata](media/custom-voice/custom-voice-diagram.png)

1.  [Sottoscrivere e creare un progetto](#set-up-your-azure-account) : creare un account Azure e creare una sottoscrizione di servizi vocali. Questa sottoscrizione unificata consente di accedere a sintesi vocale, sintesi vocale, traduzione vocale e il portale vocale personalizzato. Quindi, usando la sottoscrizione di servizi vocali, creare il primo progetto vocale personalizzato.

2.  [Caricare dati](how-to-custom-voice-create-voice.md#upload-your-datasets) : caricare dati (audio e testo) usando il portale vocale personalizzato o l'API Voice personalizzata. Dal portale è possibile esaminare e valutare i punteggi di pronuncia e i rapporti tra segnali e rumori. Per ulteriori informazioni, vedere [come preparare i dati per la voce personalizzata](how-to-custom-voice-prepare-data.md).

3.  Eseguire il [training del modello](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) : usare i dati per creare un modello vocale personalizzato per la sintesi vocale. È possibile eseguire il training di un modello in lingue diverse. Dopo il training, testare il modello e, se si è soddisfatti del risultato, è possibile distribuire il modello.

4.  [Distribuire il modello](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) : creare un endpoint personalizzato per il modello vocale da testo a riconoscimento vocale e usarlo per la sintesi vocale in prodotti, strumenti e applicazioni.

## <a name="set-up-your-azure-account"></a>Configurare l'account Azure

Per poter usare il portale Riconoscimento vocale personalizzato per creare un modello personalizzato, è necessaria una sottoscrizione di servizi vocali. Seguire queste istruzioni per creare una sottoscrizione di servizi vocali in Azure. Se non si dispone di un account Azure, è possibile iscriversi per un nuovo account.  

Dopo aver creato un account Azure e una sottoscrizione di servizi vocali, sarà necessario accedere al portale vocale personalizzato e connettere la sottoscrizione.

1. Ottenere la chiave di sottoscrizione di servizi vocali dal portale di Azure.
2. Accedere al [portale vocale personalizzato](https://aka.ms/custom-voice).
3. Selezionare la sottoscrizione e creare un progetto vocale.
4. Se si vuole passare a un'altra sottoscrizione vocale, usare l'icona a cremagliera che si trova nella parte superiore dello spostamento.

> [!NOTE]
> Il servizio Voice personalizzato non supporta la chiave di valutazione gratuita di 30 giorni. Prima di poter usare il servizio, è necessario disporre di una chiave F0 o S0 creata in Azure.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuto come dati, modelli, test ed endpoint sono organizzati in **progetti** nel portale vocale personalizzato. Ogni progetto è specifico per un paese/lingua e il sesso della voce che si vuole creare. Ad esempio, è possibile creare un progetto per una voce femminile per i bot della chat del Call Center che usano la lingua inglese nella Stati Uniti (en-US).

Per creare il primo progetto, selezionare la scheda **voce vocale/voce personalizzata** , quindi fare clic su **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, vengono visualizzate quattro schede: **Dati**, **Training**, **test**e **distribuzione**. Usare i collegamenti forniti nei [passaggi successivi](#next-steps) per informazioni su come usare ogni scheda.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare i dati vocali personalizzati](how-to-custom-voice-prepare-data.md)
- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guide: Registrare gli esempi di voce @ no__t-0
