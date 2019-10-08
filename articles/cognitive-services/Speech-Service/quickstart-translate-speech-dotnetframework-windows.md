---
title: 'Guida introduttiva: Traduzione vocale, C# (.NET Framework Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si creerà un'applicazione .NET Framework per acquisire i contenuti vocali dell'utente, convertirli in un'altra lingua e restituire il testo nella riga di comando. Questa guida è destinata agli utenti di Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327352"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Guida introduttiva: Traduzione vocale con Speech SDK per .NET Framework (Windows)

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-csharp-dotnet-windows.md) e la [sintesi vocale](quickstart-text-to-speech-dotnet-windows.md).

In questo argomento di avvio rapido si creerà un'applicazione .NET Framework per acquisire i contenuti vocali dell'utente dal microfono del computer, tradurli e trascrivere il testo tradotto nella riga di comando in tempo reale. L'applicazione può essere eseguita in Windows a 32 o 64 bit e si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2019.

Per un elenco completo di lingue disponibili per la traduzione vocale, vedere [Supporto per le lingue](language-support.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire **Program.cs** e sostituire tutto il codice con il codice seguente.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Trovare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione.

1. Trovare la stringa `YourServiceRegion` e sostituirla con l'[area](regions.md) associata alla sottoscrizione. Ad esempio, per la sottoscrizione di valutazione gratuita l'area è `westus`.

1. Dalla barra dei menu scegliere **File** > **Salva tutto**.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o selezionare **F5** per avviare l'applicazione **helloworld**.

1. Pronunciare una frase o un'espressione nel microfono del dispositivo. L'applicazione trasmette il parlato al servizio Voce, che effettua la traduzione in testo (in questo caso in tedesco). Il servizio Voce invia nuovamente il testo tradotto all'applicazione, che visualizza la traduzione nella finestra.

   ![Interfaccia utente di traduzione vocale](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio come eseguire il riconoscimento vocale da un file audio e restituire il testo tradotto come sintesi vocale, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Eseguire il training di un modello per Riconoscimento vocale personalizzato](how-to-custom-speech-train-model.md)
