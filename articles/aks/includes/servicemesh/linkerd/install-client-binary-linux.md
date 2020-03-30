---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593724"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Scaricare e installare il file binario del client linkerd Linkerd Linkerd

In una shell basata su bash su Linux o [Windows Subsystem per Linux][install-wsl], utilizzare `curl` per scaricare la versione linker come segue:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Il `linkerd` file binario client viene eseguito nel computer client e consente di interagire con la rete di servizi Linkerd. Utilizzare i comandi seguenti per `linkerd` installare il file binario del client Linkerd in una shell basata su bash su Linux o [Windows Subsystem per Linux.][install-wsl] Questi comandi copiano il file binario del client `linkerd` nel percorso del programma dell'utente standard in `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se si desidera il completamento della `linkerd` riga di comando per il file binario del client Linkerd, impostarlo come segue:

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