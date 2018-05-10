---
title: Generare i certificati di infrastruttura a chiave pubblica Azure dello Stack per la distribuzione di sistemi Azure Stack integrato | Documenti Microsoft
description: Viene descritto il processo di distribuzione certificato PKI dello Stack di Azure per i sistemi Azure Stack integrato.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 17737c2b272f2a123df3d58c62c471b3da5bebe1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Certificati di Stack Azure generazione di una richiesta di firma

Lo strumento di controllo di conformità Stack Azure descritto in questo articolo è disponibile [da PowerShell Gallery](https://aka.ms/AzsReadinessChecker). Lo strumento crea adatto per una distribuzione di Azure Stack firma le richieste di certificato (CSR). Certificati devono essere richiesti, generati e convalidati con un tempo sufficiente per testare prima della distribuzione.

Lo strumento di controllo di conformità dello Stack di Azure (AzsReadinessChecker) vengono eseguite le seguenti richieste di certificati:

 - **Richieste di certificato standard**  
    Richiesta in base alla [generare i certificati PKI per la distribuzione di Azure Stack](azure-stack-get-pki-certs.md).
 - **Tipo di richiesta**  
    Specifica se la richiesta di firma certificato sarà una singola richiesta, o più richieste.
 - **Platform-as-a-Service**  
    Facoltativamente, richiedere platform-as-a-service (PaaS) nomi ai certificati come specificato in [requisiti dei certificati di infrastruttura a chiave pubblica Azure Stack - certificati PaaS facoltativi](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Prerequisiti

Il sistema deve soddisfare i prerequisiti seguenti prima di generare CSR(s) per i certificati PKI per una distribuzione di Azure Stack:

 - Controllo di conformità di Microsoft Azure Stack
 - Attributi di certificato:
    - Nome dell'area
    - Nome di dominio completo (FQDN) esterno
    - Oggetto
 - Windows 10 o Windows Server 2016
 
  > [!NOTE]
  > Quando si riceve i certificati di backup dall'autorità di certificazione i passaggi descritti in [certificati preparare Azure Stack PKI](azure-stack-prepare-pki-certs.md) dovrà essere completato nello stesso sistema.

## <a name="generate-certificate-signing-requests"></a>Generare richieste di firma del certificato

Utilizzare questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack: 

1.  Installare AzsReadinessChecker da un prompt di PowerShell (5.1 o versione successiva), eseguendo il cmdlet seguente:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Dichiarare la **soggetto** come un dizionario ordinato. Ad esempio:  

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Se viene fornito un nome comune (CN) questo verrà sovrascritto dal nome DNS primo della richiesta di certificato.

3.  Dichiarare una directory di output che esiste già:

    ````PowerShell  
    $outputDirectory = "$ENV:USERNAME\Documents\AzureStackCSR" 
    ````
4.  Dichiarare identificare sistema

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Active Directory Federation Services

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Dichiarare **nome dell'area** e una **FQDN esterno** previsto per la distribuzione dello Stack di Azure.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` costituisce la base in cui vengono creati tutti i nomi DNS esterni nello Stack di Azure, in questo esempio, il portale sarebbe `portal.east.azurestack.contoso.com`.

6. Per generare una richiesta di certificato singolo con più nomi di soggetto alternativo:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Per includere servizi PaaS specificare il commutatore ```-IncludePaaS```

7. Per generare singole richieste per ogni nome DNS di firma del certificato:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Per includere servizi PaaS specificare il commutatore ```-IncludePaaS```

8. Esaminare l'output:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Inviare il **. REQ** file generato per l'autorità di certificazione (interno o pubblico).  La directory di output **inizio AzsReadinessChecker** contiene CSR(s) necessarie per inviare a un'autorità di certificazione.  Include inoltre una directory figlio che contiene i file INF utilizzati durante la generazione di richiesta di certificato, come riferimento. Assicurarsi che la CA genera certificati utilizzando la richiesta generata che soddisfano il [requisiti di infrastruttura a chiave pubblica di Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Passaggi successivi

[Preparare i certificati di infrastruttura a chiave pubblica di Azure Stack](azure-stack-prepare-pki-certs.md)
