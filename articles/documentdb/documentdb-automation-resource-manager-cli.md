---
title: Automazione di DocumentDB - Interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: "Usare l&quot;interfaccia della riga di comando di Azure 2.0 per gestire account di database di DocumentDB. DocumentDB è un database NoSQL basato su cloud per dati JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b286a93d7cc5f962f969e877b2f487e56cbb1a95
ms.lasthandoff: 03/30/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>Automatizzare la gestione degli account Azure DocumentDB usando l'interfaccia della riga di comando di Azure 2.0
> [!div class="op_single_selector"]
> * [Portale di Azure](documentdb-create-account.md)
> * [Interfaccia della riga di comando di Azure 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

La guida seguente illustra i comandi per automatizzare la gestione degli account del database DocumentDB usando i comandi di anteprima di DocumentDB disponibili nell'interfaccia della riga di comando di Azure 2.0 . Include anche i comandi per gestire le chiavi dell'account e le priorità di failover in [account di database tra più aree][scaling-globally]. L'aggiornamento dell'account del database consente di modificare i criteri di coerenza e di aggiungere/rimuovere le aree. Per la gestione multipiattaforma dell'account del database DocumentDB, è possibile usare [Azure Powershell](documentdb-manage-account-with-powershell.md), l'[API REST del provider di risorse][rp-rest-api] o il [portale di Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Introduzione

Per configurare l'ambiente di sviluppo con l'interfaccia della riga di comando di Azure 2.0, seguire le istruzioni riportate in [Come installare e configurare l'interfaccia della riga di comando di Azure 2.0][install-az-cli2].

Accedere al proprio account Azure eseguendo il comando riportato di seguito e seguendo le istruzioni visualizzate.

    az login

Se non è già disponibile un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups), crearne uno:

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

`<resourcegrouplocation>` deve essere una delle aree in cui DocumentDB è generalmente disponibile. L'elenco corrente delle aree geografiche è disponibile nella [pagina Aree di Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Note

* Eseguire "az documentdb -h" per ottenere un elenco completo dei comandi disponibili o visitare la [pagina di riferimento][az-documentdb-ref].
* Eseguire "az documentdb <command> -h" per ottenere un elenco di informazioni dettagliate sui parametri richiesti e opzionali per comando.

## <a id="create-documentdb-account-cli"></a> Creare un account di database DocumentDB

Questo comando consente di creare un account di database DocumentDB. Configurare il nuovo account del database come ad area singola o [a più aree][scaling-globally] con un determinato [criterio di coerenza](documentdb-consistency-levels.md). 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
                                    To enable portal access, include 104.42.195.92.
    --kind                        : The type of DocumentDB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

Esempi: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Note
* Le località devono essere aree in cui DocumentDB è disponibile a livello generale. L'elenco corrente delle aree geografiche è disponibile nella [pagina Aree di Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-cli"> </a> Aggiornare un account di database DocumentDB

Questo comando consente di aggiornare le proprietà di un account di database DocumentDB. e include il criterio di coerenza e le località in cui esiste l'account di database.

> [!NOTE]
> Il comando consente di aggiungere e rimuovere aree, ma non di modificare le priorità di failover. Per modificare le priorità di failover, vedere [sotto](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

Esempi: 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a> Aggiungere/rimuovere un'area da un account di database DocumentDB

Per aggiungere o rimuovere aree dall'account di database DocumentDB esistente, usare il comando [update](#update-documentdb-account-cli) con il flag `--locations`. L'esempio seguente illustra come creare un nuovo account e successivamente aggiungere e rimuovere aree da quell'account.

Esempio:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Eliminare un account di database DocumentDB

Questo comando consente di eliminare un account di database DocumentDB esistente.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Esempio:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Ottenere le proprietà di un account di database DocumentDB

Questo comando consente di ottenere le proprietà di un account di database DocumentDB esistente.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Esempio:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Elencare le chiavi dell'account

Quando si crea un account DocumentDB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account DocumentDB. Fornendo due chiavi di accesso, DocumentDB consente di rigenerare le chiavi senza interruzioni dell'account DocumentDB. Sono disponibili anche chiavi di sola lettura per l'autenticazione delle operazioni di sola lettura. Esistono due chiavi di lettura/scrittura (primaria e secondaria) e due chiavi di sola lettura (primaria e secondaria).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Esempio:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Rigenerare una chiave dell'account

È consigliabile modificare periodicamente le chiavi di accesso all'account DocumentDB per mantenere più sicure le connessioni. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account DocumentDB con una delle due chiavi mentre si rigenera l'altra.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Esempio:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Modificare la priorità di failover di un account di database DocumentDB

Per gli account di database tra più aree, è possibile modificare la priorità di failover delle diverse aree in cui esiste l'account del database DocumentDB. Per altre informazioni sul failover nell'account del database DocumentDB, vedere [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Esempio:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Passaggi successivi
Ora che avete un account di DocumentDB, il passaggio successivo consiste nel creare un database di DocumentDB. È possibile creare un database utilizzando uno dei seguenti:

* Il portale di Azure come descritto in [Creare un database e una raccolta DocumentDB usando il portale di Azure](documentdb-create-collection.md).
* Campioni di C# .NET nel progetto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repository [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) su GitHub.
* Gli [SDK di DocumentDB](documentdb-sdk-dotnet.md). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript.

Dopo aver creato il database, è necessario [aggiungere una o più raccolte](documentdb-create-collection.md) al database e quindi [aggiungere documenti](documentdb-view-json-document-explorer.md) alle raccolte.


Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#ExecutingSqlQueries) nei documenti con [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Per altre informazioni su DocumentDB, vedere le risorse seguenti:

* [Percorso di apprendimento per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

