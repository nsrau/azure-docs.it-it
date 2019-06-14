---
title: File di inclusione
description: File di inclusione
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e3337d0a0159ef5e57ea3bbaba1c6cc2ac8489ff
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808134"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Recuperare il nome dell'host, le porte e le chiavi di accesso usando l'interfaccia della riga di comando di Azure

Per recuperare il nome host e le porte tramite la CLI di Azure è possibile chiamare [show di redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_show)e recuperare le chiavi, è possibile chiamare [az redis elenco chiavi](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). Lo script seguente chiama questi due comandi e restituisce il nome host, porte e chiavi alla console.

```azurecli
# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Per altre informazioni su questo script, vedere [ottenere il nome host, porte e le chiavi per Cache di Azure per Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Per altre informazioni sul comando di Azure, vedere [installare Azure CLI](/cli/azure/install-azure-cli) e [Introduzione a Azure CLI](/cli/azure/get-started-with-azure-cli).
