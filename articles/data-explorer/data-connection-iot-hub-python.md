---
title: Creare una connessione dati dell'hub IoT per Azure Data Explorer tramite PythonCreate an IoT Hub data connection for Azure Data Explorer by using Python
description: In this article, you learn how to create an IoT Hub data connection for Azure Data Explorer by using Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 76c8ca24882f465bf2a973dc59736745178fc61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669523"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Creare una connessione dati dell'hub IoT per Azure Data Explorer usando Python (anteprima)Create an IoT Hub data connection for Azure Data Explorer by using Python (Preview)

> [!div class="op_single_selector"]
> * [Portale](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](data-connection-iot-hub-resource-manager.md)

In this article, you create an IoT Hub data connection for Azure Data Explorer by using Python. Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Data Explorer offre l'inserimento o il caricamento dei dati da Hub eventi, hub IoT e BLOB scritti in contenitori BLOB.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Un cluster e](create-cluster-database-python.md)un database .

* [Mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Criteri di database e tabelle](database-table-policies-python.md) (facoltativo).

* [Un hub IoT con un criterio](ingest-data-iot-hub.md#create-an-iot-hub)di accesso condiviso configurato.

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Aggiungere una connessione dati all'hub IoTAdd an IoT Hub data connection 

Nell'esempio seguente viene illustrato come aggiungere una connessione dati all'hub IoT a livello di codice. Vedere Connettere la tabella di [Azure Data Explorer all'hub IoT](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) per aggiungere una connessione dati dell'hub Iot usando il portale di Azure.See connect Azure Data Explorer table to IoT Hub for adding an Iot Hub data connection using the Azure portal.

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
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID sottoscrizione utilizzato per la creazione delle risorse.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client dell'applicazione che può accedere alle risorse nel tenant.|
| client_secret | *xxxxxxxxxxxxxx* | Segreto client dell'applicazione che può accedere alle risorse nel tenant. |
| resource_group_name | *testrg* | Nome del gruppo di risorse contenente il cluster.|
| cluster_name | *mykustocluster* | Nome del cluster.|
| database_name | *mykustodatabase* | Nome del database di destinazione nel cluster.|
| data_connection_name | *myeventhubconnect* | Il nome desiderato della connessione dati.|
| table_name | *Eventi Storm* | Nome della tabella di destinazione nel database di destinazione.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nome del mapping di colonna correlato alla tabella di destinazione.|
| data_format | *Csv* | Formato dati del messaggio.|
| iot_hub_resource_id | *ID risorsa* | ID risorsa dell'hub IoT che contiene i dati per l'inserimento.|
| shared_access_policy_name | *iothubforread* | Nome dei criteri di accesso condiviso che definisce le autorizzazioni per la connessione di dispositivi e servizi all'hub IoT. |
| consumer_group | *$Default* | Gruppo di consumer dell'hub eventi.|
| posizione | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]