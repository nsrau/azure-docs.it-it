---
title: Convalidare la registrazione di Azure per Azure Stack | Microsoft Docs
description: Usare il controllo di conformità di Azure Stack per convalidare la registrazione di Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 51753a5324bbbcbf4e951628a42dd3bf425354af
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957583"
---
# <a name="validate-azure-registration"></a>Convalidare la registrazione di Azure 
Usare lo strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) per convalidare che la sottoscrizione di Azure è pronta per l'uso con Azure Stack. Convalidare la registrazione prima di iniziare una distribuzione di Azure Stack. Il controllo di conformità di convalida:
- La sottoscrizione di Azure che è usare è un tipo supportato. Le sottoscrizioni devono essere un Provider del servizio Cloud (CSP) o Enterprise Agreement (EA). 
- L'account usato per registrare la sottoscrizione di Azure può accedere ad Azure ed è un proprietario della sottoscrizione. 

Per altre informazioni sulla registrazione di Azure Stack, vedere [registrare Azure Stack con Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità
La versione più recente dello strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) è disponibile nel download [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerequisiti
I seguenti prerequisiti sono necessari.

**Il computer in cui viene eseguito lo strumento:**
 - Windows 10 o Windows Server 2016, con connettività internet.
 - PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il comando PowerShell seguente e quindi esaminare i *principali* versione e *secondaria* versioni:  

    >`$PSVersionTable.PSVersion` 
 - Configurare [PowerShell per Azure Stack](azure-stack-powershell-install.md). 
 - Scaricare la versione più recente di [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.  

**Ambiente di Azure Active Directory:**
 - Identificare il nome utente e la password per un account che è un proprietario della sottoscrizione di Azure che verranno usati con Azure Stack.  
 - Identificare l'ID sottoscrizione per la sottoscrizione di Azure che si userà. 
 - Identificare il AzureEnvironment si utilizzerà: *AzureCloud*, *AzureGermanCloud*, o *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Convalidare la registrazione di Azure
1. In un computer che soddisfi i prerequisiti, aprire un prompt amministrativo di PowerShell e quindi eseguire il comando seguente per installare il AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Al prompt di PowerShell, eseguire il comando seguente per impostare *$registrationCredential* come l'account che è proprietario della sottoscrizione.   Sostituire *subscriptionowner@contoso.onmicrosoft.com* con l'account e il tenant. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. Al prompt di PowerShell, eseguire il comando seguente per impostare *$subscriptionID* si utilizzerà la sottoscrizione di Azure. Sostituire *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* con il proprio ID sottoscrizione.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida della sottoscrizione 
   - Specificare il valore per AzureEnvironment come *AzureCloud*, *AzureGermanCloud*, o *AzureChinaCloud*.  
   - Fornire all'amministratore di Azure Active Directory e il nome del Tenant di Azure Active Directory. 

   > `Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare che lo stato è OK per accesso e i requisiti di registrazione. Convalida riuscita viene visualizzato come segue:  
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````


## <a name="report-and-log-file"></a>File di log e report
Ogni convalida in fase di esecuzione, registra i risultati per **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Consente di visualizzare la posizione di questi file con i risultati della convalida in PowerShell. 

Questi file consentono di condividere lo stato di convalida prima di distribuire Azure Stack o analizzare i problemi di convalida. Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successiva. Il report fornisce di conferma del team di distribuzione della configurazione di identità. Il file di log consentono al team di supporto o distribuzione di analizzare i problemi di convalida. 

Per impostazione predefinita, entrambi i file vengono scritti *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Usare la **- OutputPath** ***&lt;path&gt;*** parametro alla fine di Esegui riga di comando per specificare un percorso di report diversa.   
 - Usare la **- CleanReport** parametro alla fine del comando di esecuzione per cancellare informazioni da *AzsReadinessCheckerReport.json*.  sulle esecuzioni precedenti dello strumento. Per altre informazioni, [rapporto di convalida di Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida
Se un controllo di convalida non riesce, visualizzare i dettagli sull'errore nella finestra di PowerShell. Lo strumento registra inoltre informazioni per il AzsReadinessChecker.log.

Gli esempi seguenti forniscono informazioni aggiuntive sugli errori di convalida comuni.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Utente deve essere un proprietario della sottoscrizione   
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Causa** -l'account non è un amministratore della sottoscrizione di Azure.   

**Risoluzione** -usare un account di amministratore della sottoscrizione di Azure che verrà addebitata per l'utilizzo della distribuzione di Azure Stack.


### <a name="expired-or-temporary-password"></a>Password scaduta o temporanee 
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: 48fe06f5-a5b4-4961-ad45-a86964689900
Correlation ID: 3dd1c9b2-72fb-46a0-819d-058f7562cb1f
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Causa** -l'account non è possibile accedere perché la password è scaduta o è temporanea.     

**Risoluzione** : In PowerShell eseguire e seguire le istruzioni per reimpostare la password. 
  > `Login-AzureRMAccount` 

In alternativa, eseguire l'accesso al https://portal.azure.com come l'account e l'utente dovrà cambiare la password.


### <a name="unknown-user-type"></a>Tipo di utente sconosciuto  
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d using account admin@contoso.onmicrosoft.com failed with unknown_user_type: Unknown Us
er Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Causa** -l'account non è possibile accedere all'ambiente di Azure Active Directory specificato. In questo esempio *AzureChinaCloud* è specificato come il *AzureEnvironment*.  

**Risoluzione** -verificare che l'account sia valido per l'ambiente di Azure specificato. In PowerShell eseguire il comando seguente per verificare che l'account sia valido per un ambiente specifico.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Fasi successive
[Convalidare l'identità di Azure](azure-stack-validate-identity.md)
[consente di visualizzare il report di conformità](azure-stack-validation-report.md)
[considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)

