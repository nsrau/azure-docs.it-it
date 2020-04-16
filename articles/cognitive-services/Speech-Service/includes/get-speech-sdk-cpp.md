---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400026"
---
:::row:::
    :::column span="3":::
        L'SDK per il riconoscimento vocale di C'è disponibile su Windows, Linux e macOS. Per ulteriori informazioni, vedere <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Pacchetto NuGet in C

Con **il** comando seguente `Install-Package` è possibile installare l'SDK per il riconoscimento vocale in C.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>File binari e file di intestazione di C

In alternativa, è possibile installare l'SDK per il riconoscimento vocale di C. Scaricare l'SDK come <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">pacchetto <span class="docon docon-navigate-external x-hidden-focus"></span> con estensione tar</a> e decomprimere i file in una directory di propria scelta. Il contenuto di questo pacchetto (che include i file di intestazione per entrambe le architetture di destinazione x86 e x64) è strutturato come segue:

  | Path                   | Descrizione                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licenza                                              |
  | `ThirdPartyNotices.md` | Comunicazioni di terze parti                                  |
  | `include`              | File di intestazione per il c                                 |
  | `lib/x64`              | Libreria x64 nativa per il collegamento all'applicazione |
  | `lib/x86`              | Libreria x86 nativa per il collegamento all'applicazione |

  Per creare un'applicazione, copiare o spostare i file binari e le librerie necessari nell'ambiente di sviluppo e includerli nel processo di compilazione come richiesto.

#### <a name="additional-resources"></a>Risorse aggiuntive

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Codice sorgente di Windows, Linux e macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>