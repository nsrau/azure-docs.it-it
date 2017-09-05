---
title: Gestire Cache Redis di Azure con Azure PowerShell | Microsoft Docs
description: "Informazioni su come eseguire attività amministrative per Cache Redis di Azure con Azure PowerShell."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 0a5c95eab3fd01f611fc049e80c5c506857e0b81
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Gestire Cache Redis di Azure con Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Interfaccia della riga di comando di Azure](cache-manage-cli.md)
> 
> 

Questo argomento illustra come eseguire attività comuni, come creare, aggiornare e ridimensionare le istanze di Cache Redis di Azure, come rigenerare le chiavi di accesso e come visualizzare le informazioni relative alle cache. Per un elenco completo dei cmdlet PowerShell di Cache Redis di Azure, vedere [Cmdlet di Cache Redis di Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Per altre informazioni sul modello di distribuzione classica vedere [Confronto tra distribuzione Azure Resource Manager e classica: comprensione dei modelli di distribuzione e dello stato delle risorse](../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Prerequisiti
Se è già installato PowerShell di Microsoft Azure, è necessario installare Azure PowerShell versione 1.0.0 o versione successiva. Per verificare quale versione di Azure PowerShell è installata, è possibile utilizzare il comando del relativo prompt di Microsoft PowerShell.

    Get-Module azure | format-table version


Prima di tutto è necessario accedere a Microsoft Azure con questo comando.

    Login-AzureRmAccount

Specificare l'indirizzo e-mail del proprio account Microsoft Azure e la relativa password nella finestra di accesso a Microsoft Azure.

Successivamente, se si dispone di più sottoscrizioni di Microsoft Azure, è necessario impostare la sottoscrizione di Microsoft Azure prescelta. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente, il nome della sottoscrizione è `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Per usare Windows PowerShell con Gestione risorse di Azure, è necessario disporre di quanto indicato di seguito:

* Windows PowerShell, versione 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare `$PSVersionTable` e verificare che il valore di `PSVersion` sia 3.0 o 4.0. Per installare una versione compatibile, vedere [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet `New-AzureRmRedisCache` digitare:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Come connettersi ad altri cloud
Per impostazione predefinita l'ambiente di Azure è `AzureCloud`che rappresenta l'istanza globale del cloud di Azure. Per connettersi a un'altra istanza, usare il comando `Add-AzureRmAccount` con l'opzione della riga di comando `-Environment` o -`EnvironmentName` con l'ambiente desiderato o il nome dell'ambiente.

Per visualizzare l'elenco degli ambienti disponibili, eseguire il cmdlet `Get-AzureRmEnvironment` .

### <a name="to-connect-to-the-azure-government-cloud"></a>Per connettersi ad Azure Government Cloud
Per connettersi ad Azure Government Cloud, usare uno dei comandi seguenti.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

oppure

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Per creare una cache in Azure Government Cloud, usare una delle località seguenti.

* Governo degli Stati Uniti - Virginia
* Governo degli Stati Uniti - Iowa

Per altre informazioni su Azure Government Cloud, vedere [Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/) e [Guida per gli sviluppatori di Microsoft Azure per enti pubblici](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Per connettersi ad Azure China Cloud
Per connettersi ad Azure China Cloud, usare uno dei comandi seguenti.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

oppure

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Per creare una cache in Azure China Cloud, usare una delle località seguenti.

* Cina orientale
* Cina settentrionale

Per altre informazioni su Azure China Cloud, vedere la pagina relativa ad [Azure China Cloud per Azure gestito da 21Vianet in Cina](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Per connettersi a Microsoft Azure Germania
Per connettersi a Microsoft Azure Germania, usare uno dei comandi seguenti.

    Add-AzureRMAccount -EnvironmentName AzureGermanCloud


oppure

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

Per creare una cache in Microsoft Azure Germania, usare una delle località seguenti.

* Germania centrale
* Germania nord-orientale

Per altre informazioni su Microsoft Azure Germania, vedere [Microsoft Azure Germania](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Proprietà usate per PowerShell nella Cache Redis di Azure 
La tabella seguente contiene le proprietà e le descrizioni dei parametri usati durante la creazione e la gestione di istanze di Cache Redis di Azure con Azure PowerShell.

| Parametro | Descrizione | Default |
| --- | --- | --- |
| Name |Nome della cache | |
| Location |Percorso della cache | |
| ResourceGroupName |Nome del gruppo di risorse in cui creare la cache | |
| Dimensione |Dimensioni della cache. I valori validi sono: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 MB, 1 GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1 GB |
| ShardCount |Numero di partizioni da creare quando si crea una cache Premium con clustering abilitato. I valori validi sono: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Specifica gli SKU della cache. I valori validi sono: Basic, Standard e Premium |Standard |
| RedisConfiguration |Specifica le impostazioni di configurazione di Redis. Per i dettagli di ogni impostazione, vedere la tabella [Proprietà di RedisConfiguration](#redisconfiguration-properties) seguente. | |
| EnableNonSslPort |Indica se la porta non SSL è abilitata. |False |
| MaxMemoryPolicy |Questo parametro è stato deprecato, usare invece RedisConfiguration. | |
| StaticIP |Quando si ospita la cache in una rete virtuale, specifica l'indirizzo IP univoco nella subnet per la cache. Se non specificato, ne verrà scelto uno dalla subnet. | |
| Subnet |Quando si ospita la cache in una rete virtuale, specifica il nome della subnet in cui distribuire la cache. | |
| VirtualNetwork |Quando si ospita la cache in una rete virtuale, specifica l'ID risorsa della rete virtuale in cui distribuire la cache. | |
| KeyType |Specifica la chiave di accesso da rigenerare quando si rinnovano le chiavi di accesso. Valori validi: Primario, Secondario | |

### <a name="redisconfiguration-properties"></a>Proprietà di RedisConfiguration
| Proprietà | Descrizione | Piani tariffari |
| --- | --- | --- |
| rdb-backup-enabled |Indica se la [persistenza dei dati Redis](cache-how-to-premium-persistence.md) è abilitata |Solo Premium |
| rdb-storage-connection-string |Stringa di connessione dell'account di archiviazione per la [persistenza dei dati Redis](cache-how-to-premium-persistence.md) |Solo Premium |
| rdb-backup-frequency |Frequenza di backup per la [persistenza dei dati Redis](cache-how-to-premium-persistence.md) |Solo Premium |
| maxmemory-reserved |Configura la [memoria riservata](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) per i processi non della cache |Standard e Premium |
| maxmemory-policy |Configura i [criteri di rimozione](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) per la cache |Tutti i piani tariffari |
| notify-keyspace-events |Configura le [notifiche keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Standard e Premium |
| hash-max-ziplist-entries |Configura l' [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per tipi di dati aggregati di piccole dimensioni |Standard e Premium |
| hash-max-ziplist-value |Configura l' [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per tipi di dati aggregati di piccole dimensioni |Standard e Premium |
| set-max-intset-entries |Configura l' [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per tipi di dati aggregati di piccole dimensioni |Standard e Premium |
| zset-max-ziplist-entries |Configura l' [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per tipi di dati aggregati di piccole dimensioni |Standard e Premium |
| zset-max-ziplist-value |Configura l' [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per tipi di dati aggregati di piccole dimensioni |Standard e Premium |
| database |Configura il numero di database. Questa proprietà può essere configurata solo durante la creazione della cache. |Standard e Premium |

## <a name="to-create-a-redis-cache"></a>Creare una Cache Redis
Le nuove istanze della Cache Redis di Azure vengono create con il cmdlet [New AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

> [!IMPORTANT]
> La prima volta chr si crea una cache Redis in una sottoscrizione tramite il portale di Azure, il portale registra lo spazio dei nomi `Microsoft.Cache` per la sottoscrizione. Se si prova a creare la prima cache Redis in una sottoscrizione usando PowerShell, è necessario prima registrare tale spazio dei nomi con il comando seguente. In caso contrario, i cmdlet come `New-AzureRmRedisCache` e `Get-AzureRmRedisCache` non riusciranno.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `New-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Per creare una cache con i parametri predefiniti, eseguire il comando seguente.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, e `Location` sono parametri obbligatori, ma il resto sono facoltativi e hanno valori predefiniti. L'esecuzione del comando precedente crea un'istanza di Cache Redis di Azure con SKU Standard con un nome, un percorso e un gruppo di risorse specificati e dimensioni di 1 GB con la porta non SSL disabilitata.

Per creare una cache Premium, specificare dimensioni P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) o P4 (53 GB - 530 GB). Per abilitare il clustering, specificare un numero di partizioni con il parametro `ShardCount` . L'esempio seguente crea una cache Premium P1 con 3 partizioni. Le dimensioni di una cache Premium P1 sono di 6 GB e dato che sono state specificate tre partizioni le dimensioni totale sono di 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Specificare i valori per il parametro `RedisConfiguration`, racchiudere i valori tra `{}` come coppie chiave/valore, ad esempio `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. L'esempio seguente crea una cache Standard di 1 GB con criteri di maxmemory e notifiche keyspace `allkeys-random` configurati con `KEA`. Per altre informazioni, vedere [Notifiche di Keyspace (impostazioni avanzate)](cache-configure.md#keyspace-notifications-advanced-settings) e [Criteri per la memoria](cache-configure.md#memory-policies).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Per configurare l'impostazione databases durante la creazione della cache
L'impostazione `databases` può essere configurata solo durante la creazione della cache. L'esempio seguente crea una cache P3 (26 GB) Premium con 48 database usando il cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Per altre informazioni sulla proprietà `databases` , vedere [Configurazione predefinita del server Cache Redis di Azure](cache-configure.md#default-redis-server-configuration). Per altre informazioni sulla creazione di una cache con il cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx), vedere la sezione precedente [Per creare una cache Redis](#to-create-a-redis-cache).

## <a name="to-update-a-redis-cache"></a>Per aggiornare una Cache Redis
Le nuove istanze della Cache Redis di Azure vengono aggiornate con il cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `Set-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Il cmdlet `Set-AzureRmRedisCache` può essere usato per aggiornare le proprietà, ad esempio i valori `Size`, `Sku`, `EnableNonSslPort` e `RedisConfiguration`. 

Il comando seguente aggiorna maxmemory-policy per la Cache Redis denominata myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>Ridimensionare una Cache Redis
`Set-AzureRmRedisCache` può essere usato per ridimensionare un'istanza della Cache Redis di Azure quando si modificano le proprietà `Size`, `Sku` o `ShardCount`. 

> [!NOTE]
> Il ridimensionamento dell'istanza di una cache con PowerShell è soggetto agli stessi limiti e alle stesse linee guida per il ridimensionamento di una cache dal portale di Azure. Il passaggio a un piano tariffario diverso è soggetto alle restrizioni seguenti.
> 
> * Non è possibile passare da un piano tariffario superiore a uno inferiore.
> * Non è possibile passare da una cache **Premium** a una cache **Standard** o **Basic**.
> * Non è possibile passare da una cache **Standard** a una cache **Basic**.
> * È possibile passare da una cache **Basic** a una cache **Standard**, ma non è possibile modificare contemporaneamente la dimensione. Se occorre una dimensione diversa, è possibile eseguire successivamente un'operazione di ridimensionamento in base ai propri requisiti.
> * Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È necessario passare da **Basic** a **Standard** con una prima operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
> * Non è possibile passare da una dimensione maggiore alla dimensione **C0 (250 MB)** .
> 
> Per altre informazioni, vedere [Come ridimensionare la Cache Redis di Azure](cache-how-to-scale.md).
> 
> 

L'esempio seguente illustra come ridimensionare una cache denominata `myCache` in una cache di 2,5 GB. Si noti che questo comando funziona sia con una cache Basic che una cache Standard.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Dopo l'esecuzione di questo comando, viene restituito lo stato della cache (simile a una chiamata a `Get-AzureRmRedisCache`). Si noti che il `ProvisioningState` è `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Quando l'operazione di ridimensionamento è completa, `ProvisioningState` viene modificato in `Succeeded`. Se si vuole effettuare un'operazione di ridimensionamento successiva, ad esempio la modifica da Basic a Standard e quindi modificare le dimensioni, è necessario attendere che l'operazione precedente sia stata completata. In caso contrario, verrà visualizzato un errore simile al seguente.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Per ottenere informazioni su una Cache Redis
È possibile recuperare informazioni su una cache con il cmdlet [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `Get-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Per restituire informazioni su tutte le cache nella sottoscrizione corrente, eseguire `Get-AzureRmRedisCache` senza parametri.

    Get-AzureRmRedisCache

Per restituire informazioni su tutte le cache in un gruppo di risorse specifico, eseguire `Get-AzureRmRedisCache` con il parametro `ResourceGroupName`.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Per restituire informazioni su una cache specifica, eseguire `Get-AzureRmRedisCache` con il parametro `Name` che contiene il nome della cache e il parametro `ResourceGroupName` con il gruppo di risorse che contiene tale cache.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Per recuperare le chiavi di accesso per una Cache Redis
Per recuperare le chiavi di accesso per la cache, è possibile usare il cmdlet [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `Get-AzureRmRedisCacheKey`, eseguire il comando seguente.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Per recuperare le chiavi per la cache, chiamare il cmdlet `Get-AzureRmRedisCacheKey` e passare il nome della cache e il nome del gruppo di risorse che contiene la cache.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Per rigenerare le chiavi di accesso per la Cache Redis
Per rigenerare le chiavi di accesso per la cache, è possibile usare il cmdlet [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `New-AzureRmRedisCacheKey`, eseguire il comando seguente.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Per rigenerare la chiave primaria o secondaria per la cache, chiamare il cmdlet `New-AzureRmRedisCacheKey` e passare il nome e il gruppo di risorse, quindi specificare il parametro `Primary` o `Secondary` per il parametro `KeyType`. Nell'esempio seguente viene rigenerata la chiave di accesso secondaria per una cache.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Per eliminare una Cache Redis
Per eliminare una cache Redis, usare il cmdlet [Remove AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `Remove-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Nell'esempio seguente viene rimossa la cache denominata `myCache` .

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Per importare una Cache Redis
È possibile importare dati in un'istanza della Cache Redis di Azure usando il cmdlet `Import-AzureRmRedisCache` .

> [!IMPORTANT]
> La funzionalità Importazione/Esportazione è disponibile solo per le cache del [piano Premium](cache-premium-tier-intro.md) . Per altre informazioni sull'importazione/esportazione, vedere [Importare ed esportare dati in Cache Redis di Azure](cache-how-to-import-export-data.md).
> 
> 

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `Import-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Il comando seguente importa dati dal BLOB specificato dall'URI della firma di accesso condiviso nella Cache Redis di Azure.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Per esportare una Cache Redis
È possibile esportare dati da un'istanza della Cache Redis di Azure usando il cmdlet `Export-AzureRmRedisCache` .

> [!IMPORTANT]
> La funzionalità Importazione/Esportazione è disponibile solo per le cache del [piano Premium](cache-premium-tier-intro.md) . Per altre informazioni sull'importazione/esportazione, vedere [Importare ed esportare dati in Cache Redis di Azure](cache-how-to-import-export-data.md).
> 
> 

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `Export-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Il comando seguente esporta dati da un'istanza della Cache Redis di Azure al contenitore specificato dall'URI della firma di accesso condiviso.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Per riavviare una Cache Redis
È possibile riavviare un'istanza della Cache Redis di Azure usando il cmdlet `Reset-AzureRmRedisCache` .

> [!IMPORTANT]
> La funzionalità di riavvio è disponibile solo per le cache del [piano Premium](cache-premium-tier-intro.md) . Per altre informazioni sul riavvio della cache, vedere [Come amministrare Cache Redis di Azure - Riavviare](cache-administration.md#reboot).
> 
> 

Per visualizzare un elenco di parametri disponibili e le relative descrizioni per `Reset-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Il comando seguente riavvia entrambi i nodi della cache specificata.

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'utilizzo di Windows PowerShell con Azure, vedere le risorse seguenti:

* [Documentazione del cmdlet della cache Redis di Azure su MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Cmdlet per Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765): informazioni sull'uso dei cmdlet nel modulo Azure Resource Manager.
* [Uso dei gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): informazioni su come creare e gestire gruppi di risorse nel portale di Azure.
* [Blog di Azure](http://blogs.msdn.com/windowsazure): informazioni sulle nuove funzionalità di Azure.
* [Blog di Windows PowerShell](http://blogs.msdn.com/powershell): informazioni sulle nuove funzionalità di Windows PowerShell.
* [Blog "Hey, Scripting Guy!"](http://blogs.technet.com/b/heyscriptingguy/): suggerimenti e consigli basati sull'esperienza dei membri della community di Windows PowerShell.


