---
title: "Come configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure"
description: "Istruzioni dettagliate per la configurazione di un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure, tramite l'interfaccia della riga di comando di Azure."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 98683af2ca35b687f918647602a561d37dd42b11
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite l'interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L'identità del servizio gestito offre servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando in questa esercitazione sono disponibili due opzioni:

- Usare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) tramite il portale di Azure o il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. Accedere quindi ad Azure tramite [az login](/cli/azure/#az_login). Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale e l'identità del servizio gestito assegnata dall'utente:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure

Questa sezione illustra come creare la macchina virtuale e assegnare l'identità del servizio gestito assegnata dall'utente alla macchina virtuale. Se si dispone già di una macchina virtuale che si intende usare, ignorare questa sezione e procedere alla successiva.

1. Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio. Creare un [gruppo di risorse](~/articles/azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione dell'identità del servizio gestito usando [az group create](/cli/azure/group/#az_group_create). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<LOCATION>` con valori personalizzati. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Creare un'identità del servizio gestito assegnata dall'utente tramite [az identity create](/cli/azure/identity#az_identity_create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità del servizio gestito, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<MSI NAME>` con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La risposta contiene i dettagli relativi all'identità del servizio gestito assegnata dall'utente creata ed è simile all'esempio seguente. Il valore `id` della risorsa assegnato all'identità del servizio gestito è usato nel passaggio seguente.

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

3. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm/#az_vm_create). L'esempio seguente crea una macchina virtuale associata alla nuova identità del servizio gestito assegnata dall'utente, come specificato dal parametro `--assign-identity`. Assicurarsi di sostituire i valori di `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e della proprietà `<`MSI ID>` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id' creata nel passaggio precedente: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Abilitare l'Identità del servizio gestito in una macchina virtuale di Azure esistente

1. Creare un'identità del servizio gestito assegnata dall'utente tramite [az identity create](/cli/azure/identity#az_identity_create).  Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità del servizio gestito, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<MSI NAME>` con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La risposta contiene i dettagli relativi all'identità del servizio gestito assegnata dall'utente creata ed è simile all'esempio seguente. Il valore `id` della risorsa assegnato all'identità del servizio gestito è usato nel passaggio seguente.

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

2. Assegnare l'identità del servizio gestito assegnata dall'utente alla propria macchina virtuale tramite [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<MSI ID>` sarà la proprietà `id` della risorsa dell'identità del servizio gestito assegnata dall'utente creata nel passaggio precedente:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

1. Rimuovere l'identità del servizio gestito assegnata dall'utente dalla propria macchina virtuale tramite [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<MSI NAME>` sarà la proprietà `name` della risorsa dell'identità del servizio gestito assegnata dall'utente fornita durante il comando `az identity create` (vedere gli esempi nelle sezioni precedenti):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'Identità del servizio gestito](msi-overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere: 

  - [Creare una macchina virtuale Windows con l'interfaccia della riga di comando](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Creare una macchina virtuale Linux con l'interfaccia della riga di comando](~/articles/virtual-machines/linux/quick-create-cli.md) 

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
















