---
title: Come configurare un file MSI assegnati dall'utente per una macchina virtuale di Azure mediante Azure CLI
description: "Passaggio da istruzioni dettagliate per la configurazione di un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale di Azure, tramite l'interfaccia CLI di Azure."
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
ms.openlocfilehash: 2aed60c2b35d750c892bc61c0e2693d6407c641f
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Configurare un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale, tramite l'interfaccia CLI di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identità del servizio gestito fornisce servizi di Azure con un'identità gestita in Azure Active Directory. È possibile utilizzare questa identità di autenticazione ai servizi che supportano l'autenticazione di Azure AD, senza la necessità di credenziali nel codice. 

In questo articolo si apprenderà come abilitare e rimuovere un file MSI assegnati dall'utente per una macchina virtuale di Azure, tramite l'interfaccia CLI di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Per eseguire gli esempi di script CLI in questa esercitazione, sono disponibili due opzioni:

- Utilizzare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dal portale di Azure o tramite il pulsante "Provalo", si trova nell'angolo superiore destro di ogni blocco di codice.
- [Installare la versione più recente di CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o versione successiva) se si preferisce utilizzare una console locale di CLI. Accedere quindi a Azure tramite [accesso az](/cli/azure/#login). Utilizzare un account che viene associato alla sottoscrizione di Azure in cui si desidera distribuire il file MSI assegnati dall'utente e macchina virtuale:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure

In questa sezione illustra la creazione della macchina virtuale e l'assegnazione del file MSI utente assegnato alla macchina virtuale. Se si dispone già di una macchina virtuale che si desidera utilizzare, ignorare questa sezione e procedere al successivo.

1. È possibile ignorare questo passaggio se si dispone già di un gruppo di risorse da utilizzare. Creare un [gruppo di risorse](~/articles/azure-resource-manager/resource-group-overview.md#terminology) indipendenza e la distribuzione di MSI, utilizzando [gruppo az creare](/cli/azure/group/#create). Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<LOCATION>` i valori dei parametri con valori personalizzati. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Creare un file MSI assegnati dall'utente tramite [identità az creare](/cli/azure/identity#az_identity_create).  Il `-g` parametro specifica il gruppo di risorse in cui viene creato il file MSI, e `-n` parametro specifica il nome. Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<MSI NAME>` i valori dei parametri con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La risposta contiene i dettagli per il file MSI assegnati dall'utente creato, simile al seguente. La risorsa `id` valore assegnato per il file MSI è utilizzato nel passaggio seguente.

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

3. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm/#create). L'esempio seguente crea una macchina virtuale associata con il nuovo utente assegnato file MSI, come specificato da di `--assign-identity` parametro. Assicurarsi di sostituire il `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, e `<`ID MSI >` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id' proprietà creata nel passaggio precedente: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Abilitare l'Identità del servizio gestito in una macchina virtuale di Azure esistente

1. Creare un file MSI assegnati dall'utente tramite [identità az creare](/cli/azure/identity#az_identity_create).  Il `-g` parametro specifica il gruppo di risorse in cui viene creato il file MSI, e `-n` parametro specifica il nome. Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<MSI NAME>` i valori dei parametri con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La risposta contiene i dettagli per il file MSI assegnati dall'utente creato, simile al seguente. La risorsa `id` valore assegnato per il file MSI è utilizzato nel passaggio seguente.

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

2. Assegnare il file MSI assegnati dall'utente per la macchina virtuale con [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<VM NAME>` i valori dei parametri con valori personalizzati. Il `<MSI ID>` sarà risorse l'utente assegnato MSI `id` proprietà, creato nel passaggio precedente:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

1. Rimuovere il file MSI assegnati dall'utente dal VM utilizzando [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity). Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<VM NAME>` i valori dei parametri con valori personalizzati. Il `<MSI NAME>` sarà MSI assegnati dall'utente `name` proprietà, come specificato durante la `az identity create` comando (vedere esempi nelle sezioni precedenti):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'Identità del servizio gestito](msi-overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere: 

  - [Creare una macchina virtuale Windows con l'interfaccia della riga di comando](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Creare una macchina virtuale Linux con l'interfaccia della riga di comando](~/articles/virtual-machines/linux/quick-create-cli.md) 

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
















