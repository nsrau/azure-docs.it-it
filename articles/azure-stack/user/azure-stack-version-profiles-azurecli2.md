---
title: Connettersi ad Azure Stack con CLI | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando multipiattaforma (CLI) per gestire e distribuire le risorse in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 519046081a7f9778fb430daa0cd418cf9863a2b0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57975628"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Usare i profili delle versioni API con il comando di Azure in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile seguire i passaggi descritti in questo articolo per informazioni su backup l'interfaccia della riga di comando di Azure per gestire le risorse di Azure Stack Development Kit (ASDK) dalle piattaforme client Linux, Mac e Windows.

## <a name="prepare-for-azure-cli"></a>Preparazione della riga di comando di Azure

È necessario il certificato radice di autorità di certificazione per Azure Stack per l'uso della riga di comando di Azure nel computer di sviluppo. Si utilizza il certificato per gestire le risorse tramite l'interfaccia della riga di comando.

 - **Il certificato radice CA Azure Stack** è obbligatorio se si usa l'interfaccia della riga di comando da una workstation all'esterno di ASDK.  

 - **L'endpoint di macchina virtuale gli alias** fornisce un alias, ad esempio "UbuntuLTS" o "Win2012Datacenter", che fa riferimento a un editore di immagini, offerta, SKU e versione come parametro singolo quando si distribuiscono le macchine virtuali.  

Le sezioni seguenti descrivono come ottenere questi valori.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Esportare il certificato di autorità di certificazione di Azure Stack radice

Se si usa un sistema integrato, non è necessario esportare il certificato di autorità di certificazione radice. È necessario esportare il certificato radice CA in un ASDK.

Per esportare il certificato radice ASDK nel formato PEM:

1. [Creare una macchina virtuale Windows in Azure Stack](azure-stack-quick-windows-portal.md).

2. Accedi al computer, aprire un prompt di PowerShell con privilegi elevato e quindi eseguire lo script seguente:

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

3. Copiare il certificato nel computer locale.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurare l'endpoint di alias di macchina virtuale

È possibile configurare un endpoint accessibile pubblicamente che ospita un file di alias di macchina virtuale. Il file di alias di macchina virtuale è un file JSON che fornisce un nome comune per un'immagine. Si userà il nome quando si distribuisce una macchina virtuale come parametro di comando di Azure.

1. Se si pubblica un'immagine personalizzata, prendere nota delle informazioni server di pubblicazione, offerta, SKU e versione specificati durante la pubblicazione. Se è un'immagine del marketplace, è possibile visualizzare le informazioni usando il ```Get-AzureVMImage``` cmdlet.  

2. Scaricare il [file di esempio](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) da GitHub.

3. Creare un account di archiviazione in Azure Stack. Al termine, creare un contenitore blob. Impostare i criteri di accesso per "pubblico".  

4. Caricare il file JSON nel nuovo contenitore. Al termine, è possibile visualizzare l'URL del blob. Selezionare il nome del blob e quindi selezionando l'URL dalle proprietà del blob.

### <a name="install-or-upgrade-cli"></a>Installazione o l'aggiornamento della riga di comando

Accedere alla workstation di sviluppo e installare CLI. Azure Stack richiede la versione 2.0 o versione successiva della riga di comando di Azure. La versione più recente dei profili di API richiede una versione corrente della riga di comando.  È possibile installare l'interfaccia della riga di comando usando i passaggi descritti nel [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) articolo. Per verificare se l'installazione ha avuto esito positivo, aprire un terminale o una finestra del prompt dei comandi ed eseguire il comando seguente:

```shell
az --version
```

Dovrebbe visualizzare la versione della CLI di Azure e altre librerie dipendenti installate nel computer.

### <a name="install-python-on-windows"></a>Installare Python in Windows

1. Installare [Python 3 nel sistema](https://www.python.org/downloads/).

2. Aggiornare PIP. PIP è una gestione pacchetti per Python. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```PowerShell  
    python -m pip install --upgrade pip
    ```

3. Installare il **quella ottenibile** modulo. [Quella ottenibile](https://pypi.org/project/certifi/) un modulo e una raccolta di certificati radice per convalidare l'attendibilità dei certificati SSL durante la verifica dell'identità host TLS. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```PowerShell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Installare Python in Linux

1. L'immagine di Ubuntu 16.04 include Python 2.7 e 3.5 di Python installato per impostazione predefinita. È possibile verificare la versione di Python 3 eseguendo il comando seguente:

    ```bash  
    python3 --version
    ```

2. Aggiornare PIP. PIP è una gestione pacchetti per Python. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Installare il **quella ottenibile** modulo. [Quella ottenibile](https://pypi.org/project/certifi/) è una raccolta di certificati radice per convalidare l'attendibilità dei certificati SSL durante la verifica dell'identità host TLS. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>Installare Python in macOS

1. Installare [Python 3 nel sistema](https://www.python.org/downloads/). Per le versioni di Python 3.7, Python.org fornisce due opzioni di installazione binari per il download. La variante predefinito è 64-bit-only e funziona su macOS 10.9 (Mavericks) e successive. Controllare la versione di python, aprire il terminale e digitando il comando seguente:

    ```bash  
    python3 --version
    ```

2. Aggiornare PIP. PIP è una gestione pacchetti per Python. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Installare il **quella ottenibile** modulo. [Quella ottenibile](https://pypi.org/project/certifi/) un modulo e una raccolta di certificati radice per convalidare l'attendibilità dei certificati SSL durante la verifica dell'identità host TLS. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

In questa sezione illustrerà impostazione della riga di comando se si usa Azure AD come servizio di gestione di identità e uso della riga di comando in un computer Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Considerare attendibile il certificato di autorità di certificazione di Azure Stack radice

Se si usa il ASDK, è necessario considerare attendibile il certificato radice della CA nel computer remoto. Non è necessario eseguire questa operazione con i sistemi integrati.

Per rendere attendibile il certificato radice CA Azure Stack, aggiungerlo al certificato Python esistente.

1. Trovare il percorso di certificato nel computer. Il percorso può variare in base alla quale è stato installato Python. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Prendere nota del percorso di certificati. Ad esempio: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Il percorso specifica varia in base al sistema operativo e la versione di Python installata.

2. Considerare attendibile il certificato radice CA Azure Stack aggiungendolo al certificato Python esistente.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

1. Registrare l'ambiente dello Stack di Azure eseguendo il `az cloud register` comando.

    In alcuni scenari, connettività diretta a internet in uscita viene instradata attraverso un proxy o firewall, che impone l'intercettazione SSL. In questi casi, il `az cloud register` comando può non riuscire con un errore, ad esempio "Impossibile ottenere gli endpoint dal cloud". Per risolvere questo errore, è possibile impostare le variabili di ambiente seguenti:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registrare l'ambiente. Usare i parametri seguenti durante l'esecuzione `az cloud register`.

    | Valore | Esempio | DESCRIZIONE |
    | --- | --- | --- |
    | Nome ambiente | AzureStackUser | Usare `AzureStackUser` per l'ambiente utente. Se si è l'operatore, specificare `AzureStackAdmin`. |
    | Endpoint di Resource Manager | https://management.local.azurestack.external | Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/` Il **ResourceManagerUrl** in sistemi integrati è: `https://management.<region>.<fqdn>/` Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se hai una domanda sull'endpoint di sistema integrato, contattare l'operatore cloud. |
    | Endpoint di archiviazione | local.azurestack.external | `local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Suffisso Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Immagine VM alias doc endpoint | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento che contiene gli alias di immagine di macchina virtuale. Per altre informazioni, vedere [# # # configurare l'endpoint di macchina virtuale gli alias](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Impostare l'ambiente attivo usando i comandi seguenti.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aggiornare la configurazione dell'ambiente per utilizzare il profilo di versione API specifico dello Stack di Azure. Per aggiornare la configurazione, eseguire il comando seguente:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se si esegue una versione di Azure Stack prima della compilazione 1808, è necessario usare il profilo di versione API **2017-03-09-profile** anziché il profilo di versione API **2018-03-01-hybrid**. È necessario usare una versione recente della riga di comando di Azure.
 
1. Accedere all'ambiente Azure Stack usando il `az login` comando. È possibile accedere all'ambiente Azure Stack come un utente o come un [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Accedere come un *utente*: 

     È possibile specificare il nome utente e password direttamente all'interno di `az login` comando o eseguire l'autenticazione usando un browser. È necessario eseguire quest'ultimo se l'account è abilitata l'autenticazione a più fattori:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Se l'account utente è attivata l'autenticazione a più fattori, è possibile usare la `az login` comando senza fornire il `-u` parametro. Questo comando fornisce un URL e un codice che è necessario usare per eseguire l'autenticazione.

   - Accedere come un *entità servizio*: 
    
     Prima di accedere, [creare un'entità servizio tramite il portale di Azure](azure-stack-create-service-principals.md) o CLI e assegnarle un ruolo. Accedere a questo punto, usando il comando seguente:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testare la connettività

Con tutte le impostazioni, usare della riga di comando per creare risorse in Azure Stack. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Usare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Se il gruppo di risorse viene creato correttamente, il comando precedente restituisce le proprietà seguenti della risorsa appena creata:

![Gruppo di risorse creare output](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

In questa sezione illustrerà impostazione della riga di comando se si usa Active Directory Federated Services (ADFS) come il servizio di gestione delle identità e uso della riga di comando in un computer Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Considerare attendibile il certificato di autorità di certificazione di Azure Stack radice

Se si usa il ASDK, è necessario considerare attendibile il certificato radice della CA nel computer remoto. Non è necessario eseguire questa operazione con i sistemi integrati.

1. Trovare il percorso di certificato nel computer. Il percorso può variare in base alla quale è stato installato Python. Aprire un prompt dei comandi o un prompt di PowerShell con privilegi elevato e digitare il comando seguente:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Prendere nota del percorso di certificati. Ad esempio: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Il percorso specifica varia in base al sistema operativo e la versione di Python installata.

2. Considerare attendibile il certificato radice CA Azure Stack aggiungendolo al certificato Python esistente.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

1. Registrare l'ambiente dello Stack di Azure eseguendo il `az cloud register` comando.

    In alcuni scenari, connettività diretta a internet in uscita viene instradata attraverso un proxy o firewall, che impone l'intercettazione SSL. In questi casi, il `az cloud register` comando può non riuscire con un errore, ad esempio "Impossibile ottenere gli endpoint dal cloud". Per risolvere questo errore, è possibile impostare le variabili di ambiente seguenti:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registrare l'ambiente. Usare i parametri seguenti durante l'esecuzione `az cloud register`.

    | Valore | Esempio | DESCRIZIONE |
    | --- | --- | --- |
    | Nome ambiente | AzureStackUser | Usare `AzureStackUser` per l'ambiente utente. Se si è l'operatore, specificare `AzureStackAdmin`. |
    | Endpoint di Resource Manager | https://management.local.azurestack.external | Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/` Il **ResourceManagerUrl** in sistemi integrati è: `https://management.<region>.<fqdn>/` Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se hai una domanda sull'endpoint di sistema integrato, contattare l'operatore cloud. |
    | Endpoint di archiviazione | local.azurestack.external | `local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Suffisso Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Immagine VM alias doc endpoint | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento che contiene gli alias di immagine di macchina virtuale. Per altre informazioni, vedere [# # # configurare l'endpoint di macchina virtuale gli alias](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Impostare l'ambiente attivo usando i comandi seguenti.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aggiornare la configurazione dell'ambiente per utilizzare il profilo di versione API specifico dello Stack di Azure. Per aggiornare la configurazione, eseguire il comando seguente:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se si esegue una versione di Azure Stack prima della compilazione 1808, è necessario usare il profilo di versione API **2017-03-09-profile** anziché il profilo di versione API **2018-03-01-hybrid**. È necessario usare una versione recente della riga di comando di Azure.

1. Accedere all'ambiente Azure Stack usando il `az login` comando. È possibile accedere all'ambiente Azure Stack come un utente o come un [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Accedere come un *utente*:

     È possibile specificare il nome utente e password direttamente all'interno di `az login` comando o eseguire l'autenticazione usando un browser. È necessario eseguire quest'ultimo se l'account è abilitata l'autenticazione a più fattori:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > Se l'account utente è attivata l'autenticazione a più fattori, è possibile usare la `az login` comando senza fornire il `-u` parametro. Questo comando fornisce un URL e un codice che è necessario usare per eseguire l'autenticazione.

   - Accedere come un *entità servizio*: 
    
     Preparare il file con estensione PEM da utilizzare per l'accesso dell'entità servizio.

     Nel computer client in cui l'entità è stata creata, esportare il certificato dell'entità servizio come un file pfx con la chiave privata disponibile all'indirizzo `cert:\CurrentUser\My`; il certificato nome ha lo stesso nome dell'entità.

     Convertire il file pfx in pem (usare l'utilità di OpenSSL).

     Accedi per l'interfaccia della riga di comando:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Testare la connettività

Con tutte le impostazioni, usare della riga di comando per creare risorse in Azure Stack. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Usare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Se il gruppo di risorse viene creato correttamente, il comando precedente restituisce le proprietà seguenti della risorsa appena creata:

![Gruppo di risorse creare output](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

In questa sezione illustrerà impostazione della riga di comando se si usa Azure AD come servizio di gestione di identità e uso della riga di comando in un computer Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Considerare attendibile il certificato di autorità di certificazione di Azure Stack radice

Se si usa il ASDK, è necessario considerare attendibile il certificato radice della CA nel computer remoto. Non è necessario eseguire questa operazione con i sistemi integrati.

Considerare attendibile il certificato radice CA Azure Stack aggiungendolo al certificato Python esistente.

1. Trovare il percorso di certificato nel computer. Il percorso può variare in base alla quale è stato installato Python. Dovrai disporre di pip e quella ottenibile [modulo installato](#install-python-on-linux). È possibile usare il comando Python seguito dal prompt di bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Prendere nota del percorso del certificato; ad esempio, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Il percorso specifico varia a seconda del sistema operativo e la versione di Python installata.

2. Eseguire il comando bash seguente con il percorso del certificato.

   - Per un computer Linux remoto:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Per una macchina Linux all'interno dell'ambiente Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Usare la procedura seguente per connettersi ad Azure Stack:

1. Registrare l'ambiente dello Stack di Azure eseguendo il `az cloud register` comando. In alcuni scenari, connettività diretta a internet in uscita viene instradata attraverso un proxy o firewall, che impone l'intercettazione SSL. In questi casi, il `az cloud register` comando può non riuscire con un errore, ad esempio "Impossibile ottenere gli endpoint dal cloud". Per risolvere questo errore, è possibile impostare le variabili di ambiente seguenti:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registrare l'ambiente. Usare i parametri seguenti durante l'esecuzione `az cloud register`.

    | Valore | Esempio | DESCRIZIONE |
    | --- | --- | --- |
    | Nome ambiente | AzureStackUser | Usare `AzureStackUser` per l'ambiente utente. Se si è l'operatore, specificare `AzureStackAdmin`. |
    | Endpoint di Resource Manager | https://management.local.azurestack.external | Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/` Il **ResourceManagerUrl** in sistemi integrati è: `https://management.<region>.<fqdn>/` Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se hai una domanda sull'endpoint di sistema integrato, contattare l'operatore cloud. |
    | Endpoint di archiviazione | local.azurestack.external | `local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Suffisso Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Immagine VM alias doc endpoint | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento che contiene gli alias di immagine di macchina virtuale. Per altre informazioni, vedere [# # # configurare l'endpoint di macchina virtuale gli alias](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Impostare l'ambiente attivo. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aggiornare la configurazione dell'ambiente per utilizzare il profilo di versione API specifico dello Stack di Azure. Per aggiornare la configurazione, eseguire il comando seguente:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se si esegue una versione di Azure Stack prima della compilazione 1808, è necessario usare il profilo di versione API **2017-03-09-profile** anziché il profilo di versione API **2018-03-01-hybrid**. È necessario usare una versione recente della riga di comando di Azure.

5. Accedere all'ambiente Azure Stack usando il `az login` comando. È possibile accedere all'ambiente Azure Stack come un utente o come un [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md). 

   * Accedere come un *utente*:

     È possibile specificare il nome utente e password direttamente all'interno di `az login` comando o eseguire l'autenticazione usando un browser. È necessario eseguire quest'ultimo se l'account è abilitata l'autenticazione a più fattori:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Se l'account utente è attivata l'autenticazione a più fattori, è possibile usare la `az login` comando senza fornire il `-u` parametro. Questo comando fornisce un URL e un codice che è necessario usare per eseguire l'autenticazione.
   
   * Accedere come un *entità servizio*
    
     Prima di accedere, [creare un'entità servizio tramite il portale di Azure](azure-stack-create-service-principals.md) o CLI e assegnarle un ruolo. Accedere a questo punto, usando il comando seguente:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testare la connettività

Con tutte le impostazioni, usare della riga di comando per creare risorse in Azure Stack. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Usare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```azurecli
    az group create -n MyResourceGroup -l local
```

Se il gruppo di risorse viene creato correttamente, il comando precedente restituisce le proprietà seguenti della risorsa appena creata:

![Gruppo di risorse creare output](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

In questa sezione illustrerà impostazione della riga di comando se si usa Active Directory Federated Services (ADFS) come il servizio di gestione e uso della riga di comando in un computer Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Considerare attendibile il certificato di autorità di certificazione di Azure Stack radice

Se si usa il ASDK, è necessario considerare attendibile il certificato radice della CA nel computer remoto. Non è necessario eseguire questa operazione con i sistemi integrati.

Considerare attendibile il certificato radice CA Azure Stack aggiungendolo al certificato Python esistente.

1. Trovare il percorso di certificato nel computer. Il percorso può variare in base alla quale è stato installato Python. Dovrai disporre di pip e quella ottenibile [modulo installato](#install-python-on-linux). È possibile usare il comando Python seguito dal prompt di bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Prendere nota del percorso del certificato; ad esempio, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Il percorso specifico varia a seconda del sistema operativo e la versione di Python installata.

2. Eseguire il comando bash seguente con il percorso del certificato.

   - Per un computer Linux remoto:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Per una macchina Linux all'interno dell'ambiente Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Usare la procedura seguente per connettersi ad Azure Stack:

1. Registrare l'ambiente dello Stack di Azure eseguendo il `az cloud register` comando. In alcuni scenari, connettività diretta a internet in uscita viene instradata attraverso un proxy o firewall, che impone l'intercettazione SSL. In questi casi, il `az cloud register` comando può non riuscire con un errore, ad esempio "Impossibile ottenere gli endpoint dal cloud". Per risolvere questo errore, è possibile impostare le variabili di ambiente seguenti:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registrare l'ambiente. Usare i parametri seguenti durante l'esecuzione `az cloud register`.

    | Valore | Esempio | DESCRIZIONE |
    | --- | --- | --- |
    | Nome ambiente | AzureStackUser | Usare `AzureStackUser` per l'ambiente utente. Se si è l'operatore, specificare `AzureStackAdmin`. |
    | Endpoint di Resource Manager | https://management.local.azurestack.external | Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/` Il **ResourceManagerUrl** in sistemi integrati è: `https://management.<region>.<fqdn>/` Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se hai una domanda sull'endpoint di sistema integrato, contattare l'operatore cloud. |
    | Endpoint di archiviazione | local.azurestack.external | `local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Suffisso Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` è destinato il ASDK. Per un sistema integrato, è consigliabile usare un endpoint per il sistema.  |
    | Immagine VM alias doc endpoint | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento che contiene gli alias di immagine di macchina virtuale. Per altre informazioni, vedere [# # # configurare l'endpoint di macchina virtuale gli alias](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Impostare l'ambiente attivo. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aggiornare la configurazione dell'ambiente per utilizzare il profilo di versione API specifico dello Stack di Azure. Per aggiornare la configurazione, eseguire il comando seguente:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se si esegue una versione di Azure Stack prima della compilazione 1808, è necessario usare il profilo di versione API **2017-03-09-profile** anziché il profilo di versione API **2018-03-01-hybrid**. È necessario usare una versione recente della riga di comando di Azure.

5. Accedere all'ambiente Azure Stack usando il `az login` comando. È possibile accedere all'ambiente Azure Stack come un utente o come un [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md). 

6. Accedi: 

   *  Come un **utente** usando un web browser con un codice di dispositivo:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Esecuzione del comando fornisce un URL e un codice che è necessario usare per eseguire l'autenticazione.

   * Come un'entità servizio:
        
     Preparare il file con estensione PEM da utilizzare per l'accesso dell'entità servizio.

      * Nel computer client in cui l'entità è stata creata, esportare il certificato dell'entità servizio come un file pfx con la chiave privata disponibile all'indirizzo `cert:\CurrentUser\My`; il certificato nome ha lo stesso nome dell'entità.
  
      * Convertire il file pfx in pem (usare l'utilità di OpenSSL).

     Accedi per l'interfaccia della riga di comando:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Testare la connettività

Con tutte le impostazioni, usare della riga di comando per creare risorse in Azure Stack. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Usare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```azurecli
  az group create -n MyResourceGroup -l local
```

Se il gruppo di risorse viene creato correttamente, il comando precedente restituisce le proprietà seguenti della risorsa appena creata:

![Gruppo di risorse creare output](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problemi noti

Esistono alcuni problemi noti quando si utilizza CLI in Azure Stack:

 - La modalità interattiva dell'interfaccia della riga. ad esempio, il `az interactive` comando, non è ancora supportato in Azure Stack.
 - Per ottenere l'elenco delle immagini di macchina virtuale disponibili in Azure Stack, usare il `az vm image list --all` comando anziché la `az vm image list` comando. Specifica il `--all` opzione assicura che la risposta restituisce solo le immagini disponibili nell'ambiente Azure Stack.
 - Gli alias di immagine di macchina virtuale disponibili in Azure potrebbero non essere applicabili ad Azure Stack. Quando si usano immagini di macchine virtuali, è necessario usare l'intero parametro URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) anziché l'alias dell'immagine. Questo URN deve corrispondere le specifiche di immagine in base il `az vm images list` comando.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)
- [Abilitare la CLI di Azure per gli utenti di Azure Stack (operatore)](../azure-stack-cli-admin.md)
- [Gestire le autorizzazioni utente](azure-stack-manage-permissions.md) 
