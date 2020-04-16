---
title: Migliorare la sintesi con Custom Voice - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Custom Voice è un insieme di strumenti online che ti permettono di creare una voce riconoscibile e unica per il tuo marchio. Tutto ciò che serve per iniziare sono una manciata di file audio e le trascrizioni associate. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di sintesi vocale personalizzata.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 2aacf8881a57f0677177cd341a0ddc63224b26b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402045"
---
# <a name="get-started-with-custom-voice"></a>Introduzione a Voce personalizzata

[Custom Voice](https://aka.ms/customvoice) è un insieme di strumenti online che ti permettono di creare una voce riconoscibile e unica per il tuo marchio. Tutto ciò che serve per iniziare sono una manciata di file audio e le trascrizioni associate. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di sintesi vocale personalizzata.

## <a name="whats-in-custom-voice"></a>Cosa c'è in Custom Voice?

Prima di iniziare con Voice personalizzato, sono necessari un account Azure e una sottoscrizione al servizio di riconoscimento vocale. Dopo aver creato un account, è possibile preparare i dati, eseguire il training e testare i modelli, valutare la qualità vocale e infine distribuire il modello vocale personalizzato.

Nel diagramma seguente vengono illustrati i passaggi per creare un modello vocale personalizzato utilizzando il [portale vocale personalizzato](https://aka.ms/customvoice). Usa i link per saperne di più.

![Diagramma dell'architettura di Custom Voice](media/custom-voice/custom-voice-diagram.png)

1. [Sottoscrivere e creare un progetto:](#set-up-your-azure-account) creare un account Azure e creare una sottoscrizione al servizio di riconoscimento vocale. Questo abbonamento unificato consente di accedere alla sintesi vocale, alla sintesi vocale, alla traduzione vocale e al portale Custom Voice. Quindi, usando la sottoscrizione al servizio di riconoscimento vocale, crea il primo progetto Custom Voice.

2. [Caricare dati](how-to-custom-voice-create-voice.md#upload-your-datasets) - Caricare dati (audio e testo) utilizzando il portale vocale personalizzato o l'API Custom Voice. Dal portale, è possibile esaminare e valutare i punteggi di pronuncia e i rapporti segnale-rumore. Per ulteriori informazioni, vedere [Come preparare i dati per Custom Voice](how-to-custom-voice-prepare-data.md).

3. [Eseguire il training del modello:](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) usare i dati per creare un modello vocale di sintesi vocale personalizzato. È possibile eseguire il training di un modello in lingue diverse. Dopo il training, testare il modello e, se si è soddisfatti del risultato, è possibile distribuire il modello.

4. [Distribuire il modello:](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) creare un endpoint personalizzato per il modello vocale di sintesi vocale e utilizzarlo per la sintesi vocale in prodotti, strumenti e applicazioni.

## <a name="custom-neural-voices"></a>Voci neurali personalizzate

La funzionalità di personalizzazione della voce neurale è attualmente in anteprima pubblica, limitata a clienti selezionati. Compila questo [modulo di richiesta](https://go.microsoft.com/fwlink/?linkid=2108737) per iniziare.

> [!NOTE]
> Nell'ambito dell'impegno di Microsoft nella progettazione di immediati responsabile, il nostro intento è proteggere i diritti degli individui e della società e promuovere interazioni uomo-computer trasparenti. Per questo motivo, Custom Neural Voice non è generalmente disponibile per tutti i clienti. Puoi accedere alla tecnologia solo dopo che le tue applicazioni sono state esaminate e ti sei impegnato a utilizzarla in linea con i nostri principi etici. Ulteriori informazioni sul nostro processo di [gating delle applicazioni](https://aka.ms/custom-neural-gating-overview).

## <a name="set-up-your-azure-account"></a>Configurare l'account AzureSet up your Azure account

È necessaria una sottoscrizione al servizio di riconoscimento vocale prima di poter usare il portale di riconoscimento vocale personalizzato per creare un modello personalizzato. Seguire queste istruzioni per creare una sottoscrizione al servizio di riconoscimento vocale in Azure.Follow these instructions to create a Speech service subscription in Azure. Se non si dispone di un account Azure, è possibile iscriversi per crearne uno nuovo.  

Dopo aver creato un account Azure e una sottoscrizione al servizio di riconoscimento vocale, è necessario accedere al portale vocale personalizzato e connettere la sottoscrizione.

1. Ottenere la chiave di sottoscrizione del servizio di riconoscimento vocale dal portale di Azure.Get your Speech service subscription key from the Azure portal.
2. Accedere al [portale Voce personalizzata](https://aka.ms/custom-voice).
3. Selezionare la sottoscrizione e creare un progetto di riconoscimento vocale.
4. Se si vuole passare a un altro abbonamento a Speech, usare l'icona a forma di ingranaggio nella barra di spostamento superiore.

> [!NOTE]
> Il servizio Custom Voice NON supporta la chiave di prova gratuita di 30 giorni. È necessario disporre di una chiave F0 o S0 creata in Azure prima di poter usare il servizio.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuti come dati, modelli, test ed endpoint sono organizzati in **progetti** nel portale Custom Voice. Ogni progetto è specifico per un paese / lingua e il sesso della voce che si desidera creare. Ad esempio, è possibile creare un progetto per una voce femminile per i chat bot del servizio clienti che utilizzano l'inglese negli Stati Uniti (en-US).

Per creare il primo progetto, selezionare la scheda **Sintesi vocale/Voce personalizzata,** quindi fare clic su **Nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, verranno visualizzate quattro schede: **Dati**, **Formazione**, **Testing**e **Distribuzione**. Utilizzare i collegamenti forniti in [Passaggi successivi](#next-steps) per informazioni su come utilizzare ogni scheda.

> [!IMPORTANT]
> Il [portale Custom Voice](https://aka.ms/custom-voice) è stato aggiornato di recente! Se sono stati creati dati, modelli, test ed endpoint pubblicati precedenti nel portale CRIS.ai o con le API, è necessario creare un nuovo progetto nel nuovo portale per connettersi a queste entità precedenti.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare i dati vocali personalizzati](how-to-custom-voice-prepare-data.md)
- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guida: Registrare i campioni vocali](record-custom-voice-samples.md)
