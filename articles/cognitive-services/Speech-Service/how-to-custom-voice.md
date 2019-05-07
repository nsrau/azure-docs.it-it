---
title: Che cos'è una voce personalizzata? -Servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Vocali personalizzati sono un set di strumenti online che consentono di creare una voce riconoscibile, uno di tipo per il tuo marchio. Tutto ciò che serve per iniziare sono un numero limitato di file audio e trascrizioni di associato. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di riconoscimento vocale personalizzata.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 8cf9dc6cbfc96448462aac3a64807f8beb6036ad
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156912"
---
# <a name="get-started-with-custom-voice"></a>Introduzione a vocale personalizzato

Vocali personalizzati sono un set di strumenti online che consentono di creare una voce riconoscibile, uno di tipo per il tuo marchio. Tutto ciò che serve per iniziare sono un numero limitato di file audio e trascrizioni di associato. Seguire i collegamenti seguenti per iniziare a creare un'esperienza di sintesi vocale personalizzata.

## <a name="whats-in-custom-voice"></a>Che cos'è vocali personalizzati?

Prima di iniziare con vocale personalizzato, è necessario un account Azure e una sottoscrizione di servizi di riconoscimento vocale. Dopo aver creato un account, è possibile preparare i dati, eseguire il training e testare i modelli, valutare la qualità della voce e infine distribuire il modello vocali personalizzati.

Il diagramma seguente illustra i passaggi per creare un modello vocali personalizzati usando il portale vocali personalizzati. Usare i collegamenti per altre informazioni.

![Diagramma dell'architettura vocale personalizzato](media/custom-voice/custom-voice-diagram.png)

1.  [Iscriviti e crea un progetto](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#set-up-your-azure-account) : creare un account Azure e creare una sottoscrizione di servizi di riconoscimento vocale. Questa sottoscrizione unificata consente di accedere a per il riconoscimento vocale, sintesi vocale, traduzione vocale e portale vocale personalizzato. Quindi, usando la sottoscrizione di servizi di riconoscimento vocale, creare il primo progetto vocale personalizzato.

2.  [Caricare dati](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#upload-your-datasets) -caricare i dati (audio e testo) usando il portale di vocale personalizzato o l'API vocale personalizzato. Dal portale, è possibile esaminare e valutare i punteggi di pronuncia e il rapporto segnale / rumore. Per altre informazioni, vedere [come preparare i dati per le funzionalità vocali personalizzati](how-to-custom-voice-prepare-data.md).

3.  [Il training del modello](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#build-your-voice-font) : usare i dati per creare un modello personalizzato di sintesi vocale. È possibile eseguire il training di un modello in lingue diverse. Dopo il training, il modello di test e se si è soddisfatti del risultato, è possibile distribuire il modello.

4.  [Distribuire il modello](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#create-and-use-a-custom-endpoint) : creare un endpoint personalizzato per il modello di sintesi vocale e usarlo per la sintesi vocale per i prodotti, strumenti e applicazioni.

## <a name="set-up-your-azure-account"></a>Configurare l'account di Azure

Prima di poter usare il portale di riconoscimento vocale personalizzato per creare un modello personalizzato, è necessaria una sottoscrizione di servizi di riconoscimento vocale. Seguire queste istruzioni per creare una sottoscrizione di servizi di riconoscimento vocale in Azure. Se non hai un account Azure, è possibile effettuare l'iscrizione per una nuova.  

Dopo aver creato un account Azure e una sottoscrizione di servizi di riconoscimento vocale, è necessario eseguire l'accesso al portale di vocale personalizzati e collegare la sottoscrizione.

1. Ottenere la chiave di sottoscrizione di servizi di riconoscimento vocale dal portale di Azure.
2. Accedi per il [portale personalizzato vocale](https://aka.ms/custom-voice).
3. Selezionare la sottoscrizione e creare un progetto di riconoscimento vocale.
4. Se si desidera passare a un'altra sottoscrizione di riconoscimento vocale, usare l'icona della ruota dentata che si trova nel riquadro di spostamento superiore.

> [!NOTE]
> Il servizio vocali personalizzati non supporta la chiave di valutazione gratuita di 30 giorni. È necessario disporre una F0 o una chiave S0 creata in Azure prima di poter usare il servizio.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuto, ad esempio i dati, modelli, i test e gli endpoint sono organizzati in **progetti** nel portale di vocale personalizzato. Ogni progetto è specifico di un paese/linguaggio e il genere della voce da creare. Ad esempio, è possibile creare un progetto per una voce femminile per Bot chat del servizio i clienti che usano l'inglese parlato negli Stati Uniti (en-US).

Per creare il primo progetto, selezionare la **Voice Text-to-Speech/personalizzato** tab, quindi fare clic su **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, si noterà quattro schede: **I dati**, **Training**, **test**, e **distribuzione**. Usare i collegamenti disponibili nella [passaggi successivi](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#next-steps) per imparare a utilizzare ogni scheda.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare i dati vocali personalizzati](how-to-custom-voice.md)
- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guida: Esempi di voce di record](record-custom-voice-samples.md)
