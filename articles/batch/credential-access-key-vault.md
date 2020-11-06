---
title: Accedere in modo sicuro a Key Vault con Batch
description: Informazioni su come accedere a livello di codice alle proprie credenziali da Key Vault con Azure Batch.
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 25cb05374fc0667306e2b1004b3cd237413b4409
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337492"
---
# <a name="securely-access-key-vault-with-batch"></a>Accedere in modo sicuro a Key Vault con Batch

Questo articolo illustra come configurare i nodi batch per accedere in modo sicuro alle credenziali archiviate in [Azure Key Vault](../key-vault/general/overview.md). Le credenziali di amministratore non possono essere usate in Key Vault, quindi non è possibile inserire le credenziali hardcoded per accedere a Key Vault con uno script. Si può usare un certificato che concede ai nodi Batch di accedere a Key Vault.

Per eseguire l'autenticazione a Azure Key Vault da un nodo di Batch sono necessari:

- Credenziali di Azure Active Directory (Azure AD)
- Un certificato
- Un account Batch
- Un pool di Batch con almeno un nodo

## <a name="obtain-a-certificate"></a>Ottenere un certificato

Se non si dispone già di un certificato, il modo più facile per ottenerlo è generare un certificato autofirmato usando la riga di comando di `makecert`.

Solitamente `makecert` si trova in questo percorso: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Aprire un prompt dei comandi come amministratore e passare a `makecert` usando l'esempio seguente.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Usare quindi lo strumento `makecert` per creare file di certificato autofirmato chiamati `batchcertificate.cer` e `batchcertificate.pvk`. Il nome comune usato non è importante per questa applicazione, ma è utile come riferimento per ricordare l'uso del certificato.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Per Batch è necessario un file `.pfx`. Usare lo strumento [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) per convertire i file `.cer` e `.pvk` creati da `makecert` in un file singolo di `.pfx`.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio

L'accesso a Key Vault può essere concesso a un **utente** o a un' **entità servizio**. Per accedere a Key Vault a livello di codice, usare un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il certificato creato nel passaggio precedente. L'entità servizio deve trovarsi nello stesso tenant Azure AD di Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Gli URL per l'applicazione non sono importanti, perché vengono utilizzati solo per l'accesso Key Vault.

## <a name="grant-rights-to-key-vault"></a>Concedere i diritti per Key Vault

Per recuperare i segreti da Key Vault, l'entità servizio creata in precedenza deve essere autorizzata. L'autorizzazione può essere concessa tramite il [portale di Azure](/azure/key-vault/general/assign-access-policy-portal) o con il comando di PowerShell riportato di seguito.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Assegnare un certificato a un account Batch

Creare un pool di Batch, quindi passare alla scheda del certificato nel pool e assegnare il certificato creato. Il certificato si troverà in tutti i nodi di Batch.

Assegnare quindi il certificato all'account batch. L'assegnazione del certificato all'account consente a batch di assegnarla ai pool e quindi ai nodi. Il modo più semplice per farlo è accedere al proprio account Batch sul portale, selezionare **Certificati** e quindi **Aggiungi**. Caricare il `.pfx` file generato in precedenza e fornire la password. Al termine, il certificato viene aggiunto all'elenco ed è possibile verificare l'identificazione personale.

A questo punto, quando si crea un pool di batch, è possibile passare ai **certificati** all'interno del pool e assegnare il certificato creato al pool. Quando si esegue questa operazione, assicurarsi di selezionare **LocalMachine** come percorso di archiviazione. Il certificato verrà caricato in tutti i nodi di Batch del pool.

## <a name="install-azure-powershell"></a>Installare Azure PowerShell

Per accedere a Key Vault usando script di PowerShell nei nodi è necessario installare la libreria di Azure PowerShell. Se nei nodi è installato Windows Management Framework (WMF) 5, è possibile usare il comando install-Module per scaricarlo. Se si usano nodi che non hanno WMF 5, il modo più semplice per installarlo consiste nell'aggregare il file di Azure PowerShell `.msi` con i file batch e quindi chiamare il programma di installazione come prima parte dello script di avvio del batch. Per informazioni dettagliate vedere l'esempio seguente:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Accedere all'insieme di credenziali delle chiavi

A questo punto si è pronti ad accedere Key Vault negli script in esecuzione nei nodi batch. Per accedere a Key Vault con uno script, è sufficiente autenticare lo script in Azure AD usando il certificato. Per farlo con PowerShell, usare i comandi di esempio seguenti. Specificare il GUID appropriato per **Identificazione personale** , **ID app** (l'ID dell'entità servizio) e **ID tenant** (il tenant in cui si trova l'entità servizio).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Dopo l'autenticazione, accedere normalmente a Key Vault.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Queste sono le credenziali da usare nello script.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure Key Vault](../key-vault/general/overview.md).
- Esaminare la [linea di base di sicurezza di Azure per batch](security-baseline.md).
- Informazioni sulle funzionalità di batch, ad esempio la [configurazione dell'accesso ai nodi di calcolo, l'uso di](pool-endpoint-configuration.md)nodi di [calcolo Linux](batch-linux-nodes.md)e l' [uso di endpoint privati](private-connectivity.md).