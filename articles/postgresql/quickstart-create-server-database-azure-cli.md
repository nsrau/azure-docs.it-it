---
title: 'Guida introduttiva: Creare un server - Interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL - Server singolo'
description: Guida di avvio rapido che mostra come creare un database di Azure per PostgreSQL - Server singolo tramite l'interfaccia della riga di comando di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030182"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Guida introduttiva: Creare un Database di Azure per PostgreSQL - Server singolo usando l'interfaccia della riga di comando di Azure

> [!TIP]
> Può essere opportuno usare il comando dell'interfaccia della riga di comando di Azure più semplice [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) (attualmente in anteprima). Provare l'[argomento di avvio rapido](./quickstart-create-server-up-azure-cli.md).

Questa guida di avvio rapido mostra come usare i comandi dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) per creare un server di Database di Azure per PostgreSQL in cinque minuti.  Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

## <a name="prerequisites"></a>Prerequisiti
Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure. 

```azurecli-interactive
az login
```

Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) usando il comando [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) e quindi creare il server PostgreSQL in questo gruppo di risorse. È necessario specificare un nome univoco. L'esempio seguente consente di creare un gruppo di risorse denominato `myresourcegroup` nell'area `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Creare un [database di Azure per il server PostgreSQL](overview.md) tramite il comando [az postgres server create](/cli/azure/postgres/server). Un server può contenere più database.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Ecco i dettagli per gli argomenti indicati: 

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Scegliere un nome univoco per identificare il database di Azure per il server PostgreSQL. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
posizione | westus | Località di Azure per il server.
admin-user | myadmin | Nome utente per l'account di accesso dell'amministratore. Non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**'.
admin-password | *password di protezione* | Password dell'utente amministratore. Deve contenere tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici.
sku-name|GP_Gen5_2|Immettere il nome del piano tariffario e della configurazione delle risorse di calcolo. Segue la convenzione {piano tariffario} _{generazione di calcolo}_ {Vcore} in sintassi abbreviata. Per altre informazioni, vedere [Database di Azure per PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- La versione predefinita di PostgreSQL nel server è 9.6. Un elenco di tutte le versioni supportate è disponibile [qui](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).
>- Per visualizzare tutti gli argomenti per il comando **az postgres server create**, vedere questo [documento di riferimento](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)
>- SSL viene abilitato per impostazione predefinita nel server. Per altre informazioni su SSL, vedere [Configurare la connettività SSL](./concepts-ssl-connection-security.md)

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server 
Per impostazione predefinita, il server creato non è pubblicamente accessibile ed è protetto dalle regole del firewall. È possibile configurare la regola del firewall usando il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) per concedere all'ambiente locale l'accesso per la connessione al server. 

L'esempio seguente crea una regola firewall denominata `AllowMyIP` che consente connessioni da un indirizzo IP specifico, 192.168.0.1. Sostituire con l'indirizzo IP o l'intervallo di indirizzi IP corrispondenti alla posizione da cui si effettuerà la connessione.  Se non si sa come cercare l'indirizzo IP, passare a [https://whatismyipaddress.com/](https://whatismyipaddress.com/) per ottenere l'indirizzo IP.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Per evitare problemi di connessione, assicurarsi che il firewall di rete consenta la porta 5432, che viene usata dai server di Database di Azure per PostgreSQL. 

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare l'**administratorLogin** e il **fullyQualifiedDomainName**.
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

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Connettersi al Database di Azure per PostgreSQL tramite psql
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) è un client molto diffuso per la connessione ai server PostgreSQL. È possibile connettersi al server usando **psql** con [Azure Cloud Shell](../cloud-shell/overview.md). In alternativa, è possibile usare psql nell'ambiente locale se è disponibile. Un database vuoto, "postgres", è già stato creato con il nuovo server PostgreSQL che può essere usato per la connessione a psql come mostrato di seguito 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se si preferisce usare un percorso URL per connettersi a Postgres, l'URL codifica il segno @ nel nome utente con `%40`. Ad esempio la stringa di connessione per psql sarà
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Pulire le risorse
Se queste risorse non sono necessarie per un'altra guida introduttiva/esercitazione, è possibile eliminarle eseguendo questo comando: 

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
> 
> [Distribuire un'app Web Django con PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Connettersi con l'app Node.JS](./connect-nodejs.md)

