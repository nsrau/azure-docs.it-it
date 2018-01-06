---
title: Registro dello Stack di Azure | Documenti Microsoft
description: Registro dello Stack di Azure con la sottoscrizione di Azure.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/5/2018
ms.author: jeffgilb
ms.openlocfilehash: c2edafbf483692d5a11771268a1755c11b74521f
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registro dello Stack di Azure con la sottoscrizione di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile registrare [Azure Stack](azure-stack-poc.md) con Azure per scaricare gli elementi di marketplace da Azure e per impostare i dati di commerce segnalazioni a Microsoft.

> [!NOTE]
>La registrazione è consigliata perché consente di testare importanti funzionalità di Stack di Azure, ad esempio diffusione marketplace e report di utilizzo. Dopo la registrazione dello Stack di Azure, viene segnalato l'utilizzo per Azure commerce. È possibile visualizzarlo nella sottoscrizione che è usata per la registrazione. Gli utenti di Azure il Kit di sviluppo dello Stack non sono addebitati qualsiasi utilizzo che fanno.


## <a name="get-azure-subscription"></a>Ottenere una sottoscrizione di Azure

Prima di registrare dello Stack di Azure con Azure, è necessario disporre di:

- L'ID di sottoscrizione per una sottoscrizione di Azure. Per ottenere l'ID, accedere a Azure, fare clic su **più servizi** > **sottoscrizioni**, fare clic sulla sottoscrizione che si desidera utilizzare, quindi in **Essentials** è possibile trovare il **ID sottoscrizione**. Cina, Germania e noi sottoscrizioni cloud per enti pubblici non sono attualmente supportati.
- Il nome utente e password per un account che è un proprietario per la sottoscrizione (sono supportati account MSA/2FA).
- *Non richiesto a partire dalla versione di aggiornamento di Azure Stack 1712 (1.0.180103.2):* di Azure Active Directory per la sottoscrizione di Azure. È possibile trovare la directory in Azure passando sul proprio avatar in alto a destra del portale di Azure.

Se non si dispone di una sottoscrizione di Azure che soddisfa questi requisiti, è possibile [creare un account gratuito di Azure qui](https://azure.microsoft.com/en-us/free/?b=17.06). Registrazione dello Stack di Azure non comporta alcun costo nella sottoscrizione di Azure.

## <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure  
> [!NOTE]
> Tutti questi passaggi devono essere eseguiti da un computer dotato di accesso all'endpoint con privilegi. Per Azure Stack Development Kit, sarebbe il computer host. Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure.
>

1. Aprire la console PowerShell come amministratore e [installare PowerShell per Azure Stack](azure-stack-powershell-install.md).  

2. Aggiungere l'account di Azure che consente di registrare dello Stack di Azure. Per aggiungere l'account, eseguire il `Add-AzureRmAccount` cmdlet con il set di parametri EnvironmentName **cloud**. Viene richiesto di immettere le credenziali dell'account Azure e potrebbe essere necessario utilizzare 2-factor authentication in base alla configurazione dell'account.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Se si dispone di più sottoscrizioni, eseguire il comando seguente per selezionare quello per cui che si desidera utilizzare:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Eseguire il comando seguente per registrare il provider di risorse dello Stack di Azure nella sottoscrizione di Azure:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Eliminare tutte le versioni esistenti dei moduli di PowerShell che corrispondono alla registrazione e [scaricare la versione più recente da GitHub](azure-stack-powershell-download.md).  

6. Dalla directory "AzureStack-strumenti-master" che viene creata nel passaggio precedente, passare alla cartella "Registration" e importare il modulo ".\RegisterWithAzure.psm1":  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. Nella stessa sessione di PowerShell, eseguire lo script seguente: quando vengono richieste le credenziali, specificare `azurestack\cloudadmin` come l'utente e la password è lo stesso di quello utilizzato per l'amministratore locale durante la distribuzione.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | Parametro | DESCRIZIONE |  
   |--------|-------------|
   | CloudAdminCredential | Le credenziali di dominio cloud che vengono utilizzate per [accedere all'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Il nome utente è nel formato  **\<dominio Azure Stack\>\cloudadmin**. Per il kit di sviluppo, il nome utente è impostato su **azurestack\cloudadmin**. Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure per ottenere questo valore.|  
   | PrivilegedEndpoint | Una preconfigurato PowerShell console remota che fornisce le funzionalità, ad esempio la raccolta di log e di post-attività di distribuzione. Kit di sviluppo, per l'endpoint con privilegi è ospitato nella macchina virtuale "AzS ERCS01". Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure per ottenere questo valore. Per ulteriori informazioni, vedere il [utilizzando l'endpoint con privilegi](azure-stack-privileged-endpoint.md) articolo.|  
   | BillingModel | Il modello di fatturazione che usa la sottoscrizione. I valori consentiti per questo parametro sono - **capacità**, **PayAsYouUse**, e **sviluppo**. Kit di sviluppo, per questo valore è impostato su **sviluppo**. Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure per ottenere questo valore. |

8. Al termine dello script, viene visualizzato un messaggio "attivare Azure Stack (questo passaggio può richiedere fino a 10 minuti)."




## <a name="verify-the-registration"></a>Verificare la registrazione  

1. Accedere al portale di amministrazione (https://adminportal.local.azurestack.external).

2. Fare clic su **più servizi** > **gestione Marketplace** > **aggiungere da Azure**.

3. Se viene visualizzato un elenco di prodotti disponibili in Azure (ad esempio WordPress), l'attivazione è riuscita.

> [!NOTE]
> Una volta completata la registrazione, l'avviso attivo per la registrazione non verrà più visualizzato.


## <a name="modify-the-registration"></a>Modificare la registrazione

### <a name="change-the-subscription-you-use"></a>Modificare la sottoscrizione che è utilizzare
Se si desidera modificare la sottoscrizione che è utilizzare, è necessario prima eseguire Remove-AzsRegistration, assicurarsi di essere connessi per il contesto di Azure PowerShell corretto e quindi eseguire Set-AzsRegistration a tutti i parametri modificati.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Modificare le funzionalità di diffusione o di modello di fatturazione
Se si desidera modificare il modello di fatturazione o la funzionalità di diffusione per l'installazione, è possibile chiamare la funzione di registrazione per impostare i nuovi valori. Non è necessario innanzitutto rimuovere la registrazione corrente.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Registrazione disconnessa
*Le informazioni contenute in questa sezione si applica a partire dalla versione di aggiornamento di Azure Stack 1712 (1.0.180103.2) e non sono supportate con le versioni precedenti.*

Se si sta registrando dello Stack di Azure in un ambiente disconnesso, è necessario ottenere una registrazione token dall'ambiente dello Stack di Azure e quindi usare tale token in un computer che può connettersi a Azure per la registrazione.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Ottenere una registrazione token dall'ambiente di Azure Stack
  1. Per ottenere il token di registrazione, eseguire le operazioni seguenti:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Il token di registrazione viene salvato nel file specificato per *$env:SystemDrive\RegistrationToken.txt*.

  2. Salvare il token di registrazione per l'uso in Azure macchina connessa.


### <a name="connect-to-azure-and-register"></a>Connettersi ad Azure e di registro
Eseguire i passaggi per eseguire questa procedura in un computer in grado di connettersi a Azure.

  1. [Scaricare i moduli di PowerShell più recenti che corrispondono alla registrazione di GitHub](azure-stack-powershell-download.md).  

  2. Dalla directory "AzureStack-strumenti-master" che viene creata nel passaggio precedente, passare alla cartella "Registration" e importare il modulo ".\RegisterWithAzure.psm1":  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Copia [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) in una cartella, ad esempio *C:\Temp*.

  4. Avviare PowerShell ISE come amministratore e quindi importare il modulo RegisterWithAzure.  

  5. Assicurarsi di essere connessi nel contesto di Azure PowerShell corretto che si desidera utilizzare per registrare l'ambiente dello Stack di Azure:  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Specificare il token di registrazione per registrare con Azure:

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    Facoltativamente, è possibile utilizzare il cmdlet Get-Content per puntare a un file che contiene il token di registrazione:

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Salvare il nome di risorsa di registrazione o il token di registrazione per riferimento futuro.

### <a name="remove-a-registered-resource"></a>Rimuove una risorsa registrata
Se si desidera rimuovere la risorsa di registrazione, è necessario utilizzare l'annullamento della registrazione AzsEnvironment e passare il nome di risorsa di registrazione o il token di registrazione che è utilizzato per registrare AzsEnvironment.
- **Nome di risorsa di registrazione:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Token di registrazione:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>Passaggi successivi
[Connettersi ad Azure Stack](azure-stack-connect-azure-stack.md)
