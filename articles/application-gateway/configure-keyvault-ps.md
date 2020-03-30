---
title: Configurare la terminazione SSL con i certificati dell'insieme di credenziali delle chiavi - PowerShellConfigure SSL termination with Key Vault certificates - PowerShell
titleSuffix: Azure Application Gateway
description: Informazioni su come integrare il gateway applicazione di Azure con l'insieme delle chiavi per i certificati server collegati ai listener abilitati per HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/27/2020
ms.author: victorh
ms.openlocfilehash: 15e10d34120ab5475f241235bbebeb0c7689ca14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371218"
---
# <a name="configure-ssl-termination-with-key-vault-certificates-by-using-azure-powershell"></a>Configurare la terminazione SSL con i certificati dell'insieme di chiavi tramite Azure PowerShellConfigure SSL termination with Key Vault certificates by using Azure PowerShell

[L'insieme](../key-vault/key-vault-overview.md) di credenziali delle chiavi di Azure è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere segreti, chiavi e certificati SSL. Il gateway applicazione di Azure supporta l'integrazione con Key Vault per i certificati server collegati a listener abilitati per HTTPS. Questo supporto è limitato allo SKU del gateway applicazione v2.

Per ulteriori informazioni, vedere [Terminazione SSL con i certificati dell'insieme](key-vault-certs.md)di credenziali delle chiavi .

Questo articolo illustra come usare uno script di Azure PowerShell per integrare l'insieme di credenziali delle chiavi con il gateway applicazione per i certificati di terminazione SSL.

Questo articolo richiede la versione 1.0.0 o successiva del modulo di Azure PowerShell.This article requires Azure PowerShell module version 1.0.0 or later. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Per eseguire i comandi in questo articolo, è inoltre `Connect-AzAccount`necessario creare una connessione con Azure eseguendo .

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario che sia installato il modulo ManagedServiceIdentity:

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>Script di esempio

### <a name="set-up-variables"></a>Impostare le variabili

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "TestKeyVaultAppGw"
$appgwName = "AppGwKVIntegration"
```

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>Creare un gruppo di risorse e un'identità gestita dall'utenteCreate a resource group and a user-managed identity

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-a-key-vault-policy-and-certificate-to-be-used-by-the-application-gateway"></a>Creare un insieme di credenziali delle chiavi, criteri e certificati da utilizzare dal gateway applicazioneCreate a key vault, policy, and certificate to be used by the application gateway

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
Set-AzKeyVaultAccessPolicy -VaultName $kv -EmailAddress <your email address> -PermissionsToCertificates create,get,list
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```
> [!NOTE]
> Il flag -EnableSoftDelete deve essere utilizzato per il corretto funzionamento della terminazione SSL. Se si sta configurando l'eliminazione temporanea dell'insieme di credenziali delle chiavi [tramite il portale](../key-vault/key-vault-ovw-soft-delete.md#soft-delete-behavior), il periodo di conservazione deve essere mantenuto a 90 giorni, il valore predefinito. Il gateway applicazione non supporta ancora un periodo di conservazione diverso. 

### <a name="create-a-virtual-network"></a>Crea rete virtuale

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-virtual-ip-vip-address"></a>Creare un indirizzo IP virtuale (VIP) pubblico staticoCreate a static public virtual IP (VIP) address

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-front-end-ports"></a>Creare porte di pool e front-end

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-the-ssl-certificate-to-your-key-vault"></a>Puntare il certificato SSL all'insieme di credenziali delle chiavi

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>Creare listener, regole e scalabilità automaticaCreate listeners, rules, and autoscale

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSetting -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-the-user-managed-identity-to-the-application-gateway"></a>Assegnare l'identità gestita dall'utente al gateway applicazioneAssign the user-managed identity to the application gateway

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>Creare il gateway applicazione

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni sulla terminazione SSL](ssl-overview.md)
