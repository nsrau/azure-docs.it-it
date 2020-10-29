---
title: Gestire le chiavi in un modulo di protezione hardware gestito - Azure Key Vault | Microsoft Docs
description: Questo articolo contiene informazioni su come gestire le chiavi in un modulo di protezione hardware gestito
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 3f054638e09061c652946c9c2db1a32db73c23d9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521034"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Gestire un modulo di protezione hardware gestito con l'interfaccia della riga di comando di Azure

> [!NOTE]
> Key Vault supporta due tipi di risorse: insiemi di credenziali e moduli di protezione hardware gestiti. Questo articolo riguarda il **modulo di protezione hardware gestito** . Per informazioni su come gestire un insieme di credenziali, vedere [Gestire Key Vault con l'interfaccia della riga di comando di Azure](../general/manage-with-cli2.md).

Per una panoramica del modulo di protezione hardware gestito, vedere [Che cos'è il modulo di protezione hardware gestito?](overview.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

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

> [!NOTE]
> In tutti i comandi seguenti vengono illustrati due metodi di utilizzo. Uno usa i parametri **--hsm-name** e **--name** (per il nome della chiave) e l'altro usa il parametro **--id** in cui è possibile specificare l'intero URL incluso, se necessario, il nome della chiave. Il secondo metodo è utile quando il chiamante (un utente o un'applicazione) non ha accesso in lettura al piano di controllo e ha accesso limitato al piano dati.

## <a name="create-an-hsm-key"></a>Creare una chiave del modulo di protezione hardware

Usare il comando `az keyvault key create` per creare una chiave.

### <a name="create-an-rsa-key"></a>Creare una chiave RSA

L'esempio seguente illustra come creare una chiave **RSA** a 3072 bit che verrà usata solo per le operazioni **wrapKey, unwrapKey** (--ops). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Tenere presente che l'operazione di `get` restituisce solo gli attributi di chiave e di chiave pubblica. Non restituisce la chiave privata (in caso di chiave asimmetrica) o il materiale della chiave (in caso di chiave simmetrica).

### <a name="create-an-ec-key"></a>Creare una chiave EC

Nell'esempio seguente viene illustrato come creare una chiave **EC** con una curva P-256 che verrà usata solo per le operazioni **sign e verify** (--ops) e ha due tag, **usage** e **appname** . I tag consentono di aggiungere altri metadati alla chiave per facilitarne il rilevamento e la gestione.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Creare una chiave simmetrica a 256 bit

L'esempio seguente illustra come creare una chiave **simmetrica** a 256 bit che verrà usata solo per le operazioni **encrypt e decrypt** (--ops).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Visualizzare i tag e gli attributi della chiave

Usare il comando `az keyvault key show` per visualizzare attributi, versioni e tag di una chiave.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>List keys

Usare il comando `az keyvault key list` per elencare tutte le chiavi presenti in un modulo di protezione hardware gestito.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Eliminare una chiave

Usare il comando `az keyvault key delete` per eliminare una chiave da un modulo di protezione hardware gestito. Tenere presente che l'eliminazione temporanea è sempre attiva. Di conseguenza, una chiave eliminata rimarrà nello stato eliminato e potrà essere recuperata fino a quando non sarà trascorso il numero di giorni di conservazione quando la chiave verrà eliminata (eliminata definitivamente) e non sarà possibile recuperarla.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Elencare le chiavi eliminate

Usare il comando `az keyvault key list-deleted` per elencare tutte le chiavi nello stato eliminato presenti in un modulo di protezione hardware gestito.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Ripristinare una chiave eliminata annullandone l'eliminazione

Usare il comando `az keyvault key list-deleted` per elencare tutte le chiavi nello stato eliminato presenti in un modulo di protezione hardware gestito. Se è necessario recuperare una chiave annullandone l'eliminazione con il parametro --id durante il ripristino di una chiave eliminata, è necessario prendere nota del valore `recoveryId` della chiave eliminata ottenuto con il comando `az keyvault key list-deleted`.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Rimuovere (eliminare definitivamente) una chiave

Usare il comando `az keyvault key purge` per rimuovere (eliminare definitivamente) una chiave.

> [!NOTE]
> Se la protezione dall'eliminazione è abilitata per il modulo di protezione hardware gestito, l'operazione di rimozione non sarà consentita. La chiave verrà rimossa automaticamente una volta trascorso il periodo di conservazione.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Creare il backup di una singola chiave

Usare il comando `az keyvault key backup` per creare il backup di una singola chiave. Il file di backup è un BLOB crittografato associato al dominio di sicurezza del modulo di protezione hardware di origine. Può essere ripristinato solo in moduli di protezione hardware che condividono lo stesso dominio di sicurezza. Per altre informazioni, vedere [Dominio di sicurezza](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Ripristinare una singola chiave dal backup

Usare il comando `az keyvault key restore` per ripristinare una singola chiave. Il modulo di protezione hardware di origine in cui è stato creato il backup deve condividere lo stesso dominio di sicurezza del modulo di protezione hardware di destinazione in cui viene ripristinata la chiave.

> [!NOTE]
> Il ripristino non riuscirà se è presente una chiave con lo stesso nome nello stato attivo o eliminato.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Importare una chiave da un file

Usare il comando `az keyvault key import` per importare una chiave (solo RSA e EC) da un file. Il file di certificato deve includere la chiave privata e usare la codifica PEM (come definito negli RFC [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Per importare una chiave dal modulo di protezione hardware locale al modulo di protezione hardware gestito, vedere [Importare chiavi con protezione HSM nel modulo di protezione hardware gestito (BYOK)](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sull'interfaccia della riga di comando di Azure per i comandi di Key Vault, vedere [Key Vault CLI reference](/cli/azure/keyvault) (Riferimento dell'interfaccia della riga di comando di Key Vault).
- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori di Azure Key Vault](../general/developers-guide.md).
- Per altre informazioni, vedere [Gestione dei ruoli del modulo di protezione hardware gestito](role-management.md)
- Per altre informazioni, vedere [Procedure consigliate per il modulo di protezione hardware gestito](best-practices.md)
