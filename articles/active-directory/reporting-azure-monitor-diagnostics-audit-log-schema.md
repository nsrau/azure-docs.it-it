---
title: Interpretare lo schema del log di controllo di Azure Active Directory in Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Descrizione dello schema del log di controllo di Azure AD per l'uso in Monitoraggio di Azure (anteprima)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240472"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Interpretazione dello schema del log di controllo di Azure Active Directory in Monitoraggio di Azure (anteprima)

Questo articolo illustra lo schema del log di controllo di Azure AD per l'uso in Monitoraggio di Azure. Ogni singola voce del registro è archiviata come testo, formattato come BLOB JSON, come illustrato nei due esempi seguenti. 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| Nome campo | DESCRIZIONE |
|------------|-------------|
| time       | Data e ora (in formato UTC) |
| operationName | Nome dell'operazione |
| operationVersion | Versione dell'API REST richiesta dal client |
| category | Attualmente *Controllo* è l'unico valore supportato |
| TenantId | Guid di tenant associato ai log |
| resultType | Il risultato dell'operazione può essere *Operazione completata* o *Errore* |
| resultSignature |  Questo valore non è mappato, è quindi possibile ignorare questo campo. | 
| resultDescription | Descrizione aggiuntiva del risultato, se disponibile | 
| durationMs |  Questo valore non è mappato, è quindi possibile ignorare questo campo. |
| callerIpAddress | Indirizzo IP del client che ha eseguito la richiesta | 
| correlationId | Guid facoltativo passato dal client. Questo valore consente di correlare le operazioni lato client con le operazioni lato server ed è utile durante l'analisi dei log che si estendono tra i servizi. |
| identity | Identità del token presentato al momento dell'esecuzione della richiesta. Può essere un account utente, un account di sistema o un'entità servizio. |
| level | Tipo di messaggio. Per i log di controllo, questo valore è sempre *informativo* |
| location | Posizione del datacenter |
| properties | Elenco delle proprietà supportate relative a un log di controllo. Per altre informazioni, vedere la tabella seguente. | 


| Nome proprietà | DESCRIZIONE |
|---------------|-------------|
| AuditEventCategory | Tipo di evento di controllo. Può essere *Gestione utenti*, *Gestione applicazioni* e così via.|
| Tipo di identità | *Applicazione* o *Utente* |
| Tipo di operazione | Può essere *Aggiungi*, *Aggiorna*, *Elimina* o *Altro* |
| Tipo di risorsa di destinazione | Specifica il tipo di risorsa di destinazione in cui è stata eseguita l'operazione. Può essere *Applicazione*, *Utente*, *Ruolo*, *Criteri* | 
| Nome della risorsa di destinazione | Nome della risorsa di destinazione. Ad esempio, potrebbe trattarsi di un nome di applicazione, un nome di ruolo, un nome entità utente o un nome dell'entità servizio |
| additionalTargets | Elenca le proprietà aggiuntive per operazioni specifiche. Ad esempio, per un'operazione di aggiornamento, i valori precedenti e i nuovi valori sono elencati nella sezione *targetUpdatedProperties* | 

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Altre informazioni sui log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Domande frequenti e problemi noti](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)