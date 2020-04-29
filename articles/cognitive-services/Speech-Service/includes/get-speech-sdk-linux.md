---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400029"
---
:::row:::
    :::column span="3":::
        Speech SDK supporta solo **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**e **CentOS 7/8** nelle seguenti architetture di destinazione se usate con Linux:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Quando si fa riferimento a Linux ARM64 e si usa C#, è necessario .NET Core 3. x (pacchetto dotnet-SDK-3. x). Se la destinazione è ARM32 o ARM64, Python non è supportato.

> [!NOTE]
> Le architetture x86 di Ubuntu 16,04, Ubuntu 18,04 e Debian 9 supportano solo lo sviluppo C++ con Speech SDK.

### <a name="system-requirements"></a>Requisiti di sistema

Per un'applicazione nativa, l'SDK di riconoscimento vocale si `libMicrosoft.CognitiveServices.Speech.core.so`basa su. Verificare che l'architettura di destinazione (x86, x64) corrisponda all'applicazione. A seconda della versione di Linux, potrebbero essere necessarie dipendenze aggiuntive.

- Le raccolte condivise della Libreria GNU C (inclusa la libreria di programmazione thread POSIX, `libpthreads`)
- Libreria OpenSSL (`libssl.so.1.0.0` o `libssl.so.1.0.2`)
- La libreria condivisa per le applicazioni ALSA (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 e CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Seguire le istruzioni su [come configurare RHEL/CentOS 7 per l'SDK di riconoscimento vocale](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> In RHEL/CentOS 8 seguire le istruzioni riportate in [Come configurare OpenSSL per Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
