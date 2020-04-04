---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f3864b28ba6f5bdbffe6601d059a21328d56a5e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656534"
---
:::row:::
    :::column span="3":::
        Durante lo sviluppo per iOS, sono disponibili due SDK di riconoscimento vocale. Objective-C Speech SDK è disponibile in modo nativo come pacchetto iOS CocoaPod. In alternativa, .NET Speech SDK potrebbe essere utilizzato con Xamarin.iOS in quanto implementa .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Per informazioni dettagliate sull'utilizzo di Objective-C Speech SDK con Swift, consultate Importazione di <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-C <span class="docon docon-navigate-external x-hidden-focus"> </span>in Swift. </a>

### <a name="system-requirements"></a>Requisiti di sistema

- Un macOS versione 10.3 o successiva
- IOS di destinazione 9.3 o versioni successive

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Il pacchetto iOS CocoaPod è disponibile per il download e l'uso con l'ambiente di sviluppo integrato (IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (o versione successiva). <span class="docon docon-navigate-external x-hidden-focus"></span> </a> In primo luogo, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">scaricare <span class="docon docon-navigate-external x-hidden-focus"> </span>il binario CocoaPod </a>. Estrarre il contenitore nella stessa directory per l'uso `pod` previsto, `target`creare un *FilePod* ed elencare il file come file .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS espone la versione completa di iOS SDK per gli sviluppatori .NET. Creare app iOS completamente native con C# o F# in Visual Studio. Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Risorse aggiuntive

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Codice sorgente Objective-C di iOS Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">Codice sorgente Swift di iOS Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>