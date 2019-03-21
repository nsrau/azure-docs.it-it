---
title: 'Guida introduttiva: Traduzione vocale, C# (.NET Core - Windows)'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si creerà una semplice applicazione .NET Core per acquisire i contenuti vocali dell'utente, convertirli in un'altra lingua e restituire il testo nella riga di comando. Questa guida è destinata agli utenti di Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: b0b0c256db3cc93e515b38bf7daad72b082d2d3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850259"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-core"></a>Guida introduttiva: Traduzione vocale con Speech SDK per .NET Core

In questa guida introduttiva si creerà una semplice applicazione .NET Core per acquisire i contenuti vocali dell'utente dal microfono del computer, tradurli e trascrivere il testo tradotto nella riga di comando in tempo reale. L'applicazione è progettata per l'esecuzione in Windows a 64 bit e si basa sul pacchetto [NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017.

Per un elenco completo di lingue disponibili per la traduzione vocale, vedere [Supporto per le lingue](language-support.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [ASP.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire `Program.cs` e sostituire tutto il codice in questo file con quanto segue.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Inoltre, sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu scegliere **Compila** > **Compila soluzione**. Il codice dovrebbe risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Si apre una finestra della console che chiede di dire qualcosa. Pronunciare una frase o un'espressione in inglese. Il riconoscimento vocale viene trasmesso al Servizio di riconoscimento vocale, tradotto e trascritto in formato testo, che appare nella stessa finestra.

    ![Risultato sulla console dopo una traduzione riuscita](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Risultato sulla console dopo una traduzione riuscita")


## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio come eseguire il riconoscimento vocale da un file audio e restituire il testo tradotto come sintesi vocale, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
