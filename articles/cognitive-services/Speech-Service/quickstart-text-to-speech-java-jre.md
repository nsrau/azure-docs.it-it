---
title: 'Guida introduttiva: Sintesi vocale, Java (Windows, Linux, macOS) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si imparerà a creare una semplice applicazione Java per acquisire, eseguire la sintesi vocale e riprodurre la voce con l'altoparlante predefinito.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 832525ae1441fca85f8df661b4a187c0be8d91dc
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803698"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-java"></a>Guida introduttiva: Sintesi vocale con Speech SDK per Java

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-java-jre.md), la [traduzione con sintesi vocale](quickstart-translate-speech-java-jre.md) e l'[assistente virtuale voice-first](quickstart-virtual-assistant-java-jre.md).

In questo articolo, si crea un'applicazione console Java usando [Speech SDK](speech-sdk.md). Eseguire la sintesi vocale e riprodurre la voce con l'altoparlante predefinito del PC. L'applicazione è compilata con il pacchetto Speech SDK Maven ed Eclipse Java IDE (v4.8) in Windows a 64 bit, Linux a 64 bit (Ubuntu 16.04, Ubuntu 18.04, Debian 9) o in macOS 10.13 o versioni successive. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Sistema operativo: Windows a 64 bit, Linux a 64 bit (Ubuntu 16.04, Ubuntu 18.04, Debian 9), oppure macOS 10.13 o versioni successive
* [Ambiente IDE Java Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

Se si esegue Linux, assicurarsi che queste dipendenze siano installate prima di avviare Eclipse.

* In Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* In Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Se si esegue Windows (64 bit), assicurarsi di avere installato Microsoft Visual C++ Redistributable per la piattaforma in uso.
* [Scaricare Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Sostituire tutto il codice in `Main.java` con il frammento di codice seguente:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
Immettere un testo quando viene richiesto e si sentirà l'audio sintetizzato riprodotto dall'altoparlante predefinito.

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio per eseguire il riconoscimento vocale da un file audio, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Guida introduttiva: Riconoscimento vocale, Java (Windows, Linux, macOS)](quickstart-java-jre.md)
- [Guida introduttiva: Traduzione vocale, Java (Windows, Linux, macOS)](quickstart-translate-speech-java-jre.md)
- [Personalizzare i carattere voce](how-to-customize-voice-font.md)
- [Registrare esempi vocali](record-custom-voice-samples.md)
