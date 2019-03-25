---
title: Convalidare l'identità di Azure per Azure Stack | Microsoft Docs
description: Usare il controllo di conformità di Azure Stack per convalidare l'identità di Azure.
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
ms.openlocfilehash: 3dfb87e5f6c231831cd9c007b19ad001e1fce326
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403188"
---
# <a name="validate-azure-identity"></a>Convalidare l'identità di Azure

Usare lo strumento di controllo di conformità di Azure Stack (**AzsReadinessChecker**) per convalidare che Azure Active Directory (Azure AD) è pronto per l'uso con Azure Stack. Convalidare la soluzione di identità di Azure prima di iniziare una distribuzione di Azure Stack.  

Il controllo di conformità di convalida:

- Azure Active Directory (Azure AD) come provider di identità per Azure Stack.
- L'account di Azure AD che si intende utilizzare possa accedere come amministratore globale di Azure Active Directory.

La convalida garantisce che l'ambiente è pronto per Azure Stack per archiviare le informazioni sugli utenti, applicazioni, gruppi e le entità servizio da Azure Stack in Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità

Scaricare la versione più recente dello strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) dal [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerequisiti

Sono necessari i prerequisiti seguenti:

**Il computer in cui viene eseguito lo strumento:**

- Windows 10 o Windows Server 2016, con connettività internet.
- PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il comando PowerShell seguente ed esaminare i **principali** versione e **secondaria** versioni:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell per Azure Stack configurato](azure-stack-powershell-install.md).
- La versione più recente di [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

**Ambiente di Azure Active Directory:**

- Identificare l'account di Azure AD da usare per Azure Stack e assicurarsi che sia un amministratore globale di Azure Active Directory.
- Identificare il nome del tenant Azure AD. Il nome del tenant deve essere il nome di dominio primario per Azure Active Directory; ad esempio, **contoso.onmicrosoft.com**.
- Identificare l'ambiente Azure, che si userà. Sono valori supportati per il parametro del nome ambiente **AzureCloud**, **AzureChinaCloud**, o **AzureUSGovernment**, a seconda di quale sottoscrizione di Azure è usare.

## <a name="steps-to-validate-azure-identity"></a>Passaggi per convalidare l'identità di Azure

1. In un computer che soddisfi i prerequisiti, aprire un prompt dei comandi di PowerShell con privilegi elevati e quindi eseguire il comando seguente per installare **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Al prompt di PowerShell, eseguire il comando seguente per impostare **$serviceAdminCredential** come amministratore del servizio per il tenant di Azure AD.  Sostituire **serviceadmin\@contoso.onmicrosoft.com** con il nome account e tenant:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida di Azure AD:

   - Specificare il valore nome ambiente **AzureEnvironment**. Sono valori supportati per il parametro del nome ambiente **AzureCloud**, **AzureChinaCloud**, o **AzureUSGovernment**, a seconda di quale sottoscrizione di Azure è usare.
   - Sostituire **contoso.onmicrosoft.com** con il nome del tenant Azure Active Directory.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare lo stato sia **OK** per i requisiti di installazione. Convalida riuscita viene visualizzato come nell'immagine seguente:

   ```shell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>File di log e report

Ogni convalida in fase di esecuzione, registra i risultati per **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Consente di visualizzare la posizione di questi file con i risultati della convalida in PowerShell.

Questi file consentono di condividere lo stato di convalida prima di distribuire Azure Stack o analizzare i problemi di convalida. Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successiva. Il report fornisce di conferma del team di distribuzione della configurazione di identità. Il file di log consentono al team di supporto o distribuzione di analizzare i problemi di convalida.

Per impostazione predefinita, entrambi i file vengono scritti **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Usare la **- OutputPath** ***&lt;path&gt;*** parametro alla fine di Esegui riga di comando per specificare un percorso di report diversa.
- Usare la **- CleanReport** parametro alla fine del comando per cancellare le informazioni relative alle esecuzioni precedenti dello strumento di esecuzione **AzsReadinessCheckerReport.json**.

Per altre informazioni, vedere [rapporto di convalida di Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida

Se un controllo di convalida non riesce, visualizzare i dettagli sull'errore nella finestra di PowerShell. Lo strumento registra inoltre le informazioni nel file AzsReadinessChecker.log.

Gli esempi seguenti forniscono informazioni aggiuntive sugli errori di convalida comuni.

### <a name="expired-or-temporary-password"></a>Password scaduta o temporanee

```shell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa** -l'account non è possibile accedere perché la password è scaduta o è temporanea.

**Risoluzione** : In PowerShell eseguire il comando seguente e quindi seguire le istruzioni per reimpostare la password:

```powershell
Login-AzureRMAccount
```

In alternativa, accedere al [portale di Azure](https://portal.azure.com) come proprietario dell'account e l'utente dovrà cambiare la password.

### <a name="unknown-user-type"></a>Tipo di utente sconosciuto

```shell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa** -l'account non è possibile accedere a di Azure Active Directory specificata (**AADDirectoryTenantName**). In questo esempio **AzureChinaCloud** è specificato come il **AzureEnvironment**.

**Risoluzione** -verificare che l'account sia valido per l'ambiente di Azure specificato. In PowerShell, eseguire il comando seguente per verificare che l'account sia valido per un ambiente specifico:

```powershell
Login-AzureRmAccount –EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Account non amministratore

```shell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa** -anche se l'account può effettuare l'accesso, l'account non è un amministratore di Azure Active Directory (**AADDirectoryTenantName**).  

**Risoluzione** -accedere il [portale di Azure](https://portal.azure.com) come proprietario dell'account, accedere alla **Azure Active Directory**, quindi **utenti**, quindi **selezionare il Utente**, quindi **ruolo della Directory**, quindi verificare che l'utente sia un **amministratore globale**. Se l'account è un **utente**passare alla **Azure Active Directory** > **nomi di dominio personalizzati**e verificare che il nome fornito per  **AADDirectoryTenantName** è contrassegnato come il nome di dominio primario per questa directory. In questo esempio, si tratta **contoso.onmicrosoft.com**.

Azure Stack richiede che il nome di dominio è il nome di dominio primario.

## <a name="next-steps"></a>Fasi successive

[Convalidare la registrazione di Azure](azure-stack-validate-registration.md)  
[Visualizzare il report di conformità](azure-stack-validation-report.md)  
[Considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)  
