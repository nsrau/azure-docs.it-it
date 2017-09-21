---
title: "Come configurare l'Identità del servizio gestito in una macchina virtuale di Azure tramite PowerShell"
description: "Istruzioni dettagliate per la configurazione di un'Identità del servizio gestito in una macchina virtuale di Azure, tramite PowerShell."
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
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'Identità del servizio gestito per una macchina virtuale Windows di Azure tramite PowerShell.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Installare anche [Azure PowerShell, versione 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1), se non è stato già installato.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure

Viene creata una nuova risorsa della macchina virtuale Windows abilitata per l'Identità del servizio gestito in un nuovo gruppo di risorse, usando i parametri di configurazione specificati. Si noti anche che molti di questi cmdlet possono essere eseguiti per più di 30 secondi prima di restituire un risultato, mentre la creazione di una macchina virtuale finale richiede alcuni minuti.

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale.

   ```powershell
   Login-AzureRmAccount
   ```

2. Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione della macchina virtuale e le risorse correlate, usando il cmdlet `New-AzureRmResourceGroup`. Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. Creare risorse di rete per la macchina virtuale.

   a. Creare una rete virtuale, una subnet e un indirizzo IP pubblico. Queste risorse vengono usate per offrire la connettività di rete alla macchina virtuale e connetterla a Internet:

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. Creare un gruppo di sicurezza di rete e una regola del gruppo di sicurezza di rete. Il gruppo di sicurezza di rete protegge la macchina virtuale usando le regole in entrata e in uscita. In questo caso viene creata una regola in entrata per la porta 3389 che consente connessioni desktop remoto in ingresso. È necessario anche creare una regola in ingresso per la porta 80, che consente il traffico Web in ingresso:

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. Creare una scheda di rete virtuale per la macchina virtuale. La scheda di rete connette la macchina virtuale a una subnet, a un gruppo di sicurezza di rete e a un indirizzo IP pubblico:

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. Creare la macchina virtuale

   a. Creare un oggetto della macchina virtuale configurabile. Queste impostazioni vengono usate quando si distribuisce la macchina virtuale, ad esempio l'immagine della macchina virtuale, la dimensione e la configurazione di autenticazione. Il parametro `-IdentityType "SystemAssigned"` usato nel cmdlet [New AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) fa sì che la macchina virtuale esegua il provisioning con un'Identità del servizio gestito. Il cmdlet `Get-Credential` richiede le credenziali configurate come nome utente e password per la macchina virtuale:

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. Effettuare il provisioning della nuova macchina virtuale:

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type "ManagedIdentityExtensionForWindows"` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Abilitare l'Identità del servizio gestito in una macchina virtuale di Azure esistente

Se è necessario abilitare l'Identità del servizio gestito su una macchina virtuale esistente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale:

   ```powershell
   Login-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà della macchina virtuale usando il cmdlet `Get-AzureRmVM`. Per abilitare l'Identità del servizio gestito, usare il commutatore `-IdentityType` nel cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type "ManagedIdentityExtensionForWindows"` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

Se si dispone di una macchina virtuale che non richiede più un'Identità del servizio gestito, è possibile usare il cmdlet `RemoveAzureRmVMExtension` per rimuovere l'Identità del servizio gestito dalla macchina virtuale:

1. Usare il commutatore `-Name "ManagedIdentityExtensionForWindows"` con il cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension):

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestito](msi-overview.md)
- Questo articolo è stato adattato dalla guida rapida [Creare una macchina virtuale Windows con PowerShell](../virtual-machines/windows/quick-create-powershell.md), modificata per includere le istruzioni specifiche dell'Identità di servizio gestito. 

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

















