<properties
   pageTitle="API REST degli avvisi di Log Analytics"
   description="L’API REST degli avvisi di Log Analytics consente di creare e gestire avvisi in OMS (Operations Management Suite)  In questo articolo vengono forniti i dettagli dell'API e alcuni esempi per l'esecuzione di diverse operazioni."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />


# <a name="log-analytics-alert-rest-api"></a>API REST degli avvisi di Log Analytics

L’API REST degli avvisi di Log Analytics consente di creare e gestire avvisi in OMS (Operations Management Suite)  In questo articolo vengono forniti i dettagli dell'API e alcuni esempi per l'esecuzione di diverse operazioni.

L'API REST di ricerca di Log Analytics è RESTful ed è accessibile tramite l'API REST Azure Resource Manager. In questo documento sono disponibili esempi in cui viene usata l'API di una riga di comando di PowerShell che a sua volta usa [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento della riga di comando open source che semplifica la chiamata dell'API di Azure Resource Manager. L'uso di ARMClient e PowerShell è una delle numerose opzioni di accesso all'API di ricerca di Log Analytics. Con questi strumenti è possibile usare l'API RESTful Azure Resource Manager per effettuare chiamate alle aree di lavoro di OMS ed eseguire i comandi di ricerca al loro interno. L'API fornirà risultati della ricerca per l'utente in formato JSON, consentendo di usare i risultati della ricerca in molti modi diversi a livello di codice.

## <a name="prerequisites"></a>Prerequisiti
Attualmente, gli avvisi possono essere creati solo con una ricerca salvata in Log Analytics.  Per ulteriori informazioni, fare riferimento all’ [API REST di ricerca log](log-analytics-log-search-api.md) .

## <a name="schedules"></a>Pianificazioni
Una ricerca salvata può avere una o più pianificazioni. La pianificazione definisce la frequenza con cui viene eseguita la ricerca e l'intervallo di tempo in cui vengono identificati i criteri.
Le pianificazioni includono le proprietà elencate nella tabella seguente.

| Proprietà  | Descrizione |
|:--|:--|
| Interval | La frequenza con cui viene eseguita la ricerca. Il valore è espresso in minuti. |
| QueryTimeSpan | L'intervallo di tempo durante il quale vengono valutati i criteri. Deve essere maggiore o uguale a Interval. Il valore è espresso in minuti. |
| Version | La versione API utilizzata.  Attualmente, deve sempre essere impostata su 1. |

Si consideri ad esempio una query eventi con Interval pari a 15 minuti e Timespan pari a 30 minuti. In questo caso, la query viene eseguita ogni 15 minuti e viene generato un avviso se i criteri continuano a restituire true in un intervallo di 30 minuti.

### <a name="retrieving-schedules"></a>Recupero delle pianificazioni
Utilizzare il metodo Get per recuperare tutte le pianificazioni per una ricerca salvata.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Usare il metodo Get con l'ID pianificazione per recuperare una determinata pianificazione per una ricerca salvata.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Di seguito è riportata una risposta di esempio per una pianificazione.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Creazione di una pianificazione
Usare il metodo Put con un ID pianificazione univoco per creare una nuova pianificazione.  Si noti che due pianificazioni non possono avere lo stesso ID anche se sono associate a ricerche diverse.  Quando si crea una pianificazione nella console di OMS, viene creato un GUID per l'ID pianificazione.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Modifica di una pianificazione
Usare il metodo Put con un ID pianificazione esistente per la stessa ricerca salvata per modificare la pianificazione.  Il corpo della richiesta deve includere il valore ETag della pianificazione.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Eliminazione delle pianificazioni
Per eliminare una pianificazione, utilizzare il metodo Delete con un ID pianificazione.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Azioni
Una pianificazione può avere più azioni. Un'azione può definire uno o più processi da eseguire, ad esempio l'invio di un messaggio di posta o l'avvio di un runbook o ancora può definire una soglia che determina quando i risultati di una ricerca corrispondono a certi criteri.  Alcune azioni definiranno entrambi, in modo che i processi vengano eseguiti quando viene raggiunta la soglia.

Tutte le azioni includono le proprietà elencate nella tabella seguente.  I vari tipi di avvisi hanno diverse proprietà aggiuntive che sono descritte di seguito.

| Proprietà | Descrizione |
|:--|:--|
| Type | Tipo di azione.  Attualmente, i valori possibili sono Alert e Webhook. |
| Name | Nome visualizzato per l'avviso. |
| Version | La versione API utilizzata.  Attualmente, deve sempre essere impostata su 1. |

### <a name="retrieving-actions"></a>Recupero delle azioni
Utilizzare il metodo Get per recuperare tutte le azioni per una pianificazione.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilizzare il metodo Get con l'ID azione per recuperare una determinata azione per una pianificazione.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Creazione o modifica di azioni
Usare il metodo Put con un ID azione univoco per la pianificazione per creare una nuova azione.  Quando si crea un'azione nella console di OMS, un GUID è destinato all'ID azione.

Usare il metodo Put con un ID azione esistente per la stessa ricerca salvata per modificare la pianificazione.  Il corpo della richiesta deve includere il valore ETag della pianificazione.

Il formato della richiesta per la creazione di una nuova azione varia in base al tipo di attività e alcuni esempi sono disponibili nelle sezioni seguenti.

### <a name="deleting-actions"></a>Eliminazione delle azioni
Utilizzare il metodo Delete con l'ID azione per eliminare un’azione.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Azioni di avviso
Una pianificazione deve avere una sola azione di avviso.  Le azioni di avviso includono una o più delle sezioni elencate nella tabella seguente.  Ciascuna è descritta in dettaglio di seguito.

| Sezione | Descrizione |
|:--|:--|
| Soglia | Criteri di esecuzione dell'azione. |  
| EmailNotification | Inviare messaggi a più destinatari. |
| Correzione | Avviare un runbook in Automazione di Azure per tentare di risolvere il problema identificato. |

#### <a name="thresholds"></a>Soglie
Un’azione di avviso deve avere una sola soglia.  Quando i risultati di una ricerca salvata corrispondano alla soglia di un'azione associata a tale ricerca, vengono eseguiti tutti gli altri processi in tale azione.  Inoltre, un'azione può contenere solo una soglia, in modo da poter essere utilizzata con azioni di altri tipi che non contengono soglie.

Le soglie includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Operator | Operatore di confronto soglie. <br> gt = Maggiore di <br>  lt = minore di |
| Value | Valore per la soglia. |

Si consideri ad esempio una query eventi con Interval pari a 15 minuti, Timespan pari a 30 minuti e Threshold maggiore di 10. In questo caso, la query viene eseguita ogni 15 minuti e viene generato un avviso se restituisce 10 eventi creati in un intervallo di 30 minuti.

Di seguito è riportata una risposta di esempio per un'azione con una sola soglia.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione di soglia per una pianificazione.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di soglia per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notifica tramite posta elettronica
Le notifiche tramite posta elettronica inviano i messaggi a uno o più destinatari.  Includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Recipients | Elenco di indirizzi di posta elettronica. |
| Subject | L’oggetto del messaggio. |
| Attachment | Gli allegati non sono attualmente supportati, pertanto il valore corrispondente sarà sempre "None". |

Di seguito è riportata una risposta di esempio per un'azione di notifica di posta elettronica con una determinata soglia.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione di posta elettronica per una pianificazione.  Nell'esempio seguente viene creata una notifica tramite posta elettronica con una determinata soglia, per cui il messaggio viene inviato quando i risultati della ricerca salvata superano la soglia.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di posta elettronica per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Azioni correttive
Le correzioni avviano un runbook in Automazione di Azure che tenta di risolvere il problema identificato dall'avviso.  È necessario creare un webhook per il runbook utilizzato in un'azione correttiva e quindi specificare l'URI nella proprietà WebhookUri.  Quando si crea questa azione utilizzando la console di OMS, viene creato automaticamente un nuovo webhook per il runbook.

Le correzioni includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| RunbookName | Nome del runbook. Deve corrispondere a un runbook pubblicato nell'account di automazione configurato nella soluzione di automazione nell'area di lavoro OMS. |
| WebhookUri | URL del webhook.
| Expiry | La data e l’ora di scadenza del webhook.  Se il webhook non ha una scadenza, questo può essere una data futura valida. |

Di seguito è riportata una risposta di esempio per un'azione correttiva con una determinata soglia.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione di correzione per una pianificazione.  Nell'esempio seguente viene creata una correzione con una determinata soglia, per cui il runbook viene avviato quando i risultati della ricerca salvata superano la soglia.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di correzione per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Esempio
Di seguito è riportato un esempio completo per creare un nuovo avviso di posta elettronica.  Verrà creata una nuova pianificazione con un'azione contenente una soglia e un messaggio di posta elettronica.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Azioni webhook
Le azioni webhook avviano un processo chiamando un URL e, facoltativamente, fornendo un payload da inviare.  Simili alle azioni correttive, sono destinate a webhook che possono richiamare processi diversi dai runbook di Automazione di Azure.  Hanno inoltre l'opzione aggiuntiva di fornire un payload da recapitare al processo remoto.

Le azioni webhook non hanno una soglia, ma devono invece essere aggiunte a una pianificazione che includa un'azione di avviso con una determinata soglia.  È possibile aggiungere più azioni webhook che verranno tutte eseguite al raggiungimento della soglia.

Le azioni webhook includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| WebhookUri | L’oggetto del messaggio. |
| CustomPayload | Payload personalizzato da inviare al webhook.  Il formato dipenderà dalle previsioni del webhook. |

Di seguito è riportato un esempio di risposta per un’azione webhook e un'azione di avviso associata con una determinata soglia.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Creazione o modifica di un’azione webhook
Usare il metodo Put con un ID azione univoco per creare una nuova azione di webhook per una pianificazione.  Nell'esempio seguente viene creata un'azione webhook e un’azione di avviso con una determinata soglia, in modo che il webhook verrà attivato quando i risultati della ricerca salvata supereranno tale soglia.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Usare il metodo Put con un ID azione esistente per modificare un'azione di webhook per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Passaggi successivi

- Utilizzare l’ [API REST per eseguire ricerche nei log](log-analytics-log-search-api.md) in Log Analytics.



<!--HONumber=Oct16_HO2-->


