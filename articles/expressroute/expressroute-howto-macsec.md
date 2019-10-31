---
title: 'Configurare MACsec-ExpressRoute: Azure | Microsoft Docs'
description: Questo articolo consente di configurare MACsec per proteggere le connessioni tra i router perimetrali e i router perimetrali Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 36cd1bfebe4e5379a1c8cfc551c8e003459ebf5c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162439"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Configurare MACsec in porte ExpressRoute dirette

Questo articolo illustra come configurare MACsec per proteggere le connessioni tra i router perimetrali e i router perimetrali Microsoft usando PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare quanto segue:

* Si conoscono i [flussi di lavoro di provisioning diretto ExpressRoute](expressroute-erdirect-about.md).
* È stata creata una [risorsa di porta diretta di ExpressRoute](expressroute-howto-erdirect.md).
* Se si vuole eseguire PowerShell localmente, verificare che nel computer sia installata la versione più recente di Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Accedi e seleziona la sottoscrizione corretta

Per avviare la configurazione, accedere al proprio account Azure e selezionare la sottoscrizione che si vuole usare.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. creare Azure Key Vault, segreti MACsec e identità utente

1. Creare un'istanza di Key Vault per archiviare i segreti MACsec in un nuovo gruppo di risorse.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Se è già presente un insieme di credenziali delle chiavi o un gruppo di risorse, è possibile riutilizzarli. È tuttavia fondamentale abilitare la funzionalità di [ **eliminazione** ](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-ovw-soft-delete) temporanea nell'insieme di credenziali delle chiavi esistente. Se soft-delete non è abilitato, è possibile usare i comandi seguenti per abilitarlo:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Creare un'identità utente.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Se New-AzUserAssignedIdentity non è riconosciuto come cmdlet di PowerShell valido, installare il modulo seguente (in modalità amministratore) ed eseguire di nuovo il comando precedente.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Creare una chiave di associazione di connettività (CAK) e un nome di chiave dell'associazione di connettività (CKN) e archiviarli nell'insieme di credenziali delle chiavi.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Assegnare l'autorizzazione GET all'identità utente.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Ora questa identità può ottenere i segreti, ad esempio CAK e CKN, dall'insieme di credenziali delle chiavi.
5. Impostare questa identità utente per l'uso da ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. configurare MACsec in porte ExpressRoute dirette

### <a name="to-enable-macsec"></a>Per abilitare MACsec

Ogni istanza diretta di ExpressRoute dispone di due porte fisiche. È possibile scegliere di abilitare MACsec su entrambe le porte contemporaneamente oppure abilitare MACsec su una porta alla volta. Per ridurre al minimo l'interruzione, se il ExpressRoute Direct è già presente nel servizio, è possibile eseguire questa operazione una porta al momento, spostando il traffico verso una porta attiva mentre si serve l'altra porta.

1. Impostare i segreti MACsec e crittografare e associare l'identità utente alla porta in modo che il codice di gestione di ExpressRoute possa accedere ai segreti MACsec, se necessario.

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
2. Opzionale Se le porte sono in stato di inattività amministrativo, è possibile eseguire i comandi seguenti per visualizzare le porte.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    A questo punto, MACsec è abilitato sul lato ExpressRoute Direct Ports on Microsoft. Se non è stata configurata nei dispositivi perimetrali, è possibile procedere con la configurazione con gli stessi segreti e crittografia MACsec.

### <a name="to-disable-macsec"></a>Per disabilitare MACsec

Se MACsec non è più necessario nell'istanza diretta di ExpressRoute, è possibile eseguire i comandi seguenti per disabilitarlo.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

A questo punto, MACsec è disabilitato sulle porte ExpressRoute dirette sul lato Microsoft.

### <a name="test-connectivity"></a>Testare la connettività
Dopo aver configurato MACsec (incluso l'aggiornamento della chiave MACsec) sulle porte dirette ExpressRoute, [verificare](expressroute-troubleshooting-expressroute-overview.md) che le sessioni BGP dei circuiti siano attive e in esecuzione. Se non è ancora presente alcun circuito sulle porte, crearne uno per primo e configurare il peering privato di Azure o il peering Microsoft del circuito. Se MACsec non è configurato correttamente, inclusa la mancata corrispondenza della chiave MACsec, tra i dispositivi di rete e i dispositivi di rete Microsoft, non verrà visualizzata la risoluzione ARP al livello 2 e allo stabilimento BGP al livello 3. Se tutti gli elementi sono configurati correttamente, si noterà che le route BGP sono state annunciate correttamente in entrambe le direzioni e il flusso di dati dell'applicazione di conseguenza rispetto a ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi
1. [Creare un circuito ExpressRoute in ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)
3. [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
