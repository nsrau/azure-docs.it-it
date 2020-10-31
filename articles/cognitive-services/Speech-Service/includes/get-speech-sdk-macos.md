---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 963405f0874e23553a118081b080a0f30e1942eb
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135725"
---
Quando si sviluppa per macOS, sono disponibili tre SDK per la sintesi vocale.

- Objective-C Speech SDK è disponibile in modalità nativa come pacchetto CocoaPod
- .NET Speech SDK può essere usato con **Novell. Mac** mentre implementa .NET standard 2,0
- Python Speech SDK è disponibile come modulo PyPI

> [!TIP]
> Per informazioni dettagliate sull'uso dell'SDK di riconoscimento vocale Objective-C con Swift, vedere <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">importazione di <span class="docon docon-navigate-external x-hidden-focus"></span> Objective-c in Swift </a>.

### <a name="system-requirements"></a>Requisiti di sistema

- MacOS versione 10,13 o successiva

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Il pacchetto CocoaPod macOS è disponibile per il download e l'uso con il <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">9.4.1 di Xcode ( <span class="docon docon-navigate-external x-hidden-focus"></span> o versione successiva)</a> Integrated Development Environment (IDE). Prima di tutto, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">scaricare il <span class="docon docon-navigate-external x-hidden-focus"></span> file binario CocoaPod </a>. Estrarre il pod nella stessa directory per l'uso previsto, creare un *Podfile* ed elencare `pod` come `target` .
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
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.14.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac espone la versione completa di macOS SDK per gli sviluppatori .NET per creare applicazioni Mac native con C#. Per altre informazioni, vedere <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Novell. Mac <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Guida introduttiva a macOS Speech SDK (codice sorgente Objective-C) <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Guida introduttiva a macOS Speech SDK-codice sorgente Swift <span class="docon docon-navigate-external x-hidden-focus"></span></a>