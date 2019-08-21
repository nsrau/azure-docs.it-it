---
title: Esportare il log attività di Azure
description: Esportare il log attività di Azure nella risorsa di archiviazione per l'archiviazione o hub eventi di Azure per l'esportazione all'esterno di Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: d34040722ac8793fd4bbb02f2d3fa59247f8267c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639625"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Esportare il log attività di Azure nell'archiviazione o in hub eventi di Azure
Il [log attività di Azure](activity-logs-overview.md) fornisce informazioni sugli eventi a livello di sottoscrizione che si sono verificati nella sottoscrizione di Azure. Oltre a visualizzare il log attività nel portale di Azure o copiarlo in un'area di lavoro Log Analytics in cui può essere analizzato con altri dati raccolti da monitoraggio di Azure, è possibile creare un profilo di log per archiviare il log attività in un account di archiviazione di Azure o inviarlo a un  Hub eventi.

## <a name="archive-activity-log"></a>Archiviare il log attività
L'archiviazione del log attività in un account di archiviazione è utile se si desidera conservare i dati di log con una durata superiore a 90 giorni (con il controllo completo sui criteri di conservazione) per il controllo, l'analisi statica o il backup. Se è necessario conservare gli eventi solo per 90 giorni o meno, non è necessario configurare l'archiviazione in un account di archiviazione, perché gli eventi del log attività vengono conservati nella piattaforma Azure per 90 giorni.

## <a name="stream-activity-log-to-event-hub"></a>Trasmettere il log attività a hub eventi
[Hub eventi di Azure](/azure/event-hubs/) è una piattaforma di streaming di dati e un servizio di inserimento di eventi in grado di ricevere ed elaborare milioni di eventi al secondo. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. È possibile usare la funzionalità di streaming per il log attività in due modi:
* **Trasmettere a sistemi di telemetria e registrazione di terze parti**: in futuro, la funzionalità di trasmissione di Hub eventi di Azure diventerà il meccanismo di invio del log attività a soluzioni di analisi dei log e SIEM di terze parti.
* **Creare una piattaforma di telemetria e registrazione personalizzata**: se si ha già una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili di Hub eventi consentono di inserire il log attività con la massima flessibilità. 

## <a name="prerequisites"></a>Prerequisiti

### <a name="storage-account"></a>Account di archiviazione
Se si sta archiviando il log attività, è necessario [creare un account di archiviazione](../../storage/common/storage-quickstart-create-account.md) , se non ne è già presente uno. Non usare un account di archiviazione esistente con altri dati non di monitoraggio archiviati, in modo da poter controllare meglio l'accesso ai dati di monitoraggio. Se si archiviano anche log di diagnostica e metriche in un account di archiviazione, è possibile scegliere di usare lo stesso account di archiviazione per conservare tutti i dati di monitoraggio in una posizione centrale.

L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della sottoscrizione che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.
> [!NOTE]
>  Non è al momento possibile archiviare i dati in un account di archiviazione che risiede dietro una rete virtuale protetta.

### <a name="event-hubs"></a>Hub eventi
Se si sta inviando il log attività a un hub eventi, è necessario [creare un hub eventi](../../event-hubs/event-hubs-create.md) , se non è già presente. Se in precedenza sono stati trasmessi eventi del log attività a questo spazio dei nomi di hub eventi, l'hub eventi verrà riutilizzato.

Il criterio di accesso condiviso definisce le autorizzazioni per il meccanismo di trasmissione. Per lo streaming a hub eventi sono necessarie autorizzazioni di gestione, invio e ascolto. È possibile creare o modificare i criteri di accesso condiviso per lo spazio dei nomi di hub eventi nel portale di Azure nella scheda Configura per lo spazio dei nomi di hub eventi.

Per aggiornare il profilo di log del log attività in modo da includere il flusso, è necessario disporre dell'autorizzazione ListKey per la regola di autorizzazione di hub eventi. Lo spazio dei nomi di Hub eventi non deve necessariamente trovarsi nella stessa sottoscrizione in cui vengono creati i log, purché l'utente che configura l'impostazione abbia l'accesso RBAC appropriato a entrambe le sottoscrizioni e queste ultime si trovino entrambe nello stesso tenant AAD.

Trasmettere il log attività a un hub eventi [creando un profilo di log](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Creare un profilo di log
Definire il modo in cui il log attività di Azure viene esportato usando un **profilo di log**. Ogni sottoscrizione di Azure può avere un solo profilo di log. Queste impostazioni possono essere configurate tramite l'opzione **Esporta** nel pannello log attività nel portale. oppure a livello di codice tramite l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx), i cmdlet di PowerShell o l'interfaccia della riga di comando.

Il profilo di log definisce gli elementi seguenti.

**Dove deve essere inviato il log attività.** Attualmente, le opzioni disponibili sono account di archiviazione o hub eventi.

**Le categorie di eventi da inviare.** Il significato della *categoria* nei profili di log e negli eventi del log attività è diverso. Nel profilo di log, *Category* rappresenta il tipo di operazione (Write, DELETE, Action). In un evento del log attività, la proprietà *Category*"* rappresenta l'origine o il tipo di evento (ad esempio, amministrazione, ServiceHealth e avviso).

**Quali aree (posizioni) devono essere esportate.** È necessario includere tutti i percorsi poiché molti eventi nel log attività sono eventi globali.

**Durata del mantenimento del log attività in un account di archiviazione.** Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.

Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, i criteri di conservazione non hanno alcun effetto. I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione.



> [!WARNING]
> Il formato dei dati di log nell'account di archiviazione è stato modificato in righe JSON dal 1° novembre 2018. [Vedere questo articolo per una descrizione dell'impatto e per informazioni su come aggiornare gli strumenti per gestire il nuovo formato.](diagnostic-logs-append-blobs.md)


> [!IMPORTANT]
> Se il provider di risorse Microsoft. Insights non è registrato, è possibile che venga visualizzato un errore durante la creazione di un profilo di log. Per registrare questo provider [, vedere provider e tipi di risorse di Azure](../../azure-resource-manager/resource-manager-supported-services.md) .

### <a name="create-log-profile-using-the-azure-portal"></a>Creare un profilo di log usando il portale di Azure

Creare o modificare un profilo di log con l'opzione **Esporta in hub eventi** nel portale di Azure.

1. Dal menu **monitoraggio** nella portale di Azure selezionare **Esporta in hub eventi**.

    ![Pulsante Esporta nel portale](media/activity-log-export/portal-export.png)

3. Nel pannello che viene visualizzato specificare quanto segue:
   * Aree con gli eventi da esportare. È necessario selezionare tutte le aree per assicurarsi di non perdere gli eventi chiave perché il log attività è un log globale (non regionale), quindi la maggior parte degli eventi non dispone di un'area associata. 
   * Se si vuole scrivere nell'account di archiviazione:
       * L'account di archiviazione in cui si desidera salvare gli eventi.
       * Il numero di giorni per cui si vogliono conservare questi eventi nell'archivio. (se si impostano 0 giorni, i log vengono conservati all'infinito)
   * Se si vuole scrivere nell'hub eventi:
       * Lo spazio dei nomi del bus di servizio in cui si vuole creare un hub eventi per lo streaming di questi eventi.

     ![Pannello Esporta log di controllo](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.


### <a name="configure-log-profile-using-powershell"></a>Configurare il profilo di log con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

    | Proprietà | Obbligatorio | DESCRIZIONE |
    | --- | --- | --- |
    | Name |Sì |Nome del profilo di log. |
    | StorageAccountId |No |ID risorsa dell'account di archiviazione in cui deve essere salvato il log attività. |
    | serviceBusRuleId |No |ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui creare gli hub eventi. Si tratta di una stringa nel formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. |
    | RetentionInDays |Yes |Numero di giorni per cui gli eventi devono essere conservati nell'account di archiviazione, tra 1 e 2147483647. Se il valore è zero, i log vengono conservati all'infinito. |
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

    | Proprietà | Obbligatorio | Descrizione |
    | --- | --- | --- |
    | name |Yes |Nome del profilo di log. |
    | storage-account-id |Yes |ID risorsa dell'account di archiviazione in cui salvare i log attività. |
    | locations |Yes |Elenco delimitato da spazi di aree per cui raccogliere eventi del log attività. È possibile visualizzare un elenco di tutte le aree per la sottoscrizione tramite `az account list-locations --query [].name`. |
    | days |Yes |Numero di giorni per cui devono essere conservati gli eventi, tra 1 e 365. Se il valore è zero, i log vengono archiviati per un periodo illimitato.  Se è zero, il parametro abilitato deve essere impostato su true. |
    |enabled | Yes |True o False.  Consente di abilitare o disabilitare i criteri di conservazione.  Se True, il parametro days deve essere un valore maggiore di 0.
    | categories |Yes |Elenco delimitato da spazi di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |



## <a name="activity-log-schema"></a>Schema del log attività
Indipendentemente dal fatto che venga inviato ad archiviazione di Azure o a hub eventi, i dati del log attività verranno scritti in JSON con il formato seguente.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
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
Gli elementi in questo JSON sono descritti nella tabella seguente.

| Nome dell'elemento | DESCRIZIONE |
| --- | --- |
| time |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| resourceId |ID risorsa della risorsa interessata. |
| operationName |Nome dell'operazione. |
| category |Categoria dell'azione, ad esempio scrittura o lettura. |
| resultType |Il tipo di risultato, ad esempio operazione riuscita, esito negativo, avvio |
| resultSignature |Dipende dal tipo di risorsa. |
| durationMs |Durata dell'operazione in millisecondi |
| callerIpAddress |Indirizzo IP dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. |
| correlationId |In genere un GUID in formato stringa. Gli eventi che condividono un elemento correlationId appartengono alla stessa azione. |
| identity |BLOB JSON che descrive l'autorizzazione e le attestazioni. |
| authorization |BLOB delle proprietà RBAC dell'evento. In genere include le proprietà "action", "role" e "scope". |
| level |Livello dell'evento. Uno dei valori seguenti: _Critico_, _errore_, _avviso_,informativo e _dettagliato_ |
| location |Area in cui si trova la località (o global). |
| properties |Set di coppie `<Key, Value>` ad esempio Dictionary, che descrivono i dettagli dell'evento. |

> [!NOTE]
> Le proprietà e l'utilizzo di queste proprietà possono variare a seconda della risorsa.



## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul log attività](../../azure-resource-manager/resource-group-audit.md)
* [Raccogliere log attività nei log di monitoraggio di Azure](activity-log-collect.md)
