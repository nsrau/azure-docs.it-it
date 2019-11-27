---
title: Configurare le identità gestite nella VM di Azure usando l'interfaccia della riga di comando di Azure-Azure AD
description: Istruzioni passo per passo per configurare identità gestite assegnate dal sistema e dall'utente in una macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca02505ba9b7d93cac4216916909a8c6df7fdd05
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184059"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le seguenti operazioni relative alle identità gestite per le risorse di Azure in una macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure:

- Abilitare e disabilitare l'identità gestita assegnata dal sistema in una macchina virtuale di Azure
- Aggiungere e rimuovere un'identità gestita assegnata dall'utente in una macchina virtuale di Azure

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)** .
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se si preferisce usare una console dell'interfaccia della riga di comando locale. 
      
      > [!NOTE]
      > I comandi sono stati aggiornati in base alla versione più recente dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione illustra come abilitare e disabilitare l'identità gestita assegnata dal sistema in una macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una macchina virtuale di Azure con l'identità gestita assegnata dal sistema abilitata, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale:

   ```azurecli-interactive
   az login
   ```

2. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione della macchina virtuale e le risorse correlate, usando [az group create](/cli/azure/group/#az-group-create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm/#az-vm-create). L'esempio seguente crea una macchina virtuale denominata *myVM* con un'identità gestita assegnata dal sistema, come richiesto dal parametro `--assign-identity`. I parametri `--admin-username` e `--admin-password` specificano il nome utente e la password dell'account amministrativo per l'accesso alla macchina virtuale. Aggiornare questi valori in base alle esigenze specifiche dell'ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Abilitare l'identità gestita assegnata dal sistema in una macchina virtuale di Azure esistente

Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```azurecli-interactive
   az login
   ```

2. Usare [az vm identity assign](/cli/azure/vm/identity/) con il comando `identity assign` per abilitare l'identità assegnata dal sistema in una macchina virtuale esistente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Disabilitare un'identità assegnata dal sistema in una macchina virtuale di Azure

Per disabilitare l'identità gestita assegnata dal sistema in una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

Se si dispone di una macchina virtuale che non necessita più dell'identità assegnata dal sistema ma ha ancora bisogno di identità assegnate dall'utente, usare il comando seguente:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se una macchina virtuale non necessita più dell'identità assegnata dal sistema ma non ha identità assegnate dall'utente, usare il comando seguente:

> [!NOTE]
> Il valore `none` distingue tra maiuscole e minuscole. Deve essere costituito da caratteri minuscoli. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Assegnare un'identità gestita assegnata dall'utente durante la creazione di una macchina virtuale di Azure

Per assegnare un'identità assegnata dall'utente a una macchina virtuale durante la creazione, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio. Creare un [gruppo di risorse](~/articles/azure-resource-manager/resource-group-overview.md#terminology) per contenere e distribuire l'identità gestita assegnata dall'utente mediante [az group create](/cli/azure/group/#az-group-create). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<LOCATION>` con valori personalizzati. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Creare un'identità gestita assegnata dall'utente tramite [az identity create](/cli/azure/identity#az-identity-create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità gestita assegnata dall'utente, mentre il parametro `-n` ne specifica il nome.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   La risposta contiene i dettagli relativi all'identità gestita assegnata dall'utente creata ed è simile all'esempio seguente. Il valore di ID risorsa assegnato all'identità gestita assegnata dall'utente viene usato nel passaggio seguente.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
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

3. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm/#az-vm-create). L'esempio seguente crea una macchina virtuale associata alla nuova identità assegnata dall'utente, come specificato dal parametro `--assign-identity`. Sostituire i valori dei parametri `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Assegnare un'identità gestita assegnata dall'utente a una macchina virtuale di Azure esistente

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Creare un'identità assegnata dall'utente mediante [az identity create](/cli/azure/identity#az-identity-create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

    > [!IMPORTANT]
    > La creazione di identità gestite assegnate dall'utente con caratteri speciali (ad esempio il carattere di sottolineatura) nel nome non è attualmente supportata. Usare solo caratteri alfanumerici. Ricontrollare in seguito per aggiornamenti.  Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   La risposta contiene i dettagli relativi all'identità gestita assegnata dall'utente creata ed è simile all'esempio seguente. 

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

2. Assegnare l'identità assegnata dall'utente alla macchina virtuale mediante [az vm identity assign](/cli/azure/vm). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY NAME>` è la proprietà `name` della risorsa dell'identità gestita assegnata dall'utente creata nel passaggio precedente:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure

Per rimuovere un'identità assegnata dall'utente da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor). 

Se è l'unica identità gestita assegnata dall'utente associata alla macchina virtuale, `UserAssigned` verrà rimosso dal valore del tipo di identità.  Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY>` sarà la proprietà `name` dell'identità assegnata dall'utente, che si può trovare nella sezione relativa all'identità della macchina virtuale usando `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Se la macchina virtuale non ha un'identità assegnata dal sistema e si vuole rimuovere tutte le identità gestite assegnate dall'utente al suo interno, usare il comando seguente:

> [!NOTE]
> Il valore `none` distingue tra maiuscole e minuscole. Deve essere costituito da caratteri minuscoli.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se la macchina virtuale ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità assegnate dal sistema. Usare il seguente comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere: 
  - [Creare una macchina virtuale Windows con l'interfaccia della riga di comando](../../virtual-machines/windows/quick-create-cli.md)  
  - [Creare una macchina virtuale Linux con l'interfaccia della riga di comando](../../virtual-machines/linux/quick-create-cli.md) 

















