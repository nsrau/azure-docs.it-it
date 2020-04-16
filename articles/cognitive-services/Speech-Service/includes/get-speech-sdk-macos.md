---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399906"
---
Durante lo sviluppo per macOS, sono disponibili tre SDK di riconoscimento vocale.

- Objective-C Speech SDK è disponibile in modo nativo come pacchetto CocoaPod
- .NET Speech SDK potrebbe essere utilizzato con **Xamarin.Mac** in quanto implementa .NET Standard 2.0
- Python Speech SDK è disponibile come modulo PyPI

> [!TIP]
> Per informazioni dettagliate sull'utilizzo di Objective-C Speech SDK con Swift, consultate Importazione di <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-C <span class="docon docon-navigate-external x-hidden-focus"> </span>in Swift. </a>

### <a name="system-requirements"></a>Requisiti di sistema

- Un macOS versione 10.13 o successiva

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Il pacchetto macOS CocoaPod è disponibile per il download e l'uso con l'ambiente di sviluppo integrato (IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (o versione successiva). <span class="docon docon-navigate-external x-hidden-focus"></span> </a> In primo luogo, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">scaricare <span class="docon docon-navigate-external x-hidden-focus"> </span>il binario CocoaPod </a>. Estrarre il contenitore nella stessa directory per l'uso `pod` previsto, `target`creare un *FilePod* ed elencare il file come file .
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

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac espone la versione completa di macOS SDK per gli sviluppatori .NET per creare applicazioni Mac native con C#. Per ulteriori informazioni, consultate <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Risorse aggiuntive

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">MacOS Speech SDK guida introduttivo codice sorgente Objective-C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">MacOS Speech SDK quickstart codice sorgente Swift<span class="docon docon-navigate-external x-hidden-focus"></span></a>