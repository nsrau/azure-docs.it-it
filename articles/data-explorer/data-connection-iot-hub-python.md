---
title: Creare una connessione dati dell'hub Internet per Azure Esplora dati usando Python
description: Questo articolo illustra come creare una connessione dati dell'hub Internet per Azure Esplora dati usando Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cfd92546def21972e37781bd8a4b0bfefda9111f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444214"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Creare una connessione dati dell'hub Internet per Azure Esplora dati usando Python (anteprima)

> [!div class="op_single_selector"]
> * [Portale](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modello di Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

Questo articolo illustra come creare una connessione dati dell'hub Internet per Azure Esplora dati usando Python. Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Esplora dati offre l'inserimento, il caricamento di dati, gli hub eventi, gli hub Internet e i BLOB scritti nei contenitori BLOB.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Un cluster e un database](/create-cluster-database-python.md).

* [Mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Criteri di database e tabella](database-table-policies-python.md) (facoltativo).

* [Un hub cose con criteri di accesso condiviso configurati](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Aggiungere una connessione dati dell'hub Internet 

L'esempio seguente illustra come aggiungere una connessione dati dell'hub Internet a livello di codice. Vedere [connettere Azure Esplora dati table all'hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) Internet per l'aggiunta di una connessione dati dell'hub Internet con l'portale di Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Impostazione** | **Valore consigliato** | **Descrizione campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant. Noto anche come ID directory.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID sottoscrizione usato per la creazione di risorse.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client dell'applicazione che può accedere alle risorse nel tenant.|
| client_secret | *xxxxxxxxxxxxxx* | Il segreto client dell'applicazione che può accedere alle risorse nel tenant. |
| resource_group_name | *testrg* | Nome del gruppo di risorse che contiene il cluster.|
| cluster_name | *mykustocluster* | Nome del cluster.|
| database_name | *mykustodatabase* | Nome del database di destinazione nel cluster.|
| data_connection_name | *myeventhubconnect* | Nome desiderato della connessione dati.|
| table_name | *StormEvents* | Nome della tabella di destinazione nel database di destinazione.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nome del mapping di colonne correlato alla tabella di destinazione.|
| data_format | *CSV* | Formato dati del messaggio.|
| iot_hub_resource_id | *ID risorsa* | ID risorsa dell'hub Internet che include i dati per l'inserimento.|
| shared_access_policy_name | *iothubforread* | Nome dei criteri di accesso condiviso che definiscono le autorizzazioni per i dispositivi e i servizi per la connessione all'hub Internet. |
| consumer_group | *$Default* | Il gruppo di consumer dell'hub eventi.|
| posizione | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]