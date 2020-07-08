---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77593977"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Scaricare e installare il file binario client istioctl di Istio

In una shell basata su bash in Linux o nel [sottosistema Windows per Linux][install-wsl], usare `curl` per scaricare la versione Istio e quindi estrarla con `tar` come indicato di seguito:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Il `istioctl` file binario client viene eseguito nel computer client e consente di interagire con la mesh del servizio Istio. Per installare il file binario del client `istioctl` di Istio in una shell basata su bash in Linux o in un [sottosistema di Windows per Linux][install-wsl], usare i comandi indicati di seguito. Questi comandi copiano il file binario del client `istioctl` nel percorso del programma dell'utente standard in `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se si desidera il completamento della riga di comando per il file binario del client `istioctl` di Istio, procedere alla configurazione come indicato di seguito:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10