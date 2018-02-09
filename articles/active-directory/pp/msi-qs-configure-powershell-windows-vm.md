---
title: "Come configurare l'Identità del servizio gestito in una macchina virtuale di Azure tramite PowerShell"
description: "Istruzioni dettagliate per la configurazione di un'Identità del servizio gestito in una macchina virtuale di Azure, tramite PowerShell."
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: cb87dd88c0425383243edcf12b946cb1821aabe5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'Identità del servizio gestito per una VM di Azure tramite PowerShell.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Installare anche [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (versione 4.3.1 o successiva), se non è stata già installata.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure

Per creare una VM abilitata per MSI:

1. Fare riferimento a una delle seguenti guide introduttive sulle VM di Azure, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM"). 

   > [!IMPORTANT] 
   > Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Assicurarsi di aggiungere un parametro `-IdentityType "SystemAssigned"` per eseguire il provisioning della VM con un MSI, ad esempio:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Creare una macchina virtuale Windows mediante PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Creare una macchina virtuale Linux mediante PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Abilitare l'Identità del servizio gestito in una macchina virtuale di Azure esistente

Se è necessario abilitare l'Identità del servizio gestito su una macchina virtuale esistente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```powershell
   Login-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà della macchina virtuale usando il cmdlet `Get-AzureRmVM`. Per abilitare l'Identità del servizio gestito, usare il commutatore `-IdentityType` nel cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

Se si dispone di una macchina virtuale che non richiede più un'Identità del servizio gestito, è possibile usare il cmdlet `RemoveAzureRmVMExtension` per rimuovere l'Identità del servizio gestito dalla macchina virtuale:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```powershell
   Login-AzureRmAccount
   ```

2. Usare il parametro `-Name` con il cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) specificando lo stesso nome utilizzato quando è stata aggiunta l'estensione:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestito](msi-overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:
  
  - [Creare una macchina virtuale Windows con PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Creare una macchina virtuale Linux con PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md) 

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
















