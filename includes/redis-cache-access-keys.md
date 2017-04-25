Per connettersi a un'istanza Cache Redis di Azure, è necessario specificare il nome host, le porte e le chiavi della cache. Alcuni client possono fare riferimento a questi elementi usando nomi leggermente diversi. È possibile recuperare queste informazioni nel portale di Azure o usando strumenti da riga di comando come l'interfaccia della riga di comando di Azure.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Recuperare il nome dell'host, le porte e le chiavi di accesso usando il portale di Azure
Per recuperare il nome dell'host, le porte e le chiavi di accesso usando il portale di Azure, [passare](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) alla cache nel [portale di Azure](https://portal.azure.com) e scegliere **Chiavi di accesso** e **Proprietà** dal menu **Risorsa**. 

![Impostazioni della Cache Redis](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Recuperare il nome dell'host, le porte e le chiavi di accesso usando l'interfaccia della riga di comando di Azure
Per recuperare il nome dell'host e le porte usando l'interfaccia della riga di comando di Azure 2.0, è possibile chiamare [az redis show](https://docs.microsoft.com/cli/azure/redis#show) e per recuperare le chiavi è possibile chiamare [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys). Lo script seguente chiama questi due comandi e restituisce il nome dell'host, le porte e le chiavi alla console.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Per altre informazioni su questo script, vedere [Get the hostname, ports, and keys for Azure Redis Cache](../articles/redis-cache/scripts/cache-keys-ports.md) (Recuperare il nome dell'host, le porte e le chiavi per Cache Redis di Azure). Per altre informazioni sull'interfaccia della riga di comando di Azure 2.0, vedere [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Installare l'interfaccia della riga di comando di Azure 2.0) e [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Introduzione all'interfaccia della riga di comando di Azure 2.0).
