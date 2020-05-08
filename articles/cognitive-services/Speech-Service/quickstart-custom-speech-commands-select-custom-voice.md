---
title: 'Guida introduttiva: usare comandi personalizzati con voce personalizzata (anteprima)-servizio riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo si specificherà la voce di output di un'applicazione Commands personalizzata.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872451"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Guida introduttiva: usare comandi personalizzati con la voce personalizzata (anteprima)

Nell' [articolo precedente](./quickstart-custom-speech-commands-create-parameters.md)è stato creato un nuovo progetto di comandi personalizzati per rispondere ai comandi con parametri.

In questo articolo verrà selezionata una voce di output personalizzata per l'applicazione creata.

## <a name="select-a-custom-voice"></a>Selezionare una voce personalizzata

1. Aprire il progetto [creato in precedenza](./quickstart-custom-speech-commands-create-parameters.md)
1. Selezionare **Impostazioni** dal riquadro sinistro
1. Seleziona **voce personalizzata** nel riquadro centrale
1. Selezionare la voce personalizzata o pubblica desiderata dalla tabella
1. Selezionare **Salva**

> [!div class="mx-imgBorder"]
> ![Frasi di esempio con parametri](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Per le **voci pubbliche**, i **tipi neurali** sono disponibili solo per aree specifiche. Per verificare la disponibilità, vedere [le voci standard e neurali per area/endpoint](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Per le **voci personalizzate**, è possibile crearle dalla pagina del progetto Voice personalizzato. Vedere [Introduzione alla voce personalizzata](./how-to-custom-voice.md).

A questo punto, l'applicazione risponderà alla voce selezionata, anziché alla voce predefinita.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Guida introduttiva: connettersi a un'applicazione di comando personalizzata con Speech SDK (anteprima)](./quickstart-custom-speech-commands-speech-sdk.md)

