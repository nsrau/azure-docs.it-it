---
title: Come configurare identità assegnate dal sistema e dall'utente in una macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure
description: Istruzioni dettagliate per configurare identità assegnate dal sistema e dall'utente in una macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: db32f56c55f189001e51a727ca6b5703e82dafe4
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903998"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Configurare l'identità del servizio gestita in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le seguenti operazioni di identità del servizio gestita in una macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure:
- Abilitare e disabilitare l'identità assegnata dal sistema in una macchina virtuale di Azure
- Aggiungere e rimuovere un'identità assegnata dall'utente in una macchina virtuale di Azure

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:

    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

Questa sezione illustra come abilitare e disabilitare l'identità assegnata dal sistema in una macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una macchina virtuale di Azure con l'identità assegnata dal sistema abilitata:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale:

   ```azurecli-interactive
   az login
   ```

2. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione della macchina virtuale e le risorse correlate, usando [az group create](/cli/azure/group/#az_group_create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm/#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVM* con un'identità assegnata dal sistema, come richiesto dal parametro `--assign-identity`. I parametri `--admin-username` e `--admin-password` specificano il nome utente e la password dell'account amministrativo per l'accesso alla macchina virtuale. Aggiornare questi valori in base alle esigenze specifiche dell'ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Abilitare l'identità assegnata dal sistema in una macchina virtuale di Azure esistente

Se è necessario abilitare l'identità assegnata dal sistema in una macchina virtuale esistente:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```azurecli-interactive
   az login
   ```

2. Usare [az vm identity assign](/cli/azure/vm/identity/#az_vm_identity_assign) con il comando `identity assign` per abilitare l'identità assegnata dal sistema in una macchina virtuale esistente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Disabilitare l'identità assegnata dal sistema in una macchina virtuale di Azure

> [!NOTE]
> La disabilitazione dell'identità del servizio gestita in una macchina virtuale non è attualmente supportata. Nel frattempo è possibile alternare tra l'uso delle identità assegnate dal sistema e delle identità assegnate dall'utente.

Se si ha una macchina virtuale che non ha più bisogno dell'identità assegnata dal sistema ma ha ancora bisogno di identità assegnate dall'utente, usare il comando seguente:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Per rimuovere l'estensione della macchina virtuale per l'identità del servizio gestita, usare il parametro `-n ManagedIdentityExtensionForWindows` o `-n ManagedIdentityExtensionForLinux` (a seconda del tipo di macchina virtuale) con [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità assegnata dall'utente in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Assegnare un'identità assegnata dall'utente durante la creazione di una macchina virtuale di Azure

Questa sezione descrive come creare una macchina virtuale con l'assegnazione di un'identità assegnata dall'utente. Se si dispone già di una macchina virtuale che si intende usare, ignorare questa sezione e procedere alla successiva.

1. Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio. Creare un [gruppo di risorse](~/articles/azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione dell'identità del servizio gestito usando [az group create](/cli/azure/group/#az_group_create). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<LOCATION>` con valori personalizzati. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Creare un'identità assegnata dall'utente mediante [az identity create](/cli/azure/identity#az_identity_create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità assegnata dall'utente, mentre il parametro `-n` ne specifica il nome.    
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g myResourceGroup -n myUserAssignedIdentity
```
La risposta contiene i dettagli relativi all'identità assegnata dall'utente creata ed è simile all'esempio seguente. Il valore dell'ID della risorsa assegnato all'identità assegnata dall'utente viene usato nel passaggio seguente.

```json
{
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
}
```

3. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm/#az_vm_create). L'esempio seguente crea una macchina virtuale associata alla nuova identità assegnata dall'utente, come specificato dal parametro `--assign-identity`. Sostituire i valori dei parametri `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<MSI ID>` con valori personalizzati. Per `<MSI ID>` usare la proprietà `id` della risorsa dell'identità assegnata dall'utente creata nel passaggio precedente: 

```azurecli-interactive 
az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Assegnare un'identità assegnata dall'utente a una VM di Azure esistente

1. Creare un'identità assegnata dall'utente mediante [az identity create](/cli/azure/identity#az-identity-create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<MSI NAME>` con valori personalizzati:

    > [!IMPORTANT]
    > La creazione di identità assegnate dall'utente con caratteri speciali (ad esempio il carattere di sottolineatura) nel nome non è attualmente supportata. Usare solo caratteri alfanumerici. Ricontrollare in seguito per aggiornamenti.  Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md).

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La risposta contiene i dettagli relativi all'identità del servizio gestita assegnata dall'utente creata ed è simile all'esempio seguente. Il valore `id` della risorsa assegnato all'identità assegnata dall'utente è usato nel passaggio seguente.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Assegnare l'identità assegnata dall'utente alla macchina virtuale mediante [az vm identity assign](/cli/azure/vm#az-vm-identity-assign). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<MSI ID>` sarà la proprietà `id` della risorsa dell'identità assegnata dall'utente creata nel passaggio precedente:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Rimuovere un'identità assegnata dall'utente da una macchina virtuale di Azure

> [!NOTE]
> La rimozione di tutte le identità assegnate dall'utente da una macchina virtuale non è attualmente supportata, a meno che non si disponga di un'identità assegnata dal sistema.

Se la macchina virtuale ha più identità assegnate dall'utente, è possibile rimuoverle tutte tranne l'ultima mediante [az vm identity remove](/cli/azure/vm#az-vm-identity-remove). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<MSI NAME>` è la proprietà `name` dell'identità assegnata dall'utente, che si può trovare nella sezione relativa all'identità della VM usando `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Se la macchina virtuale ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità assegnate dal sistema. Usare il comando seguente:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Contenuti correlati
- [Panoramica dell'Identità del servizio gestito](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere: 
  - [Creare una macchina virtuale Windows con l'interfaccia della riga di comando](../../virtual-machines/windows/quick-create-cli.md)  
  - [Creare una macchina virtuale Linux con l'interfaccia della riga di comando](../../virtual-machines/linux/quick-create-cli.md) 

















