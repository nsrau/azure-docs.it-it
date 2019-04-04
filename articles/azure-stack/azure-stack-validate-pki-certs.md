---
title: Convalidare i certificati di infrastruttura a chiave pubblica Azure Stack per la distribuzione di sistemi integrati di Azure Stack | Microsoft Docs
description: Viene descritto come convalidare i certificati di infrastruttura a chiave pubblica di Azure Stack per i sistemi integrati di Azure Stack. Viene illustrato l'utilizzo lo strumento di verifica certificato di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 06cb29d6d04fb314f9eefa63d7a2b628a2af846b
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481121"
---
# <a name="validate-azure-stack-pki-certificates"></a>Convalidare i certificati di infrastruttura a chiave pubblica di Azure Stack

Lo strumento di controllo di conformità di Azure Stack descritto in questo articolo è disponibile [da PowerShell Gallery](https://aka.ms/AzsReadinessChecker). È possibile usare lo strumento per verificare che il [i certificati PKI generati](azure-stack-get-pki-certs.md) adatti per la pre-distribuzione. Convalidare i certificati se si lascia un tempo sufficiente per testare ed eseguire nuovamente i certificati se necessario.

Lo strumento controllo Readiness esegue le convalide di certificato seguenti:

- **Leggere file PFX**  
    Verifica la presenza di file PFX valido, la password corretta, e se le informazioni pubbliche non sono protetto da password. 
- **Algoritmo di firma**  
    Verifica che l'algoritmo di firma non è SHA1.
- **Chiave privata**  
    Verifica che la chiave privata è presente e viene esportata con l'attributo del computer locale. 
- **Catena di certificazione**  
    Catena di certificati di verifica è intatta, tra cui un controllo per i certificati autofirmati.
- **Nomi DNS**  
    Controlla la SAN contiene i nomi DNS relativi per ogni endpoint o se un oggetto di supporto è presente il carattere jolly.
- **Utilizzo delle chiave**  
    Controlla se l'utilizzo della chiave contiene una firma digitale e crittografia chiave e dell'utilizzo chiavi avanzato contiene autenticazione server e client.
- **Dimensione della chiave**  
    Controlla se la dimensione della chiave è 2048 o superiori.
- **Ordine di catena**  
    Controlla l'ordine dei certificati di convalida che l'ordine sia corretto.
- **Altri certificati**  
    Verificare che nessun altro certificato è state incluse in PFX diverso dal certificato foglia pertinenti e la catena.
- **Nessun profilo**  
    Verifica che un nuovo utente può caricare i dati PFX senza un profilo utente caricato, che rispecchia il comportamento degli account gMSA durante la manutenzione di certificato.

> [!IMPORTANT]  
> Il certificato PKI è un file PFX e password devono essere considerate informazioni riservate.

## <a name="prerequisites"></a>Prerequisiti

Il sistema deve soddisfare i prerequisiti seguenti prima di convalidare i certificati PKI per la distribuzione di Azure Stack:

- Controllo di conformità di Microsoft Azure Stack
- I certificati SSL esportato seguendo il [istruzioni di elaborazione](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 o Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Eseguire la convalida di certificati di servizi principale

Usare questi passaggi per preparare e per convalidare i certificati di infrastruttura a chiave pubblica di Azure Stack per la distribuzione e la rotazione segreta:

1. Installare **AzsReadinessChecker** da un prompt di PowerShell (5.1 o versione successiva) eseguendo il cmdlet seguente:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Creare la struttura di directory del certificato. Nell'esempio seguente, è possibile modificare `<c:\certificates>` in un nuovo percorso di directory di propria scelta.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'api_appservice', 'ARM Admin', 'ARM Public', 'ftp_appservice', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal', 'sso_appservice', 'wildcard_dbadapter', 'wildcard_sso_appservice'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS e Graph sono necessarie se si usa AD FS come il sistema di identità. Ad esempio: 
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'api_appservice', 'ARM Admin', 'ARM Public', 'ftp_appservice', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal', 'sso_appservice', 'wildcard_dbadapter', 'wildcard_sso_appservice'
    > ```
    
     - Inserire i certificati nelle directory appropriate creata nel passaggio precedente. Ad esempio:   
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. Nella finestra di PowerShell, modificare i valori delle **RegionName** e **FQDN** appropriati all'ambiente Azure Stack ed eseguire il comando seguente:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Controllare l'output e tutti i certificati superano tutti i test. Ad esempio: 

```powershell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
```

### <a name="known-issues"></a>Problemi noti

**Sintomo**: I test vengono ignorati

**Causa**: AzsReadinessChecker Ignora determinati test se non viene soddisfatta delle dipendenze:

 - Altri certificati vengono ignorati se la catena di certificati ha esito negativo.

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Risoluzione**: Segui le istruzioni dello strumento nella sezione dei dettagli in ogni set di test per ogni certificato.

## <a name="perform-platform-as-a-service-certificate-validation"></a>La piattaforma come la convalida di un certificato di servizio

Usare questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure Stack per la piattaforma come certificati per un servizio (PaaS), se vengono pianificate le distribuzioni di SQL o MySQL o servizi App.

1.  Installare **AzsReadinessChecker** da un prompt di PowerShell (5.1 o versione successiva) eseguendo il cmdlet seguente:

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Creare una tabella hash annidate contenenti tracciati e la password per ogni certificato PaaS la necessità di convalida. Nella finestra di PowerShell eseguire:

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Modificare i valori delle **RegionName** e **FQDN** in base all'ambiente Azure Stack per avviare la convalida. Quindi eseguire:

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Verificare che l'output e che tutti i certificati passano tutti i test.

    ```powershell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>Certificati

| Directory | Certificate |
| ---    | ----        |
| acsBlob | wildcard_blob_\<region>_\<externalFQDN> |
| ACSQueue  |  wildcard_queue_\<region>_\<externalFQDN> |
| ACSTable  |  wildcard_table_\<regione > _\<externalFQDN > |
| Host dell'estensione di amministrazione  |  wildcard_adminhosting_\<regione > _\<externalFQDN > |
| Portale di amministrazione  |  adminportal_\<regione > _\<externalFQDN > |
| Amministratore ARM  |  adminmanagement_\<regione > _\<externalFQDN > |
| Pubblico ARM  |  management_\<regione > _\<externalFQDN > |
| Insieme di credenziali delle chiavi  |  wildcard_vault_\<regione > _\<externalFQDN > |
| KeyVaultInternal  |  wildcard_adminvault_\<regione > _\<externalFQDN > |
| Host dell'estensione pubblica  |  wildcard_hosting_\<regione > _\<externalFQDN > |
| Portale pubblico  |  portal_\<regione > _\<externalFQDN > |

## <a name="using-validated-certificates"></a>Utilizzo di certificati convalidati

Dopo aver convalidati i certificati per il AzsReadinessChecker, si è pronti a usarle nella distribuzione di Azure Stack o per la rotazione segreta di Azure Stack. 

 - Per la distribuzione, trasferire in modo sicuro i certificati per il tecnico di distribuzione in modo che è possibile copiare i file nel computer host come specificato nella distribuzione di [documentazione sui requisiti di infrastruttura a chiave pubblica di Azure Stack](azure-stack-pki-certs.md).
 - Per la rotazione segreta, è possibile usare i certificati per l'aggiornamento dei certificati precedenti per gli endpoint pubblici di infrastruttura dell'ambiente Azure Stack seguendo la [documentazione di Azure Stack segreto rotazione](azure-stack-rotate-secrets.md).
 - Per i servizi PaaS, è possibile usare i certificati per l'installazione di SQL, MySQL e i provider di risorse di servizi di App in Azure Stack seguendo la [panoramica dell'offerta di servizi nella documentazione di Azure Stack](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Passaggi successivi

[Integrazione delle identità datacenter](azure-stack-integrate-identity.md)
