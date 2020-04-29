---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400039"
---
:::row:::
    :::column span="3":::
        Quando si sviluppa per iOS, sono disponibili due SDK di riconoscimento vocale. Objective-C Speech SDK è disponibile in modalità nativa come pacchetto CocoaPod iOS. In alternativa, è possibile usare .NET Speech SDK con Novell. iOS mentre implementa .NET Standard 2,0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Per informazioni dettagliate sull'uso dell'SDK di riconoscimento vocale Objective-C con Swift, vedere <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">importazione di <span class="docon docon-navigate-external x-hidden-focus"> </span>Objective-c in Swift </a>.

### <a name="system-requirements"></a>Requisiti di sistema

- MacOS versione 10,3 o successiva
- Destinazione iOS 9,3 o versione successiva

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Il pacchetto CocoaPod iOS è disponibile per il download e l'uso con il <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">9.4.1 di Xcode ( <span class="docon docon-navigate-external x-hidden-focus"></span> o versione successiva)</a> Integrated Development Environment (IDE). Prima di tutto, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">scaricare il <span class="docon docon-navigate-external x-hidden-focus"> </span>file binario CocoaPod </a>. Estrarre il pod nella stessa directory per l'uso previsto, creare un *Podfile* ed elencare `pod` come `target`.
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
        Xamarin.iOS espone la versione completa di iOS SDK per gli sviluppatori .NET. Creare app iOS completamente native con C# o F# in Visual Studio. Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Novell. iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK Guida introduttiva a Objective-C codice sorgente<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK Guida introduttiva al codice sorgente Swift<span class="docon docon-navigate-external x-hidden-focus"></span></a>