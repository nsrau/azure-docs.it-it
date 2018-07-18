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
ms.date: 07/09/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 65525ffe33ddc100dd3066e7c2b52ef8a856fbc3
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934772"
---
# <a name="register-azure-stack-with-azure"></a>Registrare Azure Stack con Azure

La registrazione [Azure Stack](azure-stack-poc.md) con Azure consente di scaricare elementi di marketplace di Azure e per impostare i dati di e-commerce segnalazioni a Microsoft. Dopo la registrazione di Azure Stack, viene segnalato l'utilizzo di e-commerce di Azure e può essere visualizzato con la sottoscrizione usata per la registrazione.

> [!IMPORTANT]  
> È necessario eseguire la registrazione per supportare la funzionalità di Azure Stack completa, tra cui diffusione di marketplace. Inoltre, sarà in violazione delle condizioni di licenza, se non si registrano quando si usa il modello di fatturazione di come è a pagamento di Azure Stack. Per altre informazioni sui modelli di licenza di Azure Stack, vedere la [modalità di acquisto pagina](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Prerequisiti

Prima di registrare Azure Stack con Azure, è necessario disporre di:

- L'ID sottoscrizione per una sottoscrizione di Azure. Per ottenere l'ID, accedere ad Azure, fare clic su **altri servizi** > **sottoscrizioni**, scegliere la sottoscrizione da usare, quindi in **Essentials** è possibile trovare il ID della sottoscrizione.

  > [!NOTE]
  > Sottoscrizioni cloud US Government e Germania non sono attualmente supportate.

- Il nome utente e la password per un account che è un proprietario per la sottoscrizione (sono supportati gli account MSA/2FA).
- Registrato il provider di risorse di Azure Stack (vedere la sezione di registrazione del Provider di risorse Azure Stack di seguito per informazioni dettagliate).

Se non hai una sottoscrizione di Azure che soddisfa questi requisiti, è possibile [crea qui un account Azure gratuito](https://azure.microsoft.com/free/?b=17.06). La registrazione di Azure Stack viene addebitata alcuna tariffa nella sottoscrizione di Azure.

### <a name="powershell-language-mode"></a>Modalità di linguaggio di PowerShell

Per registrare correttamente Azure Stack, la modalità di linguaggio di PowerShell deve essere impostata su **FullLanguageMode**.  Per verificare che la modalità linguaggio corrente è impostata su full, aprire una finestra di PowerShell con privilegi elevata ed eseguire i comandi PowerShell seguenti:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Verificare che l'output restituisce **FullLanguageMode**. Se viene restituita qualsiasi altra modalità di linguaggio, registrazione dovrà essere eseguito in un altro computer o la modalità di linguaggio dovrà essere impostata su **FullLanguageMode** prima di continuare.

### <a name="bkmk_powershell"></a>Installare PowerShell per Azure Stack

È necessario usare la versione più recente di PowerShell per Azure Stack per registrazione in Azure.

Se non è già installato, [installazione di PowerShell per Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Scaricare gli strumenti di Azure Stack

Il repository GitHub di Azure Stack degli strumenti contiene i moduli di PowerShell che supportano la funzionalità di Azure Stack. inclusa la funzionalità di registrazione. Durante il processo di registrazione, è necessario importare e usare il modulo PowerShell RegisterWithAzure.psm1, trovata nel repository di strumenti di Azure Stack, registrare l'istanza di Azure Stack con Azure.

Per assicurarsi di usare la versione più recente, è necessario eliminare eventuali versioni esistenti di strumenti di Azure Stack e [scaricare la versione più recente da GitHub](azure-stack-powershell-download.md) prima di registrare con Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Registrare Azure Stack in ambienti connessi

Ambienti connessi possono accedere a internet e Azure. Per questi ambienti, è necessario registrare il provider di risorse di Azure Stack con Azure e quindi configurare il modello di fatturazione.

> [!NOTE]
> Tutti questi passaggi devono essere eseguiti da un computer dotato di accesso all'endpoint con privilegi.

### <a name="register-the-azure-stack-resource-provider"></a>Registrare il provider di risorse di Azure Stack

Per registrare il provider di risorse di Azure Stack con Azure, avviare PowerShell ISE come amministratore e usare i comandi di PowerShell seguenti con il **EnvironmentName** parametro impostato sul tipo di sottoscrizione Azure appropriata (vedere parametri seguenti).

1. Aggiungere l'account di Azure da usare per registrarsi in Azure Stack. Per aggiungere l'account, eseguire la **Add-AzureRmAccount** cmdlet. Viene chiesto di immettere le credenziali dell'account amministratore globale di Azure e potrebbe essere necessario usare l'autenticazione a 2 fattori in base alla configurazione dell'account.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parametro | DESCRIZIONE |  
   |-----|-----|
   | EnvironmentName | Il nome dell'ambiente sottoscrizione cloud di Azure. I nomi di ambiente supportati sono **AzureCloud** o, se si usa una sottoscrizione di Azure Cina **AzureChinaCloud**.  |
   |  |  |

2. Se si hanno più sottoscrizioni, eseguire il comando seguente per selezionare quello da usare:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Eseguire il comando seguente per registrare il provider di risorse di Azure Stack nella sottoscrizione di Azure:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrare Azure Stack con Azure usando il modello di fatturazione di come si-pagamento

Usare questi passaggi per registrare Azure Stack con Azure usando il modello di fatturazione di come è a pagamento.

1. Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory create quando è [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importa i **RegisterWithAzure.psm1** modulo con PowerShell:

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Successivamente, nella stessa sessione di PowerShell, assicurarsi che si è connessi al contesto di PowerShell di Azure corretta. Si tratta dell'account di azure che è stato usato per registrare il provider di risorse di Azure Stack precedente. PowerShell per l'esecuzione:

   ```powershell
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. Nella stessa sessione di PowerShell, eseguire la **Set-AzsRegistration** cmdlet. PowerShell per l'esecuzione:  

   ```powershell
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
   ```

  |Parametro|DESCRIZIONE|
  |-----|-----|
  |PrivilegedEndpointCredential|Le credenziali utilizzate per [accedere all'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Il nome utente è nel formato **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Una preconfigurati in modo remota console di PowerShell che fornisce le funzionalità di raccolta dei log e di post-attività di distribuzione. Per altre informazioni, vedere la [usando l'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) articolo.|
  |BillingModel|Il modello di fatturazione che usa la sottoscrizione. I valori per questo parametro consentiti sono: capacità PayAsYouUse e sviluppo.|
  |  |  |

  Il processo richiederà da 10 a 15 minuti. Al termine del comando, si vedranno il messaggio **"dell'ambiente è ora registrato e attivati mediante i parametri forniti."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registrare Azure Stack con Azure usando il modello di fatturazione di capacità

Seguire le stesse istruzioni usate per eseguire la registrazione tramite il modello di fatturazione di come è a pagamento, ma il numero di contratto in cui è stata acquistata la capacità di aggiungere e modificare il **BillingModel** parametro per **capacità**. Tutti gli altri parametri sono identiche.

PowerShell per l'esecuzione:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrare Azure Stack in ambienti non connessi
Se si sta registrando Azure Stack in un ambiente disconnesso (con nessuna connettività a internet), è necessario ottenere una registrazione token dall'ambiente Azure Stack e quindi usare il token in un computer che possa connettersi ad Azure e ha [PowerShell per Azure Stack installato](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Ottenere una registrazione token dall'ambiente Azure Stack

1. Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory create quando è [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importa i **RegisterWithAzure.psm1** modulo:  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Per ottenere il token di registrazione, eseguire i comandi PowerShell seguenti:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > Il token di registrazione viene salvato nel file specificato per *$FilePathForRegistrationToken*. È possibile modificare il percorso file o file a propria discrezione.

3. Salvare questo token di registrazione per l'uso in Azure connessa macchina. È possibile copiare il file o il testo da $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Connettersi ad Azure e registrazione

Nel computer che è connesso a internet, eseguire la stessa procedura per importare il modulo RegisterWithAzure.psm1 e account di accesso per il contesto di Azure Powershell corretto. Quindi chiamare Register-AzsEnvironment e specificare il token di registrazione da registrare con Azure:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

Facoltativamente, è possibile usare il cmdlet Get-Content per puntare a un file che contiene il token di registrazione:

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

  > [!NOTE]  
  > Salvare il nome di risorsa di registrazione e il token di registrazione per riferimento futuro.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperare una chiave di attivazione dalla risorsa di registrazione di Azure

Successivamente, è necessario recuperare una chiave di attivazione dalla risorsa di registrazione creata in Azure durante la registrazione AzsEnvironment.

Per ottenere la chiave di attivazione, eseguire i comandi PowerShell seguenti:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > La chiave di attivazione viene salvata nel file specificato per *$KeyOutputFilePath*. È possibile modificare il percorso file o file a propria discrezione.

### <a name="create-an-activation-resource-in-azure-stack"></a>Crea una risorsa di attivazione in Azure Stack

Tornare all'ambiente di Azure Stack con il file o il testo dalla chiave di attivazione creata da Get-AzsActivationKey. Si creerà una risorsa di attivazione in Azure Stack tramite tale chiave di attivazione. Per creare una risorsa di attivazione, eseguire i comandi di PowerShell seguenti:  

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

Usare questi passaggi per verificare che Azure Stack è stato registrato con Azure.

1. Accedi ad Azure Stack [portale di amministrazione](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;area >. &lt;fqdn >*.
2. Selezionare **altri servizi** > **Management Marketplace** > **aggiungere da Azure**.

Se viene visualizzato un elenco di elementi disponibili in Azure (ad esempio WordPress), l'attivazione è riuscita. Tuttavia, in ambienti disconnessi non noterai elementi del marketplace di Azure nel marketplace di Azure Stack.

> [!NOTE]
> Dopo aver completata la registrazione, il messaggio di avviso attiva per la registrazione non verrà più visualizzato.

## <a name="renew-or-change-registration"></a>Rinnova o Modifica registrazione

### <a name="renew-or-change-registration-in-connected-environments"></a>Rinnova o modificare la registrazione in ambienti connessi

È necessario aggiornare o rinnovare la registrazione nelle circostanze seguenti:

- Dopo aver rinnovare l'abbonamento annua basato sulla capacità.
- Quando si modifica il modello di fatturazione.
- Quando si ridimensiona le modifiche apportate (aggiunta/rimozione di nodi) per la fatturazione basata sulla capacità.

#### <a name="change-the-subscription-you-use"></a>Modificare la sottoscrizione usata

Se si desidera modificare la sottoscrizione è utilizzare, è necessario eseguire prima la **Remove-AzsRegistration** cmdlet, quindi verificare che si è connessi al contesto di Azure PowerShell corretto e infine eseguire **Set-AzsRegistration**  con uno qualsiasi per modificare i parametri:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Modificare le funzionalità di syndication o modello di fatturazione

Se si desidera modificare il modello di fatturazione o la funzionalità di diffusione per l'installazione, è possibile chiamare la funzione di registrazione per impostare i nuovi valori. Non è necessario innanzitutto rimuovere la registrazione corrente:

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Rinnova o Modifica registrazione in ambienti non connessi

È necessario aggiornare o rinnovare la registrazione nelle circostanze seguenti:

- Dopo aver rinnovare l'abbonamento annua basato sulla capacità.
- Quando si modifica il modello di fatturazione.
- Quando si ridimensiona le modifiche apportate (aggiunta/rimozione di nodi) per la fatturazione basata sulla capacità.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Rimuovere la risorsa di attivazione da Azure Stack

È necessario innanzitutto rimuovere la risorsa di attivazione da Azure Stack e quindi la risorsa di registrazione in Azure.  

Per rimuovere la risorsa di attivazione in Azure Stack, eseguire i comandi di PowerShell seguenti nell'ambiente Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Successivamente, per rimuovere la risorsa di registrazione in Azure, assicurarsi di usare un'istanza di Azure connesse computer, accedere al contesto di Azure PowerShell corretto ed eseguire i comandi di PowerShell appropriati come descritto di seguito.

È possibile usare il token di registrazione utilizzato per creare la risorsa:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Oppure è possibile usare il nome di registrazione:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Ripetere la registrazione con passaggi disconnessi

Ora sono completamente annullata in una situazione di disconnessione e si deve ripetere i passaggi per la registrazione di un ambiente Azure Stack in una situazione di disconnessione.

## <a name="next-steps"></a>Passaggi successivi

[Scaricare elementi di marketplace di Azure](azure-stack-download-azure-marketplace-item.md)
