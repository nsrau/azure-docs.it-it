---
title: 'Avvio rapido: Riconoscere i contenuti vocali, .NET Framework (Windows) - Servizi Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di riconoscimento vocale con .NET framework per Windows e Speech SDK. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: a3990bfac9199fadeff0573d7547d7e5bc551ece
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838613"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Avvio rapido: Riconoscimento vocale con Speech SDK per .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Usare questa guida per creare un'applicazione console di riconoscimento vocale con .NET framework per Windows e Speech SDK. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.

Per una rapida dimostrazione (senza compilare il progetto di Visual Studio come illustrato di seguito):

Ottenere la versione più recente di [Cognitive Services Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) da GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Una chiave di sottoscrizione per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Accesso al microfono del computer

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Apri `Program.cs` e sostituire il codice generato automaticamente con questo codice di esempio:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Individuare e sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione dei servizi Voce.

1. Individuare e sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di valutazione gratuita.

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-dotnet-windows-08-build.png "Compilazione completata")

1. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Verrà visualizzata una finestra della console in cui si chiede all'utente di parlare. Pronunciare qualche parola in italiano. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto in tempo reale in formato testo. Il risultato viene stampato sulla console.

    ![Risultato sulla console dopo un riconoscimento riuscito](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Risultato sulla console dopo un riconoscimento riuscito")

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
