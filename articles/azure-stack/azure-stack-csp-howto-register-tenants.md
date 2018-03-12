---
title: Aggiungere i tenant per l'utilizzo e fatturazione allo Stack di Azure | Documenti Microsoft
description: I passaggi necessari consente di aggiungere un utente finale dello Stack di Azure gestiti da un Provider di servizi Cloud.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 67e5a67d7cd5caf6bd4d2625969b139411d62696
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Aggiunta di un tenant per l'utilizzo e fatturazione allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo descrive i passaggi necessari aggiungere un utente finale allo Stack di Azure gestiti da un Provider del servizio Cloud (CSP). Quando al nuovo tenant utilizza le risorse, Azure Stack segnalerà utilizzo alla propria sottoscrizione di CSP.

CSP offrono spesso servizi a più clienti (tenant) nella loro distribuzione dello Stack di Azure. L'aggiunta di tenant per la registrazione dello Stack di Azure garantisce che sull'utilizzo di ogni tenant verrà segnalato e fatturato alla sottoscrizione CSP corrispondente. Se non si completano i passaggi descritti in questo articolo, per la sottoscrizione utilizzata per la registrazione iniziale dello Stack di Azure viene addebitato l'utilizzo di tenant. Prima di poter aggiungere un cliente finale allo Stack di Azure per l'utilizzo di rilevamento e di gestire i tenant, è necessario configurare Azure Stack come un provider CSP. Per procedure e le risorse, vedere [gestire informazioni sull'utilizzo e fatturazione per lo Stack di Azure come Provider di servizi Cloud](azure-stack-add-manage-billing-as-a-csp.md).

Nel diagramma seguente vengono illustrati i passaggi che è necessario che un provider CSP per l'abilitazione di un nuovo cliente per utilizzano lo Stack di Azure e per impostare l'utilizzo di rilevamento per il cliente. Aggiungendo gli utenti finali, sarà anche per gestire le risorse nello Stack di Azure. Sono disponibili due opzioni per la gestione delle relative risorse:

1. È possibile mantenere tenant del cliente finale e specificare le credenziali per la sottoscrizione di Azure Stack locale per gli utenti finali.  
2. O gli utenti finali è possono lavorare con la sottoscrizione in locale e aggiungere il provider CSP come guest con le autorizzazioni del proprietario.  

**Passaggi per aggiungere un cliente finale**

![Impostare i Provider di servizi Cloud per rilevamento dell'utilizzo e la gestione dell'account del cliente finale](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Creare un nuovo cliente Partner Center

Nel centro di Partner, creare una nuova sottoscrizione di Azure per il cliente. Per istruzioni, vedere [aggiungere un nuovo cliente](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Creare una sottoscrizione di Azure per gli utenti finali

Dopo aver creato un record dei clienti nel centro di Partner, è possibile vendere le sottoscrizioni per i prodotti nel catalogo. Per istruzioni, vedere [Create, sospendere o annullare le sottoscrizioni dei clienti](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Creare un utente guest nella directory del cliente finale

Se gli utenti finali gestisce il proprio account, creare un utente guest nella propria directory e li inviano le informazioni. L'utente finale verrà quindi aggiungere il guest e l'autorizzazione di guest per elevare **proprietario** all'account Azure Stack CSP.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Aggiornare la registrazione con la sottoscrizione del cliente finale

Aggiornare la registrazione con la nuova sottoscrizione del cliente. Azure segnala l'identità del cliente da Partner centrale dell'utilizzo del cliente. Questo passaggio garantisce che l'utilizzo di ciascun cliente viene segnalato nella sottoscrizione CSP singoli quel cliente. Monitoraggio dell'utilizzo di utente e la fatturazione questo rende molto più semplice.

> [!Note]  
> Per eseguire questo passaggio, è necessario disporre [registrato Azure Stack](azure-stack-register.md).

1. Aprire Windows PowerShell con un prompt dei comandi con privilegi elevati ed eseguire:  
    `Login-AzureRmAccount`
2. Digitare le credenziali di Azure.
3. Nella sessione di PowerShell, eseguire:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```
### <a name="new-azurermresource-powershell-parameters"></a>Parametri-AzureRmResource nuovo PowerShell
| Parametro | Descrizione |
| --- | --- | 
|registrationSubscriptionID | La sottoscrizione di Azure che è stata utilizzata per la registrazione iniziale dello Stack di Azure. |
| customerSubscriptionID | La sottoscrizione di Azure (non Azure Stack) che appartengono al cliente da registrare. Deve essere creato nell'offerta di provider CSP; in pratica, ciò significa tramite Partner Center. Se un cliente ha più di un tenant di Azure Active Directory, è necessario creare la sottoscrizione nel tenant che verrà utilizzato per accedere allo Stack di Azure.
| resourceGroup | Il gruppo di risorse in Azure in cui è archiviata la registrazione. 
| registrationName | Il nome della registrazione dello Stack di Azure. È un oggetto archiviato in Azure. | 


> [!Note]  
> I tenant devono essere registrati con ogni utilizzano lo Stack di Azure. Se si dispone di due distribuzioni di Azure Stack e un tenant è prevede di utilizzare entrambi gli elementi, è necessario aggiornare le registrazioni iniziale di ogni distribuzione con la sottoscrizione tenant.

## <a name="onboard-tenant-to-azure-stack"></a>Caricare tenant allo Stack di Azure

Configurare lo Stack di Azure per supportare gli utenti da più tenant di Azure AD per utilizzare i servizi nello Stack di Azure. Per istruzioni, vedere [abilitare multi-tenancy nello Stack di Azure](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Creare una risorsa locale nel tenant del cliente finale nello Stack di Azure

Una volta aggiunto il nuovo oggetto customer allo Stack di Azure, o tenant del cliente finale è abilitato l'account guest con privilegi di proprietario, verificare che è possibile creare una risorsa nel tenant. Ad esempio, è possibile [creare una macchina virtuale Windows con il portale di Azure Stack](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Passaggi successivi

 - Per esaminare i messaggi di errore se l'attivazione del processo di registrazione, vedere [i messaggi di errore di registrazione del Tenant](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Per ulteriori informazioni su come recuperare informazioni sull'utilizzo di risorse dallo Stack di Azure, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](/azure-stack-billing-and-chargeback.md).
 - Per esaminare come cui un cliente finale può aggiungere, come il provider CSP, come la gestione per i relativi Stack di Azure, tenant, vedere [abilitare un Provider di servizi Cloud gestire la sottoscrizione di Azure Stack](user\azure-stack-csp-enable-billing-usage-tracking.md).