---
title: "Configurare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite l'interfaccia della riga di comando di Azure"
description: "Istruzioni dettagliate per la configurazione dell'Identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite l'interfaccia della riga di comando di Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: d7a7b0c8b3f9bf0279282dbf1fed4fc8163d9170
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Configurare un'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite l'interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'identità del servizio gestito per un set di scalabilità di macchine virtuali di Azure tramite l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:

- Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Abilitare l'identità del servizio gestito durante la creazione di un set di scalabilità di macchine virtuali di Azure

Per creare un set di scalabilità di macchine virtuali basato sull'identità del servizio gestito:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure in cui si vuole distribuire il set di scalabilità di macchine virtuali:

   ```azurecli-interactive
   az login
   ```

2. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione del set di scalabilità di macchine virtuali e delle risorse correlate, usando [az group create](/cli/azure/group/#az_group_create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Creare un set di scalabilità di macchine virtuali con [az vmss create](/cli/azure/vmss/#az_vmss_create). L'esempio seguente crea un set di scalabilità di macchine virtuali denominato *myVMSS* con un'identità del servizio gestito, come richiesto dal parametro `--assign-identity`. I parametri `--admin-username` e `--admin-password` specificano il nome utente e la password dell'account amministrativo per l'accesso alla macchina virtuale. Aggiornare questi valori in base alle esigenze specifiche dell'ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Abilitare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure esistente

Se è necessario abilitare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure esistente:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

   ```azurecli-interactive
   az login
   ```

2. Usare [az vmss assign-identity](/cli/azure/vm/#az_vmss_assign_identity) con il parametro `--assign-identity` per aggiungere un'identità del servizio gestito alla macchina virtuale esistente:

   ```azurecli-interactive
   az vmss assign-identity -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Rimuovere l'identità del servizio gestito da un set di scalabilità di macchine virtuali di Azure

Se si dispone di un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità del servizio gestito:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

   ```azurecli-interactive
   az login
   ```

2. Usare l'opzione `--identities` con il comando [az vmss remove-identity](/cli/azure/vmss/#az_vmss_remove_identity) per rimuovere l'identità del servizio gestito:

   ```azurecli-interactive
   az vmss remove-identity -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'Identità del servizio gestito](overview.md)
- Per la guida introduttiva completa sulla creazione del set di scalabilità di macchine virtuali di Azure vedere: 

  - [Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
















