---
title: Convalidare l'integrazione di Graph di Azure per Azure Stack
description: Usare il controllo di conformità di Azure Stack per convalidare l'integrazione di graph per Azure Stack.
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
ms.openlocfilehash: 43f30989fa09e711fc71941e7722dcd195212472
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416232"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Convalidare l'integrazione di graph per Azure Stack

Usare lo strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) per verificare che l'ambiente sia pronto per l'integrazione di graph con Azure Stack. Convalidare l'integrazione di graph prima di iniziare l'integrazione di Data Center o prima di una distribuzione di Azure Stack.

Il controllo di conformità di convalida:

* Le credenziali per l'account del servizio creato per l'integrazione graph hanno diritti adeguati a interrogare Active Directory.
* Il *catalogo globale* può essere risolto e contattabile.
* Il KDC possa essere risolto e contattabile.
* È presente la connettività di rete necessarie.

Per altre informazioni sull'integrazione di Data Center di Azure Stack, vedere [integrazione di Data Center di Azure Stack - identità](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità

Scaricare la versione più recente dello strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) dal [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Prerequisiti

I seguenti prerequisiti sono necessari.

**Il computer in cui viene eseguito lo strumento:**

* Windows 10 o Windows Server 2016, con connettività del dominio.
* PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il comando PowerShell seguente e quindi esaminare i *principali* versione e *secondaria* versioni:  
   > `$PSVersionTable.PSVersion`
* Modulo Active Directory PowerShell.
* Versione più recente del [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

**Ambiente Active Directory:**

* Identificare il nome utente e password per un account per il servizio graph nell'istanza di Active Directory esistente.
* Identificare radice della foresta di Active Directory, nome di dominio completo.

## <a name="validate-the-graph-service"></a>Convalidare il servizio graph

1. In un computer che soddisfi i prerequisiti, aprire un prompt amministrativo di PowerShell e quindi eseguire il comando seguente per installare il AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Al prompt di PowerShell, eseguire il comando seguente per impostare il *$graphCredential* variabile all'account del grafico. Sostituire `contoso\graphservice` con il tuo account usando il `domain\username` formato.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida per il servizio graph. Specificare il valore per **- ForestFQDN** come il nome di dominio completo per la radice della foresta.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare che lo stato è OK per i requisiti di integrazione di grafico. Convalida riuscita è simile all'esempio seguente:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

Negli ambienti di produzione, testare la connettività di rete da workstation dell'operatore non è completamente indicativo della connettività disponibili in Azure Stack. Rete di indirizzi VIP pubblici del timbro Azure Stack sarà necessario la connettività per il traffico LDAP eseguire l'integrazione delle identità.

## <a name="report-and-log-file"></a>File di log e report

Ogni convalida in fase di esecuzione, registra i risultati per **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Il percorso di questi file viene visualizzato con i risultati della convalida in PowerShell.

I file di convalida consentono di condividere lo stato prima di distribuire Azure Stack o analizzare i problemi di convalida. Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successiva. Il report offre di conferma del team di distribuzione della configurazione di identità. Il file di log consentono al team di supporto o distribuzione di analizzare i problemi di convalida.

Per impostazione predefinita, entrambi i file vengono scritti `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Usare:

* **-OutputPath**: il *percorso* parametro alla fine del comando di esecuzione per specificare un percorso di report diversa.
* **-CleanReport**: il parametro alla fine del comando di esecuzione per cancellare *AzsReadinessCheckerReport.json* delle informazioni di report precedente. Per altre informazioni, vedere [rapporto di convalida di Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida

Se un controllo di convalida non riesce, dettagli sull'errore vengono visualizzati nella finestra di PowerShell. Lo strumento registra inoltre informazioni per *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare il report di conformità](azure-stack-validation-report.md)  
[Considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)  
