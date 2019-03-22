---
title: Autenticazione al Registro Azure Container con un'identità gestita
description: Fornire l'accesso alle immagini nel registro contenitori privato usando un'identità gestita di Azure assegnata dall'utente o dal sistema.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: b09348e98a0dee85338cc9f20289d83b658eb719
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338463"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Usare un'identità gestita di Azure per eseguire l'autenticazione a un registro contenitori di Azure 

Usare un'[identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) per eseguire l'autenticazione a un registro contenitori di Azure da un'altra risorsa di Azure, senza dover fornire o gestire le credenziali del registro. Ad esempio, configurare un'identità gestita assegnata dall'utente o dal sistema in una macchina virtuale Linux per accedere alle immagini del contenitore dal registro contenitori, con la stessa facilità con cui si usa un registro pubblico.

L'articolo fornisce altre informazioni sulle identità gestite e spiega come:

> [!div class="checklist"]
> * Abilitare un'identità assegnata dall'utente o dal sistema in una macchina virtuale di Azure
> * Concedere all'identità l'accesso al registro contenitori di Azure
> * Usare l'identità gestita per accedere al registro e per eseguire il pull di un'immagine del contenitore 

Per creare risorse di Azure, questo articolo richiede che sia eseguita l'interfaccia della riga di comando di Azure versione 2.0.55 o successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

Per configurare un registro contenitori ed eseguire il push di un'immagine del contenitore su di esso, è necessario disporre anche di un Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [macOS][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Perché usare un'identità gestita?

Un'identità gestita per le risorse di Azure fornisce ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Con un'identità gestita è possibile configurare [alcune risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), tra cui macchine virtuali. Quindi, usare l'identità per accedere ad altre risorse di Azure, senza passare credenziali nel codice o negli script.

Esistono due tipi di identità gestite:

* *Le identità assegnate dall'utente*, che è possibile assegnare a più risorse e sono conservate per il tempo desiderato. Le identità assegnate dall'utente sono attualmente in anteprima.

* Un'*identità gestita dal sistema*, che è univoca per una risorsa specifica, ad esempio una singola macchina virtuale, e che è conservata per tutta la durata di tale risorsa.

Dopo aver configurato una risorsa di Azure con un'identità gestita, è possibile concedere all'identità l'accesso a un'altra risorsa, proprio come per qualsiasi entità di sicurezza. Ad esempio, assegnare a un'identità gestita un ruolo con pull, push e pull o altre autorizzazioni a un registro privato in Azure. (Per un elenco completo dei ruoli di registro, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).) È possibile assegnare a un'identità l'accesso a una o più risorse.

Quindi, usare l'identità per l'autenticazione a qualsiasi [servizio che supporti l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) senza nessuna credenziale nel codice. Per usare l'identità per accedere a un registro contenitori di Azure da una macchina virtuale, eseguire l'autenticazione con Azure Resource Manager. Scegliere come eseguire l'autenticazione usando l'identità gestita, a seconda dello scenario:

* [Acquisire un token di accesso Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) a livello di codice tramite le chiamate HTTP o REST

* Usare gli [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Accedere all'interfaccia della riga di comando di Azure o a PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) con l'identità. 

## <a name="create-a-container-registry"></a>Creare un registro contenitori

Se non si ha già un registro contenitori di Azure, creare un registro ed eseguire il push di un'immagine del contenitore di esempio su di esso. Per la procedura, vedere [Avvio rapido: Creare un registro contenitori privato usando l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).

Questo articolo presuppone che l'immagine del contenitore `aci-helloworld:v1` sia archiviata nel registro. Gli esempi usano il nome di registro *myContainerRegistry*. Sostituirlo con i nomi del proprio registro e della propria immagine nei passaggi successivi.

## <a name="create-a-docker-enabled-vm"></a>Creare una macchina virtuale abilitata per Docker

Creare una macchina virtuale Ubuntu abilitata per Docker. È inoltre necessario installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nella macchina virtuale. Se si dispone già di una macchina virtuale di Azure, ignorare questo passaggio per creare la macchina virtuale.

Distribuire una macchina virtuale di Azure Ubuntu predefinita con [az vm create][az-vm-create]. L'esempio seguente crea una macchina virtuale denominata *myDockerVM* nel gruppo di risorse esistente denominato *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
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

Eseguire il comando seguente per installare Docker nella macchina virtuale:

```bash
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

Seguire i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure con APT](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) per installare l'interfaccia della riga di comando di Azure nella macchina virtuale Ubuntu. Per questo articolo, assicurarsi di installare la versione 2.0.55 o successive.

Chiudere la sessione SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Esempio 1: Accedere con un'identità assegnata dall'utente

### <a name="create-an-identity"></a>Creare un'identità

Creare un'identità nella sottoscrizione usando il comando [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). È possibile usare lo stesso gruppo di risorse usato in precedenza per creare il registro contenitori o la macchina virtuale, o un altro gruppo di risorse.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Per configurare l'identità nei passaggi seguenti, usare il comando [az identity show][az-identity-show] per archiviare l'ID risorsa dell'identità e l'ID dell'entità servizio nelle variabili.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Poiché è necessario l'ID dell'identità in un passaggio successivo quando si accede per l'interfaccia CLI dalla macchina virtuale, visualizzare il valore:

```bash
echo $userID
```

L'ID è nel formato:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Configurare la macchina virtuale con l'identità

Il comando [az vm identity assign][az-vm-identity-assign] seguente configura la macchina virtuale Docker con l'identità assegnata dall'utente:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Concedere all'identità l'accesso al registro contenitori

Configurare ora l'identità per accedere al registro contenitori. Innanzitutto usare il comando [az acr show][az-acr-show] per ottenere l'ID risorsa del registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Usare il comando [az role assignment create][az-role-assignment-create] per assegnare il ruolo AcrPull al registro. Questo ruolo offre [autorizzazioni pull](container-registry-roles.md) al registro. Per fornire autorizzazioni pull e push, assegnare il ruolo ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Usare l'identità per accedere al registro

SSH nella macchina virtuale Docker che è configurato con l'identità. Eseguire i comandi seguenti dell'interfaccia della riga di comando di Azure, tramite l'interfaccia della riga di comando di Azure installata nella macchina virtuale.

In primo luogo, eseguire l'autenticazione per il comando di Azure con [account di accesso di az][az-login], usando l'identità è stato configurato nella macchina virtuale. Per <userID>, sostituire l'ID dell'identità recuperato in un passaggio precedente. 

```azurecli
az login --identity --username <userID>
```

Quindi, eseguire l'autenticazione al Registro di sistema [account di accesso di az acr][az-acr-login]. Quando si usa questo comando, l'interfaccia della riga di comando usa il token di Active Directory creato durante l'esecuzione di `az login` per l'autenticazione della sessione con il registro contenitori. (A seconda della configurazione della macchina virtuale, potrebbe essere necessario eseguire questo comando e i comandi di Docker con `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Dovrebbe essere visualizzato il messaggio `Login succeeded`. È quindi possibile eseguire i comandi `docker` senza fornire credenziali. Ad esempio, eseguire [docker pull][docker-pull] per eseguire il pull dell'immagine `aci-helloworld:v1`, specificando il nome del server di accesso del registro. Il nome del server di accesso è costituito dal nome del registro contenitori (solo lettere minuscole) seguito da `.azurecr.io`, ad esempio `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Esempio 2 Accedere con un'identità assegnata dal sistema

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Configurare la macchina virtuale con un'identità gestita dal sistema

Il comando [az vm identity assign][az-vm-identity-assign] seguente configura la macchina virtuale Docker con un'identità assegnata dal sistema:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Usare il comando [az vm show][az-vm-show] per impostare una variabile sul valore di `principalId` (l'ID dell'entità servizio) dell'identità della macchina virtuale per usarla nei passaggi successivi.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Concedere all'identità l'accesso al registro contenitori

Configurare ora l'identità per accedere al registro contenitori. Innanzitutto usare il comando [az acr show][az-acr-show] per ottenere l'ID risorsa del registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Usare il comando [az role assignment create][az-role-assignment-create] per assegnare il ruolo AcrPull all'identità. Questo ruolo offre [autorizzazioni pull](container-registry-roles.md) al registro. Per fornire autorizzazioni pull e push, assegnare il ruolo ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Usare l'identità per accedere al registro

SSH nella macchina virtuale Docker che è configurato con l'identità. Eseguire i comandi seguenti dell'interfaccia della riga di comando di Azure, tramite l'interfaccia della riga di comando di Azure installata nella macchina virtuale.

In primo luogo, eseguire l'autenticazione con la CLI di Azure [account di accesso di az][az-login], usando l'identità assegnata dal sistema della macchina virtuale.

```azurecli
az login --identity
```

Quindi, eseguire l'autenticazione al Registro di sistema [account di accesso di az acr][az-acr-login]. Quando si usa questo comando, l'interfaccia della riga di comando usa il token di Active Directory creato durante l'esecuzione di `az login` per l'autenticazione della sessione con il registro contenitori. (A seconda della configurazione della macchina virtuale, potrebbe essere necessario eseguire questo comando e i comandi di Docker con `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Dovrebbe essere visualizzato il messaggio `Login succeeded`. È quindi possibile eseguire i comandi `docker` senza fornire credenziali. Ad esempio, eseguire [docker pull][docker-pull] per eseguire il pull dell'immagine `aci-helloworld:v1`, specificando il nome del server di accesso del registro. Il nome del server di accesso è costituito dal nome del registro contenitori (solo lettere minuscole) seguito da `.azurecr.io`, ad esempio `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite altre informazioni sull'uso delle identità gestite con il Registro Azure Container e su come:

> [!div class="checklist"]
> * Abilitare un'identità assegnata dall'utente o dal sistema in una macchina virtuale di Azure
> * Concedere all'identità l'accesso al registro contenitori di Azure
> * Usare l'identità gestita per accedere al registro e per eseguire il pull di un'immagine del contenitore

* Altre informazioni sulle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
