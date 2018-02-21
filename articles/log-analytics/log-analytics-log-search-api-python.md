---
title: Script Python per recuperare dati da Azure Log Analytics | Microsoft Docs
description: L'API di ricerca log Log Analytics consente a qualsiasi client API REST di recuperare dati da un'area di lavoro di Log Analytics.  Questo articolo fornisce uno script Python di esempio tramite l'API di ricerca log.
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: bwren
ms.openlocfilehash: a8a4ec7a6ddf2daeca6ead11460fa076a7eb5c94
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>Recuperare i dati da Log Analytics con uno script Python
L'[API di ricerca log Log Analytics](log-analytics-log-search-api.md) consente a qualsiasi client API REST di recuperare dati da un'area di lavoro di Log Analytics.  Questo articolo presenta uno script Python di esempio che utilizza l'API di ricerca log Log Analytics.  

>[!NOTE]
> Questo articolo usa le API di ricerca log con il linguaggio di query legacy di Log Analytics.  A questo articolo verrà fornito un aggiornamento per le aree di lavoro aggiornate al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md).

## <a name="authentication"></a>Authentication
Questo script utilizza un'entità servizio in Azure Active Directory per l'autenticazione nell'area di lavoro.  Le entità servizio consentono a un'applicazione client di richiedere al servizio l'autenticazione di un account anche se il client non ha il nome dell'account. Prima di eseguire lo script, creare un'entità servizio utilizzando il processo di [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../azure-resource-manager/resource-group-create-service-principal-portal.md).  È necessario fornire allo script l'ID applicazione, l'ID tenant e la chiave di autenticazione. 

> [!NOTE]
> Quando si [crea un account di Automazione di Azure](../automation/automation-create-standalone-account.md), viene creata un'entità servizio adatta all'uso con questo script.  Se si ha già un'entità servizio creata da Automazione di Azure, è possibile utilizzare quella anziché crearne una nuova, anche se può essere necessario [creare una chiave di autenticazione](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) se l'entità non ne ha già una.

## <a name="script"></a>Script
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'[API di ricerca log Log Analytics](log-analytics-log-search-api.md).