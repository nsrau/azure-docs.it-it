---
title: Convalidare la registrazione di Azure per Azure Stack | Microsoft Docs
description: Usare il controllo di conformità di Azure Stack per convalidare la registrazione di Azure.
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
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 57869de8a99c65810da0c75f81c75d93eac88412
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35998504"
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

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Dopo che viene eseguito lo strumento, esaminare l'output. Verificare che lo stato è OK per accesso e i requisiti di registrazione. Convalida riuscita viene visualizzato come nell'immagine seguente:  
![eseguire la convalida](./media/azure-stack-validate-registration/registration-validation.png)


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
![proprietario della sottoscrizione](./media/azure-stack-validate-registration/subscription-owner.png)
**causa** -l'account non è un amministratore della sottoscrizione di Azure.   

**Risoluzione** -usare un account di amministratore della sottoscrizione di Azure che verrà addebitata per l'utilizzo della distribuzione di Azure Stack.


### <a name="expired-or-temporary-password"></a>Password scaduta o temporanee 
![password scaduta](./media/azure-stack-validate-registration/expired-password.png)
**causa** -l'account non è possibile accedere perché la password è scaduta o è temporanea.     

**Risoluzione** : In PowerShell eseguire e seguire le istruzioni per reimpostare la password. 
  > `Login-AzureRMAccount` 

In alternativa, eseguire l'accesso al https://portal.azure.com come l'account e l'utente dovrà cambiare la password.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Gli account Microsoft non sono supportati per la registrazione  
![account non è supportato](./media/azure-stack-validate-registration/unsupported-account.png)
**causa** -account A Microsoft (ad esempio, Outlook.com o Hotmail.com) è stato specificato.  Questi account non sono supportati.

**Risoluzione** -usare un account e una sottoscrizione da un Provider del servizio Cloud (CSP) o un Enterprise Agreement (EA). 


### <a name="unknown-user-type"></a>Tipo di utente sconosciuto  
![utente sconosciuto](./media/azure-stack-validate-registration/unknown-user.png)
**causa** -l'account non è possibile accedere all'ambiente di Azure Active Directory specificato. In questo esempio *AzureChinaCloud* è specificato come il *AzureEnvironment*.  

**Risoluzione** -verificare che l'account sia valido per l'ambiente di Azure specificato. In PowerShell eseguire il comando seguente per verificare che l'account sia valido per un ambiente specifico.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Fasi successive
[Convalidare l'identità di Azure](azure-stack-validate-identity.md)
[consente di visualizzare il report di conformità](azure-stack-validation-report.md)
[considerazioni relative all'integrazione di Azure Stack generale](azure-stack-datacenter-integration.md)

