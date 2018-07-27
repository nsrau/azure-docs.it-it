---
title: Registrare i tenant per l'utilizzo di rilevamento in Azure Stack | Microsoft Docs
description: Informazioni dettagliate sulle operazioni usate per gestire le registrazioni tenant e la modalità di rilevamento di utilizzo dei tenant in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: efd1c1eafbff8bf56b16131e44cff6b03ce7338a
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264811"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Gestire la registrazione del tenant in Azure Stack

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo contiene informazioni dettagliate sulle operazioni che è possibile usare per gestire le registrazioni tenant e la modalità di rilevamento di utilizzo dei tenant. È possibile trovare informazioni dettagliate su come aggiungere, elenco, o rimuovere i mapping di tenant. È possibile usare PowerShell o gli endpoint API di fatturazione per gestire l'utilizzo di rilevamento.

## <a name="add-tenant-to-registration"></a>Aggiungere registrazione tenant

Questa operazione è usare quando si desidera aggiungere un nuovo tenant per la registrazione, in modo che il loro utilizzo viene segnalato in una sottoscrizione di Azure connessa con il tenant di Azure Active Directory (Azure AD).

È anche possibile usare questa operazione se si desidera modificare la sottoscrizione associata a un tenant, è possibile chiamare nuovamente PUT/New-AzureRMResource. Il mapping precedente verrà sovrascritto.

Si noti che una sola sottoscrizione di Azure può essere associata a un tenant. Se si tenta di aggiungere una seconda sottoscrizione a un tenant esistente, la prima sottoscrizione viene sovrascrittura. 

### <a name="use-api-profiles"></a>Usare i profili delle API

I cmdlet in questo articolo è necessario specificare un profilo di API quando si esegue PowerShell. I profili delle API rappresentano un set di provider di risorse di Azure e le versioni dell'API. Consentono di usare la versione corretta dell'API durante l'interazione con più cloud di Azure, ad esempio quando si lavora con globale di Azure e Azure Stack. I profili vengono specificati da un nome che corrisponde al loro data di rilascio. Con questo articolo, è necessario usare il **2017-09-03** profilo.

Per altre informazioni su Azure Stack e i profili delle API, vedere [profili della versione di gestione API in Azure Stack](user/azure-stack-version-profiles.md). Per istruzioni su come iniziare e in esecuzione con il profilo di API con PowerShell, vedere [profili di usare l'API della versione per PowerShell in Azure Stack](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>Parametri

| Parametro                  | DESCRIZIONE |
|---                         | --- |
| registrationSubscriptionID | Sottoscrizione di Azure che è stata usata per la registrazione iniziale. |
| customerSubscriptionID     | Sottoscrizione di Azure (non Azure Stack) che appartengono al cliente da registrare. Deve essere creato nell'offerta Cloud Service Provider (CSP). In pratica, ciò significa tramite Centro per i Partner. Se un cliente ha più di un tenant, è necessario creare la sottoscrizione nel tenant che verrà usato per accedere a Azure Stack. |
| resourceGroup              | Il gruppo di risorse in Azure in cui è archiviata la registrazione. |
| registrationName           | Il nome della registrazione di Azure Stack. È un oggetto archiviato in Azure. Il nome è in genere in CloudID-azurestack form, in cui CloudID è l'ID Cloud della distribuzione di Azure Stack. |

> [!Note]  
> I tenant devono essere registrati con ogni Stack di Azure usano. Se un tenant Usa più di uno Stack di Azure, è necessario aggiornare le registrazioni iniziale di ogni distribuzione con la sottoscrizione del tenant.

### <a name="powershell"></a>PowerShell

Usare il cmdlet New-AzureRmResource per aggiornare la risorsa di registrazione. Accedere ad Azure (`Add-AzureRmAccount`) usando l'account usato per la registrazione iniziale. Ecco un esempio di come aggiungere un tenant:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Chiamata all'API

**Operazione**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Risposta**: 201 creato  
**Corpo della risposta**: vuoto  

## <a name="list-all-registered-tenants"></a>Elenco registrati tutti i tenant

Ottenere un elenco di tutti i tenant che sono stati aggiunti a una registrazione.

 > [!Note]  
 > Se nessun tenant è stato registrato, non viene visualizzato una risposta.

### <a name="parameters"></a>Parametri

| Parametro                  | DESCRIZIONE          |
|---                         | ---                  |
| registrationSubscriptionId | Sottoscrizione di Azure che è stata usata per la registrazione iniziale.   |
| resourceGroup              | Il gruppo di risorse in Azure in cui è archiviata la registrazione.    |
| registrationName           | Il nome della registrazione di Azure Stack. È un oggetto archiviato in Azure. Il nome è in genere nel formato **azurestack**-***CloudID***, dove ***CloudID*** ID Cloud della distribuzione di Azure Stack.   |

### <a name="powershell"></a>PowerShell

Usare il cmdlet Get-AzureRmResovurce per elencare registrati tutti i tenant. Accedere ad Azure (`Add-AzureRmAccount`) usando l'account usato per la registrazione iniziale. Ecco un esempio di come aggiungere un tenant:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chiamata all'API

È possibile ottenere un elenco di tutti i mapping di tenant usando l'operazione GET

**Operazione**: introduzione  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Risposta**: 200  
**Corpo della risposta**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Rimuovere un mapping del tenant

È possibile rimuovere un tenant di cui è stato aggiunto a una registrazione. Se tale tenant Usa ancora le risorse in Azure Stack, il loro utilizzo viene addebitato alla sottoscrizione usata per la registrazione iniziale di Azure Stack.

### <a name="parameters"></a>Parametri

| Parametro                  | DESCRIZIONE          |
|---                         | ---                  |
| registrationSubscriptionId | ID sottoscrizione per la registrazione.   |
| resourceGroup              | Il gruppo di risorse per la registrazione.   |
| registrationName           | Il nome della registrazione.  |
| customerSubscriptionId     | L'ID sottoscrizione cliente.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chiamata all'API

È possibile rimuovere i mapping di tenant tramite l'operazione di eliminazione.

**Operazione**: eliminare  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Risposta**: 204 Nessun contenuto  
**Corpo della risposta**: vuoto

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni su come recuperare le informazioni sull'utilizzo di risorse di Azure Stack, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](/azure-stack-billing-and-chargeback.md).
