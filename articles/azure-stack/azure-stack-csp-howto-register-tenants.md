---
title: Aggiungere i tenant per l'utilizzo e fatturazione per Azure Stack | Microsoft Docs
description: I passaggi necessari aggiungono un utente finale ad Azure Stack gestito da un Provider del servizio Cloud (CSP).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: cf4acf8746a09387f5d8e71406aa6b6fc49ff531
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239843"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Aggiungere tenant per l'utilizzo e fatturazione per Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo descrive i passaggi necessari per aggiungere un utente finale a una distribuzione di Azure Stack gestita da un Provider del servizio Cloud (CSP). Quando il nuovo tenant Usa le risorse, Azure Stack segnala l'utilizzo per sottoscrizione CSP.

I CSP offrono spesso servizi a più clienti finali (tenant) nella distribuzione di Azure Stack. Aggiunta di tenant per la registrazione di Azure Stack assicura che sull'utilizzo di ogni tenant viene segnalato e addebitato alla sottoscrizione di CSP corrispondente. Se non si completano i passaggi descritti in questo articolo, utilizzo dei tenant viene addebitato alla sottoscrizione usata per la registrazione iniziale di Azure Stack. Prima di poter aggiungere un cliente finale ad Azure Stack per monitorare l'utilizzo e per gestire i tenant, è necessario configurare Azure Stack come CSP. Per i passaggi e le risorse, vedere [gestire l'utilizzo e fatturazione per Azure Stack come Provider di servizi Cloud](azure-stack-add-manage-billing-as-a-csp.md).

La figura seguente illustra i passaggi da un provider CSP deve seguire per abilitare un nuovo cliente di usare Azure Stack e configurare l'utilizzo di rilevamento per il cliente. Quando si aggiungono gli utenti finali, sono anche in grado di gestire le risorse in Azure Stack. Sono disponibili due opzioni per la gestione delle relative risorse:

1. È possibile mantenere gli utenti finali e fornire le credenziali per la sottoscrizione di Azure Stack locale per il cliente finale.  
2. Gli utenti finali possono funzionare con l'abbonamento in locale e aggiungere il CSP come guest con le autorizzazioni di proprietario.  

## <a name="steps-to-add-an-end-customer"></a>Passaggi per aggiungere un cliente finale

![Configurare il Provider di servizi Cloud per monitorare l'utilizzo e per gestire l'account del cliente end](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Creare un nuovo cliente nel centro per i Partner

Nel centro per i Partner, creare una nuova sottoscrizione di Azure per il cliente. Per istruzioni, vedere [aggiungere un nuovo cliente](https://msdn.microsoft.com/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Creare una sottoscrizione di Azure per gli utenti finali

Dopo aver creato un record del cliente nel centro per i Partner, puoi venderle sottoscrizioni a prodotti nel catalogo. Per istruzioni, vedere [crea, sospendere o annullare le sottoscrizioni dei clienti](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Creare un utente guest nella directory del cliente end

Se il cliente finale gestisce il proprio account, creare un utente guest nella propria directory e li invierà tali informazioni. L'utente finale verrà quindi aggiungere l'utente guest ed elevare le autorizzazioni guest per **proprietario** per l'account di Azure Stack CSP.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aggiornare la registrazione con la sottoscrizione del cliente end

Aggiornare la registrazione con la nuova sottoscrizione del cliente. Azure segnala l'utilizzo del cliente usando l'identità del cliente dal centro per i Partner. Questo passaggio assicura che viene segnalato l'utilizzo di ogni cliente in una singola sottoscrizione di CSP del cliente. In questo modo più semplice tenere traccia dell'utilizzo di utente e la fatturazione.

> [!NOTE]  
> Per eseguire questo passaggio, è necessario disporre [registrato di Azure Stack](azure-stack-register.md).

1. Aprire Windows PowerShell con privilegi elevati ed eseguire:  
    `Add-AzureRmAccount`
2. Digitare le credenziali di Azure.
3. Nella sessione di PowerShell, eseguire:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>PowerShell nuovo-AzureRmResource parametri

La sezione seguente descrive i parametri per il **New-AzureRmResource** cmdlet:

| Parametro | DESCRIZIONE |
| --- | --- |
|registrationSubscriptionID | Sottoscrizione di Azure che è stata usata per la registrazione iniziale di Azure Stack.|
| customerSubscriptionID | Sottoscrizione di Azure (non Azure Stack) che appartengono al cliente da registrare. Deve essere creato nell'offerta CSP; in pratica, ciò significa tramite Centro per i Partner. Se un cliente ha più di un tenant di Azure Active Directory, è necessario creare la sottoscrizione nel tenant che verrà usato per accedere a Azure Stack. L'ID sottoscrizione del cliente è necessario usare lettere minuscole. |
| resourceGroup | Il gruppo di risorse in Azure in cui è archiviata la registrazione. |
| registrationName | Il nome della registrazione di Azure Stack. È un oggetto archiviato in Azure. |
| Properties | Specifica le proprietà per la risorsa. Usare questo parametro per specificare i valori delle proprietà specifiche per il tipo di risorsa.

> [!NOTE]  
> I tenant devono essere registrati con ogni Stack di Azure usano. Se si dispone di due distribuzioni di Azure Stack e un tenant Usa entrambi i sistemi operativi, è necessario aggiornare le registrazioni iniziale di ogni distribuzione con la sottoscrizione del tenant.

### <a name="onboard-tenant-to-azure-stack"></a>Eseguire l'onboarding tenant per Azure Stack

Configurare Azure Stack per supportare gli utenti da più tenant di Azure AD per usare i servizi in Azure Stack. Per istruzioni, vedere [abilitare la multi-tenancy in Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Creare una risorsa locale nel tenant del cliente end in Azure Stack

Una volta aggiunti i nuovi clienti in Azure Stack, o tenant del cliente end ha abilitato l'account guest con privilegi di proprietario, verificare che è possibile creare una risorsa nel tenant. Ad esempio, è possibile [creare una macchina virtuale Windows con il portale di Azure Stack](user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per esaminare i messaggi di errore se vengono attivate nel processo di registrazione, vedere [i messaggi di errore di registrazione del Tenant](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
- Per altre informazioni su come recuperare le informazioni sull'utilizzo di risorse di Azure Stack, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](azure-stack-billing-and-chargeback.md).
- Per esaminare come un cliente finale può aggiungere l'utente, come CSP, come la gestione per il tenant di Azure Stack, vedere [abilitare un Provider di servizi Cloud gestire la sottoscrizione di Azure Stack](user/azure-stack-csp-enable-billing-usage-tracking.md).
