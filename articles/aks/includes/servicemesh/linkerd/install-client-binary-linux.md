---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1729eabca75ed7c7a3a43ea2c0b1617efd337f2c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530108"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Scaricare e installare il file binario client Linkerd di Linkerd

In una shell basata su bash in Linux o nel [sottosistema Windows per Linux][install-wsl], usare `curl` per scaricare la versione di Linkerd come indicato di seguito:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Il `linkerd` binario client viene eseguito nel computer client e consente di interagire con la mesh del servizio Linkerd. Usare i comandi seguenti per installare il file binario client di Linkerd `linkerd` in una shell basata su bash in un [sottosistema Linux o Windows per Linux][install-wsl]. Questi comandi copiano il file binario del client `linkerd` nel percorso del programma dell'utente standard in `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se si vuole completare la riga di comando per il file binario client di Linkerd `linkerd`, impostarlo come segue:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10