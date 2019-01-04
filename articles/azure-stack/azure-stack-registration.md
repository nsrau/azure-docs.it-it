---
title: I sistemi integrati di registrazione di Azure per Azure Stack | Microsoft Docs
description: Descrive il processo di registrazione di Azure per le distribuzioni basate su Azure Stack di Azure a più nodi.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: b036f0b1c38222b6bb3ebee1a3fab0d1613260f7
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811019"
---
# <a name="register-azure-stack-with-azure"></a>Registrare Azure Stack con Azure

La registrazione di Azure Stack con Azure consente di scaricare elementi di marketplace di Azure e per impostare i dati di e-commerce segnalazioni a Microsoft. Dopo la registrazione di Azure Stack, viene segnalato l'utilizzo di e-commerce di Azure e può essere visualizzato con la sottoscrizione usata per la registrazione.

Le informazioni contenute in questo articolo descrivono la registrazione di sistemi integrati di Azure Stack con Azure. Per informazioni sulla registrazione di ASDK con Azure, vedere [registrazione di Azure Stack](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register) nella documentazione di ASDK.

> [!IMPORTANT]  
> È necessario eseguire la registrazione per supportare la funzionalità di Azure Stack completa, compresa l'offerta nel marketplace di elementi. Inoltre, sarà in violazione delle condizioni di licenza, se non si registrano quando si usa il modello di fatturazione di come è a pagamento di Azure Stack. Per altre informazioni sui modelli di licenza di Azure Stack, vedere la [modalità di acquisto pagina](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Prerequisiti

È necessario quanto segue posto prima di registrare:

 - Verificare le credenziali
 - Impostare la modalità di linguaggio di PowerShell
 - Installare PowerShell per Azure Stack
 - Scaricare gli strumenti di Azure Stack
 - Determinare lo scenario di registrazione

### <a name="verify-your-credentials"></a>Verificare le credenziali

Prima di registrare Azure Stack con Azure, è necessario disporre di:

- L'ID sottoscrizione per una sottoscrizione di Azure. Solo EA, CSP o CSP shared services sono supportate le sottoscrizioni per la registrazione. I CSP devono decidere se si desidera [usare una sottoscrizione CSP o questi servizi avanzati](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Per ottenere l'ID, accedere ad Azure, fare clic su **tutti i servizi**. Quindi, sotto il **generale** categoria, seleziona **sottoscrizioni**, scegliere la sottoscrizione da usare, quindi in **Essentials** è possibile trovare l'ID sottoscrizione.

  > [!Note]  
  > Sottoscrizioni cloud Germania non sono attualmente supportate.

- Il nome utente e password per un account che è un proprietario per la sottoscrizione.

- L'account utente deve avere accesso alla sottoscrizione di Azure e disporre delle autorizzazioni per creare applicazioni a identità ed entità servizio nella directory associata alla sottoscrizione.

- Registrato il provider di risorse di Azure Stack (vedere la sezione di registrazione del Provider di risorse Azure Stack di seguito per informazioni dettagliate).

Dopo la registrazione, l'autorizzazione di amministratore globale di Azure Active Directory non è necessaria. Tuttavia, alcune operazioni potrebbero richiedere le credenziali di amministratore globale. Ad esempio, uno script di programma di installazione di provider di risorse o una nuova funzionalità che richiedono un'autorizzazione da concedere. È possibile temporaneamente reinstate autorizzazioni di amministratore globale dell'account o utilizzare un account di amministratore globale separata che è un proprietario del *predefinita sottoscrizione provider*.

Se non hai una sottoscrizione di Azure che soddisfa questi requisiti, è possibile [crea qui un account Azure gratuito](https://azure.microsoft.com/free/?b=17.06). La registrazione di Azure Stack viene addebitata alcuna tariffa nella sottoscrizione di Azure.

### <a name="powershell-language-mode"></a>Modalità di linguaggio di PowerShell

Per registrare correttamente Azure Stack, la modalità di linguaggio di PowerShell deve essere impostata su **FullLanguageMode**.  Per verificare che la modalità linguaggio corrente è impostata su full, aprire una finestra di PowerShell con privilegi elevata ed eseguire i cmdlet di PowerShell seguenti:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Verificare che l'output restituisce **FullLanguageMode**. Se viene restituita qualsiasi altra modalità di linguaggio, registrazione dovrà essere eseguito in un altro computer o la modalità di linguaggio dovrà essere impostata su **FullLanguageMode** prima di continuare.

### <a name="install-powershell-for-azure-stack"></a>Installare PowerShell per Azure Stack

È necessario usare la versione più recente di PowerShell per Azure Stack per registrazione in Azure.

Se non la versione più recente non è già installata, vedere [installazione di PowerShell per Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Scaricare gli strumenti di Azure Stack

Il repository GitHub di Azure Stack degli strumenti contiene i moduli di PowerShell che supportano la funzionalità di Azure Stack. inclusa la funzionalità di registrazione. Durante il processo di registrazione, è necessario importare e usare la **RegisterWithAzure.psm1** modulo di PowerShell, trovato nel repository di strumenti di Azure Stack, registrare l'istanza di Azure Stack con Azure.

Per assicurarsi di usare la versione più recente, è necessario eliminare eventuali versioni esistenti di strumenti di Azure Stack e [scaricare la versione più recente da GitHub](azure-stack-powershell-download.md) prima di registrare con Azure.

### <a name="determine-your-registration-scenario"></a>Determinare lo scenario di registrazione

Distribuzione di Azure Stack può essere *connessi* oppure *disconnesso*.

 - **Connessione effettuata**  
 Connesso indica che è stato distribuito Azure Stack, in modo che possa connettersi a Internet e in Azure. È necessario Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) per l'archivio identità. Con una distribuzione connessa, è possibile scegliere tra due modelli di fatturazione: come è a pagamento o basato sulla capacità.
    - [Registrare un connesse di Azure Stack con Azure usando il **come si-pagamento** modello di fatturazione](#register-connected-with-pay-as-you-go-billing)
    - [Registrare un connesse di Azure Stack con Azure usando il **capacità** modello di fatturazione](#register-connected-with-capacity-billing)

 - **Disconnesso**  
 Con disconnesso dall'opzione di distribuzione di Azure, è possibile distribuire e usare Azure Stack senza una connessione a Internet. Tuttavia, con una distribuzione disconnessa, si è limitati a un archivio identità di AD FS e il modello di fatturazione basato sulla capacità.
    - [Registrare un disconnesso Azure Stack usando il **capacità** modello di fatturazione ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Determinare un nome di registrazione univoci da utilizzare 
Quando si registra Azure Stack con Azure, è necessario fornire un nome di registrazione univoci. Un modo semplice per associare la sottoscrizione di Azure Stack con una registrazione di Azure consiste nell'utilizzare Azure Stack **ID Cloud**. 

> [!NOTE]
> Le registrazioni di Azure Stack usando il modello di fatturazione basato sulla capacità saranno necessario modificare il nome univoco quando si registra nuovamente dopo che tali sottoscrizioni annuale scadono se non si [eliminare la registrazione scaduta](azure-stack-registration.md#change-the-subscription-you-use) e ripetere la registrazione con Azure.

Per determinare l'ID del Cloud per la distribuzione di Azure Stack, aprire PowerShell come amministratore in un computer che possa accedere all'Endpoint con privilegi, eseguire i comandi seguenti e registrare il **CloudID** valore: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: get-azurestackstampinformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registrare connesse con la fatturazione con pagamento a consumo

Usare questi passaggi per registrare Azure Stack con Azure usando il modello di fatturazione di come è a pagamento.

> [!Note]  
> Tutti questi passaggi devono essere eseguiti da un computer dotato di accesso all'endpoint con privilegi (PEP). Per informazioni dettagliate su di PEP, vedere [usando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md).

Ambienti connessi possono accedere a internet e Azure. Per questi ambienti, è necessario registrare il provider di risorse di Azure Stack con Azure e quindi configurare il modello di fatturazione.

1. Per registrare il provider di risorse di Azure Stack con Azure, avviare PowerShell ISE come amministratore e usare i cmdlet di PowerShell seguenti con il **EnvironmentName** parametro impostato sul tipo di sottoscrizione Azure appropriata (vedere parametri seguenti).

2. Aggiungere l'account di Azure da usare per registrarsi in Azure Stack. Per aggiungere l'account, eseguire la **Add-AzureRmAccount** cmdlet. Viene chiesto di immettere le credenziali dell'account Azure e potrebbe essere necessario usare l'autenticazione a 2 fattori in base alla configurazione dell'account.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametro | DESCRIZIONE |  
   |-----|-----|
   | EnvironmentName | Il nome dell'ambiente sottoscrizione cloud di Azure. I nomi di ambiente supportati sono **AzureCloud**, **AzureUSGovernment**, o se si usa una sottoscrizione di Azure Cina **AzureChinaCloud**.  |

3. Se si hanno più sottoscrizioni, eseguire il comando seguente per selezionare quello da usare:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Eseguire il comando seguente per registrare il provider di risorse di Azure Stack nella sottoscrizione di Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory create quando è [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importa i **RegisterWithAzure.psm1** modulo con PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Successivamente, nella stessa sessione di PowerShell, assicurarsi che si è connessi al contesto di PowerShell di Azure corretta. Si tratta dell'account di Azure che è stato usato per registrare il provider di risorse di Azure Stack precedente. PowerShell per l'esecuzione:

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametro | DESCRIZIONE |  
   |-----|-----|
   | EnvironmentName | Il nome dell'ambiente sottoscrizione cloud di Azure. I nomi di ambiente supportati sono **AzureCloud**, **AzureUSGovernment**, o se si usa una sottoscrizione di Azure Cina **AzureChinaCloud**.  |

7. Nella stessa sessione di PowerShell, eseguire la **Set-AzsRegistration** cmdlet. PowerShell per l'esecuzione:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Per altre informazioni sul cmdlet Set-AzsRegistration, vedere [riferimento registrazione](#registration-reference).

  Il processo richiederà da 10 a 15 minuti. Al termine del comando, si vedranno il messaggio **"dell'ambiente è ora registrato e attivati mediante i parametri forniti."**

## <a name="register-connected-with-capacity-billing"></a>Registrare connesse con la fatturazione di capacità

Usare questi passaggi per registrare Azure Stack con Azure usando il modello di fatturazione di come è a pagamento.

> [!Note]  
> Tutti questi passaggi devono essere eseguiti da un computer dotato di accesso all'endpoint con privilegi (PEP). Per informazioni dettagliate su di PEP, vedere [usando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md).

Ambienti connessi possono accedere a internet e Azure. Per questi ambienti, è necessario registrare il provider di risorse di Azure Stack con Azure e quindi configurare il modello di fatturazione.

1. Per registrare il provider di risorse di Azure Stack con Azure, avviare PowerShell ISE come amministratore e usare i cmdlet di PowerShell seguenti con il **EnvironmentName** parametro impostato sul tipo di sottoscrizione Azure appropriata (vedere parametri seguenti).

2. Aggiungere l'account di Azure da usare per registrarsi in Azure Stack. Per aggiungere l'account, eseguire la **Add-AzureRmAccount** cmdlet. Viene chiesto di immettere le credenziali dell'account Azure e potrebbe essere necessario usare l'autenticazione a 2 fattori in base alla configurazione dell'account.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametro | DESCRIZIONE |  
   |-----|-----|
   | EnvironmentName | Il nome dell'ambiente sottoscrizione cloud di Azure. I nomi di ambiente supportati sono **AzureCloud**, **AzureUSGovernment**, o se si usa una sottoscrizione di Azure Cina **AzureChinaCloud**.  |

3. Se si hanno più sottoscrizioni, eseguire il comando seguente per selezionare quello da usare:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Eseguire il comando seguente per registrare il provider di risorse di Azure Stack nella sottoscrizione di Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory create quando è [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importa i **RegisterWithAzure.psm1** modulo con PowerShell:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > È possibile disabilitare l'utilizzo di creazione di report con il parametro UsageReportingEnabled per il **Set-AzsRegistration** cmdlet, impostando il parametro su false. 
   
  Per altre informazioni sul cmdlet Set-AzsRegistration, vedere [riferimento registrazione](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrare disconnessi con la fatturazione di capacità

Se si sta registrando Azure Stack in un ambiente disconnesso (con nessuna connettività a internet), è necessario ottenere una registrazione token dall'ambiente Azure Stack e quindi usare il token in un computer che possa connettersi ad Azure e ha [PowerShell per Azure Stack installato](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Ottenere una registrazione token dall'ambiente Azure Stack

1. Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory create quando è [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importa i **RegisterWithAzure.psm1** modulo:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Per ottenere il token di registrazione, eseguire i cmdlet di PowerShell seguenti:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Per altre informazioni sul cmdlet Get-AzsRegistrationToken, vedere [riferimento registrazione](#registration-reference).

   > [!Tip]  
   > Il token di registrazione viene salvato nel file specificato per *$FilePathForRegistrationToken*. È possibile modificare il percorso file o file a propria discrezione.

3. Salvare questo token di registrazione per l'uso in Azure connessa macchina. È possibile copiare il file o il testo da $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Connettersi ad Azure e registrazione

Nel computer connesso a Internet, eseguire la stessa procedura per importare il modulo RegisterWithAzure.psm1 ed eseguire l'accesso al contesto di Azure Powershell corretto. Quindi chiamare Register-AzsEnvironment. Specificare il token di registrazione da registrare con Azure. Se si desidera registrare più di un'istanza di Azure Stack usando lo stesso ID di sottoscrizione di Azure, specificare un nome di registrazione univoci. Eseguire il cmdlet seguente:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

Facoltativamente, è possibile usare il cmdlet Get-Content per puntare a un file che contiene il token di registrazione:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Salvare il nome di risorsa di registrazione e il token di registrazione per riferimento futuro.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperare una chiave di attivazione dalla risorsa di registrazione di Azure

Successivamente, è necessario recuperare una chiave di attivazione dalla risorsa di registrazione creata in Azure durante la registrazione AzsEnvironment.

Per ottenere la chiave di attivazione, eseguire i cmdlet di PowerShell seguenti:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > La chiave di attivazione viene salvata nel file specificato per *$KeyOutputFilePath*. È possibile modificare il percorso file o file a propria discrezione.

### <a name="create-an-activation-resource-in-azure-stack"></a>Crea una risorsa di attivazione in Azure Stack

Tornare all'ambiente di Azure Stack con il file o il testo dalla chiave di attivazione creata da Get-AzsActivationKey. Si creerà una risorsa di attivazione in Azure Stack tramite tale chiave di attivazione. Per creare una risorsa di attivazione, eseguire i cmdlet di PowerShell seguenti:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Facoltativamente, è possibile usare il cmdlet Get-Content per puntare a un file che contiene il token di registrazione:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Verificare la registrazione di Azure Stack

È possibile usare la **gestione delle aree** riquadro per verificare che la registrazione di Azure Stack è riuscita. Questo riquadro è disponibile nel dashboard predefinito nel portale di amministrazione. Lo stato può essere registrato o non registrato. Se la registrazione, nonché l'ID sottoscrizione di Azure che è utilizzato per registrare lo Stack di Azure con il gruppo di risorse di registrazione e il nome.

1. Accedi per il [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external).

2. Dal Dashboard, selezionare **gestione delle aree**.

    [ ![Nel riquadro Gestione regione](media/azure-stack-registration/admin1sm.png "nel riquadro Gestione area") ](media/azure-stack-registration/admin1.png#lightbox)

3. Selezionare **Proprietà**. Questo pannello mostra lo stato e i dettagli dell'ambiente. Lo stato può essere **Registered** oppure **non è registrato**. Se la registrazione, nonché l'ID sottoscrizione di Azure che è utilizzato per registrare Azure Stack, con il gruppo di risorse di registrazione e il nome.

## <a name="renew-or-change-registration"></a>Rinnova o Modifica registrazione

### <a name="renew-or-change-registration-in-connected-environments"></a>Rinnova o modificare la registrazione in ambienti connessi

È necessario aggiornare o rinnovare la registrazione nelle circostanze seguenti:

- Dopo aver rinnovare l'abbonamento annua basato sulla capacità.
- Quando si modifica il modello di fatturazione.
- Quando si ridimensiona le modifiche apportate (aggiunta/rimozione di nodi) per la fatturazione basata sulla capacità.

#### <a name="change-the-subscription-you-use"></a>Modificare la sottoscrizione usata

Se si desidera modificare la sottoscrizione è utilizzare, è necessario eseguire prima la **Remove-AzsRegistration** cmdlet, quindi verificare che si è connessi al contesto di Azure PowerShell corretto e infine eseguire **Set-AzsRegistration**  con gli eventuali parametri modificati inclusi \<modello di fatturazione\>:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Modificare il modello di fatturazione o sull'offerta di funzionalità

Se si desidera modificare il modello di fatturazione o come offrono funzionalità per l'installazione, è possibile chiamare la funzione di registrazione per impostare i nuovi valori. Non è necessario innanzitutto rimuovere la registrazione corrente:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Rinnova o Modifica registrazione in ambienti non connessi

È necessario aggiornare o rinnovare la registrazione nelle circostanze seguenti:

- Dopo aver rinnovare l'abbonamento annua basato sulla capacità.
- Quando si modifica il modello di fatturazione.
- Quando si ridimensiona le modifiche apportate (aggiunta/rimozione di nodi) per la fatturazione basata sulla capacità.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Rimuovere la risorsa di attivazione da Azure Stack

È necessario innanzitutto rimuovere la risorsa di attivazione da Azure Stack e quindi la risorsa di registrazione in Azure.  

Per rimuovere la risorsa di attivazione in Azure Stack, eseguire i cmdlet di PowerShell seguenti nell'ambiente Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Successivamente, per rimuovere la risorsa di registrazione in Azure, assicurarsi di usare un'istanza di Azure connesse computer, accedere al contesto di Azure PowerShell corretto ed eseguire i cmdlet di PowerShell appropriati come descritto di seguito.

È possibile usare il token di registrazione utilizzato per creare la risorsa:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Oppure è possibile usare il nome di registrazione:

  ```Powershell
  $registrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Ripetere la registrazione con passaggi disconnessi

Ora sono completamente annullata in una situazione di disconnessione e si deve ripetere i passaggi per la registrazione di un ambiente Azure Stack in una situazione di disconnessione.

### <a name="disable-or-enable-usage-reporting"></a>Disabilitare o abilitare i report di utilizzo

Per gli ambienti Azure Stack che usano un modello di fatturazione di capacità, disattivare la segnalazione con utilizzo il **UsageReportingEnabled** parametro usando la **Set-AzsRegistration** o  **Get-AzsRegistrationToken** cmdlet. Azure Stack riporta le metriche di utilizzo per impostazione predefinita. Gli operatori con utilizza la capacità o che supportano un ambiente disconnesso saranno necessario disattivare la segnalazione di utilizzo.

#### <a name="with-a-connected-azure-stack"></a>Con un connesse di Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Con un disconnesso di Azure Stack

1. Per modificare il token di registrazione, eseguire i cmdlet di PowerShell seguenti:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Il token di registrazione viene salvato nel file specificato per *$FilePathForRegistrationToken*. È possibile modificare il percorso file o file a propria discrezione.

2. Salvare questo token di registrazione per l'uso in Azure connessa macchina. È possibile copiare il file o il testo da $FilePathForRegistrationToken.

## <a name="move-a-registration-resource"></a>Spostare una risorsa di registrazione
Lo spostamento di una risorsa di registrazione tra gruppi di risorse nella stessa sottoscrizione **è** supportato per tutti gli ambienti. Tuttavia, lo spostamento di una risorsa di registrazione tra sottoscrizioni è supportato solo per i CSP quando entrambe le sottoscrizioni di risolvere per lo stesso ID di Partner. Per altre informazioni sullo spostamento delle risorse in un nuovo gruppo di risorse, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Riferimento di registrazione

### <a name="set-azsregistration"></a>Set-AzsRegistration

È possibile usare Set-AzsRegistration registrare Azure Stack con Azure e abilitare o disabilitare l'offerta di elementi in marketplace e report sull'utilizzo.

Per eseguire il cmdlet, è necessario:
- Una sottoscrizione di Azure globale di qualsiasi tipo.
- È necessario anche accedere Azure PowerShell con un account che sia proprietario o collaboratore alla sottoscrizione.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| Parametro | type | DESCRIZIONE |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Le credenziali utilizzate per [accedere all'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Il nome utente è nel formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | string | Una preconfigurati in modo remota console di PowerShell che fornisce le funzionalità di raccolta dei log e di post-attività di distribuzione. Per altre informazioni, vedere la [usando l'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) articolo. |
| AzureContext | Oggetto PSObject |  |
| ResourceGroupName | string |  |
| ResourceGroupLocation | string |  |
| BillingModel | string | Il modello di fatturazione che usa la sottoscrizione. I valori consentiti per questo parametro sono: Capacità PayAsYouUse e sviluppo. |
| MarketplaceSyndicationEnabled | True/False | Determina se la funzionalità di gestione di marketplace è disponibile nel portale. Impostare su true se la registrazione con connettività internet. Impostare su false se la registrazione in ambienti disconnessi. Per le registrazioni disconnesse, il [dello strumento di diffusione offline](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) può essere usato per il download di elementi del marketplace. |
| UsageReportingEnabled | True/False | Azure Stack riporta le metriche di utilizzo per impostazione predefinita. Gli operatori con utilizza la capacità o che supportano un ambiente disconnesso saranno necessario disattivare la segnalazione di utilizzo. I valori consentiti per questo parametro sono: È true, False. |
| AgreementNumber | string |  |
| registrationName | string | Impostare un nome univoco per la registrazione se si esegue lo script di registrazione in più di un'istanza di Azure Stack usando la stessa sottoscrizione di Azure ID. Il parametro ha un valore predefinito pari **AzureStackRegistration**. Tuttavia, se si usa lo stesso nome in più di un'istanza di Azure Stack, lo script avrà esito negativo. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken genererà un token di registrazione dai parametri di input.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parametro | type | DESCRIZIONE |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Le credenziali utilizzate per [accedere all'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Il nome utente è nel formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | string |  Una preconfigurati in modo remota console di PowerShell che fornisce le funzionalità di raccolta dei log e di post-attività di distribuzione. Per altre informazioni, vedere la [usando l'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) articolo. |
| AzureContext | Oggetto PSObject |  |
| ResourceGroupName | string |  |
| ResourceGroupLocation | string |  |
| BillingModel | string | Il modello di fatturazione che usa la sottoscrizione. I valori consentiti per questo parametro sono: Capacità PayAsYouUse e sviluppo. |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | Azure Stack riporta le metriche di utilizzo per impostazione predefinita. Gli operatori con utilizza la capacità o che supportano un ambiente disconnesso saranno necessario disattivare la segnalazione di utilizzo. I valori consentiti per questo parametro sono: È true, False. |
| AgreementNumber | string |  |


## <a name="next-steps"></a>Passaggi successivi

[Scaricare elementi di marketplace di Azure](azure-stack-download-azure-marketplace-item.md)
