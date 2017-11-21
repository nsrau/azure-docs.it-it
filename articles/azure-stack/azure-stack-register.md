---
title: Registro dello Stack di Azure | Documenti Microsoft
description: Registro dello Stack di Azure con la sottoscrizione di Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registro dello Stack di Azure con la sottoscrizione di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile registrare [Azure Stack](azure-stack-poc.md) con Azure per scaricare gli elementi di marketplace da Azure e per impostare i dati di commerce segnalazioni a Microsoft. 

> [!NOTE]
>La registrazione è consigliata perché consente di testare importanti funzionalità di Stack di Azure, ad esempio diffusione marketplace e report di utilizzo. Dopo la registrazione dello Stack di Azure, viene segnalato l'utilizzo per Azure commerce. È possibile visualizzarlo nella sottoscrizione che è usata per la registrazione. Gli utenti di Azure il Kit di sviluppo dello Stack non sono addebitati qualsiasi utilizzo che fanno.
>


## <a name="get-azure-subscription"></a>Ottenere una sottoscrizione di Azure

Prima di registrare dello Stack di Azure con Azure, è necessario disporre di:

- L'ID di sottoscrizione per una sottoscrizione di Azure. Per ottenere l'ID, accedere a Azure, fare clic su **più servizi** > **sottoscrizioni**, fare clic sulla sottoscrizione che si desidera utilizzare, quindi in **Essentials** è possibile trovare il **ID sottoscrizione**. Cina, Germania e noi sottoscrizioni cloud per enti pubblici non sono attualmente supportati.
- Il nome utente e password per un account che è un proprietario per la sottoscrizione (sono supportati account MSA/2FA).
- Azure Active Directory per la sottoscrizione di Azure. È possibile trovare la directory in Azure passando sul proprio avatar in alto a destra del portale di Azure. 

Se non si dispone di una sottoscrizione di Azure che soddisfa questi requisiti, è possibile [creare un account gratuito di Azure qui](https://azure.microsoft.com/en-us/free/?b=17.06). Registrazione dello Stack di Azure non comporta alcun costo nella sottoscrizione di Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registrare il provider di risorse di Azure Stack in Azure
> [!NOTE] 
> Questo passaggio deve essere completato solo una volta in un ambiente dello Stack di Azure.
>

1. Avviare una sessione di Powershell come amministratore.
2. Accedi all'account di Azure che è un proprietario della sottoscrizione di Azure (è possibile utilizzare il cmdlet AzureRmAccount di account di accesso per accedere e quando si accede, assicurarsi di impostare il parametro - EnvironmentName "Cloud").
3. Registrare il provider di risorse di Azure "Microsoft.AzureStack".

**Esempio:** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure

> [!NOTE]
> Tutti questi passaggi devono essere eseguiti da un computer dotato di accesso all'endpoint con privilegi. Per Azure Stack Development Kit, sarebbe il computer host. Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure.
>

1. Aprire la console PowerShell come amministratore e [installare PowerShell per Azure Stack](azure-stack-powershell-install.md).  

2. Aggiungere l'account di Azure che si utilizzerà la registrazione dello Stack di Azure. A tale scopo, eseguire il `Add-AzureRmAccount` cmdlet senza parametri. Viene richiesto di immettere le credenziali dell'account Azure e potrebbe essere necessario utilizzare 2-factor authentication in base alla configurazione dell'account.  

3. Se si dispone di più sottoscrizioni, eseguire il comando seguente per selezionare quello per cui che si desidera utilizzare:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Eliminare tutte le versioni esistenti dei moduli di Powershell che corrispondono alla registrazione e [scaricare la versione più recente da GitHub](azure-stack-powershell-download.md).  

5. Dalla directory "AzureStack-strumenti-master" che viene creata nel passaggio precedente, passare alla cartella "Registration" e importare il modulo ".\RegisterWithAzure.psm1":  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. Nella stessa sessione di PowerShell, eseguire lo script seguente. Quando vengono richieste le credenziali, specificare `azurestack\cloudadmin` come l'utente e la password è lo stesso di quello utilizzato per l'amministratore locale durante la distribuzione.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Parametro | Descrizione |
   | -------- | ------------- |
   | CloudAdminCredential | Le credenziali di dominio cloud che vengono utilizzate per [accedere all'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Il nome utente è nel formato "\<dominio Azure Stack\>\cloudadmin". Per il kit di sviluppo, il nome utente è impostato su "azurestack\cloudadmin". Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure per ottenere questo valore.|
   | AzureSubscriptionId | La sottoscrizione di Azure che consente di registrare dello Stack di Azure.|
   | AzureDirectoryTenantName | Nome della directory del tenant di Azure associato alla sottoscrizione di Azure. La risorsa di registrazione verrà creata in questo tenant di directory. |
   | PrivilegedEndpoint | Una preconfigurato PowerShell console remota che fornisce le funzionalità, ad esempio la raccolta di log e di post-attività di distribuzione. Kit di sviluppo, per l'endpoint con privilegi è ospitato nella macchina virtuale "AzS ERCS01". Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure per ottenere questo valore. Per ulteriori informazioni, vedere il [utilizzando l'endpoint con privilegi](azure-stack-privileged-endpoint.md) argomento.|
   | BillingModel | Il modello di fatturazione che usa la sottoscrizione. I valori consentiti per questo parametro sono "Capacità", "PayAsYouUse" e "Development". Kit di sviluppo, per questo valore è impostato su "Development". Se si utilizza un sistema integrato, contattare l'operatore di Stack di Azure per ottenere questo valore. |

7. Al termine dello script, viene visualizzato un messaggio "attivare Azure Stack (questo passaggio può richiedere fino a 10 minuti)." 

## <a name="verify-the-registration"></a>Verificare la registrazione

1. Accedere al portale di amministrazione (https://adminportal.local.azurestack.external).
2. Fare clic su **più servizi** > **gestione Marketplace** > **aggiungere da Azure**.
3. Se viene visualizzato un elenco di prodotti disponibili in Azure (ad esempio WordPress), l'attivazione è riuscita.

> [!NOTE]
> Una volta completata la registrazione, l'avviso attivo per la registrazione non verrà più visualizzato.

## <a name="next-steps"></a>Passaggi successivi

[Connettersi ad Azure Stack](azure-stack-connect-azure-stack.md)

