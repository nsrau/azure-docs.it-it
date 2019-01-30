---
title: Convalidare l'integrazione di AD FS per Azure Stack
description: Usare il controllo di conformità di Azure Stack per convalidare l'integrazione di AD FS per Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 2200b9a48d7f83d6785c8dbb4a7b02be52fca75a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241067"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>Convalidare l'integrazione di AD FS per Azure Stack

Usare lo strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) per verificare che l'ambiente sia pronto per l'integrazione di Active Directory Federation Services (ADFS) con Azure Stack. Convalidare l'integrazione di AD FS prima di iniziare l'integrazione di Data Center o prima di una distribuzione di Azure Stack.

Il controllo di conformità di convalida:

* Il *metadati della federazione* contiene gli elementi XML validi per la federazione.
* Il *certificato SSL di AD FS* può essere recuperato e una catena di trust può essere compilato. In corrispondenza del timestamp ADFS devono considerare attendibile la catena di certificati SSL. Il certificato deve essere firmato dallo stesso *autorità di certificazione* usato per i certificati di distribuzione di Azure Stack o da un partner di autorità radice attendibile. Per l'elenco completo dei partner dell'autorità radice attendibile, vedere [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* Il *certificato di firma di AD FS* è attendibile e non sta raggiungendo una condizione di scadenza.

Per altre informazioni sull'integrazione di Data Center di Azure Stack, vedere [integrazione di Data Center di Azure Stack - identità](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità

Scaricare la versione più recente dello strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) dal [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerequisiti

I seguenti prerequisiti sono necessari.

**Il computer in cui viene eseguito lo strumento:**

* Windows 10 o Windows Server 2016, con connettività del dominio.
* PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il comando PowerShell seguente e quindi esaminare i *principali* versione e *secondaria* versioni:  
   > `$PSVersionTable.PSVersion`
* Versione più recente del [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

**Ambiente attivo Directory Federation Services:**

È necessario almeno uno dei seguenti formati dei metadati:

* L'URL per i metadati della federazione AD FS. Un esempio è `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Il file XML dei metadati di federazione. Un esempio è FederationMetadata.

## <a name="validate-ad-fs-integration"></a>Convalidare l'integrazione di AD FS

1. In un computer che soddisfi i prerequisiti, aprire un prompt amministrativo di PowerShell e quindi eseguire il comando seguente per installare AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida. Specificare il valore per **- CustomADFSFederationMetadataEndpointUri** come URI per i metadati della federazione.

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare che lo stato è OK per i requisiti di integrazione di AD FS. Convalida riuscita è simile all'esempio seguente:

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

Negli ambienti di produzione, test di catene di certificati di attendibilità da workstation dell'operatore non è completamente indicano le condizioni di attendibilità di infrastruttura a chiave pubblica nell'infrastruttura di Azure Stack. Rete VIP pubblico dell'indicatore di Azure Stack richiede la connettività per l'elenco CRL per l'infrastruttura PKI.

## <a name="report-and-log-file"></a>File di log e report

Ogni convalida in fase di esecuzione, registra i risultati per **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Il percorso di questi file viene visualizzato con i risultati della convalida in PowerShell.

I file di convalida consentono di condividere lo stato prima di distribuire Azure Stack o analizzare i problemi di convalida. Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successiva. Il report offre di conferma del team di distribuzione della configurazione di identità. Il file di log consentono al team di supporto o distribuzione di analizzare i problemi di convalida.

Per impostazione predefinita, entrambi i file vengono scritti `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Usare:

* **-OutputPath**: Il *percorso* parametro alla fine del comando di esecuzione per specificare un percorso di report diversa.
* **-CleanReport**: Il parametro alla fine del comando di esecuzione per cancellare AzsReadinessCheckerReport.json delle informazioni di report precedente. Per altre informazioni, vedere [rapporto di convalida di Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida

Se un controllo di convalida non riesce, dettagli sull'errore vengono visualizzati nella finestra di PowerShell. Lo strumento registra inoltre informazioni per *AzsReadinessChecker.log*.

Gli esempi seguenti forniscono informazioni aggiuntive sugli errori di convalida comuni.

### <a name="command-not-found"></a>Comando non trovato

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Causa**: Autoload PowerShell non è stato possibile caricare correttamente il modulo di controllo di conformità.

**Risoluzione**: Importare il modulo di controllo di conformità in modo esplicito. Copiare e incollare il codice seguente in PowerShell e l'aggiornamento \<versione\> con il numero per la versione attualmente installata.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare il report di conformità](azure-stack-validation-report.md)  
[Considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)  
