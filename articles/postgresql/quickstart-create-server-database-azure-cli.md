---
title: 'Avvio rapido: Creare un server - Interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL - server singolo'
description: Questo argomento di avvio rapido illustra come creare un server di Database di Azure per PostgreSQL con l'interfaccia della riga di comando di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d174e410aaef876dfe97af62750322641de95fd3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659455"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Avvio rapido: Creare un server di Database di Azure per PostgreSQL con l'interfaccia della riga di comando di Azure

Questo argomento di avvio rapido illustra come usare i comandi dell'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) per creare un server singolo di Database di Azure per PostgreSQL in cinque minuti. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

    > [!TIP]
    >  Può essere opportuno usare il comando dell'interfaccia della riga di comando di Azure più semplice, [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), attualmente disponibile in anteprima. Provare l'[argomento di avvio rapido](./quickstart-create-server-up-azure-cli.md).

- Selezionare l'ID sottoscrizione specifico nell'account usando il comando [az account set](/cli/azure/account).

    - Prendere nota del valore di **id** dell'output di **az login** da usare come valore per l'argomento **subscription** nel comando. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az-account-list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) usando il comando [az group create](/cli/azure/group#az-group-create) e quindi creare il server PostgreSQL in questo gruppo di risorse. È necessario specificare un nome univoco. L'esempio seguente consente di creare un gruppo di risorse denominato `myresourcegroup` nell'area `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Creare un [server di Database di Azure per PostgreSQL](overview.md) tramite il comando [az postgres server create](/cli/azure/postgres/server). Un server può contenere più database.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Ecco i dettagli per gli argomenti precedenti: 

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Nome univoco che identifica il server di Database di Azure per PostgreSQL. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) Deve contenere da 3 a 63 caratteri. Per altre informazioni, vedere [Regole di denominazione di Database di Azure per PostgreSQL](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql).
resource-group | myresourcegroup | Nome del gruppo di risorse di Azure.
posizione | westus | Località di Azure per il server.
admin-user | myadmin | Nome utente per l'account di accesso dell'amministratore. Non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**.
admin-password | *password di protezione* | Password dell'utente amministratore. Deve contenere da 8 a 128 caratteri di tre categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici.
sku-name|GP_Gen5_2| Nome del piano tariffario e della configurazione delle risorse di calcolo. Segue la convenzione {piano tariffario} _{generazione di calcolo}_ {vCore} nella sintassi abbreviata. Per altre informazioni, vedere [Piano tariffario di Database di Azure per PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- La versione predefinita di PostgreSQL nel server è 9.6. Per informazioni su tutte le versioni supportate, vedere [Versioni principali supportate di PostgreSQL](./concepts-supported-versions.md).
>- Per visualizzare tutti gli argomenti per il comando **az postgres server create**, vedere questo [documento di riferimento](/cli/azure/postgres/server#az-postgres-server-create).
>- SSL viene abilitato per impostazione predefinita nel server. Per altre informazioni su SSL, vedere [Configurare la connettività SSL](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server 
Per impostazione predefinita, il server creato non è accessibile pubblicamente ed è protetto con regole del firewall. È possibile configurare le regole del firewall usando il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) per concedere all'ambiente locale l'accesso per la connessione al server. 

L'esempio seguente crea una regola firewall denominata `AllowMyIP` che consente connessioni da un indirizzo IP specifico, 192.168.0.1. Sostituire l'indirizzo IP o l'intervallo di indirizzi IP con quelli corrispondenti alla posizione da cui si effettuerà la connessione. Se non si conosce l'indirizzo IP, passare a [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) per ottenerlo.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Per evitare problemi di connettività, verificare che il firewall della rete consenta la porta 5432. Il server di Database di Azure per PostgreSQL usa tale porta. 

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Prendere nota dei valori di **administratorLogin** e **fullyQualifiedDomainName**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Connettersi al server di Database di Azure per PostgreSQL tramite psql
Il client [psql](https://www.postgresql.org/docs/current/static/app-psql.html) è una scelta comune per la connessione ai server PostgreSQL. È possibile connettersi al server usando psql con [Azure Cloud Shell](../cloud-shell/overview.md). È anche possibile usare psql nell'ambiente locale, se è disponibile. Viene automaticamente creato un database vuoto, **postgres**, con un nuovo server PostgreSQL. È possibile usare tale database per connettersi con psql, come illustrato nel codice seguente. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Se si preferisce usare un percorso URL per connettersi a Postgres, l'URL codifica il segno @ nel nome utente con `%40`. Ad esempio, la stringa di connessione per psql sarà:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Pulizia delle risorse
Se queste risorse non sono necessarie per un'altra guida di avvio rapido o un'esercitazione, è possibile eliminarle eseguendo il comando seguente. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se si vuole eliminare solo il server appena creato, è possibile eseguire il comando [az postgres server delete](/cli/azure/postgres/server).

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)
