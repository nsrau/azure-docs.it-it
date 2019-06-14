---
title: Come configurare system e le identità gestito assegnata dall'utente su una scala di macchine virtuali di Azure impostata tramite la CLI di Azure
description: Procedura dettagliata le istruzioni per la configurazione di sistema e le identità gestito assegnata dall'utente su una scala di macchine virtuali di Azure impostato, utilizzando CLI di Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04a3c9eba1f6498796a7e617b400649963c996d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290931"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprenderà come eseguire le seguenti identità gestite per le operazioni delle risorse di Azure in un set di scalabilità di macchine virtuali di Azure, tramite la CLI di Azure:
- Abilitare e disabilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure
- Aggiungere e rimuovere un'identità gestita assegnata dall'utente in un set di scalabilità di macchine virtuali di Azure


## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)** .
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere le seguenti assegnazioni di controllo degli accessi in base al ruolo:

    > [!NOTE]
    > Non sono necessarie altre assegnazioni di ruoli della directory di Azure AD.

    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per creare un set di scalabilità di macchine virtuali e abilitare e rimuovere da un set di scalabilità di macchine virtuali l'identità gestita assegnata dal sistema e/o dall'utente.
    - [Managed Identity Contributor (Collaboratore per identità gestita)](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare un'identità gestita assegnata dall'utente.
    - [Operatore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-operator) per assegnare e rimuovere un'identità gestita assegnata dall'utente da e verso un set di scalabilità di macchine virtuali.
- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 
      
      > [!NOTE]
      > I comandi sono stati aggiornati in base alla versione più recente dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

In questa sezione descrive come abilitare e disabilitare l'identità assegnata dal sistema gestito per un macchina virtuale di Azure set di scalabilità con Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali di Azure

Per creare un set di scalabilità di macchine virtuali con l'identità gestita assegnata dal sistema abilitata:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure in cui si vuole distribuire il set di scalabilità di macchine virtuali:

   ```azurecli-interactive
   az login
   ```

2. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione del set di scalabilità di macchine virtuali e delle risorse correlate, usando [az group create](/cli/azure/group/#az-group-create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Creare un set di scalabilità di macchine virtuali con [az vmss create](/cli/azure/vmss/#az-vmss-create). L'esempio seguente crea un set di scalabilità di macchine virtuali denominato *myVMSS* con un'identità gestita assegnata dal sistema, come richiesto dal parametro `--assign-identity`. I parametri `--admin-username` e `--admin-password` specificano il nome utente e la password dell'account amministrativo per l'accesso alla macchina virtuale. Aggiornare questi valori in base alle esigenze specifiche dell'ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente

Se è necessario abilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

   ```azurecli-interactive
   az login
   ```

2. Usare il comando [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) per abilitare un'identità gestita assegnata dal sistema a una VM esistente:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Disabilitare un'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali di Azure

Se un set di scalabilità di macchine virtuali non necessità più dell'identità gestita assegnata dal sistema ma necessita ancora delle identità gestite assegnate dall'utente, usare il comando seguente:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se si ha una macchina virtuale che non ha più bisogno dell'identità gestita assegnata dal sistema e non dispone di identità gestite assegnate dall'utente, usare il comando seguente:

> [!NOTE]
> Il valore `none` distingue tra maiuscole e minuscole. Deve essere costituito da caratteri in minuscolo. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

> [!NOTE]
> Se è stato eseguito il provisioning di identità gestita per le risorse di Azure della macchina virtuale dell'estensione (deprecato), è necessario rimuoverla usando [delete di estensione az vmss](https://docs.microsoft.com/cli/azure/vm/). Per altre informazioni, vedere [eseguire la migrazione dall'estensione della macchina virtuale al servizio metadati dell'istanza di Azure per l'autenticazione](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione descrive come abilitare e rimuovere un'identità gestita assegnata dall'utente mediante l'interfaccia della riga di comando di Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Assegnare un'identità gestita assegnata dall'utente durante la creazione di un set di scalabilità di macchine virtuali

In questa sezione illustra la creazione di un set di scalabilità di macchine virtuali e l'assegnazione di un'identità gestita assegnata dall'utente per il set di scalabilità di macchine virtuali. Se si dispone già di un set di scalabilità di macchine virtuali da usare, ignorare questa sezione e procedere al successivo.

1. Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio. Creare un [gruppo di risorse](~/articles/azure-resource-manager/resource-group-overview.md#terminology) per contenere e distribuire l'identità gestita assegnata dall'utente mediante [az group create](/cli/azure/group/#az-group-create). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<LOCATION>` con valori personalizzati. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Creare un'identità gestita assegnata dall'utente tramite [az identity create](/cli/azure/identity#az-identity-create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità gestita assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   La risposta contiene i dettagli relativi all'identità gestita assegnata dall'utente creata ed è simile all'esempio seguente. Il valore `id` della risorsa assegnato all'identità gestita assegnata dall'utente viene usato nel passaggio seguente.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Creare un macchina virtuale set di scalabilità usando [az vmss create](/cli/azure/vmss/#az-vmss-create). L'esempio seguente crea un set di scalabilità di macchine virtuali associati con la nuova identità assegnata dall'utente gestito, come specificato da di `--assign-identity` parametro. Sostituire i valori dei parametri `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY>` con valori personalizzati. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Associare l'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali esistente

1. Creare un'identità gestita assegnata dall'utente mediante [az identity create](/cli/azure/identity#az-identity-create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità gestita assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   La risposta contiene i dettagli relativi all'identità gestita assegnata dall'utente creata ed è simile all'esempio seguente.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Assegnare l'identità gestito assegnata dall'utente per la scalabilità di macchine virtuali impostato utilizzando [identità di az vmss assegnare](/cli/azure/vmss/identity). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY>` è la proprietà `name` della risorsa dell'identità assegnata dall'utente creata nel passaggio precedente:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali di Azure

Per rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali, usare [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Se è l'unica identità gestita assegnata dall'utente associata al set di scalabilità di macchine virtuali, `UserAssigned` verrà rimosso dal valore del tipo di identità.  Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY>` sarà la proprietà `name` dell'identità gestita assegnata dall'utente, che si può trovare nella sezione relativa all'identità del set di scalabilità di macchine virtuali usando `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Se il set di scalabilità di macchine virtuali non ha un'identità gestita assegnata dal sistema e si vuole rimuovere tutte le identità gestite assegnate dall'utente al suo interno, usare il comando seguente:

> [!NOTE]
> Il valore `none` distingue tra maiuscole e minuscole. Deve essere costituito da caratteri in minuscolo.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se il set di scalabilità di macchine virtuali ha identità gestite sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità gestite assegnate dal sistema. Usare il comando seguente:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per la guida introduttiva completa sulla creazione del set di scalabilità di macchine virtuali di Azure vedere: 

  - [Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















