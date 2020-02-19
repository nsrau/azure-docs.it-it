---
title: Accedi in modo sicuro Key Vault con Azure Batch batch
description: Informazioni su come accedere a livello di codice alle credenziali da Key Vault usando Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 14cbacf43e83dc768e9a85620df131533b746671
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463102"
---
# <a name="securely-access-key-vault-with-batch"></a>Accedi in modo sicuro Key Vault con batch

Questo articolo illustra come configurare i nodi batch per accedere in modo sicuro alle credenziali archiviate in Azure Key Vault. Non è possibile inserire le credenziali di amministratore in Key Vault, quindi le credenziali di hardcoded per accedere Key Vault da uno script. La soluzione consiste nell'usare un certificato che conceda ai nodi di batch l'accesso a Key Vault. Con pochi passaggi è possibile implementare l'archiviazione delle chiavi sicure per batch.

Per eseguire l'autenticazione Azure Key Vault da un nodo batch, è necessario:

- Credenziale Azure Active Directory (Azure AD)
- Un certificato
- Un account batch
- Un pool di batch con almeno un nodo

## <a name="obtain-a-certificate"></a>Ottenere un certicato

Se non si dispone già di un certificato, il modo più semplice per ottenerne uno consiste nel generare un certificato autofirmato usando lo strumento da riga di comando `makecert`.

In genere è possibile trovare `makecert` in questo percorso: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Aprire un prompt dei comandi come amministratore e passare a `makecert` usando l'esempio seguente.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Usare quindi lo strumento `makecert` per creare file di certificato autofirmati, denominati `batchcertificate.cer` e `batchcertificate.pvk`. Il nome comune (CN) usato non è importante per questa applicazione, ma è utile per fare in modo che indichi l'uso del certificato.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch richiede un file di `.pfx`. Usare lo strumento [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) per convertire i file di `.cer` e `.pvk` creati da `makecert` in un singolo file `.pfx`.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio

L'accesso a Key Vault viene concesso a un **utente** o a un' **entità servizio**. Per accedere a Key Vault a livello di codice, usare un'entità servizio con il certificato creato nel passaggio precedente.

Per altre informazioni sulle entità servizio di Azure, vedere [oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> L'entità servizio deve trovarsi nello stesso tenant Azure AD del Key Vault.

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

Gli URL per l'applicazione non sono importanti perché vengono utilizzati solo per l'accesso Key Vault.

## <a name="grant-rights-to-key-vault"></a>Concedere i diritti per Key Vault

L'entità servizio creata nel passaggio precedente richiede l'autorizzazione per recuperare i segreti da Key Vault. L'autorizzazione può essere concessa tramite il portale di Azure o con il comando di PowerShell riportato di seguito.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Assegnare un certificato a un account batch

Creare un pool di batch, quindi passare alla scheda certificato nel pool e assegnare il certificato creato. Il certificato è ora in tutti i nodi batch.

Successivamente, è necessario assegnare il certificato all'account batch. L'assegnazione del certificato all'account consente di assegnarla ai pool e quindi ai nodi. Il modo più semplice per eseguire questa operazione è passare all'account batch nel portale, passare a **certificati**e selezionare **Aggiungi**. Caricare il file di `.pfx` generato in [ottenere un certificato](#obtain-a-certificate) e fornire la password. Al termine, il certificato viene aggiunto all'elenco ed è possibile verificare l'identificazione personale.

A questo punto, quando si crea un pool di batch, è possibile passare a **certificati** all'interno del pool e assegnare il certificato creato al pool. Quando si esegue questa operazione, assicurarsi di selezionare **LocalMachine** per il percorso dell'archivio. Il certificato viene caricato in tutti i nodi batch del pool.

## <a name="install-azure-powershell"></a>Installare Azure PowerShell

Se si prevede di accedere Key Vault usando gli script di PowerShell nei nodi, è necessario che sia installata la libreria di Azure PowerShell. Esistono diversi modi per eseguire questa operazione. se nei nodi è installato Windows Management Framework (WMF) 5, è possibile usare il comando install-Module per scaricarlo. Se si usano nodi che non hanno WMF 5, il modo più semplice per installarlo consiste nell'aggregare il Azure PowerShell `.msi` file con i file batch e quindi chiamare il programma di installazione come prima parte dello script di avvio del batch. Per informazioni dettagliate, vedere questo esempio:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Accedere all'insieme di credenziali delle chiavi

A questo punto, tutti i set di impostazioni consentono di accedere Key Vault negli script in esecuzione nei nodi batch. Per accedere Key Vault da uno script, è sufficiente che lo script esegua l'autenticazione per Azure AD utilizzando il certificato. Per eseguire questa operazione in PowerShell, usare i comandi di esempio seguenti. Specificare il GUID appropriato per l'identificazione **personale**, l'ID **app** (l'ID dell'entità servizio) e l' **ID tenant** (il tenant in cui è presente l'entità servizio).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Dopo l'autenticazione, accedere all'insieme di credenziali delle credenziali come si farebbe normalmente.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Queste sono le credenziali da usare nello script.
