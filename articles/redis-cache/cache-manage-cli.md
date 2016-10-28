<properties 
	pageTitle="Come creare e gestire Cache Redis di Azure tramite l'interfaccia della riga di comando di Azure (Azure CLI) | Microsoft Azure" 
	description="Come installare l'interfaccia della riga di comando di Azure su qualsiasi piattaforma, come utilizzarla per connettersi all'account Azure e come creare e gestire una Cache Redis dall'interfaccia della riga di comando di Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/> 

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/> 

# Come creare e gestire Cache Redis di Azure tramite l'interfaccia della riga di comando di Azure (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Interfaccia della riga di comando di Azure](cache-manage-cli.md)

L'interfaccia della riga di comando di Azure è un ottimo modo di gestire l'infrastruttura di Azure da qualsiasi piattaforma. In questo articolo viene illustrato come creare e gestire le istanze di Cache Redis di Azure tramite la CLI di Azure.

## Prerequisiti

Per creare e gestire le istanze di Cache Redis di Azure utilizzando CLI di Azure, è necessario completare i passaggi seguenti.

-	È necessario disporre di un account Azure. Se non è disponibile, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi istanti.
-	[Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)
-	Connettere l'installazione dell’interfaccia della riga di comando di Azure con un account Azure personale o con un account di lavoro o scolastico di Azure, e accedere dall’interfaccia della riga di comando di Azure utilizzando il comando `azure login`. Per comprendere le differenze e scegliere, vedere [Connettersi a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure (Azure CLI)](../xplat-cli-connect.md).
-	Prima di eseguire uno dei seguenti comandi, passare l’interfaccia della riga di comando di Azure in modalità di Gestione delle risorse eseguendo il comando `azure config mode arm`. Per altre informazioni, vedere [Impostare la modalità di Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode).

## Proprietà di Cache Redis

Le seguenti proprietà vengono utilizzate durante la creazione e l’aggiornamento delle istanze di Cache Redis.

| Proprietà | Switch | Descrizione |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name | -n, --name | Nome della Cache Redis. |
| gruppo di risorse | -g, --resource-group | Nome del gruppo di risorse. |
| location | -l, --location | Posizione in cui creare una cache. |
| size | -z, --size | Dimensione della Cache Redis. Valori validi: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku | -x, sku: | SKU di Redis. Deve essere uno di: [Basic, Standard, Premium] |
| EnableNonSslPort | -e, --enable-non-ssl-port | Proprietà EnableNonSslPort della Cache Redis. Aggiungere questo flag se si desidera abilitare la porta SSL Non per la cache |
| Configurazione di Redis | -c, --redis-configuration | Configurazione di Redis. Immettere qui una stringa in formato JSON di chiavi di configurazione e valori. Formato: "{"":"","":""}" |
| Configurazione di Redis | -f, --redis-configuration-file | Configurazione di Redis. Immettere qui il percorso di un file contenente le chiavi di configurazione e i valori. Formato per l’immissione del file: {"": "","": ""} |
| Numero di partizioni | -r, --shard-count | Numero di partizioni da creare su una cache di cluster Premium con il clustering. |
| Rete virtuale | -v, --virtual-network | Quando si ospita la cache in una rete virtuale, specifica l'ID risorsa esatto ARM della rete virtuale in cui distribuire la cache Redis. Formato di esempio: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo di chiave | -t, --key-type | Tipo di chiave per il rinnovo. Valori validi: [primario, secondario] |
| StaticIP | -p, --static-ip <static-ip> | Quando si ospita la cache in una rete virtuale, specifica l'indirizzo IP univoco nella subnet per la cache. Se non specificato, ne verrà scelto uno dalla subnet. |
| Subnet | t, --subnet <subnet> | Quando si ospita la cache in una rete virtuale, specifica il nome della subnet in cui distribuire la cache. |
| VirtualNetwork | -v, --virtual-network <virtual-network> | Quando si ospita la cache in una rete virtuale, specifica l'ID risorsa esatto ARM della rete virtuale in cui distribuire la cache Redis. Formato di esempio: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscription | -s, --subscription | L'identificatore della sottoscrizione. |

## Vedere tutti i comandi di Cache Redis

Per visualizzare tutti i comandi di Cache Redis e i relativi parametri, utilizzare il comando `azure rediscache -h`.

	C:\>azure rediscache -h
	help:    Commands to manage your Azure Redis Cache(s)
	help:
	help:    Create a Redis Cache
	help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Delete an existing Redis Cache
	help:      rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    List all Redis Caches within your Subscription or Resource Group
	help:      rediscache list [options]
	help:
	help:    Show properties of an existing Redis Cache
	help:      rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Change settings of an existing Redis Cache
	help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
	help:
	help:    Renew the authentication key for an existing Redis Cache
	help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help  output usage information
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Creare una Cache Redis

Per creare una Cache Redis, utilizzare il comando seguente:

	azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache create -h`.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                               output usage information
	help:      -v, --verbose                                            use verbose output
	help:      -vv                                                      more verbose with debug output
	help:      --json                                                   use json output
	help:      -n, --name <name>                                        Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>                    Name of the Resource Group
	help:      -l, --location <location>                                Location to create cache.
	help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
	help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
	help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
	help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
	help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
	help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
	help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
	help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
	help:      -s, --subscription <id>                                  the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Eliminare una Cache Redis esistente

Per eliminare una Cache Redis, utilizzare il comando seguente:

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache delete -h`.

	C:\>azure rediscache delete -h
	help:    Delete an existing Redis Cache
	help:
	help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Elenco di tutte le Cache Redis all'interno della sottoscrizione o di un gruppo di risorse

Per elencare tutte le Cache Redis all'interno della sottoscrizione o un gruppo di risorse, utilizzare il comando seguente:

	azure rediscache list [options]

Per ulteriori informazioni su questo comando, eseguire il comando `azure rediscache list -h`.

	C:\>azure rediscache list -h
	help:    List all Redis Caches within your Subscription or Resource Group
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

## Visualizzare le proprietà di una Cache Redis esistente

Per visualizzare le proprietà di una Cache Redis esistente, utilizzare il comando seguente:

	azure rediscache show [--name <name> --resource-group <resource-group>]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache show -h`.

	C:\>azure rediscache show -h
	help:    Show properties of an existing Redis Cache
	help:
	help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## Modificare le impostazioni di una Cache Redis esistente

Per modificare le impostazioni di una Cache Redis esistente, utilizzare il comando seguente:

	azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache set -h`.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
	help:
	help:    Options:
	help:      -h, --help                                               output usage information
	help:      -v, --verbose                                            use verbose output
	help:      -vv                                                      more verbose with debug output
	help:      --json                                                   use json output
	help:      -n, --name <name>                                        Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>                    Name of the Resource Group
	help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
	help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
	help:      -s, --subscription <subscription>                        the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Rinnovare la chiave di autenticazione per una Cache Redis esistente

Per rinnovare la chiave di autenticazione per una Cache Redis esistente, utilizzare il comando seguente:

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Specificare `Primary` o `Secondary` per `key-type`.

Per altre informazioni su questo comando, eseguire il comando `azure rediscache renew-key -h`.

	C:\>azure rediscache renew-key -h
	help:    Renew the authentication key for an existing Redis Cache
	help:
	help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
	help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Chiavi di elenco primario e secondario di una Cache Redis esistente

Per le chiavi di elenco primario o secondario di una Cache Redis esistente, utilizzare il comando seguente:

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Per altre informazioni su questo comando, eseguire il comando `azure rediscache list-keys -h`.

	C:\>azure rediscache list-keys -h
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:
	help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<!---HONumber=AcomDC_0921_2016-->