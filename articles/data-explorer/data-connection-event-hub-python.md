---
title: Creare una connessione dati di hub eventi per Esplora dati di Azure con Python
description: Questo articolo illustra come creare una connessione dati di hub eventi per Esplora dati di Azure usando Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 113c039c9b7f0319bb724bcde1432044c0770672
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667680"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Creare una connessione dati di hub eventi per Esplora dati di Azure con Python

> [!div class="op_single_selector"]
> * [di Microsoft Azure](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modello di Azure Resource Manager](data-connection-event-hub-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Esplora dati offre l'inserimento (caricamento dei dati) dagli hub eventi, dagli hub Internet e dai BLOB scritti nei contenitori BLOB. In questo articolo si crea una connessione dati di hub eventi per Esplora dati di Azure usando Python.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account Azure gratuito](https://azure.microsoft.com/free/).
* Creare [un cluster e un database](create-cluster-database-csharp.md)
* Creazione del [mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Impostare i [criteri di database e tabella](database-table-policies-csharp.md) (facoltativo)
* Creare un [Hub eventi con i dati per](ingest-data-event-hub.md#create-an-event-hub)l'inserimento. 

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Aggiungere una connessione dati dell'hub eventi

L'esempio seguente illustra come aggiungere una connessione dati di hub eventi a livello di codice. Vedere [connettersi all'hub eventi](ingest-data-event-hub.md#connect-to-the-event-hub) per aggiungere una connessione dati di hub eventi usando il portale di Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
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
| event_hub_resource_id | *ID risorsa* | ID risorsa dell'hub eventi che include i dati per l'inserimento. |
| consumer_group | *$Default* | Il gruppo di consumer dell'hub eventi.|
| location | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]