---
title: Generare i certificati di infrastruttura a chiave pubblica Azure Stack per la distribuzione di sistemi integrati di Azure Stack | Microsoft Docs
description: Descrive il processo di distribuzione di certificati di infrastruttura a chiave pubblica di Azure Stack per i sistemi integrati di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: e0556eb5cc3d0f140067a4e3b4a9054a47b91417
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481486"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Firma richiedere la generazione di certificati di Azure Stack

È possibile utilizzare lo strumento di controllo di conformità di Azure Stack per creare richieste di firma del certificato (CSR) appropriati per la distribuzione di Azure Stack. Certificati devono essere richiesti, generati e convalidati un periodo di tempo sufficiente per eseguire il test prima della distribuzione. È possibile ottenere lo strumento [da PowerShell Gallery](https://aka.ms/AzsReadinessChecker).

È possibile usare lo strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) per richiedere i certificati seguenti:

- **Le richieste di certificato standard** in base ai [generare i certificati PKI per la distribuzione di Azure Stack](azure-stack-get-pki-certs.md).
- **Platform-as-a-Service**: È possibile richiedere i nomi di platform-as-a-service (PaaS) per i certificati come specificato nella [requisiti dei certificati di infrastruttura a chiave pubblica Azure Stack - certificati PaaS facoltativo](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Prerequisiti

Il sistema deve soddisfare i prerequisiti seguenti prima di generare il CSR(s) per i certificati PKI per la distribuzione di Azure Stack:

- Controllo di conformità di Microsoft Azure Stack
- Attributi del certificato:
  - Nome area
  - Nome di dominio completo esterno (FQDN)
  - Oggetto
- Windows 10 o Windows Server 2016

  > [!NOTE]  
  > Quando si riceve i certificati il failback da un'autorità di certificazione i passaggi descritti in [preparare Azure Stack PKI certificates](azure-stack-prepare-pki-certs.md) dovrà essere completato nello stesso sistema.

## <a name="generate-certificate-signing-requests"></a>Generare richieste di firma del certificato

Usare questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure Stack:

1. Installare AzsReadinessChecker da un prompt di PowerShell (5.1 o versione successiva) eseguendo il cmdlet seguente:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Dichiarare la **soggetto** come un dizionario ordinato. Ad esempio: 

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Se viene fornito un nome comune (CN) questo verrà sovrascritto dal nome DNS prima della richiesta di certificato.

3. Dichiarare una directory di output esiste già. Ad esempio: 

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Dichiarare il sistema di identità

    Azure Active Directory

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory Federation Services

    ```powershell
    $IdentitySystem = "ADFS"
    ```

5. Dichiarare **nome dell'area** e un **FQDN esterno** destinata la distribuzione di Azure Stack.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` costituisce la base su cui vengono creati tutti i nomi DNS esterni in Azure Stack, in questo esempio, il portale sarebbe `portal.east.azurestack.contoso.com`.  

6. Per generare le richieste per ogni nome DNS di firma del certificato:

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Per includere i servizi PaaS, specificare il commutatore ```-IncludePaaS```

7. In alternativa, per gli ambienti di sviluppo/Test, per generare una richiesta di certificato singolo con nomi alternativi dei soggetti più aggiungere **- RequestType SingleCSR** parametro e il valore (**non** consigliato per ambienti di produzione):

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Per includere i servizi PaaS, specificare il commutatore ```-IncludePaaS```

8. Esaminare l'output:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Inviare il **. REQ** file generato da un'autorità di certificazione (interno o pubblico).  La directory di output **New-AzsCertificateSigningRequest** contiene il CSR(s) necessari per l'invio a un'autorità di certificazione.  La directory contiene inoltre, per riferimento, una directory figlio che contiene i file INF usati durante la generazione della richiesta di certificato. Assicurarsi che l'autorità di certificazione genera certificati usando la richiesta generata che soddisfano le [requisiti di infrastruttura a chiave pubblica di Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Passaggi successivi

[Preparare i certificati di infrastruttura a chiave pubblica di Azure Stack](azure-stack-prepare-pki-certs.md)
