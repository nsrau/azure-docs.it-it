---
title: 'Avvio rapido: Traduzione vocale, Java (Windows, Linux)- Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si creerà una semplice applicazione Java per acquisire i contenuti vocali dell'utente, convertirli in un'altra lingua e restituire il testo nella riga di comando. Questa guida è destinata agli utenti di Windows e Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 23582e25a7695f4573863b77b83d73408e77ac4e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105638"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Guida introduttiva: Traduzione vocale con Speech SDK per Java

In questa guida introduttiva si creerà una semplice applicazione Java per acquisire i contenuti vocali dell'utente dal microfono del computer, tradurli e trascrivere il testo tradotto nella riga di comando in tempo reale. Quest'applicazione è progettata per l'esecuzione in Windows a 64 bit o Ubuntu Linux a 64 bit 16.04/18.04 ed è integrata con il pacchetto Speech SDK Maven e l'ambiente IDE Java Eclipse.

Per un elenco completo di lingue disponibili per la traduzione vocale, vedere [Supporto per le lingue](language-support.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Sistema operativo: Windows a 64 bit o Ubuntu Linux a 64 bit 16.04/18.04
* [Ambiente IDE Java Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

Se si esegue Ubuntu 16.04/18.04, assicurarsi che queste dipendenze siano installate prima di avviare Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
```

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Sostituire tutto il codice in `Main.java` con il frammento di codice seguente:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.

L'input vocale dal microfono verrà trascritto in tedesco e registrato nella finestra della console. Per arrestare il riconoscimento vocale, premere "Invio".

![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio come eseguire il riconoscimento vocale da un file audio e restituire il testo tradotto come sintesi vocale, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Guida introduttiva: Riconoscimento vocale, Java (Windows, Linux)](quickstart-java-jre.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
