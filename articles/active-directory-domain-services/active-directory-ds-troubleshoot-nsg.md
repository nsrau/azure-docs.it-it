---
title: 'Azure Active Directory Domain Services: Risoluzione dei problemi relativi alla configurazione del gruppo di sicurezza di rete | Microsoft Docs'
description: Risoluzione dei problemi relativi alla configurazione del gruppo di sicurezza di rete di Azure AD Domain Services
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure Active Directory Domain Services: Risoluzione dei problemi relativi alla configurazione del gruppo di sicurezza di rete



## <a name="aadds104-network-error"></a>AADDS104: Errore di rete

**Messaggio di avviso:** *Microsoft non riesce a raggiungere i controller di dominio per questo dominio gestito. È possibile che questo problema si verifichi se un gruppo di sicurezza di rete configurato sulla rete virtuale impedisce l'accesso al dominio gestito oppure se è presente una route definita dall'utente che blocca il traffico in ingresso da Internet.*

La causa più comune degli errori di rete per Azure Active Directory Domain Services può essere attribuita a configurazioni di gruppo errate. Per assicurarsi che Microsoft sia in grado di gestire e manutenere un dominio gestito, è necessario usare un gruppo di sicurezza di rete (NSG) per consentire l'accesso a [porte specifiche](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services) all'interno della subnet. Se queste porte vengono bloccate, Microsoft non potrà accedere alle risorse necessarie, a scapito del servizio. Durante la creazione del gruppo di sicurezza di rete, tenere aperte queste porte per evitare possibili interruzioni del servizio.

## <a name="sample-nsg"></a>Esempio di NSG
La tabella seguente illustra un esempio di gruppo di sicurezza di rete finalizzato a proteggere il dominio gestito e consentire al contempo a Microsoft di monitorare, gestire e aggiornare le informazioni.

![esempio di NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services richiede un accesso in uscita senza restrizioni. Si consiglia di non creare alcuna regola aggiuntiva in uscita per i gruppi di sicurezza di rete.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Aggiunta di una regola a un gruppo di sicurezza di rete tramite il portale di Azure
Se non si desidera usare PowerShell, è possibile aggiungere manualmente delle regole singole per NSG tramite il portale di Azure. Seguire questi passaggi per creare delle regole nel gruppo di sicurezza di rete.

1. Passare alla pagina [Gruppi di sicurezza di rete](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) nel portale di Azure
2. Scegliere dalla tabella il gruppo di sicurezza di rete associato al dominio.
3. In Impostazioni nel riquadro di sinistra, fare clic su **Regole di sicurezza in ingresso** o **Regole di sicurezza in uscita**.
4. Creare la regola facendo clic su **Aggiungi** e inserire le informazioni. Fare clic su **OK**.
5. Verificare che la regola sia stata creata cercandola nella tabella delle regole.


## <a name="create-a-default-nsg-using-powershell"></a>Creare un gruppo di sicurezza di rete predefinito con PowerShell

I passaggi iniziali possono essere usati per creare un nuovo gruppo di sicurezza di rete con PowerShell, che consente di mantenere tutte le porte necessarie per l'esecuzione di Azure AD Domain Services mentre ogni accesso indesiderato viene bloccato.


Questa soluzione richiede l'installazione e l'esecuzione di [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0).

1. Connettersi alla directory di Azure AD.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Accedere alla sottoscrizione di Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. Creare un gruppo di sicurezza di rete con tre regole. Lo script seguente definisce tre regole per il gruppo di sicurezza di rete che consentono l'accesso alle porte necessarie per eseguire Azure AD Domain Services. Quindi, lo script crea un nuovo gruppo di sicurezza di rete contenente tali regole. È possibile aggiungere delle regole supplementari secondo necessità usando lo stesso formato.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. Infine, lo script associa il gruppo di sicurezza di rete con la rete virtuale e la subnet scelta.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>Script completo

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>Il gruppo di sicurezza di rete predefinito non blocca l'accesso alla porta usata per l'accesso LDAP sicuro. Per informazioni su come creare una regola per la porta, consultare [questo articolo](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="contact-us"></a>Contatti
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
