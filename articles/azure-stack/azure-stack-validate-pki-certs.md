---
title: La convalida dei certificati di infrastruttura a chiave pubblica Azure dello Stack per la distribuzione di sistemi Azure Stack integrato | Documenti Microsoft
description: Viene descritto come convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack per i sistemi Azure Stack integrato. Viene illustrato l'utilizzo di Azure Stack certificato utilità.
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
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605611"
---
# <a name="validate-azure-stack-pki-certificates"></a>La convalida dei certificati di infrastruttura a chiave pubblica di Azure Stack

Lo strumento di controllo di conformità Stack Azure descritto in questo articolo è disponibile [da PowerShell Gallery](https://aka.ms/AzsReadinessChecker). È possibile utilizzare lo strumento per verificare che il [generati i certificati PKI](azure-stack-get-pki-certs.md) sono adatti per pre-distribuzione. Se si lascia un tempo sufficiente per testare ed eseguire nuovamente i certificati se necessario, è necessario convalidare i certificati.

Lo strumento di controllo di conformità vengono eseguite le convalide certificato seguenti:

- **Leggere PFX**  
    Verifica la presenza di file PFX valido, la password corretta e viene generato un avviso se informazioni pubblico non protetto da password. 
- **Algoritmo di firma**  
    Verifica che l'algoritmo di firma non è SHA1.
- **Chiave privata**  
    Verifica che la chiave privata è presente e che viene esportata con l'attributo di computer locale. 
- **Catena di certificazione**  
    Catena di certificati controlli sia intatta incluso un controllo per i certificati autofirmati.
- **Nomi DNS**  
    Controlla la SAN contiene nomi DNS pertinenti per ogni endpoint, o un tipo di supporto con caratteri jolly non è presente.
- **Utilizzo della chiave**  
    Controlla se l'utilizzo della chiave contiene firma e crittografia chiave e utilizzo chiavi avanzato include autenticazione server e client.
- **Dimensione della chiave**  
    Controlla se la dimensione della chiave è 2048 o superiori.
- **Ordine di concatenamento**  
    Controlla l'ordine dei certificati di convalida che l'ordine sia corretto.
- **Altri certificati**  
    Verificare che altri certificati non sono state incluse in PFX diverso dal certificato foglia pertinenti e la catena.
- **Nessun profilo**  
    Verifica che un nuovo utente consente di caricare i dati PFX senza un profilo utente caricato, simulando il comportamento di account gMSA durante la manutenzione di certificato.

> [!IMPORTANT]  
> Il certificato PKI è un file PFX e password devono essere considerate come informazioni riservate.

## <a name="prerequisites"></a>Prerequisiti

Il sistema deve soddisfare i prerequisiti seguenti prima di convalidare i certificati PKI per una distribuzione di Azure Stack:

- Controllo di conformità di Microsoft Azure Stack
- I certificati SSL esportato seguendo il [istruzioni relative alla preparazione](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 o Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Eseguire la convalida di certificati di servizi dei componenti di base

Utilizzare questa procedura per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack per la distribuzione e la rotazione del segreto:

1. Installare **AzsReadinessChecker** da un prompt di PowerShell (5.1 o versione successiva), eseguendo il cmdlet seguente:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. Creare la struttura di directory di certificato. Nell'esempio seguente, è possibile modificare `<c:\certificates>` in un nuovo percorso di directory di propria scelta.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > ADFS e grafico sono necessarie se si utilizza ADFS come il sistema di identità.
    
     - Posizione dei certificati nelle directory appropriate creato nel passaggio precedente. Ad esempio:   
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. Nella finestra di PowerShell, modificare i valori delle **RegionName** e **FQDN** appropriate per l'ambiente dello Stack di Azure ed eseguire la seguente:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. Controllare l'output e tutti i certificati superano tutti i test. Ad esempio: 

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Problemi noti

**Sintomo**: test vengono ignorati

**Causa**: AzsReadinessChecker Ignora determinati test se non viene soddisfatta una dipendenza:

 - Altri certificati vengono ignorate se si verifica un errore di catena di certificati.

    ````PowerShell  
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

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Risoluzione**: seguire indicazioni dello strumento nella sezione dei dettagli in ogni set di test per ogni certificato.

## <a name="perform-platform-as-a-service-certificate-validation"></a>La piattaforma come la convalida di un certificato di servizio

Eseguire questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack per la piattaforma come certificati di servizio (PaaS), se sono pianificate la distribuzioni SQL o MySQL o servizi App.

1.  Installare **AzsReadinessChecker** da un prompt di PowerShell (5.1 o versione successiva), eseguendo il cmdlet seguente:

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  Creare un oggetto hashtable nidificato che contiene i percorsi e la password per ogni certificato PaaS la necessità di convalida. Nella finestra di PowerShell eseguire:

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Modificare i valori delle **RegionName** e **FQDN** in base all'ambiente dello Stack di Azure per avviare la convalida. Quindi eseguire:

    ```PowerShell
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Verificare che l'output e che tutti i certificati passano tutti i test.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Utilizzo di certificati convalidati

Dopo che i certificati sono stati convalidati dal AzsReadinessChecker, si è pronti per usarli nella distribuzione di Azure Stack o per la rotazione dei segreta dello Stack di Azure. 

 - Per la distribuzione, trasferire in modo sicuro i certificati al tecnico del servizio di distribuzione in modo che sia possibile copiarli nel computer host come specificato nella distribuzione di [documentazione requisiti di infrastruttura a chiave pubblica di Azure Stack](azure-stack-pki-certs.md).
 - Per la rotazione dei segreta, è possibile utilizzare i certificati per l'aggiornamento precedente dei certificati per gli endpoint pubblici infrastruttura dell'ambiente dello Stack di Azure seguendo la [documentazione di Azure Stack segreto rotazione](azure-stack-rotate-secrets.md).
 - Per i servizi PaaS, è possibile utilizzare i certificati per l'installazione di SQL, MySQL e i provider di risorse di servizi App nello Stack di Azure seguendo la [panoramica dell'offerta di servizi nella documentazione di Azure Stack](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Passaggi successivi

[Integrazione delle identità datacenter](azure-stack-integrate-identity.md)