---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 14f9d0936a4d1949cf8d7fc69bbb782ee447bdba
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668766"
---
:::row:::
    :::column span="3":::
        Speech SDK supporta solo **Ubuntu 16.04/18.04**, **Debian 9,** **Red Hat Enterprise Linux (RHEL) 7/8**e **CentOS 7/8** sulle seguenti architetture di destinazione quando vengono utilizzate con Linux:
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
> Quando si utilizza la destinazione Linux ARM64 e si usa C, è necessario .NET Core 3.x (pacchetto dotnet-sdk-3.x). Se la destinazione è ARM32 o ARM64, Python non è supportato.

> [!NOTE]
> Le architetture x86 di Ubuntu 16.04, Ubuntu 18.04 e Debian 9 supportano solo lo sviluppo di C, con Speech SDK.

### <a name="system-requirements"></a>Requisiti di sistema

Per un'applicazione nativa, Speech `libMicrosoft.CognitiveServices.Speech.core.so`SDK si basa su . Assicurarsi che l'architettura di destinazione (x86, x64) corrisponda all'applicazione. A seconda della versione Linux, potrebbero essere necessarie dipendenze aggiuntive.

- Le raccolte condivise della Libreria GNU C (inclusa la libreria di programmazione thread POSIX, `libpthreads`)
- La libreria OpenSSL (`libssl.so.1.0.0` o `libssl.so.1.0.2`)
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
> Seguire le istruzioni su [come configurare RHEL/CentOS 7 per Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> Su RHEL/CentOS 8, seguire le istruzioni su [come configurare OpenSSL per Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
