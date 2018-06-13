---
title: Convalidare l'identità di Azure per lo Stack di Azure | Documenti Microsoft
description: Utilizzare il controllo di conformità dello Stack di Azure per convalidare l'identità di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937829"
---
# <a name="validate-azure-identity"></a>Convalidare l'identità di Azure 
Utilizzare lo strumento di controllo di conformità dello Stack di Azure (AzsReadinessChecker) per convalidare che Azure Active Directory (Azure AD) è pronta per l'uso con lo Stack di Azure. Convalidare la soluzione di identità Azure prima di iniziare una distribuzione di Azure Stack.  

Il controllo di conformità di convalida:
 - Azure Active Directory (Azure AD) come provider di identità per lo Stack di Azure.
 - L'account di Azure AD che si intende utilizzare possa accedere come amministratore globale di Azure Active Directory. 

La convalida garantisce che l'ambiente è pronto per lo Stack di Azure archiviare informazioni su utenti, le applicazioni, gruppi ed entità servizio dallo Stack di Azure in Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Ottenere lo strumento di controllo di conformità
Scaricare la versione più recente dello strumento di controllo di conformità dello Stack di Azure (AzsReadinessChecker) dal [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerequisiti
I seguenti prerequisiti devono essere presenti.

**Il computer in cui viene eseguito lo strumento:**
 - Windows 10 o Windows Server 2016, con connettività internet.
 - PowerShell 5.1 o versione successiva. Per controllare la versione in uso, eseguire il seguente cmd PowerShell ed esaminare il *principali* versione e *secondaria* versioni:  

   > `$PSVersionTable.PSVersion`
 - Configurare [PowerShell per Azure Stack](azure-stack-powershell-install.md). 
 - La versione più recente di [Verifica conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

**Ambiente di Azure Active Directory:**
 - Identificare l'account di Azure Active Directory usata per lo Stack di Azure e assicurarsi che sia un amministratore globale Azure Active Directory.
 - Identificare il nome del Tenant di Azure AD. Il nome del tenant deve essere il *primario* nome di dominio per Azure Active Directory. Ad esempio *contoso.onmicrosoft.com*. 
 - Identificare AzureEnvironement si utilizzerà: *cloud*, *AzureGermanCloud*, o *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Convalidare l'identità di Azure 
1. In un computer che soddisfi i prerequisiti, aprire un prompt dei comandi di PowerShell amministrativi e quindi eseguire il comando seguente per installare il AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Al prompt di PowerShell, eseguire il comando seguente per impostare *$serviceAdminCredential* come l'amministratore del servizio per il Tenant di Azure AD.  Sostituire *serviceadmin@contoso.onmicrosoft.com* con l'account e tenant. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Al prompt di PowerShell, eseguire il comando seguente per avviare la convalida di Azure Active Directory. 
   - Specificare il valore per AzureEnvironment come *cloud*, *AzureGermanCloud*, o *AzureChinaCloud*.  
   - Specificare il nome del Tenant Azure Active Directory per sostituire *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Quando si esegue lo strumento, esaminare l'output. Verificare lo stato sia **OK** per accesso e i requisiti di installazione. Convalida riuscita viene visualizzato come nell'immagine seguente: 
 
![eseguire la convalida](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>File di log e report
Ogni convalida in fase di esecuzione, registra i risultati per **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. La posizione di questi file viene visualizzato con i risultati della convalida in PowerShell.

Questi file consentono di condividere lo stato di convalida prima di distribuire Azure Stack o analizzare i problemi di convalida.  Entrambi i file di rendere persistenti i risultati di ogni controllo di convalida successivi. Il report fornisce la conferma del team di distribuzione della configurazione dell'identità. Il file di log consentono il team di distribuzione o il supporto di analizzare i problemi di convalida. 

Per impostazione predefinita, entrambi i file vengono scritti *C:\Users\<nomeutente > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Usare la **- OutputPath** ***&lt;percorso&gt;*** parametro alla fine di Esegui riga di comando per specificare un percorso di report diverso.   
 - Usare la **- CleanReport** parametro alla fine del comando di esecuzione per cancellare informazioni da *AzsReadinessCheckerReport.json*.  sulle esecuzioni precedenti dello strumento. 

Per ulteriori informazioni [rapporto di convalida dello Stack Azure](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errori di convalida
Se un controllo di convalida non riesce, visualizzare i dettagli dell'errore nella finestra di PowerShell. Lo strumento registra inoltre informazioni per il AzsReadinessChecker.log.

Negli esempi seguenti forniscono istruzioni sulla comuni errori di convalida.

### <a name="expired-or-temporary-password"></a>Password scaduta o temporanee 
 
![scadenza password](./media/azure-stack-validate-identity/expired-password.png)
**causa** -l'account non è possibile accedere perché la password è scaduta o è temporanea.     

**Risoluzione** - In PowerShell eseguire il comando seguente e quindi seguire le istruzioni per reimpostare la password.  
> `Login-AzureRMAccount`

In alternativa, effettuare l'accesso https://portal.azure.com come l'account e l'utente dovrà cambiare la password.
### <a name="unknown-user-type"></a>Tipo di utente sconosciuto 
 
![utente sconosciuto](./media/azure-stack-validate-identity/unknown-user.png)
**causa** -l'account non è possibile accedere a Azure Active Directory specificata (AADDirectoryTenantName). In questo esempio, *AzureChinaCloud* è specificato come il *AzureEnvironment*.

**Risoluzione** -verificare che l'account sia valido per l'ambiente di Azure specifico. In PowerShell eseguire il comando seguente per verificare che l'account sia valido per un ambiente specifico: account di accesso-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>L'account non amministratore 
 
![non amministratore](./media/azure-stack-validate-identity/not-admin.png)

**Causa** -anche se l'account è stato possibile accedere, l'account non è un amministratore di Azure Active Directory (AADDirectoryTenantName).  

**Risoluzione** -account di accesso nel https://portal.azure.com come account, passare alla **Azure Active Directory** > **utenti** > *selezionare l'utente*  >  **Ruolo della directory**, quindi verificare che l'utente sia un **amministratore globale**.  Se l'account è un utente, andare al **Azure Active Directory** > **dominio personalizzato** denomina e confermare che il nome specificato per *AADDirectoryTenantName* è contrassegnato come il nome di dominio primario per questa directory.  In questo esempio, che è *contoso.onmicrosoft.com*. 

Stack di Azure richiede che il nome di dominio è il nome di dominio primario.

## <a name="next-steps"></a>Fasi successive
[Convalidare una registrazione di Azure](azure-stack-validate-registration.md)  
[Visualizzare il report di conformità](azure-stack-validation-report.md)  
[Considerazioni relative all'integrazione generale Azure Stack](azure-stack-datacenter-integration.md)  

