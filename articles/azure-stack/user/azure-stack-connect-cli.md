---
title: Connettersi a Azure Stack con CLI | Documenti Microsoft
description: Informazioni su come utilizzare l'interfaccia della riga di comando multipiattaforma (CLI) per gestire e distribuire le risorse sullo Stack di Azure
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: mabrigg
ms.openlocfilehash: 5d15815e9b1d20ab03b5716de45ad0fa77a11057
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Installare e configurare CLI per l'utilizzo con Azure Stack

In questo articolo viene illustrato il processo di Azure interfaccia della riga di comando (CLI) per gestire le risorse di Azure Stack Development Kit da Linux e piattaforme client Mac. 

## <a name="install-cli"></a>Installare l'interfaccia della riga di comando

Successivamente, accedere a una workstation di sviluppo e installare CLI. Stack di Azure richiede la versione 2.0 di CLI di Azure. È possibile installare che tramite la procedura descritta nel [installare Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) articolo. Per verificare se l'installazione sia avvenuta correttamente, aprire un terminale o una finestra del prompt dei comandi ed eseguire il comando seguente:

```azurecli
az --version
```

Dovrebbe visualizzare la versione di CLI di Azure e altre librerie dipendenti che sono installati nel computer.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Considerare attendibile il certificato radice CA Azure Stack

Ottenere il certificato radice CA Azure Stack dall'operatore Azure Stack e verificare l'attendibilità. Per considerare attendibile il certificato radice CA Azure Stack, li aggiunge al certificato Python esistente. Se si eseguono CLI da un computer Linux che viene creato all'interno dell'ambiente dello Stack di Azure, eseguire il comando bash seguente:

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Se si eseguono CLI da un computer all'esterno dell'ambiente Azure Sack, è necessario innanzitutto impostare [connettività VPN a Azure Stack](azure-stack-connect-azure-stack.md). Ora di copiare il certificato PEM esportato in precedenza nella workstation di sviluppo ed eseguire i comandi seguenti, a seconda del sistema operativo della workstation di sviluppo.

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Ottenere l'endpoint di alias di macchina virtuale

Prima che gli utenti possono creare macchine virtuali tramite l'interfaccia CLI, è necessario contattare l'operatore di Stack di Azure e ottenere l'URI dell'endpoint alias macchina virtuale. Ad esempio, Azure Usa il seguente URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. L'amministratore del cloud deve configurare un endpoint simile per lo Stack di Azure con le immagini che sono disponibili sul mercato di Stack di Azure. Gli utenti devono passare l'URI dell'endpoint per il `endpoint-vm-image-alias-doc` parametro per il `az cloud register` come indicato nella sezione successiva. 
   

## <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Utilizzare la procedura seguente per connettersi allo Stack di Azure:

1. Registrare l'ambiente dello Stack di Azure eseguendo il `az cloud register` comando.
   
   a. Per registrare il *cloud amministrativi* ambiente, utilizzare:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Per registrare il *utente* ambiente, utilizzare:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. Impostare l'ambiente attivo utilizzando i comandi seguenti.

   a. Per il *cloud amministrativi* ambiente, utilizzare:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Per il *utente* ambiente, utilizzare:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Aggiornare la configurazione dell'ambiente per utilizzare il profilo di versione API specifico dello Stack di Azure. Per aggiornare la configurazione, eseguire il comando seguente:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Accedere all'ambiente dello Stack di Azure usando il `az login` comando. È possibile accedere per l'ambiente dello Stack di Azure come un utente o come un [dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Accedere come un *utente*: È possibile specificare il nome utente e password direttamente all'interno di `az login` comando o eseguire l'autenticazione utilizzando un browser. È necessario eseguire quest'ultimo se l'account è attivata l'autenticazione a più fattori.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Se l'account utente è attivata l'autenticazione a più fattori, è possibile utilizzare il `az login command` senza fornire il `-u` parametro. Esecuzione del comando fornisce un URL e un codice che è necessario utilizzare per l'autenticazione.
   
   * Accedere come un *dell'entità servizio*: prima di accedere, [creare un'entità servizio tramite il portale di Azure](azure-stack-create-service-principals.md) o CLI e assegnarlo a un ruolo. Accedere a questo punto, tramite il comando seguente:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che abbiamo tutti gli elementi del programma di installazione, è possibile utilizzare CLI per creare le risorse all'interno dello Stack di Azure. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Utilizzare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Se il gruppo di risorse viene creato correttamente, il comando precedente restituisce le seguenti proprietà della risorsa appena creata:

![Gruppo di risorse Crea output](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problemi noti
Esistono alcuni problemi noti che è necessario considerare quando si usa CLI nello Stack di Azure:

* Ad esempio la modalità interattiva CLI il `az interactive` comando non è ancora supportato nello Stack di Azure.
* Per ottenere l'elenco delle immagini di macchina virtuale disponibile nello Stack di Azure, utilizzare il `az vm images list --all` comando anziché il `az vm image list` comando. Specifica il `--all` opzione assicura che risposta restituisce solo le immagini disponibili nell'ambiente dello Stack di Azure. 
* Gli alias di immagine di macchina virtuale che sono disponibili in Azure potrebbero non essere applicabili allo Stack di Azure. Quando si utilizzano le immagini di macchina virtuale, è necessario utilizzare l'intero parametro URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) anziché l'alias di immagine. Questo URN deve corrispondere le specifiche di immagine come derivati dal `az vm images list` comando.
* Per impostazione predefinita, 2.0 CLI usa "Standard_DS1_v2" come le dimensioni dell'immagine macchina virtuale predefinito. Tuttavia, la dimensione non è ancora disponibile nello Stack di Azure, in tal caso, è necessario specificare il `--size` parametro in modo esplicito durante la creazione di una macchina virtuale. È possibile ottenere l'elenco di dimensioni delle macchine virtuali che sono disponibili nello Stack di Azure mediante il `az vm list-sizes --location <locationName>` comando.


## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)

[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
