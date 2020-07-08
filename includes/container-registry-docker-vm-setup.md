---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82982436"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Creare una macchina virtuale abilitata per Docker

A scopo di test, usare una VM Ubuntu abilitata per Docker per accedere a un registro contenitori di Azure. Per usare l'autenticazione Azure Active Directory per il registro di sistema, installare anche l'interfaccia della riga di comando di [Azure][azure-cli] nella macchina virtuale. Se si dispone già di una macchina virtuale di Azure, ignorare questo passaggio di creazione.

È possibile usare lo stesso gruppo di risorse per la macchina virtuale e il registro contenitori. Questa configurazione semplifica la pulizia alla fine, ma non è obbligatoria. Se si sceglie di creare un gruppo di risorse separato per la macchina virtuale e la rete virtuale, eseguire il comando [AZ Group create][az-group-create]. Nell'esempio seguente si presuppone che siano state impostate variabili di ambiente per il nome del gruppo di risorse e il percorso del registro di sistema:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Distribuire ora una macchina virtuale di Azure Ubuntu predefinita con [AZ VM create][az-vm-create]. L'esempio seguente crea una macchina virtuale denominata *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Per creare la macchina virtuale sono necessari alcuni minuti. Dopo aver eseguito il comando, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Usare questo indirizzo per stabilire connessioni SSH alla macchina virtuale.

### <a name="install-docker-on-the-vm"></a>Installare Docker nella macchina virtuale

Una volta che la macchina virtuale è in esecuzione, stabilire una connessione SSH alla macchina virtuale. Sostituire *publicIpAddress* con l'indirizzo IP pubblico della macchina virtuale.

```bash
ssh azureuser@publicIpAddress
```

Eseguire i comandi seguenti per installare Docker nella VM Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Dopo l'installazione, eseguire il comando seguente per verificare la corretta esecuzione di Docker nella macchina virtuale:

```bash
sudo docker run -it hello-world
```

Output:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Seguire i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure con APT](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) per installare l'interfaccia della riga di comando di Azure nella macchina virtuale Ubuntu. Ad esempio:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Uscire dalla connessione SSH.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group