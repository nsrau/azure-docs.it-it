---
title: API REST di ricerca nei log di Log Analytics | Documentazione Microsoft
description: Questa guida contiene un&quot;esercitazione di base che descrive come usare l&quot;API REST di ricerca di Log Analytics in Operations Management Suite (OMS) e offre esempi che indicano come usare i comandi.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: b12f823d723b013755fc868b883faefa2072eb75
ms.openlocfilehash: 9b21fed003f96dbf7ebd72d6f46fff91acbf039e
ms.lasthandoff: 12/02/2016


---
# <a name="log-analytics-log-search-rest-api"></a>API REST di Log Analytics per la ricerca nei log
Questa guida fornisce un'esercitazione di base, inclusi alcuni esempi, per l'uso dell'API REST di Log Analytics per la ricerca. Log Analytics fa parte di Operations Management Suite (OMS).

> [!NOTE]
> In precedenza Log Analytics era chiamato Operational Insights e questo è il motivo per cui questo nome viene usato nel provider di risorse.
>
>

## <a name="overview-of-the-log-search-rest-api"></a>Panoramica dell'API REST di ricerca di log
L'API REST di ricerca di Log Analytics è RESTful e accessibile tramite l'API Azure Resource Manager. Questo articolo presenta alcuni esempi di accesso all'API tramite [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento da riga di comando open source che semplifica la chiamata dell'API di Azure Resource Manager. L'uso di ARMClient è una delle numerose opzioni disponibili per accedere all'API di ricerca di Log Analytics. Un'altra possibilità è quella di usare il modulo Azure PowerShell per OperationalInsights che include cmdlet per l'accesso alla ricerca. Con questi strumenti è possibile usare l'API di Azure Resource Manager per effettuare chiamate alle aree di lavoro di OMS ed eseguire i comandi di ricerca al loro interno. L'API restituisce i risultati della ricerca in formato JSON, consentendo di usare i risultati della ricerca in molti modi diversi a livello di codice.

È possibile usare Azure Resource Manager tramite una [libreria per NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) e l'[API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Per altre informazioni, vedere le pagine Web collegate.

> [!NOTE]
> Se si usa un comando di aggregazione, ad esempio `|measure count()` o `distinct`, ogni chiamata per la ricerca può restituire fino a 500.000 record. Le ricerche che non includono un comando di aggregazione restituiscono fino a 5.000 record.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Esercitazione di base sull'API REST di ricerca di Log Analytics
### <a name="to-use-armclient"></a>Per usare ARMClient
1. Installare [Chocolatey](https://chocolatey.org/), un gestore di pacchetti open source per Windows. Aprire una finestra di prompt di comandi come amministratore ed eseguire il comando seguente:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. Installare ARMClient eseguendo il comando seguente:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>Per eseguire una ricerca usando ARMClient
1. Effettuare l'accesso usando il proprio account Microsoft oppure l'account aziendale o dell'istituto di istruzione:

    ```
    armclient login
    ```

    Se l’accesso viene effettuato correttamente, vengono elencate tutte le sottoscrizioni associate all'account specificato.

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Ottenere le aree di lavoro di Operations Management Suite.

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Una chiamata Get riuscita genera tutte le aree di lavoro associate alla sottoscrizione.

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Creare la variabile di ricerca.

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Eseguire la ricerca usando la nuova variabile di ricerca.

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Esempi di riferimento per l'API REST di ricerca di Log Analytics
I codici di esempio seguenti mostrano come usare l’API di ricerca.

### <a name="search---actionread"></a>Ricerca - Azione/Lettura
**Url di esempio:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Richiesta:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
La tabella seguente descrive le proprietà disponibili.

| **Proprietà** | **Descrizione** |
| --- | --- |
| top |Il numero massimo di risultati da restituire. |
| highlight |Contiene i parametri pre e post, in genere usati per evidenziare i campi corrispondenti |
| pre |Antepone la stringa specificata ai campi corrispondenti. |
| post |Appone la stringa specificata ai campi corrispondenti. |
| query |La query di ricerca usata per raccogliere e restituire i risultati. |
| start |L’inizio dell'intervallo di tempo da cui si desidera trovare risultati. |
| end |La fine dell'intervallo di tempo da cui si desidera trovare risultati. |

**Risposta:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Ricerca/{ID} - Azione/Lettura
**Richiedere il contenuto di una ricerca salvata:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> Se la ricerca restituisce lo stato 'Sospeso', il polling dei risultati aggiornati può essere effettuato tramite questa API. Dopo 6 min il risultato della ricerca verrà rimosso dalla cache e sarà restituito Http Gone. Se la richiesta di ricerca iniziale restituisce immediatamente lo stato di operazione riuscita, i risultati non vengono aggiunti alla cache e l'API restituisce HTTP Gone in caso di query. Il contenuto di un risultato HTTP 200 è nello stesso formato della richiesta di ricerca iniziale, ma con i valori aggiornati.
>
>

### <a name="saved-searches"></a>Ricerche salvate
**Elenco di richieste delle ricerche salvate:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Metodi supportati: GET PUT DELETE

Metodi di raccolta supportati: GET

La tabella seguente descrive le proprietà disponibili.

| Proprietà | Descrizione |
| --- | --- |
| ID |Identificatore univoco. |
| ETag |**Obbligatoria per l'applicazione di patch**. Aggiornata dal server a ogni scrittura. Il valore deve essere uguale al valore archiviato attuale o a "*" per effettuare l'aggiornamento. Viene restituito il codice 409 per i valori obsoleti/non validi. |
| properties.query |**Obbligatoria**. Query di ricerca. |
| properties.displayName |**Obbligatoria**. Nome visualizzato della query, definito dall'utente. |
| properties.category |**Obbligatoria**. Categoria della query, definita dall'utente. |

> [!NOTE]
> L'API di ricerca di Log Analytics restituisce attualmente le ricerche salvate create dall'utente quando viene eseguito il polling di ricerche salvate in un'area di lavoro. L'API non restituisce le ricerche salvate fornite dalle soluzioni.
>
>

### <a name="create-saved-searches"></a>Creare le ricerche salvate
**Richiesta:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Eliminare le ricerche salvate
**Richiesta:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Aggiornare le ricerche salvate
 **Richiesta:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadati - Solo JSON
Ecco come visualizzare i campi per tutti i tipi di log per i dati raccolti nell'area di lavoro. Ad esempio, se si vuole sapere se il tipo di evento include un campo denominato Computer, questa query rappresenta uno dei modi per controllare.

**Richiesta di campi:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Risposta:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

La tabella seguente descrive le proprietà disponibili.

| **Proprietà** | **Descrizione** |
| --- | --- |
| name |Nome del campo. |
| displayName |Nome visualizzato del campo. |
| type |Tipo del valore del campo. |
| facetable |Combinazione delle proprietà "indexed", "stored" e "facet" attuali. |
| display |Proprietà "display" attuale. True se il campo è visibile nella ricerca. |
| ownerType |Ridotta solo ai tipi appartenenti agli indirizzi IP caricati. |

## <a name="optional-parameters"></a>Parametri facoltativi
Le informazioni seguenti illustrano i parametri facoltativi disponibili.

### <a name="highlighting"></a>Evidenziazione
Il parametro "Highlight" è un parametro facoltativo che può essere usato per richiedere che il sottosistema di ricerca includa un set di marcatori nella risposta.

Questi marcatori indicano l'inizio e la fine del testo evidenziato che corrisponde ai termini forniti nella query di ricerca.
È possibile specificare i marcatori di inizio e di fine che vengono usati dalla ricerca per eseguire il wrapping del termine evidenziato.

**Query di ricerca di esempio**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Risultati di esempio:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Si noti che il risultato precedente contiene un messaggio di errore con prefisso e suffisso.

## <a name="computer-groups"></a>Gruppi di computer
I gruppi di computer sono ricerche salvate speciali che restituiscono un set di computer.  È possibile usare un gruppo di computer in altre query per limitare i risultati ai computer nel gruppo.  Un gruppo di computer viene implementato come una ricerca salvata con un tag Gruppo con il valore Computer.

Di seguito viene riportata una risposta di esempio per un gruppo di computer.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>Recupero dei gruppi di computer
Per recuperare un gruppo di computer, usare il metodo Get con l'ID del gruppo.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Creazione o aggiornamento di un gruppo di computer
Per creare un gruppo di computer, usare il metodo Put con un ID di ricerca salvato univoco. Se si usa un ID di gruppo di computer esistente, tale gruppo viene modificato. Quando si crea un gruppo di computer nel portale di Log Analytics, l'ID viene creato dal gruppo e dal nome.

La query usata per la definizione del gruppo deve restituire un insieme di computer per il gruppo per funzionare correttamente.  È consigliabile concludere la query con `| Distinct Computer` per assicurarsi che vengano restituiti i dati corretti.

La definizione di una ricerca salvata deve includere un tag denominato Gruppo con un valore Computer affinché la ricerca venga classificata come un gruppo di computer.

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>Eliminazione di gruppi di computer
Per eliminare un gruppo di computer, usare il metodo Delete con l'ID del gruppo.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come creare query usando i campi personalizzati come criteri, vedere l'articolo relativo alle [ricerche nei log](log-analytics-log-searches.md) .

