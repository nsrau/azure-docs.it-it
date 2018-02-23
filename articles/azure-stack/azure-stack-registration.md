---
title: Registrazione di Azure per lo Stack di Azure integrati sistemi | Documenti Microsoft
description: "Descrive il processo di registrazione di Azure per le distribuzioni di Azure Stack Azure connesse a più nodi."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d5b77bb43c48bd286708ca96699b20be0f761baa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure
È possibile registrare dello Stack di Azure con Azure per scaricare gli elementi di marketplace da Azure e per impostare i dati di commerce segnalazioni a Microsoft. Dopo la registrazione dello Stack di Azure, viene segnalato l'utilizzo per Azure commerce. È possibile visualizzarlo nella sottoscrizione che è usata per la registrazione.

> [!IMPORTANT]
> La registrazione è obbligatoria se si sceglie il modello di fatturazione retribuzione come-di-uso. In caso contrario, sarà in violazione delle condizioni di licenza della distribuzione Azure Stack come l'utilizzo in caso contrario non verranno segnalato.

## <a name="before-you-register-azure-stack-with-azure"></a>Prima di registrare dello Stack di Azure con Azure
Prima di registrare dello Stack di Azure con Azure, è necessario disporre di:

- L'ID di sottoscrizione per una sottoscrizione di Azure. Per ottenere l'ID, accedere a Azure, fare clic su **più servizi** > **sottoscrizioni**, fare clic sulla sottoscrizione che si desidera utilizzare, quindi in **Essentials** è possibile trovare il ID della sottoscrizione. 

  > [!NOTE]
  > Cina, Germania e noi sottoscrizioni cloud per enti pubblici non sono attualmente supportati. 

- Il nome utente e password per un account che è un proprietario per la sottoscrizione (sono supportati account MSA/2FA)
- *Non richiesto a partire dalla versione di aggiornamento di Azure Stack 1712 (180106.1)*: Azure Active Directory per la sottoscrizione di Azure. È possibile trovare la directory in Azure passando sul proprio avatar in alto a destra del portale di Azure. 
- Registrare il provider di risorse di Azure Stack (vedere la sezione di registrare Provider di risorse Stack Azure seguente per informazioni dettagliate)

Se non si dispone di una sottoscrizione di Azure che soddisfa questi requisiti, è possibile [creare un account gratuito di Azure qui](https://azure.microsoft.com/free/?b=17.06). Registrazione dello Stack di Azure non comporta alcun costo nella sottoscrizione di Azure.

### <a name="bkmk_powershell"></a>Installare PowerShell per Azure Stack
È necessario utilizzare la versione più recente di PowerShell per lo Stack di Azure per registrare il sistema con Azure.

Se non già installato, [installare PowerShell per Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Scaricare gli strumenti di Azure Stack
Il repository di GitHub Stack Azure tools contiene i moduli di PowerShell che supportano la funzionalità di Azure Stack; inclusa la funzionalità di registrazione. Durante la registrazione del processo è necessario importare e utilizzare il modulo di PowerShell RegisterWithAzure.psm1, trovato nel repository strumenti dello Stack di Azure, per registrare l'istanza dello Stack di Azure con Azure. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Registro dello Stack di Azure in ambienti connessi
Ambienti connessi possono accedere a internet e Azure. Per questi ambienti, è necessario registrare il provider di risorse dello Stack di Azure con Azure e quindi configurare il modello di fatturazione.

### <a name="register-the-azure-stack-resource-provider"></a>Registrare il provider di risorse di Azure Stack
Per registrare il provider di risorse dello Stack di Azure con Azure, avviare Powershell ISE come amministratore e utilizzare i seguenti comandi di PowerShell. Questi comandi verranno:
- Richiedere di eseguire l'accesso come proprietario della sottoscrizione di Azure per essere utilizzato e impostare il `EnvironmentName` parametro **cloud**.
- Registrare il provider di risorse di Azure **Microsoft.AzureStack**.

PowerShell per eseguire:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrazione dello Stack di Azure con Azure utilizzando il modello di fatturazione retribuzione come-di-utilizzo
Utilizzare la procedura seguente per registrare dello Stack di Azure con Azure utilizzando il modello di fatturazione retribuzione come-di-uso.

Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory creati quando si [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importazione di **RegisterWithAzure.psm1** modulo con PowerShell: 

PowerShell per eseguire:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
Quindi, nella stessa sessione di PowerShell, eseguire il **Set AzsRegistration** cmdlet. Quando vengono richieste le credenziali, specificare il proprietario della sottoscrizione di Azure.  

PowerShell per eseguire:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Parametro|DESCRIZIONE|
|-----|-----|
|CloudAdminCredential|Oggetto PowerShell che contiene informazioni sulle credenziali (nome utente e password) per il proprietario della sottoscrizione di Azure.|
|PrivilegedEndpoint|Una preconfigurato PowerShell console remota che fornisce le funzionalità, ad esempio la raccolta di log e di post-attività di distribuzione. Per ulteriori informazioni, vedere il [utilizzando l'endpoint con privilegi](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint) articolo.|
|BillingModel|Il modello di fatturazione che usa la sottoscrizione. I valori per questo parametro consentiti sono: capacità, PayAsYouUse e sviluppo.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registro dello Stack di Azure con Azure utilizzando il modello di fatturazione di capacità
Seguire le stesse istruzioni usate per la registrazione con il modello di fatturazione, pagamento come-di-uso ma il numero di contratto in cui è stata acquistata capacità di aggiungere e modificare il `BillingModel` parametro **capacità**. Tutti gli altri parametri non vengono modificate.

PowerShell per eseguire:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registro dello Stack di Azure in ambienti disconnessi 
*Le informazioni contenute in questa sezione si applica a partire dalla versione di aggiornamento di Azure Stack 1712 (180106.1) e non sono supportate con le versioni precedenti.*

Se si sta registrando dello Stack di Azure in un ambiente disconnesso (con nessuna connettività a internet), è necessario ottenere una registrazione token dall'ambiente dello Stack di Azure e quindi usare tale token in un computer che possa connettersi ad Azure e ha [PowerShell per lo Stack di Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Ottenere una registrazione token dall'ambiente di Azure Stack
  1. Per ottenere il token di registrazione, eseguire i comandi di PowerShell seguenti:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Il token di registrazione viene salvato nel file specificato per *$env:SystemDrive\RegistrationToken.txt*.

  2. Salvare il token di registrazione per l'uso in Azure macchina connessa.


### <a name="connect-to-azure-and-register"></a>Connettersi ad Azure e di registro
Avviare PowerShell ISE come amministratore e passare al **registrazione** cartella il **AzureStack-strumenti-master** directory creati quando si [scaricato gli strumenti di Azure Stack](#bkmk_tools). Importazione di **RegisterWithAzure.psm1** modulo: 

PowerShell per eseguire:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
Successivamente, nella stessa sessione di PowerShell, specificare il token di registrazione per registrare con Azure:

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
> Salvare il nome di risorsa di registrazione o il token di registrazione per riferimento futuro.

## <a name="verify-azure-stack-registration"></a>Verificare la registrazione di Azure Stack
Usare questi passaggi per verificare che Azure Stack sia registrato correttamente con Azure.
1. Accedi allo Stack di Azure [portale dell'amministratore](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https &#58; / / adminportal. *&lt;area >. &lt;fqdn >*.
2. Fare clic su **più servizi** > **gestione Marketplace** > **aggiungere da Azure**.

Se viene visualizzato un elenco di prodotti disponibili in Azure (ad esempio WordPress), l'attivazione è riuscita.

> [!NOTE]
> Una volta completata la registrazione, l'avviso attivo per la registrazione non verrà più visualizzato.

## <a name="renew-or-change-registration"></a>Rinnovare o modificare la registrazione
È necessario aggiornare o rinnovare la registrazione nelle circostanze seguenti:
- Dopo aver rinnovato la sottoscrizione annua basata sulla capacità.
- Quando si modifica il modello di fatturazione.
- Quando si scala modifiche (aggiunta o rimozione di nodi) per la fatturazione basata sulla capacità.

### <a name="change-the-subscription-you-use"></a>Modificare la sottoscrizione che è utilizzare
Se si desidera modificare la sottoscrizione è utilizzare, è innanzitutto necessario eseguire il **Remove AzsRegistration** cmdlet, quindi verificare che si è connessi al contesto di Azure PowerShell corretto e infine eseguire **Set AzsRegistration**  con i parametri per modificare:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Modificare le funzionalità di diffusione o di modello di fatturazione
Se si desidera modificare il modello di fatturazione o la funzionalità di diffusione per l'installazione, è possibile chiamare la funzione di registrazione per impostare i nuovi valori. Non occorre prima rimuovere la registrazione corrente: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>Rimuove una risorsa registrata
Se si desidera rimuovere una registrazione, quindi è necessario utilizzare **UnRegister-AzsEnvironment** cmdlet e passare il nome di risorsa di registrazione o la registrazione di token è stato utilizzato per **Register-AzsEnvironment**.

Per rimuovere una registrazione con un nome di risorsa:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
Per rimuovere una registrazione usando un token di registrazione:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

## <a name="next-steps"></a>Passaggi successivi

[Integrazione di monitoraggio esterno](azure-stack-integrate-monitor.md)