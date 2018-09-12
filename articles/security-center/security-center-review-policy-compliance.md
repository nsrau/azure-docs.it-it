---
title: Verifica della conformità ai criteri del Centro sicurezza con l'API REST di Azure | Microsoft Docs
description: Imparare a usare le API REST di Azure per esaminare la conformità corrente con i criteri del Centro sicurezza.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 1443486590859aac5591aff2ab0551bed9228d7b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301750"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Verifica della conformità ai criteri del Centro sicurezza con le API REST

Il Centro sicurezza convalida periodicamente le risorse di Azure rispetto ai criteri di sicurezza definiti. Il Centro sicurezza fornisce anche un'API REST che consente di verificare la conformità dalle applicazioni; è possibile eseguire direttamente query sul servizio o importare i risultati JSON in altre applicazioni. 

Qui è riportata la procedura per recuperare il set di raccomandazioni corrente da tutte le risorse di Azure associate con una sottoscrizione.

Per recuperare il set di raccomandazioni corrente:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Compilare la richiesta  

Il parametro `{subscription-id}` è obbligatorio e deve contenere l'ID sottoscrizione per la sottoscrizione di Azure che definisce i criteri. Se sono disponibili più sottoscrizioni, vedere [Uso di più sottoscrizioni](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

Il parametro `api-version` è obbligatorio. Al momento questi endpoint sono supportati solo per `api-version=2015-06-01-preview`. 

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare su `application/json`.|  
|*Authorization:*|Richiesto. Impostare su un [token di accesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valido. |  

## <a name="response"></a>Risposta  

Il codice di stato 200 (OK) viene restituito per una risposta corretta, che contiene un elenco delle attività consigliate per proteggere le risorse di Azure.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Ogni elemento in **value** rappresenta una raccomandazione:

|Proprietà Response|DESCRIZIONE|
|----------------|----------|
|**state** | Indica se la raccomandazione è `active` o `resolved`. |
|**creationTimeUtc** | Data e ora, nel formato UTC, che indica quando è stata creata la raccomandazione. |
|**lastStateChangeUtc** | Data e ora, nel formato UTV, dell'ultima modifica dello stato, se presente. |
|**securityTaskParameters** | Descrive in dettaglio la raccomandazione; le proprietà variano in base alla raccomandazione sottostante. |
||
  
Per le raccomandazioni attualmente supportate, vedere [Implementare le raccomandazioni sulla sicurezza](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Altri codici di stato indicano condizioni di errore. In questi casi l'oggetto risposta include una descrizione che spiega il motivo per cui la richiesta non è riuscita.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Risposta di esempio  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

Questa risposta mostra due indicazioni; ogni elemento dell'elenco corrisponde a una raccomandazione specifica. Il primo consiglia di crittografare la risorsa di archiviazione in una macchina virtuale Linux e il secondo suggerisce di abilitare il controllo per un server SQL.

Le raccomandazioni variano in base ai criteri che sono stati attivati. Per altre informazioni, incluse le raccomandazioni correnti disponibili, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Vedere anche   
- [Impostare i criteri di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [API REST del provider di risorse di Sicurezza di Azure](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Introduzione all'API REST di Azure](https://docs.microsoft.com/rest/api/azure/)   
- [Modulo PowerShell di Centro sicurezza di Azure](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
