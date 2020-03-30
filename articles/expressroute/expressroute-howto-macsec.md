---
title: 'Azure ExpressRoute: Configure MACsec'
description: Questo articolo consente di configurare MACsec per proteggere le connessioni tra i router perimetrali e i router perimetrali di Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083555"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Configurare MACsec sulle porte dirette ExpressRouteConfigure MACsec on ExpressRoute Direct ports

Questo articolo consente di configurare MACsec per proteggere le connessioni tra i router perimetrali e i router perimetrali di Microsoft tramite PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare quanto segue:

* Si comprende i flussi di lavoro di [provisioning diretto ExpressRoute](expressroute-erdirect-about.md).
* È stata creata una [risorsa porta diretta ExpressRoute.](expressroute-howto-erdirect.md)
* Se si vuole eseguire PowerShell in locale, verificare che nel computer sia installata la versione più recente di Azure PowerShell.If you want to run PowerShell locally, verify that the latest version of Azure PowerShell is installed on your computer.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Accedi e seleziona l'abbonamento giusto

Per avviare la configurazione, accedere all'account Azure e selezionare la sottoscrizione che si vuole usare.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Creare l'insieme di credenziali delle chiavi di Azure, i segreti MACsec e l'identità dell'utente

1. Creare un'istanza dell'insieme di credenziali delle chiavi per archiviare i segreti MACsec in un nuovo gruppo di risorse.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Se si dispone già di un insieme di credenziali delle chiavi o di un gruppo di risorse, è possibile riutilizzarli. Tuttavia, è fondamentale abilitare la funzione [ **di eliminazione temporanea** ](../key-vault/key-vault-ovw-soft-delete.md) nell'insieme di credenziali delle chiavi esistente. Se l'eliminazione temporanea non è abilitata, è possibile utilizzare i seguenti comandi per abilitarla:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Creare un'identità utente.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Se New-AzUserAssignedIdentity non viene riconosciuto come cmdlet di PowerShell valido, installare il modulo seguente (in modalità amministratore) ed eseguire nuovamente il comando precedente.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Creare una chiave di associazione di connettività (CAK) e un nome di chiave di associazione di connettività (CKN) e archiviarli nell'insieme di credenziali delle chiavi.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Assegnare l'autorizzazione GET all'identità dell'utente.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Ora questa identità può ottenere i segreti, ad esempio CAK e CKN, dall'insieme di credenziali delle chiavi.
5. Impostare questa identità utente per essere utilizzata da ExpressRoute.Set this user identity to be used by ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Configurare MACsec sulle porte ExpressRoute Direct

### <a name="to-enable-macsec"></a>Per abilitare MACsec

Ogni istanza di ExpressRoute Direct ha due porte fisiche. È possibile scegliere di abilitare MACsec su entrambe le porte contemporaneamente oppure abilitare MACsec su una porta alla volta. Questa operazione può essere una porta alla volta (passando il traffico a una porta attiva durante la manutenzione dell'altra porta) può contribuire a ridurre al minimo l'interruzione se ExpressRoute Direct è già in servizio.

1. Impostare i segreti MACsec e la crittografia e associare l'identità utente alla porta in modo che il codice di gestione ExpressRoute possa accedere ai segreti MACsec, se necessario.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Facoltativo) Se le porte sono in stato amministrativo giù è possibile eseguire i seguenti comandi per visualizzare le porte.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    A questo punto, MACsec è abilitato sulle porte ExpressRoute Direct sul lato Microsoft. Se non è stato configurato nei dispositivi perimetrali, è possibile procedere alla configurazione con gli stessi segreti MACsec e crittografia.

### <a name="to-disable-macsec"></a>Per disabilitare MACsec

Se MACsec non è più desiderato nell'istanza di ExpressRoute Direct, è possibile eseguire i comandi seguenti per disabilitarla.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

A questo punto, MACsec è disabilitato sulle porte ExpressRoute Direct sul lato Microsoft.

### <a name="test-connectivity"></a>Testare la connettività
Dopo aver configurato MACsec (incluso l'aggiornamento della chiave MACsec) sulle porte ExpressRoute Direct, [verificare](expressroute-troubleshooting-expressroute-overview.md) se le sessioni BGP dei circuiti sono attive e in esecuzione. Se non si dispone ancora di alcun circuito sulle porte, crearne uno e configurare Peering privato di Azure o Peering Microsoft del circuito. Se MACsec non è configurato correttamente, inclusa la mancata corrispondenza del tasto MACsec tra i dispositivi di rete e i dispositivi di rete di Microsoft, non verrà visualizzata la risoluzione ARP al livello 2 e alla struttura BGP al livello 3. Se tutto è configurato correttamente, dovresti vedere le route BGP annunciate correttamente in entrambe le direzioni e il flusso di dati dell'applicazione di conseguenza su ExpressRoute.If everything is configured properly, you should see the BGP routes advertised correctly in both directions and your application data flowaccordingly over ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi
1. [Creare un circuito ExpressRoute in ExpressRoute DirectCreate an ExpressRoute circuit on ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)
3. [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
