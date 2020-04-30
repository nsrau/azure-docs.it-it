---
title: 'Guida introduttiva: Eseguire la sintesi vocale, Java (Windows, Linux, macOS) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si imparerà a creare una semplice applicazione Java per acquisire, eseguire la sintesi vocale e riprodurre la voce con l'altoparlante predefinito.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: 31bfe77c85723d6c24872f1e52ede60936f7804e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275177"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Sostituire tutto il codice in `Main.java` con il frammento di codice seguente:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/text-to-speech/src/speechsdk/quickstart/Main.java#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
Immettere un testo quando richiesto e si sentirà l'audio sintetizzato riprodotto dall'altoparlante predefinito.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
