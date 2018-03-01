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
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: cc9a61314de7e10afe370c3b1307d03544a379d5
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Risolvere i problemi di configurazione di rete non valida per il dominio gestito
Questo articolo consente di individuare e risolvere gli errori di configurazione relativi alla rete che causano il messaggio di avviso seguente:

## <a name="alert-aadds104-network-error"></a>Avviso AADDS104: errore di rete
**Messaggio di avviso:** *Microsoft non riesce a raggiungere i controller di dominio per questo dominio gestito. È possibile che questo problema si verifichi se un gruppo di sicurezza di rete configurato sulla rete virtuale impedisce l'accesso al dominio gestito oppure se è presente una route definita dall'utente che blocca il traffico in ingresso da Internet.*

Le configurazioni di NSG non validi sono la causa più comune degli errori di rete per Azure AD Domain Services. Il gruppo di sicurezza di rete (NSG) configurato per la rete virtuale deve consentire l'accesso a [porte specifiche](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Se queste porte sono bloccate, Microsoft non può monitorare o aggiornare il dominio gestito. Inoltre, si verificano ripercussioni sulla sincronizzazione tra la directory Azure AD e il dominio gestito. Durante la creazione dell'NSG, tenere aperte queste porte per evitare possibili interruzioni del servizio.


## <a name="sample-nsg"></a>Esempio di NSG
La tabella seguente illustra un esempio di gruppo di sicurezza di rete finalizzato a proteggere il dominio gestito e consentire al contempo a Microsoft di monitorare, gestire e aggiornare le informazioni.

![esempio di NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services richiede un accesso in uscita senza restrizioni dalla rete virtuale. Si consiglia di non creare regole NSG aggiuntive che limitano l'accesso in uscita per la rete virtuale.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Aggiungere una regola a un gruppo di sicurezza di rete tramite il portale di Azure
Se non si desidera usare PowerShell, è possibile aggiungere manualmente delle regole singole per NSG tramite il portale di Azure. Per creare regole nel gruppo di sicurezza di rete, svolgere i passaggi seguenti:

1. Passare alla pagina [Gruppi di sicurezza di rete](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) nel portale di Azure
2. Dalla tabella scegliere l'NSG associato alla subnet in cui è abilitato il dominio gestito.
3. In **Impostazioni** nel pannello a sinistra fare clic su **Regole di sicurezza in ingresso** o **Regole di sicurezza in uscita**.
4. Creare la regola facendo clic su **Aggiungi** e inserire le informazioni. Fare clic su **OK**.
5. Verificare che la regola sia stata creata cercandola nella tabella delle regole.


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>Creare un NSG per Azure AD Domain Services con PowerShell
Questo NSG è configurato per consentire il traffico in ingresso alle porte richieste da Azure AD Domain Services e allo stesso tempo negare qualsiasi altro accesso in ingresso indesiderato.

**Prerequisito: installare e configurare Azure PowerShell** Seguire le istruzioni per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Si consiglia di usare la versione più recente del modulo Azure PowerShell. Se è già stata installata una versione precedente del modulo Azure PowerShell, eseguire l'aggiornamento alla versione più recente.
>

Usare la procedura seguente per creare un nuovo NSG con PowerShell. 
1. Accedere alla sottoscrizione di Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

2. Creare un gruppo di sicurezza di rete con tre regole. Lo script seguente definisce tre regole per il gruppo di sicurezza di rete che consentono l'accesso alle porte necessarie per eseguire Azure AD Domain Services. Quindi, lo script crea un nuovo gruppo di sicurezza di rete contenente tali regole. Usare lo stesso formato per aggiungere altre regole che consentano il traffico in ingresso, se richiesto da carichi di lavoro distribuiti nella rete virtuale.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
  -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
  -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
  13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
  52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
  52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
  -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
  -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Create the NSG with the 3 rules above
  $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
  -Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule
  ```

3. Infine, associare l'NSG alla rete virtuale e alla subnet scelte.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Script completo per creare e applicare un NSG per Azure AD Domain Services
>[!TIP]
> Si consiglia di usare la versione più recente del modulo Azure PowerShell. Se è già stata installata una versione precedente del modulo Azure PowerShell, eseguire l'aggiornamento alla versione più recente.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Login-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
-Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
-Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
-SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
-Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Create the NSG with the 3 rules above
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
-Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```

> [!NOTE]
> Il gruppo di sicurezza di rete predefinito non blocca l'accesso alla porta usata per l'accesso LDAP sicuro. Per bloccare l'accesso LDAP sicuro su Internet, vedere [questo articolo](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="need-help"></a>Richiesta di assistenza
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
