---
title: Registrazione di Azure per lo Stack di Azure integrati sistemi | Documenti Microsoft
description: Descrive il processo di registrazione di Azure per le distribuzioni di Azure Stack Azure connesse a più nodi.
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
ms.date: 06/01/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 4f1492180c31f69eb438b012cf489a5851189136
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714851"
---
# <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure
La registrazione [Azure Stack](azure-stack-poc.md) con Azure consente di scaricare gli elementi di marketplace da Azure e per impostare i dati di commerce segnalazioni a Microsoft. Dopo la registrazione dello Stack di Azure, viene segnalato l'utilizzo per Azure commerce ed è possibile visualizzare la sottoscrizione usata per la registrazione.

> [!IMPORTANT]
> La registrazione è necessario per supportare le funzionalità complete dello Stack di Azure, tra cui diffusione marketplace. Inoltre, sarà in violazione delle condizioni di licenza se non si registra quando si utilizza il modello di fatturazione retribuzione-come--l'utilizzo dello Stack di Azure. Per ulteriori informazioni sui modelli di licenza dello Stack di Azure, vedere la [come acquistare pagina](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Prerequisiti
Prima di registrare dello Stack di Azure con Azure, è necessario disporre di:

- L'ID di sottoscrizione per una sottoscrizione di Azure. Per ottenere l'ID, accedere a Azure, fare clic su **più servizi** > **sottoscrizioni**, fare clic sulla sottoscrizione che si desidera utilizzare, quindi in **Essentials** è possibile trovare il ID della sottoscrizione.

  > [!NOTE]
  > Germania e le sottoscrizioni cloud intendersi non sono attualmente supportate.

- Il nome utente e password per un account che è un proprietario per la sottoscrizione (sono supportati account MSA/2FA).
- Registrare il provider di risorse dello Stack di Azure (vedere la sezione di registrare Provider di risorse Stack Azure seguente per informazioni dettagliate).

Se non si dispone di una sottoscrizione di Azure che soddisfa questi requisiti, è possibile [creare un account gratuito di Azure qui](https://azure.microsoft.com/free/?b=17.06). Registrazione dello Stack di Azure non comporta alcun costo nella sottoscrizione di Azure.

### <a name="powershell-language-mode"></a>Modalità di linguaggio di PowerShell
Per registrare correttamente dello Stack di Azure, è necessario impostare la modalità di linguaggio di PowerShell **FullLanguageMode**.  Per verificare che la modalità di lingua corrente è impostata su full, aprire una finestra di PowerShell con privilegi elevata ed eseguire i comandi di PowerShell seguenti:

```powershell
$ExecutionContext.SessionState.LanguageMode
```
Verificare che l'output restituisce **FullLanguageMode**. Se viene restituita qualsiasi altra modalità di linguaggio, registrazione dovrà essere eseguito in un altro computer o la modalità di linguaggio dovrà essere impostata su **FullLanguageMode** prima di continuare.


### <a name="bkmk_powershell"></a>Installare PowerShell per Azure Stack
È necessario utilizzare la versione più recente di PowerShell per lo Stack di Azure per registrare con Azure.

Se non già installato, [installare PowerShell per Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Scaricare gli strumenti di Azure Stack
Il repository di GitHub Stack Azure tools contiene i moduli di PowerShell che supportano la funzionalità di Azure Stack; inclusa la funzionalità di registrazione. Durante il processo di registrazione, è necessario importare e usare il modulo PowerShell RegisterWithAzure.psm1, trovata nel repository di strumenti di Azure Stack, per registrare l'istanza dello Stack di Azure con Azure.

Per verificare che si utilizza la versione più recente, è necessario eliminare le versioni esistenti degli strumenti di Azure Stack e [scaricare la versione più recente da GitHub](azure-stack-powershell-download.md) prima di registrare con Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Registro dello Stack di Azure in ambienti connessi
Ambienti connessi possono accedere a internet e Azure. Per questi ambienti, è necessario registrare il provider di risorse dello Stack di Azure con Azure e quindi configurare il modello di fatturazione.

> [!NOTE]
> Da un computer dotato di accesso all'endpoint con privilegi, è necessario eseguire tutti questi passaggi.

### <a name="register-the-azure-stack-resource-provider"></a>Registrare il provider di risorse di Azure Stack
Per registrare il provider di risorse dello Stack di Azure con Azure, avviare PowerShell ISE come amministratore e utilizzare i seguenti comandi di PowerShell con il **EnvironmentName** parametro impostata sul tipo di sottoscrizione Azure appropriata (vedere parametri sotto).

1. Aggiungere l'account di Azure che consente di registrare dello Stack di Azure. Per aggiungere l'account, eseguire la **Add-AzureRmAccount** cmdlet. Viene chiesto di immettere le credenziali dell'account amministratore globale di Azure e potrebbe essere necessario utilizzare 2-factor authentication in base alla configurazione del tuo account.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parametro | DESCRIZIONE |  
   |-----|-----|
   | EnvironmentName | Il nome dell'ambiente sottoscrizione cloud di Azure. I nomi di ambiente supportati sono **cloud** o, se si utilizza una sottoscrizione di Azure, Cina **AzureChinaCloud**.  |
   |  |  |

2. Se si dispone di più sottoscrizioni, eseguire il comando seguente per selezionare quello per cui che si desidera utilizzare:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Eseguire il comando seguente per registrare il provider di risorse dello Stack di Azure nella sottoscrizione di Azure:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrazione dello Stack di Azure con Azure utilizzando il modello di fatturazione retribuzione come-di-utilizzo
Utilizzare questi passaggi per registrare dello Stack di Azure a usare Azure con il modello di fatturazione retribuzione-come--l'utilizzo.

1. Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory creati quando si [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importazione di **RegisterWithAzure.psm1** modulo con PowerShell:

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Successivamente, nella stessa sessione di PowerShell, assicurarsi di che essere connessi per il contesto di PowerShell Azure corretto. Si tratta dell'account di azure che è stata utilizzata per registrare il provider di risorse di Azure Stack precedente. PowerShell per eseguire:

  ```powershell
  Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
  ```

3. Nella stessa sessione di PowerShell, eseguire la **Set-AzsRegistration** cmdlet. PowerShell per eseguire:  

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
  |PrivilegedEndpoint|Una preconfigurato PowerShell console remota che fornisce le funzionalità, ad esempio la raccolta di log e di post-attività di distribuzione. Per ulteriori informazioni, vedere il [utilizzando l'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) articolo.|
  |BillingModel|Il modello di fatturazione che usa la sottoscrizione. I valori per questo parametro consentiti sono: capacità, PayAsYouUse e sviluppo.|
  |  |  |

  Il processo richiederà compreso tra 10 e 15 minuti. Al termine del comando, verrà visualizzato il messaggio **"l'ambiente è ora registrato e attivati mediante i parametri forniti."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registro dello Stack di Azure con Azure utilizzando il modello di fatturazione di capacità
Seguire le stesse istruzioni usate per la registrazione con il modello di fatturazione, pagamento-come--l'utilizzo, ma il numero di contratto in cui è stata acquistata capacità di aggiungere e modificare il **BillingModel** parametro **capacità**. Tutti gli altri parametri non vengono modificate.

PowerShell per eseguire:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registro dello Stack di Azure in ambienti disconnessi
*Le informazioni contenute in questa sezione si applica a partire dalla versione di aggiornamento di Azure Stack 1712 (180106.1) e non sono supportate con le versioni precedenti.*

Se si sta registrando dello Stack di Azure in un ambiente disconnesso (con nessuna connettività a internet), è necessario ottenere una registrazione token dall'ambiente dello Stack di Azure e quindi usare tale token in un computer che possa connettersi ad Azure e ha [PowerShell per lo Stack di Azure](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Ottenere una registrazione token dall'ambiente di Azure Stack

1. Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory creati quando si [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importazione di **RegisterWithAzure.psm1** modulo:  

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Per ottenere il token di registrazione, eseguire i comandi di PowerShell seguenti:  

  ```Powershell
  $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
  $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
  ```

  > [!TIP]  
  > Il token di registrazione viene salvato nel file specificato per *$FilePathForRegistrationToken*. È possibile modificare il percorso file o nome file a propria discrezione.

3. Salvare il token di registrazione per l'uso in Azure macchina connessa. È possibile copiare il file o il testo da $FilePathForRegistrationToken.


### <a name="connect-to-azure-and-register"></a>Connettersi ad Azure e di registro
Nel computer che è connesso a internet, eseguire gli stessi passaggi per importare il modulo RegisterWithAzure.psm1 e l'account di accesso per il contesto di Azure Powershell corretto. Quindi, chiamare Register AzsEnvironment e specificare il token di registrazione per registrare con Azure:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
Facoltativamente, è possibile utilizzare il cmdlet Get-Content per puntare a un file che contiene il token di registrazione:

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
  > [!NOTE]  
  > Salvare il nome di risorsa di registrazione e il token di registrazione per riferimento futuro.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperare una chiave di attivazione dalla risorsa di registrazione di Azure
Successivamente, è necessario recuperare la risorsa di registrazione creata in Azure durante la registrazione AzsEnvironment una chiave di attivazione.

Per ottenere la chiave di attivazione, eseguire i comandi di PowerShell seguenti:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```
  > [!TIP]   
  > La chiave di attivazione viene salvata nel file specificato per *$KeyOutputFilePath*. È possibile modificare il percorso file o nome file a propria discrezione.

### <a name="create-an-activation-resource-in-azure-stack"></a>Creare una risorsa di attivazione nello Stack di Azure
Ripristinare l'ambiente dello Stack di Azure con i file di testo dalla chiave di attivazione creata da Get-AzsActivationKey. Si creerà una risorsa di attivazione nello Stack di Azure utilizzando la chiave di attivazione. Per creare una risorsa di attivazione eseguire i comandi di PowerShell seguenti:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Facoltativamente, è possibile utilizzare il cmdlet Get-Content per puntare a un file che contiene il token di registrazione:

  ```Powershell   
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Verificare la registrazione di Azure Stack
Usare questi passaggi per verificare che Azure Stack sia registrato correttamente con Azure.
1. Accedi allo Stack di Azure [portale dell'amministratore](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;area >. &lt;fqdn >*.
2. Fare clic su **più servizi** > **gestione Marketplace** > **aggiungere da Azure**.

Se viene visualizzato un elenco di prodotti disponibili in Azure (ad esempio WordPress), l'attivazione è riuscita. Tuttavia, in ambienti disconnessi è non visualizzeranno gli elementi di Azure marketplace in marketplace dello Stack di Azure.

> [!NOTE]
> Una volta completata la registrazione, l'avviso attivo per la registrazione non verrà più visualizzato.

## <a name="renew-or-change-registration"></a>Rinnovare o modificare la registrazione
### <a name="renew-or-change-registration-in-connected-environments"></a>Rinnovare o modificare la registrazione in ambienti connessi
È necessario aggiornare o rinnovare la registrazione nelle circostanze seguenti:
- Dopo aver rinnovato la sottoscrizione annua basata sulla capacità.
- Quando si modifica il modello di fatturazione.
- Quando si scala modifiche (aggiunta o rimozione di nodi) per la fatturazione basata sulla capacità.

#### <a name="change-the-subscription-you-use"></a>Modificare la sottoscrizione che è utilizzare
Se si desidera modificare la sottoscrizione è utilizzare, è innanzitutto necessario eseguire il **Remove AzsRegistration** cmdlet, quindi verificare che si è connessi al contesto di Azure PowerShell corretto e infine eseguire **Set AzsRegistration**  con i parametri per modificare:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Modificare le funzionalità di diffusione o di modello di fatturazione
Se si desidera modificare il modello di fatturazione o la funzionalità di diffusione per l'installazione, è possibile chiamare la funzione di registrazione per impostare i nuovi valori. Non occorre prima rimuovere la registrazione corrente:

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Rinnovare o modificare la registrazione in ambienti disconnessi
È necessario aggiornare o rinnovare la registrazione nelle circostanze seguenti:
- Dopo aver rinnovato la sottoscrizione annua basata sulla capacità.
- Quando si modifica il modello di fatturazione.
- Quando si scala modifiche (aggiunta o rimozione di nodi) per la fatturazione basata sulla capacità.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Rimuovere la risorsa di attivazione dallo Stack di Azure
È necessario innanzitutto rimuovere la risorsa di attivazione dallo Stack di Azure e quindi la risorsa di registrazione in Azure.  

Per rimuovere la risorsa di attivazione nello Stack di Azure, eseguire i comandi di PowerShell seguenti Stack di Azure nell'ambiente in uso:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Successivamente, verificare che si è connessi a una connessione di Azure per rimuovere la risorsa di registrazione in Azure, computer, accedere al contesto di Azure PowerShell corretto ed eseguire i comandi di PowerShell appropriati come descritto di seguito.

È possibile utilizzare il token di registrazione utilizzato per creare la risorsa:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Oppure è possibile utilizzare il nome di registrazione:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Ripetere la registrazione usando passaggi disconnessi
Si sono ora completamente annullata la registrazione in uno scenario disconnesso e devono ripetere i passaggi per la registrazione di un ambiente dello Stack di Azure in uno scenario disconnesso.

## <a name="next-steps"></a>Passaggi successivi

[Scaricare gli elementi di marketplace da Azure](azure-stack-download-azure-marketplace-item.md)
