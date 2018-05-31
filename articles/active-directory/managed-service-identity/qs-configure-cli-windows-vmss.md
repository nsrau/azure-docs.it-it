---
title: Come configurare identità assegnate dal sistema e dall'utente in un VMSS di Azure mediante l'interfaccia della riga di comando di Azure
description: Istruzioni passo per passo per configurare identità assegnate dal sistema e dall'utente in un VMSS di Azure mediante l'interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: faf526082a9a38d5d98443ff2b74eac4eef1ca08
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157456"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Configurare un'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite l'interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo descrive come eseguire le seguenti operazioni di identità del servizio gestita in un set di scalabilità di macchine virtuali (VMSS) mediante l'interfaccia della riga di comando di Azure:
- Abilitare e disabilitare l'identità assegnata dal sistema in un VMSS di Azure
- Aggiungere e rimuovere un'identità assegnata dall'utente in un VMSS di Azure


## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:

- Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

Questa sezione descrive come abilitare e disabilitare l'identità assegnata dal sistema per un VMSS di Azure mediante l'interfaccia della riga di comando di Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Abilitare l'identità assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali di Azure

Per creare un set di scalabilità di macchine virtuali con l'identità assegnata dal sistema abilitata:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure in cui si vuole distribuire il set di scalabilità di macchine virtuali:

   ```azurecli-interactive
   az login
   ```

2. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione del set di scalabilità di macchine virtuali e delle risorse correlate, usando [az group create](/cli/azure/group/#az_group_create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Creare un set di scalabilità di macchine virtuali con [az vmss create](/cli/azure/vmss/#az_vmss_create). L'esempio seguente crea un set di scalabilità di macchine virtuali denominato *myVMSS* con un'identità assegnata dal sistema, come richiesto dal parametro `--assign-identity`. I parametri `--admin-username` e `--admin-password` specificano il nome utente e la password dell'account amministrativo per l'accesso alla macchina virtuale. Aggiornare questi valori in base alle esigenze specifiche dell'ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Abilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente

Se è necessario abilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

   ```azurecli-interactive
   az login
   ```

2. Usare il comando [az vmss identity assign](/cli/azure/vmss/identity/#az_vmss_identity_assign) per abilitare un'identità assegnata dal sistema a una VM esistente:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Disabilitare l'identità assegnata dal sistema da un set di scalabilità di macchine virtuali di Azure

> [!NOTE]
> La disabilitazione dell'identità del servizio gestita da un set di scalabilità di macchine virtuali non è attualmente supportata. Nel frattempo è possibile alternare tra l'uso delle identità assegnate dal sistema e delle identità assegnate dall'utente. Ricontrollare in seguito per aggiornamenti.

Se si dispone di un set di scalabilità di macchine virtuali che non ha più bisogno di un'identità assegnata dal sistema, ma ha ancora bisogno di identità assegnate dagli utenti, eseguire il comando seguente:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Per rimuovere l'estensione identità del servizio gestita della VM, usare il comando [az vmss identity remove](/cli/azure/vmss/identity/#az_vmss_remove_identity) per rimuovere l'identità assegnata dal sistema da un VMSS:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione descrive come abilitare e rimuovere un'identità assegnata dall'utente mediante l'interfaccia della riga di comando di Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Assegnare un'identità assegnata dall'utente durante la creazione di un VMSS di Azure

Questa sezione descrive come creare un VMSS e assegnare un'identità assegnata dall'utente al VMSS. Se si dispone già di un VMSS che si intende usare, ignorare questa sezione e procedere con la successiva.

1. Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio. Creare un [gruppo di risorse](~/articles/azure-resource-manager/resource-group-overview.md#terminology) per contenere e distribuire l'identità assegnata dall'utente mediante [az group create](/cli/azure/group/#az_group_create). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<LOCATION>` con valori personalizzati. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Creare un'identità assegnata dall'utente mediante [az identity create](/cli/azure/identity#az-identity-create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

    > [!IMPORTANT]
    > La creazione delle identità assegnate dall'utente supporta solo caratteri alfanumerici e trattino (numeri 0-9, lettere a-z e A-Z, -). Inoltre, il nome può avere al massimo 24 caratteri perché l'assegnazione alla macchina virtuale/set di scalabilità di macchine virtuali funzioni correttamente. Ricontrollare in seguito per aggiornamenti. Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
La risposta contiene i dettagli relativi all'identità assegnata dall'utente creata ed è simile all'esempio seguente. Il valore `id` della risorsa assegnato all'identità assegnata dall'utente viene usato nel passaggio seguente.

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

3. Creare un VMSS mediante [az vmss create](/cli/azure/vmss/#az-vmss-create). L'esempio seguente crea un VMSS associato alla nuova identità assegnata dall'utente, come specificato dal parametro `--assign-identity`. Sostituire i valori dei parametri `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY ID>` con valori personalizzati. Per `<USER ASSIGNED IDENTITY ID>` usare la proprietà `id` della risorsa dell'identità assegnata dall'utente creata nel passaggio precedente: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Assegnare un'identità assegnata dall'utente a una VM di Azure esistente

1. Creare un'identità assegnata dall'utente mediante [az identity create](/cli/azure/identity#az-identity-create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

    > [!IMPORTANT]
    > La creazione di identità assegnate dall'utente con caratteri speciali (ad esempio il carattere di sottolineatura) nel nome non è supportata attualmente. Usare solo caratteri alfanumerici. Ricontrollare in seguito per aggiornamenti.  Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
La risposta contiene i dettagli relativi all'identità assegnata dall'utente creata ed è simile all'esempio seguente. Il valore `id` della risorsa assegnato all'identità assegnata dall'utente viene usato nel passaggio seguente.

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

2. Assegnare l'identità assegnata dall'utente al VMSS mediante [az vmss identity assign](/cli/azure/vmss/identity#az_vm_assign_identity). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY ID>` sarà la proprietà `id` della risorsa dell'identità assegnata dall'utente creata nel passaggio precedente:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Rimuovere un'identità assegnata dall'utente da un VMSS di Azure

> [!NOTE]
>  La rimozione di tutte le identità assegnate dall'utente da un set di scalabilità di macchine virtuali non è supportata, a meno che non si disponga di un'identità assegnata dal sistema. 

Se il VMSS dispone di più identità assegnate dall'utente, è possibile rimuoverle tutte tranne l'ultima mediante [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<MSI NAME>` è la proprietà name dell'identità assegnata dall'utente, che si può trovare nella sezione relativa all'identità della VM usando `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Se il VMSS ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente passando a usare solo le identità assegnate dal sistema. Usare il comando seguente: 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'Identità del servizio gestito](overview.md)
- Per la guida introduttiva completa sulla creazione del set di scalabilità di macchine virtuali di Azure vedere: 

  - [Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















