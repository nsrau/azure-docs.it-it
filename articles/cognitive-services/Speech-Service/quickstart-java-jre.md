---
title: 'Avvio rapido: Riconoscimento vocale, Java (Windows, Linux)- Servizi Voce'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si imparerà a creare una semplice applicazione Java per acquisire e trascrivere i contenuti vocali dell'utente dal microfono del computer.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: 83d6c430d0973d4b45fcfc40bbb93ae283d1133d
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447583"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Avvio rapido: Riconoscimento vocale con Speech SDK per Java

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo, si crea un'applicazione console Java usando [Speech SDK](speech-sdk.md). Avviene la trascrizione del riconoscimento vocale in tempo reale dal microfono del PC. L'applicazione è compilata con il pacchetto Speech SDK Maven ed Eclipse Java IDE (v4.8) su Windows a 64 bit o Ubuntu Linux 16.04 / 18.04 a 64 bit. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Sistema operativo: Windows (a 64 bit) o Ubuntu Linux 16.04/18.04 (a 64 bit)
* [Ambiente IDE Java Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

Se si esegue Ubuntu 16.04/18.04, assicurarsi che queste dipendenze siano installate prima di avviare Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se si esegue Windows (64 bit) assicurarsi di avere installato Microsoft Visual C++ Redistributable per la piattaforma in uso.
* [Scaricare Microsoft Visual C++ Redistributable per Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)


## <a name="create-and-configure-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Sostituire tutto il codice in `Main.java` con il frammento di codice seguente:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
I successivi 15 secondi di input vocale dal microfono verranno riconosciuti e registrati nella finestra della console.

![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio per eseguire il riconoscimento vocale da un file audio, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Guida introduttiva: Traduzione vocale, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
