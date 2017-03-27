---
title: "Panoramica del log attività di Azure | Microsoft Docs"
description: "Informazioni sul log attività di Azure e su come usarlo per comprendere gli eventi che si verificano all&quot;interno della sottoscrizione di Azure."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 4ec026a5b95170d0eba244123c37cd3c8fab150a
ms.lasthandoff: 03/09/2017


---
# <a name="overview-of-the-azure-activity-log"></a>Panoramica del log attività di Azure
Il **log attività di Azure** fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. Il log attività era noto in precedenza come "log di controllo" o "log operativo", perché segnala eventi del piano di controllo per le sottoscrizioni. L'uso del log attività permette di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. Il log attività non include le operazioni di lettura (GET) o quelle per le risorse che usano il modello classico/"RDFE".

![Log attività o altri tipi di log ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Figura 1: Log attività o altri tipi di log

Il log attività è diverso dal [log di diagnostica](monitoring-overview-of-diagnostic-logs.md). I log attività contengono dati relativi alle operazioni su una risorsa esterna. I log di diagnostica vengono generati da una risorsa e contengono informazioni sul funzionamento di tale risorsa.

Per recuperare eventi dal log attività è possibile usare il portale di Azure, l'interfaccia della riga di comando, i cmdlet di PowerShell e l'API REST di Monitoraggio di Azure.


> [!WARNING]
> Il log attività di Azure è destinato principalmente alle attività che si verificano in Azure Resource Manager. Non tiene traccia delle risorse che usano il modello classico/RDFE. Alcuni tipi di risorse classiche dispongono di un provider di risorse proxy in Azure Resource Manager (ad esempio, Microsoft.ClassicCompute). Se un utente interagisce con un tipo di risorsa classica tramite Azure Resource Manager con questi provider di risorse di proxy, le operazioni verranno visualizzate nel log attività. Se un utente interagisce con un tipo di risorsa classica nel portale classico o all'esterno dei proxy di Azure Resource Manager, le azioni dell'utente verranno registrate solo nel log delle operazioni. Il log delle operazioni è accessibile solo nel portale classico.
>
>

Guardare il video seguente di introduzione al log attività.
[!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]

## <a name="what-you-can-do-with-the-activity-log"></a>Che cosa si può fare con il log attività
Ecco alcune delle attività che è possibile eseguire con il log attività:

![Log attività di Azure](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* [Creare un avviso di posta elettronica o webhook che attiva un evento del log attività.](insights-auditlog-to-webhook-email.md)
* [Trasmetterlo a un **hub eventi**](monitoring-stream-activity-logs-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.
* Analizzarlo in Power BI usando il [**pacchetto di contenuto di Power BI**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
* [Salvarlo in un **account di archiviazione** per fini di archiviazione o di ispezione manuale](monitoring-archive-activity-log.md). È possibile specificare il tempo di conservazione in giorni tramite i **profili di log**.
* Eseguire query e visualizzarlo nel **portale di Azure**.
* Eseguire query tramite l'API REST, i cmdlet di PowerShell o l'interfaccia della riga di comando.


È possibile usare un account di archiviazione o un hub eventi dello spazio dei nomi che non si trovi nella stessa sottoscrizione di quello che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="export-the-activity-log-with-log-profiles"></a>Esportare il log attività con i profili di log
Un **profilo di log** controlla la modalità di esportazione del log attività. Un profilo di log permette di configurare quanto segue:

* Destinazione del log attività, ad esempio un account di archiviazione o un hub eventi.
* Categorie di eventi da inviare (scrittura, eliminazione o azione). *Il significato di "categoria" nei profili del log è diverso da quello negli eventi del log attività. Nel profilo del log, la "Categoria" rappresenta il tipo di operazione (scrittura, eliminazione, azione). In un evento del log attività, la proprietà "categoria" rappresenta l'origine o il tipo di evento (ad esempio, amministrazione, ServiceHealth, avviso e altro).*
* Aree o località da esportare.
* Per quanto tempo il log attività deve essere mantenuto nell'account di archiviazione.
    - Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
    - Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o OMS, i criteri di conservazione non hanno alcun effetto.
    - I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente.

Queste impostazioni possono essere configurate tramite l'opzione "Esporta" nel pannello Log attività nel portale oppure a livello di codice tramite l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx), i cmdlet di PowerShell o l'interfaccia della riga di comando. Una sottoscrizione può avere un solo profilo di log.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurare i profili di log tramite il portale di Azure
È possibile trasmettere il log attività a un hub eventi o memorizzarlo in un account di archiviazione usando l'opzione "Esporta" nel portale di Azure.

1. Passare al pannello **Log attività** usando il menu sul lato sinistro del portale.

    ![Passare al log attività nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Fare clic sul pulsante **Esporta** nella parte superiore del pannello.

    ![Pulsante Esporta nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Nel pannello visualizzato è possibile selezionare:  

   * aree per cui esportare gli eventi
   * account di archiviazione in cui salvare gli eventi
   * numero di giorni di conservazione degli eventi nella risorsa di archiviazione (se si impostano 0 giorni, i log vengono conservati all'infinito)
   * spazio dei nomi del bus di servizio in cui creare un hub eventi per la trasmissione di questi eventi.

     ![Pannello Esporta log di controllo](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurare i profili di log tramite i cmdlet di Azure PowerShell
#### <a name="get-existing-log-profile"></a>Ottenere un profilo di log esistente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Aggiungere un profilo di log
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Proprietà | Obbligatorio | Description |
| --- | --- | --- |
| Name |Sì |Nome del profilo di log. |
| StorageAccountId |No |ID risorsa dell'account di archiviazione in cui salvare il log attività. |
| serviceBusRuleId |No |ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui creare gli hub eventi. Si tratta di una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`. |
| Località |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. |
| RetentionInDays |Sì |Numero di giorni per cui gli eventi devono essere mantenuti, compreso tra 1 e 2147483647. Se il valore è zero, i log vengono mantenuti per un periodo illimitato. |
| Categorie |No |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |

#### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurare i profili di log tramite l'interfaccia della riga di comando multipiattaforma di Azure
#### <a name="get-existing-log-profile"></a>Ottenere un profilo di log esistente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
La proprietà `name` deve essere il nome del profilo di log.

#### <a name="add-a-log-profile"></a>Aggiungere un profilo di log
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Proprietà | Obbligatorio | Description |
| --- | --- | --- |
| name |Sì |Nome del profilo di log. |
| storageId |No |ID risorsa dell'account di archiviazione in cui salvare il log attività. |
| serviceBusRuleId |No |ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui creare gli hub eventi. Si tratta di una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`. |
| locations |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. |
| RetentionInDays |Sì |Numero di giorni per cui gli eventi devono essere mantenuti, compreso tra 1 e 2147483647. Se il valore è zero, i log vengono mantenuti per un periodo illimitato. |
| Categorie |No |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |

#### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Schema di eventi
Per ogni evento nel log attività esiste un BLOB JSON simile all'esempio seguente:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nome dell'elemento | Description |
| --- | --- |
| autorizzazione |BLOB delle proprietà RBAC dell'evento. In genere include le proprietà "action", "role" e "scope". |
| caller |Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. |
| channels |Uno dei valori seguenti: "Admin" o "Operation". |
| correlationId |In genere un GUID in formato stringa. Gli eventi che condividono un elemento correlationId appartengono alla stessa azione. |
| Description |Testo statico che descrive un evento. |
| eventDataId |Identificatore univoco di un evento. |
| eventSource |Nome del servizio o dell'infrastruttura di Azure che ha generato l'evento. |
| httpRequest |BLOB che descrive la richiesta HTTP. In genere include "clientRequestId", "clientIpAddress" e "method" (metodo HTTP, ad esempio PUT). |
| necessario |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose" |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata. |
| resourceProviderName |Nome del provider di risorse della risorsa interessata. |
| resourceUri |ID risorsa della risorsa interessata. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. |
| status |Stringa che descrive lo stato dell'operazione. Alcuni dei valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |In genere si tratta del codice di stato HTTP della chiamata REST corrispondente, ma può includere altre stringhe che descrivono uno stato secondario, come i valori comuni seguenti: OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (codice di stato HTTP: 503), Gateway Timeout (codice di stato HTTP: 504). |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |
| nextLink |Token di continuazione per recuperare il set di risultati successivo quando sono suddivisi in più risposte. In genere è necessario quando sono presenti più di 200 record. |

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sul log attività (in precedenza, log di controllo)](../azure-resource-manager/resource-group-audit.md)
* [Trasmettere il log attività di Azure a Hub eventi](monitoring-stream-activity-logs-event-hubs.md)

