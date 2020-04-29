---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400026"
---
:::row:::
    :::column span="3":::
        C++ Speech SDK è disponibile in Windows, Linux e macOS. Per ulteriori informazioni, vedere <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft. CognitiveServices. Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Pacchetto NuGet C++

È possibile installare C++ Speech SDK da **Gestione pacchetti** con il comando seguente `Install-Package` .

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>File binari e file di intestazione C++

In alternativa, è possibile installare C++ Speech SDK da file binari. Scaricare l'SDK come <a href="https://aka.ms/csspeech/linuxbinary" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> pacchetto con estensione tar</a> e decomprimere i file in una directory di propria scelta. Il contenuto di questo pacchetto, che include i file di intestazione per le architetture di destinazione x86 e x64, è strutturato nel modo seguente:

  | Path                   | Descrizione                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licenza                                              |
  | `ThirdPartyNotices.md` | Comunicazioni di terze parti                                  |
  | `include`              | File di intestazione per C++                                 |
  | `lib/x64`              | Libreria x64 nativa per il collegamento all'applicazione |
  | `lib/x86`              | Libreria x86 nativa per il collegamento all'applicazione |

  Per creare un'applicazione, copiare o spostare i file binari e le librerie necessari nell'ambiente di sviluppo e includerli nel processo di compilazione come richiesto.

#### <a name="additional-resources"></a>Risorse aggiuntive

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Codice sorgente di avvio rapido C++ per Windows, Linux e macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>