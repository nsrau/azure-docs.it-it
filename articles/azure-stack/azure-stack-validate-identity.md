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
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: b43fb3ff158a7df609d7a828192815db6b15963b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850061"
---
# <a name="validate-azure-identity"></a>Convalidare l'identità di Azure 
Usare lo strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) per verificare che Azure Active Directory (Azure AD) sia pronta per l'uso con Azure Stack. Convalidare la soluzione di identità di Azure prima di iniziare una distribuzione di Azure Stack.  

Il controllo di conformità di convalida:
 - Azure Active Directory (Azure AD) come provider di identità per Azure Stack.
 - L'account di Azure AD che si intende utilizzare possa accedere come amministratore globale di Azure Active Directory. 

La convalida garantisce che l'ambiente è pronto per Azure Stack per archiviare le informazioni sugli utenti, applicazioni, gruppi e le entità servizio da Azure Stack in Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità
Scaricare la versione più recente dello strumento di controllo di conformità di Azure Stack (AzsReadinessChecker) dal [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerequisiti
I seguenti prerequisiti sono necessari.

**Il computer in cui viene eseguito lo strumento:**
 - Windows 10 o Windows Server 2016, con connettività internet.
 - PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il comando PowerShell seguente e quindi esaminare i *principali* versione e *secondaria* versioni:  

   > `$PSVersionTable.PSVersion`
 - Configurare [PowerShell per Azure Stack](azure-stack-powershell-install.md). 
 - La versione più recente di [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

**Ambiente di Azure Active Directory:**
 - Identificare l'account di Azure AD, si verrà usata per Azure Stack e assicurarsi che sia un amministratore di Azure Active Directory globale.
 - Identificare il nome del Tenant di Azure AD. Il nome del tenant deve essere il *primaria* nome di dominio per Azure Active Directory. Ad esempio, *contoso.onmicrosoft.com*. 
 - Identificare il AzureEnvironment si userà. Valori supportati per il parametro name di ambiente sono AzureCloud, AzureChinaCloud o AzureUSGovernment a seconda di quale sottoscrizione di Azure in uso.

## <a name="validate-azure-identity"></a>Convalidare l'identità di Azure 
1. In un computer che soddisfi i prerequisiti, aprire un prompt amministrativo di PowerShell e quindi eseguire il comando seguente per installare il AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Al prompt di PowerShell, eseguire il comando seguente per impostare *$serviceAdminCredential* dell'amministratore del servizio per il Tenant Azure AD.  Sostituire *serviceadmin\@contoso.onmicrosoft.com* con l'account e il tenant. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida di Azure AD. 
   - Specificare il valore del nome di ambiente per AzureEnvironment. Valori supportati per il parametro name di ambiente sono AzureCloud, AzureChinaCloud o AzureUSGovernment a seconda di quale sottoscrizione di Azure in uso.  
   - Specificare il nome del Tenant Azure Active Directory per sostituire *contoso.onmicrosoft.com*. 

   > `Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare lo stato sia **OK** per i requisiti di installazione. Convalida riuscita viene visualizzato come nell'immagine seguente: 
 
```PowerShell
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

Questi file consentono di condividere lo stato di convalida prima di distribuire Azure Stack o analizzare i problemi di convalida.  Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successiva. Il report fornisce di conferma del team di distribuzione della configurazione di identità. Il file di log consentono al team di supporto o distribuzione di analizzare i problemi di convalida. 

Per impostazione predefinita, entrambi i file vengono scritti *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Usare la **- OutputPath** ***&lt;path&gt;*** parametro alla fine di Esegui riga di comando per specificare un percorso di report diversa.   
 - Usare la **- CleanReport** parametro alla fine del comando di esecuzione per cancellare informazioni da *AzsReadinessCheckerReport.json*.  sulle esecuzioni precedenti dello strumento. 

Per altre informazioni, [rapporto di convalida di Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida
Se un controllo di convalida non riesce, visualizzare i dettagli sull'errore nella finestra di PowerShell. Lo strumento registra inoltre informazioni per il AzsReadinessChecker.log.

Gli esempi seguenti forniscono informazioni aggiuntive sugli errori di convalida comuni.

### <a name="expired-or-temporary-password"></a>Password scaduta o temporanee 
 
```PowerShell
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

**Risoluzione** : In PowerShell eseguire il comando seguente e quindi seguire le istruzioni per reimpostare la password.  
> `Login-AzureRMAccount`

In alternativa, eseguire l'accesso al https://portal.azure.com come l'account e l'utente dovrà cambiare la password.
### <a name="unknown-user-type"></a>Tipo di utente sconosciuto 
 
```PowerShell
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
**Causa** -l'account non è possibile accedere a Azure Active Directory specificata (AADDirectoryTenantName). In questo esempio *AzureChinaCloud* è specificato come il *AzureEnvironment*.

**Risoluzione** -verificare che l'account sia valido per l'ambiente di Azure specificato. In PowerShell eseguire il comando seguente per verificare che l'account sia valido per un ambiente specifico:   Login-AzureRmAccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Account non amministratore 
 
```PowerShell
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

**Causa** -anche se l'account può accedere correttamente, l'account non è un amministratore di Azure Active Directory (AADDirectoryTenantName).  

**Risoluzione** -eseguire l'accesso al https://portal.azure.com come account, passare alla **Azure Active Directory** > **utenti** > *selezionare l'utente*  >  **Ruolo della directory**, quindi verificare che l'utente sia una **amministratore globale**.  Se l'account è un utente, andare al **Azure Active Directory** > **Custom domain** nomi e verificare che il nome fornito per *AADDirectoryTenantName* è contrassegnato come il nome di dominio primario per questa directory.  In questo esempio, si tratta *contoso.onmicrosoft.com*. 

Azure Stack richiede che il nome di dominio è il nome di dominio primario.

## <a name="next-steps"></a>Fasi successive
[Convalidare la registrazione di Azure](azure-stack-validate-registration.md)  
[Visualizzare il report di conformità](azure-stack-validation-report.md)  
[Considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)  

