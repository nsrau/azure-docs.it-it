---
title: Identità gestita nell'attività ACR
description: Abilitare un'identità gestita per le risorse di Azure in un'attività di Container Registry di Azure per consentire all'attività di accedere ad altre risorse di Azure, inclusi altri registri di contenitori privati.
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
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Usare un'identità gestita da Azure nelle attività ACR 

Abilitare un' [identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) in un' [attività ACR](container-registry-tasks-overview.md), in modo che l'attività possa accedere ad altre risorse di Azure, senza dover fornire o gestire le credenziali. Ad esempio, usare un'identità gestita per consentire a un passaggio dell'attività di eseguire il pull o il push delle immagini del contenitore in un altro registro.

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per abilitare un'identità gestita assegnata dall'utente o dal sistema in un'attività ACR. È possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si vuole usarlo localmente, è richiesta la versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

A scopo illustrativo, i comandi di esempio in questo articolo usano [AZ ACR task create][az-acr-task-create] per creare un'attività di compilazione di un'immagine di base che Abilita un'identità gestita. Per gli scenari di esempio per accedere alle risorse protette da un'attività ACR usando un'identità gestita, vedere:

* [Autenticazione tra più registri](container-registry-tasks-cross-registry-authentication.md)
* [Accedi a risorse esterne con segreti archiviati in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Perché usare un'identità gestita?

Un'identità gestita per le risorse di Azure offre servizi di Azure selezionati con un'identità gestita automaticamente in Azure Active Directory. È possibile configurare un'attività ACR con un'identità gestita in modo che l'attività possa accedere ad altre risorse di Azure protette, senza passare le credenziali nei passaggi dell'attività.

Esistono due tipi di identità gestite:

* *Identità assegnate dall'utente*, che è possibile assegnare a più risorse e mantenerle per il tempo desiderato. Le identità assegnate dall'utente sono attualmente in anteprima.

* *Identità assegnata dal sistema*, che è univoca per una risorsa specifica, ad esempio un'attività ACR, e dura per la durata di tale risorsa.

È possibile abilitare uno o entrambi i tipi di identità in un'attività ACR. Concedere all'identità l'accesso a un'altra risorsa, analogamente a qualsiasi entità di sicurezza. Quando viene eseguita, l'attività usa l'identità per accedere alla risorsa in qualsiasi passaggio dell'attività che richiede l'accesso.

## <a name="steps-to-use-a-managed-identity"></a>Passaggi per usare un'identità gestita

Seguire questi passaggi di alto livello per usare un'identità gestita con un'attività ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (facoltativo) creare un'identità assegnata dall'utente

Se si prevede di usare un'identità assegnata dall'utente, usare un'identità esistente o creare l'identità usando l'interfaccia della riga di comando di Azure o altri strumenti di Azure. Ad esempio, usare il comando [AZ Identity create][az-identity-create] . 

Se si prevede di usare solo un'identità assegnata dal sistema, ignorare questo passaggio. Quando si crea l'attività ACR, si crea un'identità assegnata dal sistema.

### <a name="2-enable-identity-on-an-acr-task"></a>2. abilitare l'identità in un'attività ACR

Quando si crea un'attività ACR, è possibile abilitare facoltativamente un'identità assegnata dall'utente, un'identità assegnata dal sistema o entrambe. Ad esempio, passare il `--assign-identity` parametro quando si esegue il comando [AZ ACR task create][az-acr-task-create] nell'interfaccia della riga di comando di Azure.

Per abilitare un'identità assegnata dal sistema, `--assign-identity` passare senza valore o `assign-identity [system]`. Il comando di esempio seguente crea un'attività Linux da un repository GitHub pubblico che compila `hello-world` l'immagine e Abilita un'identità gestita assegnata dal sistema:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Per abilitare un'identità assegnata dall'utente, `--assign-identity` passare con un valore dell' *ID risorsa* dell'identità. Il comando di esempio seguente crea un'attività Linux da un repository GitHub pubblico che compila `hello-world` l'immagine e Abilita un'identità gestita assegnata dall'utente:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

È possibile ottenere l'ID risorsa dell'identità eseguendo il comando [AZ Identity Show][az-identity-show] . L'ID di risorsa per l'ID *myUserAssignedIdentity* nel gruppo di risorse *myResourceGroup* è nel formato seguente: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. concedere le autorizzazioni di identità per accedere ad altre risorse di Azure

A seconda dei requisiti dell'attività, concedere le autorizzazioni di identità per accedere ad altre risorse di Azure. Tra gli esempi sono inclusi:

* Assegnare all'identità gestita un ruolo con pull, push e pull o altre autorizzazioni per un registro contenitori di destinazione in Azure. Per un elenco completo dei ruoli del registro di sistema, vedere [autorizzazioni e ruoli di container Registry di Azure](container-registry-roles.md). 
* Assegnare un ruolo all'identità gestita per leggere i segreti in un insieme di credenziali delle chiavi di Azure.

Usare l' [interfaccia](../role-based-access-control/role-assignments-cli.md) della riga di comando di Azure o altri strumenti di Azure per gestire l'accesso basato sui ruoli alle risorse. Ad esempio, eseguire il comando [AZ Role Assignment create][az-role-assignment-create] per assegnare l'identità a un ruolo alla risorsa. 

Nell'esempio seguente viene assegnata un'identità gestita alle autorizzazioni per effettuare il pull da un registro contenitori. Il comando specifica l' *ID entità* dell'identità dell'attività e l' *ID risorsa* del registro di sistema di destinazione.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (facoltativo) aggiungere le credenziali all'attività

Se l'attività richiede credenziali per eseguire il pull o il push di immagini in un altro registro personalizzato o per accedere ad altre risorse, aggiungere le credenziali all'attività. Eseguire il comando [AZ ACR Task Credential Add][az-acr-task-credential-add] per aggiungere le credenziali e passare il `--use-identity` parametro per indicare che l'identità può accedere alle credenziali. 

Ad esempio, per aggiungere le credenziali per un'identità assegnata dal sistema per l'autenticazione con il registro *targetregistry*contenitori di Azure `use-identity [system]`targetregistry, passare:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Per aggiungere le credenziali per un'identità assegnata dall'utente per l'autenticazione con il *targetregistry*del `use-identity` registro di sistema, passare con un valore dell' *ID client* dell'identità. Ad esempio:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

È possibile ottenere l'ID client dell'identità eseguendo il comando [AZ Identity Show][az-identity-show] . L'ID client è un GUID del modulo `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="5-run-the-task"></a>5. eseguire l'attività

Dopo aver configurato un'attività con un'identità gestita, eseguire l'attività. Ad esempio, per testare una delle attività create in questo articolo, attivarla manualmente con il comando [AZ ACR task run][az-acr-task-run] . Se sono stati configurati trigger attività automatizzati aggiuntivi, l'attività viene eseguita quando viene attivato automaticamente.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come abilitare e usare un'identità gestita assegnata dall'utente o assegnata dal sistema in un'attività ACR. Per gli scenari di accesso alle risorse protette da un'attività ACR usando un'identità gestita, vedere:

* [Autenticazione tra più registri](container-registry-tasks-cross-registry-authentication.md)
* [Accedi a risorse esterne con segreti archiviati in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
