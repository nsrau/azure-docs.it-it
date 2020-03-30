---
title: Accesso sicuro all'insieme di credenziali delle chiavi con Batch - Azure BatchSecurely access Key Vault with Batch - Azure Batch
description: Informazioni su come accedere a livello di codice alle credenziali dall'insieme di credenziali delle chiavi usando Azure Batch.Learn how to programmatically access your credentials from Key Vault using Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192303"
---
# <a name="securely-access-key-vault-with-batch"></a>Accedere in modo sicuro a Key Vault con Batch

In this article, you'll learn how to set up Batch nodes to securely access credentials stored in Azure Key Vault. Non ha senso inserire le credenziali di amministratore nell'insieme di credenziali delle chiavi, quindi codificare le credenziali per accedere all'insieme di credenziali delle chiavi da uno script. La soluzione consiste nell'utilizzare un certificato che concede ai nodi Batch l'accesso all'insieme di credenziali delle chiavi. Con pochi passaggi, è possibile implementare l'archiviazione sicura delle chiavi per Batch.With a few steps, we can implement secure key storage for Batch.

Per eseguire l'autenticazione all'insieme di credenziali delle chiavi di Azure da un nodo Batch, è necessario:To authenticate to Azure Key Vault from a Batch node, you need:

- Una credenziale di Azure Active Directory (Azure AD)An Azure Active Directory (Azure AD) credential
- Un certificato
- Un account Batch
- Un pool Batch con almeno un nodo

## <a name="obtain-a-certificate"></a>Ottenere un certicato

Se non si dispone già di un certificato, il modo più semplice per `makecert` ottenerlo consiste nel generare un certificato autofirmato utilizzando lo strumento da riga di comando.

In genere `makecert` è possibile `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`trovare in questo percorso: . Aprire un prompt dei comandi `makecert` come amministratore e passare all'esempio seguente.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Successivamente, utilizzare `makecert` lo strumento per creare `batchcertificate.cer` file `batchcertificate.pvk`di certificato autofirmati denominati e . Il nome comune (CN) utilizzato non è importante per questa applicazione, ma è utile renderlo qualcosa che indica a cosa viene utilizzato il certificato.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch richiede `.pfx` un file. Utilizzare lo strumento [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) per convertire `.cer` i file e `.pvk` creati da `makecert` in un singolo `.pfx` file.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio

L'accesso all'insieme di credenziali delle chiavi viene concesso a un **utente** o a un'entità **servizio.** Per accedere all'insieme di credenziali delle chiavi a livello di codice, usare un'entità servizio con il certificato creato nel passaggio precedente.

Per altre informazioni sulle entità servizio di Azure, vedere Oggetti applicazione e entità servizio in Azure Active Directory.For more information on Azure service principals, see [Application and service principal objects in Azure Active Directory.](../active-directory/develop/app-objects-and-service-principals.md)

> [!NOTE]
> L'entità servizio deve trovarsi nello stesso tenant di Azure AD dell'insieme di credenziali delle chiavi.

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

Gli URL per l'applicazione non sono importanti poiché vengono usati solo per l'accesso all'insieme di credenziali delle chiavi.

## <a name="grant-rights-to-key-vault"></a>Concedere i diritti all'insieme di credenziali delle chiaviGrant rights to Key Vault

L'entità servizio creata nel passaggio precedente deve disporre dell'autorizzazione per recuperare i segreti dall'insieme di credenziali delle chiavi. L'autorizzazione può essere concessa tramite il portale di Azure o con il comando PowerShell riportato di seguito.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Assegnare un certificato a un account BatchAssign a certificate to a Batch account

Creare un pool Batch, quindi passare alla scheda Del certificato nel pool e assegnare il certificato creato. Il certificato è ora su tutti i nodi Batch.The certificate is now on all Batch nodes.

Successivamente, è necessario assegnare il certificato all'account Batch.Next, we need to assign the certificate to the Batch account. L'assegnazione del certificato all'account consente di assegnarlo ai pool e quindi ai nodi. Il modo più semplice per eseguire questa operazione consiste nell'accedere all'account Batch nel portale, passare a **Certificati**e selezionare **Aggiungi**. Caricare `.pfx` il file generato nel menu [Ottenere un certificato](#obtain-a-certificate) e fornire la password. Al termine, il certificato viene aggiunto all'elenco ed è possibile verificare l'identificazione personale.

Ora, quando si crea un pool Batch, è possibile passare a **Certificati** all'interno del pool e assegnare il certificato creato a tale pool. In questo caso, assicurarsi di selezionare **LocalMachine** per il percorso dell'archivio. Il certificato viene caricato in tutti i nodi Batch del pool.

## <a name="install-azure-powershell"></a>Installare Azure PowerShell

Se si prevede di accedere all'insieme di credenziali delle chiavi usando gli script di PowerShell nei nodi, è necessaria l'installazione della libreria di Azure PowerShell.If you plan of accessing Key Vault using PowerShell scripts on your nodes, then you need the Azure PowerShell library installed. Esistono alcuni modi per eseguire questa operazione, se nei nodi è installato Windows Management Framework (WMF) 5, è possibile utilizzare il comando install-module per scaricarlo. Se si usano nodi che non dispongono di WMF 5, il modo più `.msi` semplice per installarlo consiste nell'impacchettare il file di Azure PowerShell con i file Batch e quindi chiamare il programma di installazione come prima parte dello script di avvio Batch. Vedere questo esempio per i dettagli:See this example for details:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Accedere all'insieme di credenziali delle chiavi

Ora siamo tutti di installazione per accedere a Key Vault in script in esecuzione su nodi Batch. Per accedere all'insieme di credenziali delle chiavi da uno script, è sufficiente che lo script esegua l'autenticazione in Azure AD usando il certificato. A tale scopo in PowerShell, usare i comandi di esempio seguenti. Specificare il GUID appropriato per **Identificazione personale,** **ID app** (ID applicazione (ID servizio) e **ID tenant** (il tenant in cui è presente l'entità servizio).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Una volta autenticato, accedere a KeyVault come si farebbe normalmente.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Queste sono le credenziali da utilizzare nello script.
