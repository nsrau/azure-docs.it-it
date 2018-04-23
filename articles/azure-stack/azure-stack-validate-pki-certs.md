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
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
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
    Controlla la SAN contiene i nomi DNS pertinenti per ogni endpoint o un supporto con caratteri jolly non è presente.
- **Utilizzo della chiave**  
    Controlla se l'utilizzo della chiave contiene firma e crittografia chiave e utilizzo chiavi avanzato include autenticazione server e client.
- **Dimensione della chiave**  
    Controlla se la dimensione della chiave è 2048 o superiori.
- **Ordine di concatenamento**  
    Controlla l'ordine dei certificati di convalida che l'ordine sia corretto.
- **Altri certificati**  
    Verificare che altri certificati non sono state incluse in PFX diverso dal certificato foglia pertinenti e la catena.

> [!IMPORTANT]  
> Il certificato PKI è un file PFX e password devono essere considerate come informazioni riservate.

## <a name="prerequisites"></a>Prerequisiti

Il sistema deve soddisfare i prerequisiti seguenti prima di convalidare i certificati PKI per una distribuzione di Azure Stack:

- Controllo di conformità di Microsoft Azure Stack
- I certificati SSL esportato seguendo il [istruzioni relative alla preparazione](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 o Windows Server 2016

## <a name="perform-certificate-validation"></a>Eseguire la convalida dei certificati

Utilizzare questi passaggi per preparare e per convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack:

1. Installare AzsReadinessChecker da un prompt di PowerShell (5.1 o versione successiva), eseguendo il cmdlet seguente:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. Creare la struttura di directory di certificato. Nell'esempio seguente, è possibile modificare `<c:\certificates>` in un nuovo percorso di directory di propria scelta.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - Posizione dei certificati nelle directory appropriate creato nel passaggio precedente. Ad esempio:   
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - E così via... 

3. Nella finestra di PowerShell eseguire:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. Esaminare l'output per verificare che tutti i certificati superato i test. Ad esempio: 

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
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
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

## <a name="using-validated-certificates"></a>Utilizzo di certificati convalidati

Dopo che i certificati sono stati convalidati dal AzsReadinessChecker, si è pronti per usarli nella distribuzione di Azure Stack o per la rotazione dei segreta dello Stack di Azure. 

 - Per la distribuzione, trasferire in modo sicuro i certificati al tecnico del servizio di distribuzione in modo che sia possibile copiarli nel computer host come specificato nella distribuzione di [documentazione requisiti di infrastruttura a chiave pubblica di Azure Stack](azure-stack-pki-certs.md).
 - Per la rotazione dei segreta, è possibile utilizzare i certificati per l'aggiornamento precedente dei certificati per gli endpoint pubblici infrastruttura dell'ambiente dello Stack di Azure seguendo la [documentazione di Azure Stack segreto rotazione](azure-stack-rotate-secrets.md).

## <a name="next-steps"></a>Passaggi successivi

[Integrazione delle identità datacenter](azure-stack-integrate-identity.md)
