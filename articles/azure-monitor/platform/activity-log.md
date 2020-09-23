---
title: Log attività di Azure
description: Visualizzare il log attività di Azure e inviarlo a log di monitoraggio di Azure, Hub eventi di Azure e archiviazione di Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 089c53c72ae2c4cf6216937e8977b64a7abf80fc
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983201"
---
# <a name="azure-activity-log"></a>Log attività di Azure
Il log attività è un [log di piattaforma](platform-logs-overview.md) in Azure che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione. Tali dati includono le informazioni relative, ad esempio, alla modifica di una risorsa o all'avvio di una macchina virtuale. È possibile visualizzare il log attività nel portale di Azure o recuperarne le voci con PowerShell e l'interfaccia della riga di comando. Per altre funzionalità, è necessario creare un'impostazione di diagnostica per inviare il log attività ai [log di monitoraggio di Azure](data-platform-logs.md), a hub eventi di Azure in modo che inoltri al di fuori di Azure o ad archiviazione di Azure per l'archiviazione. Questo articolo fornisce informazioni dettagliate sulla visualizzazione del log attività e sull'invio a destinazioni diverse.

Per informazioni dettagliate sulla creazione di un'impostazione di diagnostica, vedere [creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](diagnostic-settings.md) .

> [!NOTE]
> Le voci nel log attività sono generate dal sistema e non possono essere modificate o eliminate.

## <a name="view-the-activity-log"></a>Visualizzare il log attività
È possibile accedere al log attività dalla maggior parte dei menu nella portale di Azure. Il menu da cui viene aperto determina il filtro iniziale. Se viene aperto dal menu **monitoraggio** , l'unico filtro sarà nella sottoscrizione. Se viene aperto dal menu di una risorsa, il filtro verrà impostato su tale risorsa. È sempre possibile modificare il filtro se si desidera visualizzare tutte le altre voci. Fare clic su **Aggiungi filtro** per aggiungere ulteriori proprietà al filtro.

![Visualizzare il log attività](./media/activity-logs-overview/view-activity-log.png)

Per una descrizione delle categorie del log attività, vedere [schema degli eventi del log attività di Azure](activity-log-schema.md#categories).

### <a name="view-change-history"></a>Visualizzare la cronologia modifiche

Per alcuni eventi, è possibile visualizzare la cronologia delle modifiche, che mostra le modifiche apportate durante l'intervallo di tempo dell'evento. Selezionare un evento nel log attività di cui si desidera visualizzare maggiori dettagli. Selezionare la scheda **Cronologia modifiche (anteprima)** per visualizzare eventuali modifiche associate a tale evento.

![Elenco di cronologia modifiche per un evento](media/activity-logs-overview/change-history-event.png)

Se sono presenti modifiche associate all'evento, viene visualizzato un elenco di modifiche che è possibile selezionare. Viene visualizzata la pagina **Cronologia modifiche (anteprima)** . In questa pagina sono elencate le modifiche apportate alla risorsa. Nell'esempio seguente è possibile vedere non solo che la macchina virtuale ha modificato le dimensioni, ma le dimensioni precedenti della macchina virtuale erano precedenti alla modifica e a cosa è stato modificato. Per altre informazioni sulla cronologia delle modifiche, vedere [ottenere le modifiche alle risorse](../../governance/resource-graph/how-to/get-resource-changes.md).

![Pagina della cronologia delle modifiche che mostra le differenze](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Altri metodi per recuperare gli eventi del log attività
È anche possibile accedere agli eventi del log attività usando i metodi seguenti.

- Usare il cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog) per recuperare il log attività da PowerShell. Vedere gli [esempi di PowerShell per monitoraggio di Azure](../samples/powershell-samples.md#retrieve-activity-log).
- Usare [az monitor activity-log](/cli/azure/monitor/activity-log) per recuperare il log attività dall'interfaccia della riga di comando.  Vedere gli [esempi dell'interfaccia della riga di comando di Monitoraggio di Azure](../samples/cli-samples.md#view-activity-log).
- Usare l'[API REST di Monitoraggio di Azure](/rest/api/monitor/) per recuperare il log attività da un client REST. 


## <a name="send-to-log-analytics-workspace"></a>Inviare all'area di lavoro Log Analytics
 Inviare il log attività a un'area di lavoro di Log Analytics per abilitare le funzionalità dei [log di monitoraggio di Azure](data-platform-logs.md) , inclusi i seguenti:

- Correlare i dati del log attività con altri dati di monitoraggio raccolti da monitoraggio di Azure.
- Consolidare le voci di log da più sottoscrizioni e tenant di Azure in un'unica posizione per l'analisi insieme.
- Usare le query di log per eseguire analisi complesse e ottenere informazioni approfondite sulle voci del log attività.
- Usare gli avvisi del log con le voci di attività che consentono una logica più complessa per gli avvisi.
- Archivia le voci del log attività per più di 90 giorni.
- Nessun inserimento di dati o di conservazione dei dati per i dati del log attività archiviati in un'area di lavoro Log Analytics.

[Creare un'impostazione di diagnostica](diagnostic-settings.md) per inviare il log attività a un'area di lavoro log Analytics. È possibile inviare il log attività da una singola sottoscrizione a un massimo di cinque aree di lavoro. Per raccogliere i log tra i tenant è necessario [Azure Lighthouse](../../lighthouse/index.yml).

I dati del log attività in un'area di lavoro Log Analytics vengono archiviati in una tabella denominata *AzureActivity* che è possibile recuperare con una [query di log](../log-query/log-query-overview.md) in [log Analytics](../log-query/get-started-portal.md). La struttura di questa tabella varia a seconda della [categoria della voce di log](activity-log-schema.md). Per una descrizione delle proprietà della tabella, vedere [informazioni di riferimento sui dati di monitoraggio di Azure](/azure/azure-monitor/reference/tables/azureactivity).

Per visualizzare, ad esempio, un conteggio dei record del log attività per ogni categoria, utilizzare la query seguente.

```kusto
AzureActivity
| summarize count() by Category
```

Per recuperare tutti i record nella categoria amministrativa, utilizzare la query seguente.

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Invia a hub eventi di Azure
Inviare il log attività a hub eventi di Azure per inviare le voci all'esterno di Azure, ad esempio a un SIEM di terze parti o ad altre soluzioni di log Analytics. Gli eventi del log attività dagli hub eventi vengono utilizzati in formato JSON con un `records` elemento contenente i record in ogni payload. Lo schema dipende dalla categoria ed è descritto nello [schema dall'account di archiviazione e hub eventi](activity-log-schema.md).

Di seguito sono riportati i dati di output di esempio di hub eventi per un log attività:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Invia ad archiviazione di Azure
Inviare il log attività a un account di archiviazione di Azure se si desidera conservare i dati di log più di 90 giorni per il controllo, l'analisi statica o il backup. Se è necessario conservare gli eventi solo per 90 giorni o meno, non è necessario configurare l'archiviazione in un account di archiviazione, perché gli eventi del log attività vengono conservati nella piattaforma Azure per 90 giorni.

Quando si invia il log attività ad Azure, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento. I BLOB nel contenitore usano la convenzione di denominazione seguente:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, un blob specifico potrebbe avere un nome simile al seguente:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m = 00) è sempre 00, perché gli eventi del log delle risorse sono suddivisi in singoli BLOB all'ora.

Ogni evento viene archiviato nella PT1H.jssu file con il formato seguente che usa uno schema di primo livello comune, ma in caso contrario è univoco per ogni categoria, come descritto in  [schema del log attività](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Metodi di raccolta legacy
Questa sezione descrive i metodi legacy per la raccolta del log attività usati prima delle impostazioni di diagnostica. Se si usano questi metodi, è necessario prendere in considerazione la transizione alle impostazioni di diagnostica che offrono una migliore funzionalità e coerenza con i log delle risorse.

### <a name="log-profiles"></a>Profili dei log
I profili di log sono il metodo legacy per l'invio del log attività ad hub eventi o archiviazione di Azure. Utilizzare la procedura seguente per continuare a utilizzare un profilo di log o per disabilitarlo in preparazione per la migrazione a un'impostazione di diagnostica.

1. Dal menu **monitoraggio di Azure** nella portale di Azure selezionare **log attività**.
3. Fare clic su **Impostazioni di diagnostica**.

   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Fare clic sul banner viola per l'esperienza legacy.

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Configurare il profilo di log con PowerShell

Se esiste già un profilo di log, prima di tutto è necessario rimuovere il profilo di log esistente e quindi crearne uno nuovo.

1. Usare `Get-AzLogProfile` per determinare se esiste già un profilo di log.  Se esiste un profilo di log, annotare la proprietà *Name* .

1. Usare `Remove-AzLogProfile` per rimuovere il profilo di log usando il valore della proprietà *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Usare `Add-AzLogProfile` per creare un nuovo profilo di log:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Proprietà | Obbligatoria | Descrizione |
    | --- | --- | --- |
    | Nome |Sì |Nome del profilo di log. |
    | StorageAccountId |No |ID risorsa dell'account di archiviazione in cui deve essere salvato il log attività. |
    | serviceBusRuleId |No |ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui creare gli hub eventi. Si tratta di una stringa nel formato: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Posizione |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. |
    | RetentionInDays |Sì |Numero di giorni per cui gli eventi devono essere conservati nell'account di archiviazione, tra 1 e 365. Se il valore è zero, i log vengono conservati all'infinito. |
    | Category |No |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono _Write_, _Delete_e _Action_. |

### <a name="example-script"></a>Script di esempio
Di seguito è riportato uno script di PowerShell di esempio per creare un profilo di log che scrive il log attività in un account di archiviazione e in un hub eventi.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurare il profilo di log tramite l'interfaccia della riga di comando

Se esiste già un profilo di log, è innanzitutto necessario rimuovere il profilo di log esistente e quindi crearne uno nuovo.

1. Usare `az monitor log-profiles list` per determinare se esiste già un profilo di log.
2. Usare `az monitor log-profiles delete --name "<log profile name>` per rimuovere il profilo di log usando il valore della proprietà *name*.
3. Usare `az monitor log-profiles create` per creare un nuovo profilo di log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Proprietà | Obbligatoria | Descrizione |
    | --- | --- | --- |
    | name |Sì |Nome del profilo di log. |
    | storage-account-id |Sì |ID risorsa dell'account di archiviazione in cui salvare i log attività. |
    | locations |Sì |Elenco delimitato da spazi di aree per cui raccogliere eventi del log attività. È possibile visualizzare un elenco di tutte le aree per la sottoscrizione tramite `az account list-locations --query [].name`. |
    | days |Sì |Numero di giorni per cui devono essere conservati gli eventi, tra 1 e 365. Se il valore è zero, i log vengono archiviati per un periodo illimitato.  Se è zero, il parametro Enabled deve essere impostato su false. |
    |Enabled | Sì |Vero o falso.  Consente di abilitare o disabilitare i criteri di conservazione.  Se True, il parametro days deve essere un valore maggiore di 0.
    | Categorie |Sì |Elenco delimitato da spazi di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |


### <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Il metodo legacy per l'invio del log attività in un'area di lavoro Log Analytics è la connessione del log nella configurazione dell'area di lavoro. 

1. Dal menu **log Analytics aree di lavoro** della portale di Azure selezionare l'area di lavoro per raccogliere il log attività.
1. Nella sezione **origini dati dell'area di lavoro** del menu dell'area di lavoro selezionare **log attività di Azure**.
1. Fare clic sulla sottoscrizione che si desidera connettere.

    ![Screenshot Mostra Log Analytics area di lavoro con un log attività di Azure selezionato.](media/activity-log-collect/workspaces.png)

1. Fare clic su **Connetti** per connettere il log attività nella sottoscrizione all'area di lavoro selezionata. Se la sottoscrizione è già connessa a un'altra area di lavoro, fare clic su **Disconnetti** prima per disconnetterla.

    ![Connettere le aree di lavoro](media/activity-log-collect/connect-workspace.png)


Per disabilitare l'impostazione, eseguire la stessa procedura e fare clic su **Disconnetti** per rimuovere la sottoscrizione dall'area di lavoro.

### <a name="data-structure-changes"></a>Modifiche alla struttura dei dati
Le impostazioni di diagnostica inviano gli stessi dati del metodo legacy utilizzato per inviare il log attività con alcune modifiche alla struttura della tabella *AzureActivity* .

Le colonne nella tabella seguente sono state deprecate nello schema aggiornato. Sono ancora presenti in *AzureActivity* , ma non avranno dati. La sostituzione di queste colonne non è nuova, ma contengono gli stessi dati della colonna deprecata. Sono in un formato diverso, quindi potrebbe essere necessario modificare le query di log che le usano. 

| Colonna deprecata | Colonna sostitutiva |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> In alcuni casi, i valori in queste colonne possono essere tutti in lettere maiuscole. Se si dispone di una query che include queste colonne, è necessario utilizzare l' [operatore = ~](/azure/kusto/query/datatypes-string-operators) per eseguire un confronto senza distinzione tra maiuscole e minuscole.

La colonna seguente è stata aggiunta a *AzureActivity* nello schema aggiornato:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Soluzione di monitoraggio Analisi log attività
La soluzione di monitoraggio di Azure Log Analytics verrà presto deprecata e sostituita da una cartella di lavoro usando lo schema aggiornato nell'area di lavoro Log Analytics. È comunque possibile usare la soluzione se è già abilitata, ma può essere usata solo se si sta raccogliendo il log attività usando le impostazioni legacy. 



### <a name="use-the-solution"></a>Usare la soluzione
È possibile accedere alle soluzioni di monitoraggio dal menu **monitoraggio** della portale di Azure. Per aprire la pagina **Panoramica** con i riquadri della soluzione, selezionare **altre informazioni** nella sezione **Insights** . Il riquadro **log attività di Azure** Visualizza un conteggio del numero di record **AzureActivity** nell'area di lavoro.

![Riquadro Log attività di Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Fare clic sul riquadro **log attività di Azure** per aprire la visualizzazione **log attività di Azure** . La vista include le parti di visualizzazione nella tabella seguente. Ogni parte elenca fino a 10 elementi corrispondenti ai criteri delle parti per l'intervallo di tempo specificato. È possibile eseguire una query di log che restituisce tutti i record corrispondenti facendo clic su **Visualizza tutto** nella parte inferiore della parte.

![Dashboard Log attività di Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Abilitare la soluzione per le nuove sottoscrizioni
A breve non sarà più possibile aggiungere la soluzione analisi dei log attività alla sottoscrizione usando il portale di Azure. È possibile aggiungerlo utilizzando la procedura seguente con un modello di Gestione risorse. 

1. Copiare il codice JSON seguente in un file denominato *ActivityLogTemplate*. JSON.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Distribuire il modello usando i comandi di PowerShell seguenti:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Passaggi successivi

* Leggere una [panoramica dei log della piattaforma di Azure](platform-logs-overview.md)
* [Creare un'impostazione di diagnostica per inviare i log attività ad altre destinazioni](diagnostic-settings.md)
