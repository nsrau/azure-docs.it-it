---
title: Gestire Cache Redis di Azure con l'interfaccia della riga di comando classica di Azure | Microsoft Docs
description: Come installare l'interfaccia della riga di comando classica di Azure su qualsiasi piattaforma, come usarla per connettersi all'account Azure e come creare e gestire Cache Redis di Azure dall'interfaccia della riga di comando classica.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 3b4756635ae0ab0d282975a6376e60da5f148917
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755430"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Come creare e gestire Cache Redis di Azure mediante l'interfaccia della riga di comando classica di Azure
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Interfaccia della riga di comando classica di Azure](cache-manage-cli.md)
>

L'interfaccia della riga di comando classica di Azure è un ottimo modo di gestire l'infrastruttura di Azure da qualsiasi piattaforma. In questo articolo viene illustrato come creare e gestire le istanze di Cache Redis di Azure mediante l'interfaccia della riga di comando classica di Azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Per script di esempio dell'interfaccia della riga di comando di Azure più recenti, vedere gli [esempi di Cache Redis di Azure con l'interfaccia della riga di comando di Azure](cli-samples.md).

## <a name="prerequisites"></a>Prerequisiti
Per creare e gestire le istanze di Cache Redis di Azure usando l'interfaccia della riga di comando classica di Azure è necessario completare i seguenti passaggi.

* È necessario disporre di un account Azure. Se non è disponibile, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi istanti.
* [Installare l'interfaccia della riga di comando classica di Azure](../cli-install-nodejs.md).
* Connettere l'installazione dell’interfaccia della riga di comando di Azure con un account Azure personale o con un account di lavoro o scolastico di Azure e accedere dall’interfaccia della riga di comando classica usando il comando `azure login`.
* Prima di eseguire uno dei seguenti comandi, passare l’interfaccia della riga di comando classica in modalità di Gestione risorse eseguendo il comando `azure config mode arm`. Per altre informazioni, vedere l'articolo [Usare l'interfaccia della riga di comando classica di Azure per gestire risorse e gruppi di risorse di Azure](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Proprietà di Cache Redis di Azure
Le seguenti proprietà vengono utilizzate durante la creazione e l’aggiornamento delle istanze di Cache Redis di Azure.

| Proprietà | Switch | Description |
| --- | --- | --- |
| name |-n, --name |Nome della cache Redis di Azure. |
| gruppo di risorse |-g, --resource-group |Nome del gruppo di risorse. |
| location |-l, --location |Posizione in cui creare una cache. |
| size |-z, --size |Dimensioni della cache Redis di Azure. Valori validi: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, sku: |SKU di Redis. Deve essere uno di: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |Proprietà EnableNonSslPort della cache Redis di Azure. Aggiungere questo flag se si desidera abilitare la porta SSL Non per la cache |
| Configurazione di Redis |-c, --redis-configuration |Configurazione di Redis. Immettere qui una stringa in formato JSON di chiavi di configurazione e valori. Formato: "{"":"","":""}" |
| Configurazione di Redis |-f, --redis-configuration-file |Configurazione di Redis. Immettere qui il percorso di un file contenente le chiavi di configurazione e i valori. Formato per l’immissione del file: {"": "","": ""} |
| Numero di partizioni |-r, --shard-count |Numero di partizioni da creare su una cache di cluster Premium con il clustering. |
| Rete virtuale |-v, --virtual-network |Quando si ospita la cache in una rete virtuale, specifica l'ID risorsa ARM esatto della rete virtuale in cui distribuire Cache Redis di Azure. Formato di esempio: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo di chiave |-t, --key-type |Tipo di chiave per il rinnovo. Valori validi: [primario, secondario] |
| StaticIP |-p,--static-IP \<static-IP \> |Quando si ospita la cache in una rete virtuale, specifica l'indirizzo IP univoco nella subnet per la cache. Se non specificato, ne verrà scelto uno dalla subnet. |
| Subnet |t,--\<subnet subnet \> |Quando si ospita la cache in una rete virtuale, specifica il nome della subnet in cui distribuire la cache. |
| VirtualNetwork |-v,--Virtual-Network \<virtual-Network \> |Quando si ospita la cache in una rete virtuale, specifica l'ID risorsa ARM esatto della rete virtuale in cui distribuire Cache Redis di Azure. Formato di esempio: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Sottoscrizione |-s, --subscription |L'identificatore della sottoscrizione. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Visualizzare tutti i comandi di Cache Redis di Azure
Per visualizzare tutti i comandi di Cache Redis di Azure e i relativi parametri, usare il comando `azure rediscache -h`.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Cache for Redis(s)
    help:
    help:    Create an Azure Cache for Redis
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Azure Cache for Redis
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Azure Cache for Redis
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Azure Cache for Redis
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-an-azure-cache-for-redis"></a>Creare una cache Redis di Azure
Per creare una cache Redis di Azure, usare il comando seguente:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache create -h`.

    C:\>azure rediscache create -h
    help:    Create an Azure Cache for Redis
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>Eliminare una cache Redis di Azure esistente
Per eliminare una cache Redis di Azure usare il comando seguente:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache delete -h`.

    C:\>azure rediscache delete -h
    help:    Delete an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Elencare tutte le cache Redis di Azure all'interno della sottoscrizione o del gruppo di risorse
Per elencare tutte le cache Redis di Azure all'interno della sottoscrizione o del gruppo di risorse, usare il comando seguente:

    azure rediscache list [options]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache list -h`.

    C:\>azure rediscache list -h
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Visualizzare le proprietà di una cache Redis di Azure esistente
Per visualizzare le proprietà di una cache Redis di Azure esistente, usare il comando seguente:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache show -h`.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Modificare le impostazioni di una cache Redis di Azure esistente
Per modificare le impostazioni di una cache Redis di Azure esistente, usare il comando seguente:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache set -h`.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Rinnovare la chiave di autenticazione di una cache Redis di Azure esistente
Per rinnovare la chiave di autenticazione di una cache Redis di Azure esistente, usare il comando seguente:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Specificare `Primary` o `Secondary` per `key-type`.

Per altre informazioni su questo comando, eseguire il comando `azure rediscache renew-key -h`.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Elencare le chiavi primarie e secondarie di una cache Redis di Azure esistente
Per elencare le chiavi primarie e secondarie di una cache Redis di Azure esistente, usare il comando seguente:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache list-keys -h`.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
