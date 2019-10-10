---
title: Script di esempio di crittografia dischi di Azure
description: Questo articolo è l'appendice per la crittografia del disco Microsoft Azure per le macchine virtuali Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6874372d64fdbb667a23f2ded37a68cd68e32993
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245084"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Script di esempio di crittografia dischi di Azure 

Questo articolo fornisce script di esempio per la preparazione dei dischi rigidi virtuali pre-crittografati e di altre attività.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="list-vms-and-secrets"></a>Elencare le macchine virtuali e i segreti

Elencare tutte le macchine virtuali crittografate nella sottoscrizione:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Elencare tutti i segreti di crittografia del disco usati per crittografare le VM in un insieme di credenziali delle chiavi:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Script dei prerequisiti di crittografia dischi di Azure
Se si ha già familiarità con i prerequisiti per Crittografia dischi di Azure, è possibile usare lo [script di PowerShell per i prerequisiti di Crittografia dischi di Azure](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Per un esempio d'uso di questo script di PowerShell, vedere [Guida introduttiva alla crittografia di una macchina virtuale](disk-encryption-powershell-quickstart.md). È possibile rimuovere i commenti da una sezione dello script, a partire dalla riga 211, per crittografare tutti i dischi per le macchine virtuali esistenti in un gruppo di risorse esistente. 

La tabella seguente illustra i parametri che possono essere usati nello script di PowerShell: 

|Parametro|Descrizione|Obbligatorio?|
|------|------|------|
|$resourceGroupName| Nome del gruppo di risorse a cui appartiene l'insieme di credenziali delle chiavi.  Verrà creato un nuovo gruppo di risorse con questo nome, se non esiste già.| True|
|$keyVaultName|Nome dell'insieme di credenziali delle chiavi in cui inserire le chiavi di crittografia. Verrà creato un nuovo insieme con questo nome, se non esiste già.| True|
|$location|Percorso dell'insieme di credenziali delle chiavi. Assicurarsi che l'insieme di credenziali delle chiavi e le macchine virtuali da crittografare si trovino nello stesso percorso. Ottenere un elenco di percorsi con `Get-AzLocation`.|True|
|$subscriptionId|Identificatore della sottoscrizione di Azure da usare.  È possibile ottenere l'ID della sottoscrizione con `Get-AzSubscription`.|True|
|$aadAppName|Nome dell'applicazione Azure AD che verrà usata per scrivere segreti nell'insieme di credenziali delle chiavi. Viene creata una nuova applicazione con questo nome, se non esiste già. Se l'app esiste già, passare il parametro aadClientSecret allo script.|False|
|$aadClientSecret|Segreto client dell'applicazione Azure AD che è stato creato in precedenza.|False|
|$keyEncryptionKeyName|Nome della chiave di crittografia della chiave facoltativa nell'insieme di credenziali delle chiavi. Verrà creata una nuova chiave con questo nome, se non esiste già.|False|

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Crittografare o decrittografare le macchine virtuali senza un'app Azure AD

- [Abilitare la crittografia del disco in una macchina virtuale Windows esistente o in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Disabilitare la crittografia in una VM Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Crittografare o decrittografare le macchine virtuali senza un'app Azure AD (versione precedente) 
 
- [Abilitare la crittografia del disco in una macchina virtuale Windows esistente o in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Disabilitare la crittografia in una VM Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Creare un nuovo disco gestito crittografato da un BLOB di archiviazione/disco rigido virtuale pre-crittografato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Crea un nuovo disco gestito crittografato a partire da un disco rigido virtuale pre-crittografato con le corrispondenti impostazioni di crittografia

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparare un disco rigido virtuale Windows pre-crittografato
Le sezioni seguenti sono necessarie per preparare un disco rigido virtuale Windows pre-crittografato per la distribuzione come disco rigido virtuale crittografato in Azure IaaS. Usare le informazioni per preparare e avviare una nuova macchina virtuale Windows VM (disco rigido virtuale) in Azure Site Recovery o Azure. Per altre informazioni su come preparare e caricare un disco rigido virtuale, vedere [Caricare un disco rigido virtuale generalizzato e usarlo per creare nuove macchine virtuali in Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aggiornare i criteri di gruppo per consentire la protezione non TPM del sistema operativo
Configurare l'impostazione **Crittografia unità BitLocker** di Criteri di gruppo per BitLocker, disponibile in **Criteri del computer locale** > **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows**. Cambiare questa impostazione in **Unità del sistema operativo** > **Richiedi autenticazione aggiuntiva all'avvio** > **Consenti BitLocker senza un TPM compatibile**, come illustrato nella figura seguente:

![Microsoft Antimalware in Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installare i componenti della funzionalità BitLocker
Per Windows Server 2012 e versioni successive, usare il comando seguente:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Per Windows Server 2008 R2, usare il comando seguente:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparare il volume del sistema operativo per BitLocker tramite `bdehdcfg`
Per comprimere la partizione del sistema operativo e preparare il computer per BitLocker, eseguire il comando [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment), se necessario:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteggere il volume del sistema operativo usando BitLocker
Usare il comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) per abilitare la crittografia sul volume di avvio usando una protezione con chiave esterna. Salvare anche la chiave esterna (file con estensione bek) nell'unità o nel volume esterno. La crittografia viene abilitata nel volume di sistema/di avvio al riavvio successivo.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Preparare la macchina virtuale con un disco rigido virtuale dati/di risorse separato per recuperare la chiave esterna usando BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Caricare il VHD crittografato in un account di archiviazione di Azure
Dopo l'abilitazione della crittografia DM-Crypt, il disco rigido virtuale crittografato locale dovrà essere caricato nell'account di archiviazione.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Caricare il segreto per la VM pre-crittografata nell'insieme di credenziali delle chiavi
Il segreto di crittografia del disco ottenuto in precedenza deve essere caricato come segreto nell'insieme di credenziali delle chiavi.  A questo scopo è necessario concedere l'autorizzazione set Secret e l'autorizzazione wrapkey all'account che caricherà i segreti.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave
Per configurare il segreto nell'insieme di credenziali delle chiavi, usare [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). La passphrase viene codificata come stringa Base64 e quindi caricata nell'insieme di credenziali delle chiavi. Assicurarsi anche che i tag seguenti siano impostati quando si crea il segreto nell'insieme di credenziali delle chiavi.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Usare `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo senza usare una chiave di crittografia della chiave](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave
Prima di caricare il segreto nell'insieme di credenziali delle chiavi, è possibile crittografarlo usando una chiave di crittografia della chiave. Usare l'[API](https://msdn.microsoft.com/library/azure/dn878066.aspx) WRAP per crittografare prima di tutto il segreto con la chiave di crittografia della chiave. L'output di questa operazione WRAP si basa su una stringa con codifica Base 64 dell'URL che può essere quindi caricata come segreto con il cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret).

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Usare `$KeyEncryptionKey` e `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo usando una chiave di crittografia della chiave](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Specificare un URL del segreto quando si collega un disco del sistema operativo

###  <a name="without-using-a-kek"></a>Senza l'uso di una chiave di crittografia della chiave (KEK)
Quando si collega il disco del sistema operativo è necessario passare `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Uso di una chiave di crittografia della chiave (KEK)
Quando si collega il disco del sistema operativo, vedere `$KeyEncryptionKey` e `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
