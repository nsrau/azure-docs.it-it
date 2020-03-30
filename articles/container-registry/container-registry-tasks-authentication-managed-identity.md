---
title: Identità gestita nell'attività ACR
description: Abilitare un'identità gestita per le risorse di Azure in un'attività Del Registro di sistema del contenitore di Azure per consentire all'attività di accedere ad altre risorse di Azure, inclusi altri registri del contenitore privato.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111775"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Usare un'identità gestita da Azure nelle attività ACRUse an Azure-managed identity in ACR Tasks 

Abilitare [un'identità gestita per](../active-directory/managed-identities-azure-resources/overview.md) le risorse di Azure in [un'attività ACR,](container-registry-tasks-overview.md)in modo che l'attività possa accedere ad altre risorse di Azure, senza dover fornire o gestire le credenziali. Ad esempio, usare un'identità gestita per consentire a un passaggio dell'attività di eseguire il pull o il push delle immagini del contenitore in un altro Registro di sistema.

In questo articolo viene illustrato come usare l'interfaccia della riga di comando di Azure per abilitare un'identità gestita assegnata dall'utente o dal sistema in un'attività ACR. È possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure.You can use the Azure Cloud Shell or a local installation of the Azure CLI. Se si desidera utilizzarlo in locale, è necessaria la versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI.

A scopo illustrativo, i comandi di esempio in questo articolo usano [az acr task create][az-acr-task-create] per creare un'attività di compilazione dell'immagine di base che abilita un'identità gestita. Per scenari di esempio per accedere alle risorse protette da un'attività ACR usando un'identità gestita, vedere:For sample scenarios to access secured resources from an ACR task using a managed identity, see:

* [Autenticazione tra più registri](container-registry-tasks-cross-registry-authentication.md)
* [Accedere a risorse esterne con segreti archiviati nell'insieme di credenziali delle chiavi di AzureAccess external resources with secrets stored in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Perché usare un'identità gestita?

Un'identità gestita per le risorse di Azure fornisce i servizi di Azure selezionati con un'identità gestita automaticamente in Azure Active Directory.A managed identity for Azure resources provides selected Azure services with an automatically managed identity in Azure Active Directory. È possibile configurare un'attività ACR con un'identità gestita in modo che l'attività possa accedere ad altre risorse di Azure protette, senza passare le credenziali nei passaggi dell'attività.

Esistono due tipi di identità gestite:

* *Identità assegnate*dall'utente , che è possibile assegnare a più risorse e persistono per tutto il tempo desiderato. Le identità assegnate dall'utente sono attualmente in anteprima.

* *Un'identità assegnata dal sistema,* univoca per una risorsa specifica, ad esempio un'attività ACR, e dura per la durata della risorsa.

È possibile abilitare uno o entrambi i tipi di identità in un'attività ACR. Concedere l'accesso all'identità a un'altra risorsa, proprio come qualsiasi entità di sicurezza. Quando l'attività viene eseguita, usa l'identità per accedere alla risorsa in tutti i passaggi dell'attività che richiedono l'accesso.

## <a name="steps-to-use-a-managed-identity"></a>Passaggi per l'uso di un'identità gestitaSteps to use a managed identity

Seguire questi passaggi generali per usare un'identità gestita con un'attività ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Facoltativo) Creare un'identità assegnata dall'utente

Se si prevede di usare un'identità assegnata dall'utente, usare un'identità esistente o creare l'identità usando l'interfaccia della riga di comando di Azure o altri strumenti di Azure.If you plan to use a user-assigned identity, use an existing identity, or create the identity using the Azure CLI or other Azure tools. Ad esempio, utilizzare il comando [az identity create.][az-identity-create] 

Se si prevede di utilizzare solo un'identità assegnata dal sistema, ignorare questo passaggio. Si crea un'identità assegnata dal sistema quando si crea l'attività ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Abilitare l'identità in un'attività ACR

Quando si crea un'attività ACR, abilitare facoltativamente un'identità assegnata dall'utente, un'identità assegnata dal sistema o entrambe. Ad esempio, `--assign-identity` passare il parametro quando si esegue il comando [az acr task create][az-acr-task-create] nell'interfaccia della riga di comando di Azure.For example, pass the parameter when you run the az acr task create command in the Azure CLI.

Per abilitare un'identità `--assign-identity` assegnata dal `assign-identity [system]`sistema, passare senza alcun valore o . Il comando di esempio seguente crea un'attività Linux `hello-world` da un repository GitHub pubblico che compila l'immagine e abilita un'identità gestita assegnata dal sistema:The following example command creates a Linux task from a public GitHub repository which builds the image and enables a system-assigned managed identity:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Per abilitare un'identità `--assign-identity` assegnata dall'utente, passare con un valore dell'ID *risorsa* dell'identità. Il comando di esempio seguente crea un'attività Linux `hello-world` da un repository GitHub pubblico che compila l'immagine e abilita un'identità gestita assegnata dall'utente:The following example command creates a Linux task from a public GitHub repository which builds the image and enables a user-assigned managed identity:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

È possibile ottenere l'ID risorsa dell'identità eseguendo il comando [az identity show.][az-identity-show] L'ID risorsa per l'ID *myUserAssignedIdentity* nel gruppo di risorse *myResourceGroup* ha il formato seguente: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Concedere le autorizzazioni di identità per accedere ad altre risorse di Azure

A seconda dei requisiti dell'attività, concedere le autorizzazioni di identità per accedere ad altre risorse di Azure.Depending on the requirements of your task, grant the identity permissions to access other Azure resources. Tra gli esempi sono inclusi:

* Assegnare all'identità gestita un ruolo con pull, push e pull o altre autorizzazioni per un registro del contenitore di destinazione in Azure.Assign the managed identity a role with pull, pull and pull, or other permissions to a target container registry in Azure. Per un elenco completo dei ruoli del Registro di sistema, vedere Autorizzazioni e ruoli del [Registro di](container-registry-roles.md)sistema contenitore di Azure. 
* Assegnare all'identità gestita un ruolo per leggere i segreti in un insieme di credenziali delle chiavi di Azure.Assign the managed identity a role to read secrets in an Azure key vault.

Usare [l'interfaccia della riga di comando](../role-based-access-control/role-assignments-cli.md) di Azure o altri strumenti di Azure per gestire l'accesso basato sui ruoli alle risorse. Ad esempio, eseguire il comando [az role assignment create][az-role-assignment-create] per assegnare all'identità un ruolo alla risorsa. 

Nell'esempio seguente viene assegnata a un'identità gestita le autorizzazioni per il pull da un registro contenitori. Il comando specifica *l'ID entità* dell'identità dell'attività e l'ID *risorsa* del Registro di sistema di destinazione.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Facoltativo) Aggiungere credenziali all'attività

Se l'attività necessita di credenziali per eseguire il pull o il push delle immagini in un altro registro personalizzato o per accedere ad altre risorse, aggiungere le credenziali all'attività. Eseguire il comando [az acr credential add][az-acr-task-credential-add] per `--use-identity` aggiungere le credenziali e passare il parametro per indicare che l'identità può accedere alle credenziali. 

Ad esempio, per aggiungere le credenziali per un'identità assegnata dal `use-identity [system]`sistema per l'autenticazione con il *registro*di sistema del contenitore di Azure , passare:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Per aggiungere le credenziali per un'identità assegnata dall'utente per l'autenticazione con il registro di *sistema targetregistry*, passare `use-identity` con un valore dell'ID *client* dell'identità. Ad esempio:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

È possibile ottenere l'ID client dell'identità eseguendo il comando [az identity show.][az-identity-show] L'ID client è un `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID nel formato .

### <a name="5-run-the-task"></a>5. Eseguire l'attività

Dopo aver configurato un'attività con un'identità gestita, eseguire l'attività. Ad esempio, per testare una delle attività create in questo articolo, attivarla manualmente utilizzando il comando [az acr task run.][az-acr-task-run] Se sono stati configurati trigger di attività aggiuntivi automatizzati, l'attività viene eseguita quando viene attivata automaticamente.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come abilitare e utilizzare un'identità gestita assegnata dall'utente o dal sistema in un'attività ACR. Per gli scenari per accedere alle risorse protette da un'attività ACR usando un'identità gestita, vedere:For scenarios to access secured resources from an ACR task using a managed identity, see:

* [Autenticazione tra più registri](container-registry-tasks-cross-registry-authentication.md)
* [Accedere a risorse esterne con segreti archiviati nell'insieme di credenziali delle chiavi di AzureAccess external resources with secrets stored in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
