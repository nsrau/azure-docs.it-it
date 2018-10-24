---
title: Convalidare l'integrazione di ad FS per Azure Stack
description: Usare il controllo di conformità di Azure Stack per convalidare l'integrazione di ad FS per Azure Stack.
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
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 0aa13f2fced9122163d5278b5bb50cc1e11a0379
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947391"
---
# <a name="validate-adfs-integration-for-azure-stack"></a>Convalidare l'integrazione di ad FS per Azure Stack

Usare lo strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) per verificare che l'ambiente sia pronto per l'integrazione di ad FS con Azure Stack. È necessario convalidare l'integrazione di ad FS prima di iniziare l'integrazione di data center o prima di una distribuzione di Azure Stack.

Il controllo di conformità di convalida:

* Il *metadati della federazione* contiene gli elementi XML validi per la federazione.
* Il *certificato SSL ADFS* possono essere recuperati e catena di certificati può essere compilato. In corrispondenza del timestamp ADFS devono considerare attendibile la catena di certificati SSL. Il certificato deve essere firmato dallo stesso *autorità di certificazione* come i certificati di distribuzione di Azure Stack o da un partner di autorità radice attendibile. Per l'elenco completo dei partner dell'autorità radice attendibile, vedere: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca
* *Certificato di firma di ad FS* è attendibile e non sta raggiungendo una condizione di scadenza.

Per altre informazioni sull'integrazione di Azure Stack data center, vedere [integrazione di Data Center di Azure Stack - identità](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità

Scaricare la versione più recente dello strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) dal [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerequisiti

I seguenti prerequisiti sono necessari.

**Il computer in cui viene eseguito lo strumento:**

* Windows 10 o Windows Server 2016, con connettività del dominio.
* PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il comando PowerShell seguente e quindi esaminare i *principali* versione e *secondaria* versioni:  
   > `$PSVersionTable.PSVersion`
* La versione più recente del [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

**Ambiente attivo Directory Federation Services:**

È necessario almeno uno dei seguenti formati dei metadati:

* L'URL dei metadati di federazione di ad FS. Ad esempio: `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
* Il file XML dei metadati di federazione. Ad esempio, FederationMetadata

## <a name="validate-adfs-integration"></a>Convalidare l'integrazione di ad FS

1. In un computer che soddisfi i prerequisiti, aprire un prompt amministrativo di PowerShell e quindi eseguire il comando seguente per installare il AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida. Specificare il valore per **- CustomADFSFederationMetadataEndpointUri** come URI per i metadati della federazione:

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare che lo stato è OK per i requisiti di integrazione di ad FS. Convalida riuscita risulterà simile al seguente.

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

Negli ambienti di produzione, catene di certificati di attendibilità da una workstation di operatori di test non può essere considerato completamente indicano le condizioni di attendibilità di infrastruttura a chiave pubblica nell'infrastruttura di Azure Stack. Rete di indirizzo VIP pubblico dell'indicatore di Azure Stack richiede la connettività per l'elenco CRL per l'infrastruttura PKI.

## <a name="report-and-log-file"></a>File di log e report

Ogni convalida in fase di esecuzione, registra i risultati per **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Consente di visualizzare la posizione di questi file con i risultati della convalida in PowerShell.

I file di convalida consentono di condividere lo stato prima di distribuire Azure Stack o analizzare i problemi di convalida. Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successiva. Il report fornisce di conferma del team di distribuzione della configurazione di identità. Il file di log consentono al team di supporto o distribuzione di analizzare i problemi di convalida.

Per impostazione predefinita, entrambi i file vengono scritti `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Usare:

* **-OutputPath** *percorso* parametro alla fine di Esegui riga di comando per specificare un percorso di report diversa.
* **-CleanReport** parametro alla fine del comando di esecuzione per cancellare *AzsReadinessCheckerReport.json* delle informazioni di report precedente. Per altre informazioni, vedere [rapporto di convalida di Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida

Se un controllo di convalida non riesce, visualizzare i dettagli sull'errore nella finestra di PowerShell. Lo strumento registra inoltre informazioni per il *AzsReadinessChecker.log*.

Gli esempi seguenti forniscono informazioni aggiuntive sugli errori di convalida comuni.

### <a name="command-not-found"></a>Comando non trovato

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Causa** -Autoload PowerShell non è riuscito a caricare correttamente il modulo di controllo di conformità.

**Risoluzione** -modulo di controllo di conformità di importazione in modo esplicito. Copiare e incollare il codice seguente in PowerShell e l'aggiornamento di \<versione\> con il numero di versione per la versione attualmente installata.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Fasi successive

[Visualizzare il report di conformità](azure-stack-validation-report.md)  
[Considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)  
