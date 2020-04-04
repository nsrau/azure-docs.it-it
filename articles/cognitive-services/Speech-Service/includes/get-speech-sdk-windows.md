---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: dea6a1afaa2348fc5054bee20c534936dcafe5b5
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656692"
---
:::row:::
    :::column span="3":::
        Speech SDK supporta Windows 10 e Windows Server 2016 o versioni successive. Le versioni precedenti **non** sono ufficialmente supportate. È possibile utilizzare parti di Speech SDK con versioni precedenti di Windows, anche se non è consigliato.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Requisiti di sistema

L'SDK di riconoscimento vocale in Windows richiede il <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">file ridistribuibile <span class="docon docon-navigate-external x-hidden-focus"></span> di Microsoft Visual C, per Visual Studio 2019</a> nel sistema.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Installazione per x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Installazione per x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Installazione per ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Per l'input del microfono, è necessario installare le raccolte Media Foundation. Queste raccolte sono parte di Windows 10 e Windows Server 2016. È possibile utilizzare Speech SDK senza queste raccolte finché non viene utilizzato il microfono come dispositivo di input audio.

I file Speech SDK richiesti possono essere distribuiti nella stessa directory dell'applicazione. In questo modo l'applicazione può accedere direttamente alle raccolte. Assicurarsi di selezionare la versione corretta (x86/x64) che corrisponde all'applicazione.

| Nome                                            | Funzione                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK di base, necessario per la distribuzione nativa e gestita |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Obbligatorio per le distribuzioni gestite                      |

> [!NOTE]
> A partire dalla versione 1.3.0 il file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornito nelle versioni precedenti) non è più necessario. La funzionalità è ora integrata nell'SDK di base.

> [!IMPORTANT]
> Per il progetto di Windows Form App (.NET Framework) C , assicurarsi che le librerie siano incluse nelle impostazioni di distribuzione del progetto. È possibile controllare `Properties -> Publish Section`questo sotto . Fare `Application Files` clic sul pulsante e trovare le librerie corrispondenti dall'elenco a discesa. Assicurarsi che il `Included`valore sia impostato su . Visual Studio includerà il file quando il progetto viene pubblicato/distribuito.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
