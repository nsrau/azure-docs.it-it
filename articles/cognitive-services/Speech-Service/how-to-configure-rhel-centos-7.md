---
title: Come configurare RHEL/CentOS 7 - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare RHEL/CentOS 7 in modo che sia possibile usare Speech SDK.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639163"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Configurare RHEL/CentOS 7 per Speech SDK

Red Hat Enterprise Linux (RHEL) 8 x64 e CentOS 8 x64 sono ufficialmente supportati da Speech SDK versione 1.10.0 e successive. È anche possibile utilizzare l'SDK di riconoscimento vocale in RHEL/CentOS 7 x64, ma ciò richiede l'aggiornamento del compilatore C .

Per verificare la versione del compilatore di C, eseguire:

```bash
g++ --version
```

Se il compilatore è installato, l'output dovrebbe essere simile al seguente:If the compiler is installed, the output should look like this:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Questo messaggio indica che è installato GCC versione principale 4. Questa versione non dispone del supporto completo per lo standard C, utilizzato da Speech SDK. Se si tenta di compilare un programma in C, con questa versione di GCC e le intestazioni di Speech SDK, si creeranno errori di compilazione.

È anche importante controllare la versione della libreria di runtime condivisa di C, ovvero libstdc. La maggior parte dell'SDK vocale viene implementata come librerie native di C, il che significa che dipende da libstdc, indipendentemente dal linguaggio utilizzato per sviluppare applicazioni.

Per trovare il percorso di libstdc nel sistema, eseguire:

```bash
ldconfig -p | grep libstdc++
```

L'uscita su vaniglia RHEL/CentOS 7 (x64) è:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

In base a questo messaggio, è consigliabile controllare le definizioni di versione con questo comando:Based on this message, you'll want to check the version definitions with this command:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

L'output dovrebbe essere simile al seguente:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Speech SDK richiede **CXXABI_1.3.9** e **GLIBCXX_3.4.21.** È possibile trovare queste `ldd libMicrosoft.CognitiveServices.Speech.core.so` informazioni eseguendo sulle librerie Speech SDK dal pacchetto Linux.You can find this information by running on the Speech SDK libraries from the Linux package.

> [!NOTE]
> È consigliabile che la versione di GCC installata nel sistema sia almeno **5.4.0,** con librerie di runtime corrispondenti.

## <a name="example"></a>Esempio

Questo è un comando di esempio che illustra come configurare RHEL/CentOS 7 x64 per lo sviluppo (C , C , Java, Python) con Speech SDK 1.10.0 o versione successiva:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su Speech SDK](speech-sdk.md)
