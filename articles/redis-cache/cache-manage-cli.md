<properties 
	pageTitle="Come creare e gestire Cache Redis di Azure tramite l'interfaccia della riga di comando di Azure (Azure CLI)" 
	description="Come installare l'interfaccia della riga di comando di Azure su qualsiasi piattaforma, come utilizzarla per connettersi all'account Azure e come creare e gestire una Cache Redis dall'interfaccia della riga di comando di Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Come creare e gestire Cache Redis di Azure tramite l'interfaccia della riga di comando di Azure (Azure CLI)

L'interfaccia della riga di comando di Azure è un ottimo modo di gestire l'infrastruttura di Azure da qualsiasi piattaforma. In questo articolo viene illustrato come creare e gestire le istanze di Cache Redis di Azure tramite la CLI di Azure.

## Prerequisiti

Per creare e gestire le istanze di Cache Redis di Azure utilizzando CLI di Azure, è necessario completare i passaggi seguenti.

-	È necessario disporre di un account Azure. Se non si dispone di uno, è possibile creare un [account di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/) in pochi istanti.
-	[Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
-	Connettere l'installazione di Azure CLI con un account Azure personale o con un account di lavoro o scolastico di Azure, e accedere dal CLI di Azure utilizzando il comando `azure login`. Per comprendere le differenze e scegliere, vedere [Connetti a una sottoscrizione di Azure dall'interfaccia della riga di comando Azure (Azure CLI)](../xplat-cli-connect.md).
-	Prima di eseguire uno dei seguenti comandi, passare CLI Azure in modalità di gestione delle risorse eseguendo il comando `azure config mode arm`. Per ulteriori informazioni, vedere [Impostare la modalità di gestione risorse di Azure](../virtual-machines/xplat-cli-azure-resource-manager.md#setting-the-azure-resource-manager-mode).

## Proprietà di Cache Redis

Le seguenti proprietà vengono utilizzate durante la creazione e l’aggiornamento delle istanze di Cache Redis.

| Proprietà | Switch | Descrizione |
|------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| name | -n, --name | Nome della Cache Redis. |
| gruppo di risorse | -g, --resource-group | Nome del gruppo di risorse. |
| location | -l, --location | Posizione in cui creare una cache. |
| size | -z, --size | Dimensione della Cache Redis. Valori validi: [C0, C1, C2, C3, C4, C5, C6] |
| sku | -x, sku: | SKU di Redis. Deve essere uno di: [Standard di base] |
| MaxMemoryPolicy | -m, --max-memory-policy | Proprietà MaxMemoryPolicy della Cache Redis. Valori validi: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL] |
| EnableNonSslPort | -e, --enable-non-ssl-port | Proprietà EnableNonSslPort della Cache Redis. Aggiungere questo flag se si desidera abilitare la porta SSL Non per la cache |
| subscription | -s, --subscription | L'identificatore della sottoscrizione. |
| tipo di chiave | -t, --key-type | Tipo di chiave per il rinnovo. Valori validi: [primario, secondario] |

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
	help:      rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
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

Per ulteriori informazioni su questo comando, eseguire il comando `azure rediscache create -h`.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -l, --location <location>                    Location to create cache.
	help:      -z, --size <size>                            Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6]
	help:      -x, --sku <sku>                              Redis SKU. Should be one of : [Basic, Standard]
	help:      -m, --max-memory-policy <max-memory-policy>  MaxMemoryPolicy property of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -e, --enable-non-ssl-port                    EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -s, --subscription <id>                      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Eliminare una Cache Redis esistente

Per eliminare una Cache Redis, utilizzare il comando seguente:

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

Per ulteriori informazioni su questo comando, eseguire il comando `azure rediscache delete -h`.

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

## Elenco di tutte le Cache Redis di all'interno della sottoscrizione o di un gruppo di risorse

Per elencare tutte le cache di all'interno della sottoscrizione o un gruppo di risorse, utilizzare il comando seguente:

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

Per ulteriori informazioni su questo comando, eseguire il comando `azure rediscache show -h`.

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

## Modificare le impostazioni di una Cache Redis esistente

Per modificare le impostazioni di una Cache Redis esistente, utilizzare il comando seguente:

	azure rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]

Per ulteriori informazioni su questo comando, eseguire il comando `azure rediscache set -h`.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -m, --max-memory-policy <max-memory-policy>  Max Memory Policy of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -s, --subscription <subscription>            the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Rinnovare la chiave di autenticazione per una Cache Redis esistente

Per rinnovare la chiave di autenticazione per una Cache Redis esistente, utilizzare il comando seguente:

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Specificare `Primary` o `Secondary` per `key-type`.

Per ulteriori informazioni su questo comando, eseguire il comando `azure rediscache renew-key -h`.

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
	help:      -t, --key-type <key-type>              type of key to renew
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Chiavi di elenco primario e secondario di una Cache Redis esistente

Per le chiavi di elenco primario o secondario di una Cache Redis esistente, utilizzare il comando seguente:

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Per ulteriori informazioni su questo comando, eseguire il comando `azure rediscache list-keys -h`.

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

<!---HONumber=Oct15_HO1-->