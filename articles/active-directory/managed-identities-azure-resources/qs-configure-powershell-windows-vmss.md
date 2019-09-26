---
title: Come configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite PowerShell
description: Istruzioni dettagliate per configurare identità gestite assegnate dal sistema e dall'utente in un set di scalabilità di macchine virtuali mediante PowerShell.
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
ms.openlocfilehash: 5fa3100cae9b1a2c9ca320776cc357f3720b3473
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309988"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Configurare identità gestite per le risorse di Azure in set di scalabilità di macchine virtuali tramite PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le operazioni relative alle identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali mediante PowerShell:
- Abilitare e disabilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali
- Aggiungere e rimuovere un'identità gestita assegnata dall'utente in un set di scalabilità di macchine virtuali

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)** .
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere le seguenti assegnazioni di controllo degli accessi in base al ruolo:

    > [!NOTE]
    > Non sono necessarie altre assegnazioni di ruoli della directory di Azure AD.

    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per creare un set di scalabilità di macchine virtuali e abilitare e rimuovere da un set di scalabilità di macchine virtuali l'identità gestita assegnata dal sistema e/o dall'utente.
    - [Managed Identity Contributor (Collaboratore per identità gestita)](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare un'identità gestita assegnata dall'utente.
    - [Operatore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-operator) per assegnare e rimuovere un'identità gestita assegnata dall'utente da e verso un set di scalabilità di macchine virtuali.
- Installare [la versione più recente di Azure PowerShell](/powershell/azure/install-az-ps), se non è già installata. 

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione descrive come abilitare e rimuovere un'identità gestita assegnata dal sistema mediante Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali di Azure

Per creare un set di scalabilità di macchine virtuali con l'identità gestita assegnata dal sistema abilitata:

1. Vedere l' *esempio 1* nell'articolo di riferimento per il cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.  Aggiungere il parametro `-IdentityType SystemAssigned` al cmdlet `New-AzVmssConfig`:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente

Se è necessario abilitare un'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente:

1. Accedere al portale di Azure con `Connect-AzAccount`. Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali, ad esempio "Collaboratore macchina virtuale":

   ```powershell
   Connect-AzAccount
   ```

2. Recuperare innanzitutto le proprietà del set di scalabilità di macchine virtuali tramite il cmdlet [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss). Per abilitare un'identità gestita assegnata dal sistema, usare l'opzione `-IdentityType` nel cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Disabilitare l'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali di Azure

Se un set di scalabilità di macchine virtuali non necessità più dell'identità gestita assegnata dal sistema ma necessita ancora delle identità gestite assegnate dall'utente, usare il cmdlet seguente:

1. Accedere al portale di Azure con `Connect-AzAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali, ad esempio "Collaboratore macchina virtuale":

2. Eseguire il cmdlet seguente:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Se si ha un set di scalabilità di macchine virtuali che non ha più bisogno dell'identità gestita assegnata dal sistema e non dispone di identità gestite assegnate dall'utente, usare i comandi seguenti:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali tramite Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Assegnare un'identità gestita assegnata dall'utente durante la creazione di un set di scalabilità di macchine virtuali di Azure

Attualmente non è possibile creare un nuovo set di scalabilità di macchine virtuali con un'identità gestita assegnata dall'utente tramite PowerShell. Per informazioni sull'aggiunta di un'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali esistente, vedere la sezione successiva. Ricontrollare in seguito per aggiornamenti.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Assegnare un'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali di Azure esistente

Per assegnare un'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali di Azure esistente:

1. Accedere al portale di Azure con `Connect-AzAccount`. Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali, ad esempio "Collaboratore macchina virtuale":

   ```powershell
   Connect-AzAccount
   ```

2. Recuperare innanzitutto le proprietà del set di scalabilità di macchine virtuali tramite il cmdlet `Get-AzVM`. Quindi, per assegnare un'identità gestita assegnata dall'utente al set di scalabilità di macchine virtuali di Azure, usare le opzioni `-IdentityType` e `-IdentityID` nel cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` con valori personalizzati.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali di Azure

Se il set di scalabilità di macchine virtuali ha più identità gestite assegnate dall'utente, è possibile rimuoverle tutte tranne l'ultima tramite i comandi seguenti. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY NAME>` è la proprietà del nome dell'identità gestita assegnata dall'utente, che deve rimanere nel set di scalabilità di macchine virtuali. È possibile trovare queste informazioni nella sezione relativa all'identità del set di scalabilità di macchine virtuali usando `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Se il set di scalabilità di macchine virtuali non ha un'identità gestita assegnata dal sistema e si vuole rimuovere tutte le identità gestite assegnate dall'utente al suo interno, usare il comando seguente:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se il set di scalabilità di macchine virtuali ha identità gestite sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità gestite assegnate dall'utente iniziando a usare solo identità gestite assegnate dal sistema.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:
  
  - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creare una macchina virtuale Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















