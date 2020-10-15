---
title: Gestione dei ruoli del piano dati del modulo di protezione hardware gestito - Azure Key Vault | Microsoft Docs
description: Questo articolo contiene informazioni su come gestire le assegnazione di ruolo per il modulo di protezione hardware gestito
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 814167425fcd39e90edccd952e1a3e4fbd570988
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818030"
---
# <a name="managed-hsm-role-management"></a>Gestione dei ruoli del modulo di protezione hardware gestito

> [!NOTE]
> Key Vault supporta due tipi di risorsa: insiemi di credenziali e moduli di protezione hardware gestiti. Questo articolo riguarda il **modulo di protezione hardware gestito**. Per informazioni su come gestire un insieme di credenziali, vedere [Gestire Key Vault con l'interfaccia della riga di comando di Azure](../general/manage-with-cli2.md).

Per una panoramica del modulo di protezione hardware gestito, vedere [Che cos'è il modulo di protezione hardware gestito?](overview.md). Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questo articolo illustra come gestire i ruoli per un piano dati del modulo di protezione hardware gestito. Per informazioni sul modello di controllo di accesso del modulo di protezione hardware gestito, vedere [Controllo di accesso del modulo di protezione hardware gestito](access-control.md).

Per consentire a un'entità di sicurezza, ad esempio un utente, un'entità servizio, un gruppo o un'identità gestita, di eseguire operazioni del piano dati del modulo di protezione hardware gestito, è necessario assegnare all'entità un ruolo che consenta l'esecuzione di tali operazioni. Se, ad esempio, si vuole consentire a un'applicazione di eseguire un'operazione di firma usando una chiave, è necessario assegnare all'applicazione un ruolo che includa "Microsoft.KeyVault/managedHSM/keys/sign/action" tra le azioni dati. Un ruolo può essere assegnato a un ambito specifico. Il controllo degli accessi in base al ruolo locale del modulo di protezione hardware gestito supporta due ambiti, a livello di modulo di protezione hardware (`/` o `/keys`) e per chiave (`/keys/<keyname>`).

Per un elenco di tutti i ruoli predefiniti del modulo di protezione hardware gestito e delle relative operazioni consentite, vedere [Ruoli predefiniti del modulo di protezione hardware gestito](built-in-roles.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare i comandi dell'interfaccia della riga di comando di Azure in questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando di Azure versione 2.12.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
* Un modulo di protezione hardware gestito nella sottoscrizione. Vedere [Avvio rapido: Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure](quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure usando l'interfaccia della riga di comando è possibile digitare:

```azurecli
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-new-role-assignment"></a>Creare una nuova assegnazione di ruolo

### <a name="assign-roles-for-all-keys"></a>Assegnare ruoli per tutte le chiavi

Usare il comando `az keyvault role assignment create` per assegnare un ruolo **Managed HSM Crypto Officer** all'utente identificato dal nome dell'entità utente **user2\@contoso.com** per tutte le **chiavi** (ambito `/keys`) in ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Assegnare il ruolo per una chiave specifica

Usare il comando `az keyvault role assignment create` per assegnare un ruolo **Managed HSM Crypto Officer** all'utente identificato dal nome dell'entità utente **user2\@contoso.com** per una chiave specifica denominata **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Elencare le assegnazioni di ruolo esistenti

Usare `az keyvault role assignment list` per elencare le assegnazioni di ruolo.

Tutte le assegnazioni di ruolo nell'ambito / (impostazione predefinita quando non è specificato alcun valore per --scope) per tutti gli utenti (impostazione predefinita quando è specificato un valore per --assignee)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Tutte le assegnazioni di ruolo a livello del modulo di protezione hardware per un utente specifico **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

Tutte le assegnazioni di ruolo per un utente specifico **user2@contoso.com** per una chiave specifica **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Un'assegnazione di ruolo specifica per il ruolo **Managed HSM Crypto Officer** per un utente specifico **user2@contoso.com** per una chiave specifica **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Eliminare un'assegnazione di ruolo

Usare il comando `az keyvault role assignment delete` per eliminare un ruolo **Managed HSM Crypto Officer** assegnato all'utente **user2\@contoso.com** per la chiave **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Elencare tutte le definizioni del ruolo disponibili

Usare il comando `az keyvault role definition list` per elencare tutte le definizioni del ruolo.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>Passaggi successivi

- Panoramica del [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).
- Esercitazione sulla [gestione dei ruoli del modulo di protezione hardware gestito](role-management.md)
- Per altre informazioni, vedere [Modello di controllo di accesso del modulo di protezione hardware gestito](access-control.md)
- Vedere tutti i [ruoli predefiniti per il controllo degli accessi in base al ruolo del modulo di protezione hardware gestito](built-in-roles.md)
