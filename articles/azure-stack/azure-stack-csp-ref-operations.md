---
title: Registrare i tenant per l'utilizzo di rilevamento nello Stack di Azure | Documenti Microsoft
description: "Informazioni dettagliate sulle operazioni che consente di gestire le registrazioni tenant e la modalità di registrazione dell'utilizzo di tenant nello Stack di Azure."
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
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: be15fbc5fad79f1079b901b3d6cb4948c45a0ab4
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Gestire la registrazione tenant nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

Questo articolo contiene informazioni dettagliate sulle operazioni che è possibile utilizzare per gestire le registrazioni tenant e la modalità di registrazione dell'utilizzo di tenant. È possibile trovare informazioni dettagliate su come aggiungere, elenco, oppure rimuovere i mapping di tenant. È possibile utilizzare PowerShell o l'endpoint dell'API di fatturazione per gestire l'utilizzo di rilevamento.

## <a name="add-tenant-to-registration"></a>Aggiungere la registrazione di tenant

Utilizzare questa operazione quando si desidera aggiungere un nuovo tenant per la registrazione, in modo che viene segnalato l'utilizzo in una sottoscrizione di Azure connessa con i relativi tenant di Azure Active Directory (Azure AD).

È inoltre possibile utilizzare questa operazione se si desidera modificare la sottoscrizione associata a un tenant, è possibile chiamare PUT/New-AzureRMResource nuovamente. Il mapping precedente viene sovrascritto.

Si noti che solo una sottoscrizione di Azure può essere associata a un tenant. Se si tenta di aggiungere una seconda sottoscrizione a un tenant esistente, la prima sottoscrizione verrà sovrascrittura. 


| Parametro                  | Descrizione |
|---                         | --- |
| registrationSubscriptionID | La sottoscrizione di Azure che è stata utilizzata per la registrazione iniziale. |
| customerSubscriptionID     | La sottoscrizione di Azure (non Azure Stack) che appartengono al cliente da registrare. Deve essere creato nell'offerta di Provider del servizio Cloud (CSP). In pratica, ciò significa tramite Partner Center. Se un cliente dispone di più tenant, è necessario creare la sottoscrizione nel tenant che verrà utilizzato per accedere allo Stack di Azure. |
| resourceGroup              | Il gruppo di risorse in Azure in cui è archiviata la registrazione. |
| registrationName           | Il nome della registrazione dello Stack di Azure. È un oggetto archiviato in Azure. Il nome è in genere in CloudID-azurestack modulo, dove CloudID è l'ID di Cloud della distribuzione di Azure Stack. |

> [!Note]  
> I tenant devono essere registrati con ogni utilizzano lo Stack di Azure. Se un tenant utilizza più di uno Stack di Azure, è necessario aggiornare le registrazioni iniziale di ogni distribuzione con la sottoscrizione tenant.

### <a name="powershell"></a>PowerShell

Utilizzare il cmdlet New-AzureRmResource per aggiornare la risorsa di registrazione. Accedere a Azure (`Login-AzureRMAccount`) utilizzando l'account utilizzato per la registrazione iniziale. Di seguito è riportato un esempio di come aggiungere un tenant:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Chiamata API

**Operazione**: inserire  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Risposta**: 201 creato  
**Corpo della risposta**: vuoto  

## <a name="list-all-registered-tenants"></a>Elenco registrati tutti i tenant

Ottenere un elenco di tutti i tenant che sono stati aggiunti a una registrazione.

 > [!Note]  
 > Se nessun tenant è stato registrato, si riceverà una risposta.

### <a name="parameters"></a>Parametri

| Parametro                  | Descrizione          |
|---                         | ---                  |
| registrationSubscriptionId | La sottoscrizione di Azure che è stata utilizzata per la registrazione iniziale.   |
| resourceGroup              | Il gruppo di risorse in Azure in cui è archiviata la registrazione.    |
| registrationName           | Il nome della registrazione dello Stack di Azure. È un oggetto archiviato in Azure. Il nome è in genere sotto forma di **azurestack**-***CloudID***, dove ***CloudID*** è l'ID Cloud della distribuzione di Azure Stack.   |

### <a name="powershell"></a>PowerShell

Utilizzare il cmdlet Get-AzureRmResovurce per elencare registrati tutti i tenant. Accedere a Azure (`Login-AzureRMAccount`) utilizzando l'account utilizzato per la registrazione iniziale. Di seguito è riportato un esempio di come aggiungere un tenant:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chiamata API

È possibile ottenere un elenco di tutti i mapping di tenant usando l'operazione di recupero

**Operazione**: GET  
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

È possibile rimuovere un tenant in cui è stato aggiunto a una registrazione. Se tale tenant continua a usare risorse nello Stack di Azure, viene addebitato il loro utilizzo per la sottoscrizione per la registrazione di Azure Stack iniziale utilizzata.

### <a name="parameters"></a>Parametri

| Parametro                  | Descrizione          |
|---                         | ---                  |
| registrationSubscriptionId | ID di sottoscrizione per la registrazione.   |
| resourceGroup              | Il gruppo di risorse per la registrazione.   |
| registrationName           | Il nome della registrazione.  |
| customerSubscriptionId     | L'ID sottoscrizione del cliente.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chiamata API

È possibile rimuovere i mapping di tenant mediante l'operazione di eliminazione.

**Operazione**: eliminare  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Risposta**: 204 Nessun contenuto  
**Corpo della risposta**: vuoto

## <a name="next-steps"></a>Passaggi successivi

 - Per ulteriori informazioni su come recuperare informazioni sull'utilizzo di risorse dallo Stack di Azure, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](/azure-stack-billing-and-chargeback.md).
