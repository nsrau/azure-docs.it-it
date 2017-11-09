---
title: "Come configurare l'Identità del servizio gestito in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure"
description: "Istruzioni dettagliate per la configurazione dell'Identità del servizio gestito in una macchina virtuale di Azure, tramite l'interfaccia della riga di comando di Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: fe276fe802eceb1f062ed8bda685dd44a1e3d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite l'interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'Identità del servizio gestito per una VM di Azure tramite l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure

Per creare una VM abilitata per MSI:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/#login). Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale:

   ```azurecli-interactive
   az login
   ```

2. Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione della macchina virtuale e le risorse correlate, usando [az group create](/cli/azure/group/#create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm/#create). L'esempio seguente crea una macchina virtuale denominata *myVM* con un'Identità del servizio gestito, come richiesto dal parametro `--assign-identity`. I parametri `--admin-username` e `--admin-password` specificano il nome utente e la password dell'account amministrativo per l'accesso alla macchina virtuale. Aggiornare questi valori in base alle esigenze specifiche dell'ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Abilitare l'Identità del servizio gestito in una macchina virtuale di Azure esistente

Se è necessario abilitare l'Identità del servizio gestito su una macchina virtuale esistente:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/#login). Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```azurecli-interactive
   az login
   ```

2. Usare [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity) con il parametro `--assign-identity` per aggiungere un'identità del servizio gestito alla macchina virtuale esistente:

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

Se si dispone di una macchina virtuale per cui non è più necessaria un'Identità del servizio gestito:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/#login). Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```azurecli-interactive
   az login
   ```

2. Utilizzare il parametro `-n ManagedIdentityExtensionForWindows` o `-n ManagedIdentityExtensionForLinux` (a seconda del tipo di VM) con [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) per rimuovere MSI:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestito](msi-overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere: 

  - [Creare una macchina virtuale Windows con l'interfaccia della riga di comando](../virtual-machines/windows/quick-create-cli.md)  
  - [Creare una macchina virtuale Linux con l'interfaccia della riga di comando](../virtual-machines/linux/quick-create-cli.md) 

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
















