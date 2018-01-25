---
title: Informazioni di riferimento sull'API di controllo di Azure Active Directory | Microsoft Docs
description: Come iniziare a usare l'API di controllo di Azure Active Directory
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5cdf80ff1cc49b1582302d411ee6fcc8f193c021
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-audit-api-reference"></a>Informazioni di riferimento sull'API di controllo di Azure Active Directory
Questo argomento fa parte di una raccolta di argomenti sull'API di creazione report di Azure Active Directory.  
La creazione di report di Azure Active Directory fornisce un'API che consente di accedere ai dati di controllo tramite codice o strumenti correlati.
L'obiettivo di questo argomento è fornire informazioni di riferimento sull' **API di controllo**.

Vedere:

* [Log di controllo](active-directory-reporting-azure-portal.md#activity-reports) per informazioni più concettuali

* [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started.md) .


Per:

- Domande frequenti, leggere le [Domande Frequenti](active-directory-reporting-faq.md) 

- Problemi, [inviare un ticket di supporto](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Gli utenti con ruolo di amministratore della sicurezza o con autorizzazioni di lettura per la sicurezza
* Gli amministratori globali
* Qualsiasi applicazione che dispone di autorizzazione per accedere all'API. L'autorizzazione dell'app può essere configurata solo in base alle autorizzazioni dell'amministratore globale.

## <a name="prerequisites"></a>prerequisiti
Per accedere a questo report tramite l'API di creazione report, è necessario:

* Un' [edizione gratuita di Azure Active Directory o superiore](active-directory-editions.md)
* Aver completato i [prerequisiti di accesso all'API di creazione report di Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Accesso all'API
È possibile accedere a questa API tramite [Graph Explorer](https://graphexplorer2.cloudapp.net) o a livello di codice, ad esempio usando PowerShell. Al fine di consentire a Per PowerShell di interpretare correttamente la sintassi del filtro OData usata nelle chiamate REST di Graph di AAD, è necessario fare uso dell'apice inverso, ovvero l'accento grave, per eseguire l'"escape" del carattere $. L'apice inverso viene usato come [carattere di escape di PowerShell](https://technet.microsoft.com/library/hh847755.aspx)e consente a PowerShell di eseguire un'interpretazione letterale del carattere $, che evita la confusione con il nome di una variabile di PowerShell (ad esempio: $filter).

Questo argomento si concentra su Graph Explorer. Per un esempio di PowerShell, vedere questo [script di PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Endpoint API
È possibile accedere a questa API tramite l'URI seguente:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Non esiste alcun limite al numero di record restituiti dall'API di controllo di Azure AD (usando l'impaginazione OData).
Per i limiti di conservazione sui dati dei report, consultare [Criteri di conservazione dei report](active-directory-reporting-retention.md).

Questa chiamata restituisce i dati in batch. Ogni batch contiene un massimo di 1000 record.  
Per ottenere il batch successivo di record, usare il link Avanti. Ottenere le informazioni sullo skiptoken dal primo set di record restituiti. Il token skip si trova alla fine del set di risultati.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtri supportati
È possibile restringere il numero di record restituiti da una chiamata API usando un filtro.  
Per i dati relativi all'API di accesso sono supportati i filtri seguenti:

* **$top=\<numero di record da restituire\>**: per limitare il numero di record restituiti. Si tratta di un'operazione impegnativa. Non è consigliabile usare questo filtro se si desidera restituire migliaia di oggetti.     
* **$filter=\<istruzione per il filtro\>**: per specificare il tipo di record da restituire, sulla base dei campi filtro supportati

## <a name="supported-filter-fields-and-operators"></a>Operatori e campi dei filtri supportati
Per specificare il tipo di record da restituire, è possibile compilare un'istruzione per il filtro che può contenere uno o una combinazione dei campi filtro seguenti:

* [activityDate](#activitydate): definisce una data o un intervallo di date
* [category](#category): definisce la categoria in base alla quale applicare il filtro
* [activityStatus](#activitystatus): definisce lo stato di un'attività
* [activityType](#activitytype): definisce il tipo di un'attività
* [activity](#activity) - definisce l'attività come stringa  
* [actor/name](#actorname): definisce l'attore mediante il nome dell'attore
* [actor/objectid](#actorobjectid) - definisce l'attore mediante l'ID dell'attore   
* [actor/upn](#actorupn): definisce l'attore mediante il nome dell'entità utente (UPN) dell'attore 
* [target/name](#targetname): definisce la destinazione mediante il nome dell'attore
* [target/name](#targetobjectid) - definisce la destinazione mediante l'ID della destinazione  
* [target/upn](#targetupn) - definisce l'attore mediante il nome dell'entità utente (UPN) dell'attore   

- - -
### <a name="activitydate"></a>activityDate
**Operatori supportati**: eq, ge, le, gt, lt

**Esempio**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Note**:

datetime deve essere in formato UTC

- - -
### <a name="category"></a>category

**Valori supportati**:

| Categoria                         | Valore     |
| :--                              | ---       |
| Directory principale                   | Directory |
| Gestione delle password self-service | SSPR      |
| Gestione gruppi self-service    | SSGM      |
| Provisioning degli account             | Sincronizzazione      |
| Automated Password Rollover (Rollover automatizzato delle password)      | Automated Password Rollover (Rollover automatizzato delle password) |
| Identity Protection              | IdentityProtection |
| Utenti invitati                    | Utenti invitati |
| Servizio MIM                      | Servizio MIM |



**Operatori supportati**: eq

**Esempio**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>activityStatus

**Valori supportati**:

| Stato attività | Valore |
| :--             | ---   |
| Success         | 0     |
| Esito negativo         | - 1   |

**Operatori supportati**: eq

**Esempio**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>activityType
**Operatori supportati**: eq

**Esempio**:

    $filter=activityType eq 'User'    

**Note**:

fa distinzione tra maiuscole e minuscole

- - -
### <a name="activity"></a>activity
**Operatori supportati**: eq, contains, startsWith

**Esempio**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Note**:

fa distinzione tra maiuscole e minuscole

- - -
### <a name="actorname"></a>actor/name
**Operatori supportati**: eq, contains, startsWith

**Esempio**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Note**:

non fa distinzione tra maiuscole e minuscole

- - -
### <a name="actorobjectid"></a>actor/objectid
**Operatori supportati**: eq

**Esempio**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>target/name
**Operatori supportati**: eq, contains, startsWith

**Esempio**:

    $filter=targets/any(t: t/name eq 'some name')    

**Note**:

Non fa distinzione tra maiuscole e minuscole.

- - -
### <a name="targetupn"></a>target/upn
**Operatori supportati**: eq, startsWith

**Esempio**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Note**:

* Non fa distinzione tra maiuscole e minuscole.
* È necessario aggiungere lo spazio dei nomi completo quando si eseguono query su Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

- - -
### <a name="targetobjectid"></a>target/name
**Operatori supportati**: eq

**Esempio**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>actor/upn
**Operatori supportati**: eq, startsWith

**Esempio**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Note**:

* Non fa distinzione tra maiuscole e minuscole. 
* È necessario aggiungere lo spazio dei nomi completo quando si eseguono query su Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Passaggi successivi
* Si desidera vedere esempi sulle attività di sistema filtrate? Vedere gli [esempi dell'API di controllo Azure Active Directory](active-directory-reporting-api-audit-samples.md).
* Si desiderano altre informazioni sull'API di creazione report di Azure AD? Vedere [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started.md).

