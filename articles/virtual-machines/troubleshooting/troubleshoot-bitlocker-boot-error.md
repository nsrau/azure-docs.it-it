---
title: Risoluzione dei problemi di avvio di BitLocker in una VM di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi di avvio di BitLocker in una VM di Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 116748d7887ebf2ad821e3159c7c1bdcc2428121
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "64684766"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Problemi di avvio di BitLocker in una macchina virtuale di Azure

 Questo articolo descrive i potenziali problemi di BitLocker che possono verificarsi all'avvio di una macchina virtuale Windows in Microsoft Azure.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintomo

 La macchina virtuale Windows non si avvia. Quando si controllano gli screenshot nella finestra [Diagnostica di avvio](../windows/boot-diagnostics.md), viene visualizzato uno dei messaggi di errore seguenti:

- Collegare l'unità USB contenente la chiave BitLocker

- Il tuo account è bloccato. Immettere la chiave di ripristino per continuare (layout tastiera: US) Troppi tentativi di accesso con informazioni errate. Il PC è stato bloccato per proteggere la privacy dell'utente. Per recuperare la chiave di ripristino, visitare https://windows.microsoft.com/recoverykeyfaq da un altro PC o dispositivo mobile. Nel caso sia necessario, l'ID della chiave è XXXXXXX. In alternativa, è possibile ripristinare le impostazioni di fabbrica del PC.

- Digitare la password per sbloccare l'unità [ ] Premere INS per visualizzare la password durante la digitazione.
- Immetti la chiave di ripristino Carica la chiave di ripristino da un dispositivo USB.

## <a name="cause"></a>Causa

Questo problema può verificarsi se la macchina virtuale non riesce a individuare il file della chiave di ripristino di BitLocker (BEK) per decrittografare il disco crittografato.

## <a name="solution"></a>Soluzione

Per risolvere questo problema, arrestare e deallocare la macchina virtuale e quindi riavviarla. Questa operazione forza la macchina virtuale a recuperare il file con estensione BEK da Azure Key Vault e quindi lo inserisce nel disco crittografato. 

Se questo metodo non risolve il problema, seguire questa procedura per ripristinare manualmente il file con estensione BEK:

1. Creare uno snapshot del disco di sistema della macchina virtuale in questione come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).
2. [Collegare il disco di sistema a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md) crittografata da BitLocker. Questa operazione è necessaria per eseguire il comando [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) che è disponibile solo nella macchina virtuale crittografata da BitLocker.

    Quando si collega un disco gestito, potrebbe essere visualizzato un messaggio di errore che indica la presenza di impostazioni di crittografia e l'impossibilità di usare il disco come disco dati. In questo caso, eseguire lo script seguente per provare nuovamente a collegare il disco:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Non è possibile collegare un disco gestito a una macchina virtuale che è stata ripristinata dall'immagine di un BLOB.

3. Dopo aver collegato il disco, creare una connessione desktop remota alla macchina virtuale di ripristino in modo da poter eseguire alcuni script di Azure PowerShell. Verificare di aver installato la [versione più recente di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nella macchina virtuale di ripristino.

4. Aprire una sessione di Azure PowerShell con privilegi elevati (Esegui come amministratore). Eseguire i comandi seguenti per accedere alla sottoscrizione di Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Eseguire lo script seguente per controllare il nome del file con estensione BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Di seguito è riportato un esempio di output. Individuare il nome del file con estensione BEK per il disco collegato. In questo caso si presuppone che la lettera di unità del disco collegato sia F e che il file con estensione BEK sia EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Se vengono visualizzati due volumi duplicati, il volume con il timestamp più recente è il file con estensione BEK corrente usato dalla macchina virtuale di ripristino.

    Se il valore di **Content Type** è **Wrapped BEK**, passare agli [scenari relativi alla chiave di crittografia della chiave (KEK)](#key-encryption-key-scenario).

    Dopo aver ottenuto il nome del file con estensione BEK dell'unità, per sbloccare l'unità è necessario creare il file secret-file-name.BEK. 

6.  Scaricare il file con estensione BEK sul disco di ripristino. L'esempio seguente salva il file con estensione BEK nella cartella C:\BEK. Verificare che il percorso `C:\BEK\` esista prima di eseguire gli script.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Per sbloccare il disco collegato usando il file con estensione BEK, eseguire il comando seguente:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    In questo esempio, il disco del sistema operativo collegato è l'unità F. Verificare di usare la lettera di unità corretta. 

    - Se il disco è stato sbloccato correttamente usando la chiave BEK, il problema di BitLocker può considerarsi risolto. 

    - Se l'uso della chiave BEK non ha sbloccato il disco, è possibile sospendere la protezione per disattivare temporaneamente BitLocker con il comando seguente:
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Se si vuole ricreare la macchina virtuale usando il disco di sistema, è necessario decrittografare completamente l'unità. A tale scopo, usare il comando seguente:

        ```powershell
        manage-bde -off F:
        ```
8.  Scollegare il disco dalla macchina virtuale di ripristino e quindi ricollegarlo alla macchina virtuale in questione come disco di sistema. Per altre informazioni, vedere [Risolvere i problemi relativi a una macchina virtuale Windows collegando il disco del sistema operativo a una macchina virtuale di ripristino](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Scenario con chiave di crittografia della chiave

In uno scenario con chiave di crittografia della chiave, seguire questa procedura:

1. Verificare che l'account utente connesso richieda l'autorizzazione senza wrapping nei criteri di accesso dell'insieme di credenziali delle chiavi in **UTENTE|Autorizzazioni chiave| Operazioni crittografiche|Annulla il wrapping della chiave**.
2. Salvare gli script seguenti in un file con estensione PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Impostare i parametri. Lo script elabora il segreto della chiave KEK per creare la chiave BEK e quindi lo salva in una cartella locale nella macchina virtuale di ripristino.

4. All'avvio dello script viene visualizzato l'output seguente:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Al termine dell'esecuzione dello script viene visualizzato l'output seguente:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Per sbloccare il disco collegato usando il file con estensione BEK, eseguire il comando seguente:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    In questo esempio, il disco del sistema operativo collegato è l'unità F. Verificare di usare la lettera di unità corretta. 

    - Se il disco è stato sbloccato correttamente usando la chiave BEK, il problema di BitLocker può considerarsi risolto. 

    - Se l'uso della chiave BEK non ha sbloccato il disco, è possibile sospendere la protezione per disattivare temporaneamente BitLocker con il comando seguente:
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Se si vuole ricreare la macchina virtuale usando il disco di sistema, è necessario decrittografare completamente l'unità. A tale scopo, usare il comando seguente:

        ```powershell
        manage-bde -off F:
        ```

6. Scollegare il disco dalla macchina virtuale di ripristino e quindi ricollegarlo alla macchina virtuale in questione come disco di sistema. Per altre informazioni, vedere [Risolvere i problemi relativi a una macchina virtuale Windows collegando il disco del sistema operativo a una macchina virtuale di ripristino](troubleshoot-recovery-disks-windows.md).
