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
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: a777fc1d9052eb58bbebd319fe6cc7f42a09cb9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403613"
---
# <a name="validate-azure-registration"></a>Convalidare la registrazione di Azure

Usare lo strumento di controllo di conformità di Azure Stack (**AzsReadinessChecker**) per convalidare che la sottoscrizione di Azure è pronta per l'uso con Azure Stack. Convalidare la registrazione prima di iniziare una distribuzione di Azure Stack. Il controllo di conformità di convalida che:

- La sottoscrizione di Azure che è usare è un tipo supportato. Le sottoscrizioni devono essere un Provider del servizio Cloud (CSP) o Enterprise Agreement (EA).
- L'account usato per registrare la sottoscrizione di Azure può accedere ad Azure ed è un proprietario della sottoscrizione.

Per altre informazioni sulla registrazione di Azure Stack, vedere [registrare Azure Stack con Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità

Scaricare la versione più recente di **AzsReadinessChecker** dalle [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerequisiti

Sono necessari i prerequisiti seguenti:

**Il computer in cui viene eseguito lo strumento:**

- Windows 10 o Windows Server 2016, con connettività internet.
- PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il cmdlet di PowerShell seguente e quindi esaminare i **principali** e **secondaria** versioni:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell per Azure Stack configurato](azure-stack-powershell-install.md).
- La versione più recente di [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker).  

**Ambiente di Azure Active Directory:**

- Identificare il nome utente e la password per un account che è un proprietario della sottoscrizione di Azure che verranno usati con Azure Stack.  
- Identificare l'ID sottoscrizione per la sottoscrizione di Azure che si userà.
- Identificare le **AzureEnvironment** si userà. Sono valori supportati per il parametro del nome ambiente **AzureCloud**, **AzureChinaCloud**, o **AzureUSGovernment**, a seconda di quale sottoscrizione di Azure si è utilizzo.

## <a name="steps-to-validate-azure-registration"></a>Passaggi per convalidare la registrazione di Azure

1. In un computer che soddisfi i prerequisiti, aprire un prompt di PowerShell con privilegi elevato e quindi eseguire il comando seguente per installare **AzsReadinessChecker**:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Al prompt di PowerShell, eseguire il comando seguente per impostare `$registrationCredential` come l'account che è proprietario della sottoscrizione. Sostituire `subscriptionowner@contoso.onmicrosoft.com` con il nome account e tenant:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Come CSP, quando si usa una sottoscrizione di servizi condivisi o IUR, è necessario fornire le credenziali di un utente da tale AAD corrispondente. In genere sarà simile a `subscriptionowner@iurcontoso.onmicrosoft.com`. Tale utente deve avere le credenziali appropriate, come descritto nel passaggio precedente.

3. Al prompt di PowerShell, eseguire il comando seguente per impostare `$subscriptionID` la sottoscrizione di Azure da usare. Sostituire `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con il proprio ID sottoscrizione:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida della sottoscrizione:

   - Specificare il valore per `AzureEnvironment` come **AzureCloud**, **AzureGermanCloud**, oppure **AzureChinaCloud**.  
   - Fornire all'amministratore di Azure Active Directory e il nome del tenant Azure Active Directory.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare che lo stato sia corretto per l'accesso sia i requisiti di registrazione. Completamento della convalida output appare simile all'esempio seguente:

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>File di log e report

Ogni convalida in fase di esecuzione, registra i risultati per **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Consente di visualizzare la posizione di questi file e i risultati di convalida in PowerShell.

Questi file consentono di condividere lo stato di convalida prima di distribuire Azure Stack o analizzare i problemi di convalida. Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successiva. Il report fornisce di conferma del team di distribuzione della configurazione di identità. Il file di log consentono al team di supporto o distribuzione di analizzare i problemi di convalida.

Per impostazione predefinita, entrambi i file vengono scritti **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Usare la **- OutputPath** ***&lt;path&gt;*** parametro alla fine di Esegui riga di comando per specificare un percorso di report diversa.
- Usare la **- CleanReport** parametro alla fine del comando per cancellare le informazioni relative alle esecuzioni precedenti dello strumento di esecuzione **AzsReadinessCheckerReport.json**.

Per altre informazioni, vedere [rapporto di convalida di Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida

Se un controllo di convalida non riesce, visualizzare i dettagli sull'errore nella finestra di PowerShell. Lo strumento registra inoltre informazioni per il **AzsReadinessChecker.log** file.

Gli esempi seguenti forniscono informazioni aggiuntive sugli errori di convalida comuni:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Utente deve essere un proprietario della sottoscrizione

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -l'account non è un amministratore della sottoscrizione di Azure.

**Risoluzione** -usare un account di amministratore della sottoscrizione di Azure che verrà addebitata per l'utilizzo della distribuzione di Azure Stack.

### <a name="expired-or-temporary-password"></a>Password scaduta o temporanee

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -l'account non è possibile accedere, perché la password è scaduta o è temporanea.

**Risoluzione** : In PowerShell eseguire il comando seguente e seguire le istruzioni per reimpostare la password.

```powershell
Login-AzureRMAccount
```

In alternativa, accedere al [portale di Azure](https://portal.azure.com) come proprietario dell'account e l'utente dovrà cambiare la password.

### <a name="unknown-user-type"></a>Tipo di utente sconosciuto  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -l'account non è possibile accedere all'ambiente di Azure Active Directory specificato. In questo esempio **AzureChinaCloud** è specificato come il **AzureEnvironment**.  

**Risoluzione** -verificare che l'account sia valido per l'ambiente di Azure specificato. In PowerShell, eseguire il comando seguente per verificare che l'account sia valido per un ambiente specifico:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Fasi successive

- [Convalidare l'identità di Azure](azure-stack-validate-identity.md)
- [Visualizzare il report di conformità](azure-stack-validation-report.md)
- [Considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)
