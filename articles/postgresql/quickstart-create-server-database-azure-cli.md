---
title: Creare un database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Guida di avvio rapido alla creazione e alla gestione di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure.
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 01/02/2018
ms.custom: mvc
ms.openlocfilehash: ab07172d62806631f73c1df35c7d646e83ad5221
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Creare un database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Il database di Azure per PostgreSQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida di avvio rapido mostra come creare un database di Azure per il server PostgreSQL in un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) tramite l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

Se si esegue l'interfaccia della riga di comando in locale, è necessario accedere al proprio account con il comando [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Si noti la proprietà **id** dell'output del comando per il nome della sottoscrizione corrispondente.
```azurecli-interactive
az login
```

Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account#az_account_set). Sostituire la proprietà **id** dell'output **az login** per la sottoscrizione nel segnaposto dell'ID sottoscrizione.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. È necessario specificare un nome univoco. Nell'esempio seguente viene creato un gruppo di risorse denominato `myresourcegroup` nella posizione `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un [database di Azure per il server PostgreSQL](overview.md) tramite il comando [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). Un server contiene un gruppo di database gestiti come gruppo. 

L'esempio seguente crea un server chiamato `mypgserver-20170401` nel gruppo di risorse `myresourcegroup` con account di accesso dell'amministratore del server `mylogin`. Poiché viene eseguito il mapping del nome di un server a un nome DNS, il nome deve essere univoco a livello globale in Azure. Sostituire `<server_admin_password>` con il proprio valore.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida di avvio rapido. Prendere nota di queste informazioni per usarle in seguito.

Per impostazione predefinita, il database **postgres** viene creato al di sotto del server. Il database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) è un database predefinito che può essere usato da utenti, utilità e applicazioni di terze parti. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Creare una regola del firewall a livello di server per PostgreSQL Azure tramite il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) o [PgAdmin](https://www.pgadmin.org/), di connettersi al server tramite il firewall del servizio PostgreSQL Azure. 

È possibile impostare una regola del firewall relativa a un intervallo IP per consentire la connessione dalla rete. L'esempio seguente usa [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) per creare una regola del firewall `AllowAllIps` per un intervallo di indirizzi IP. Per aprire tutti gli indirizzi IP, usare 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo finale.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Il server PostgreSQL Azure comunica sulla porta 5432. Quando si esegue la connessione da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. Richiedere al reparto IT di aprire la porta 5432 per connettersi al server di database SQL di Azure.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

Il risultato è in formato JSON. Annotare i valori di **administratorLogin** e **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Connettersi al database PostgreSQL tramite psql

Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) per connettersi a un server PostgreSQL Azure. È ora possibile usare l'utilità della riga di comando psql per connettersi al server PostgreSQL Azure.

1. Eseguire il comando psql seguente per connettersi a un database di Azure per il server PostgreSQL
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Ad esempio, il comando seguente permette di connettersi al database predefinito chiamato **postgres** nel server PostgreSQL **mypgserver-20170401.postgres.database.azure.com** usando le credenziali di accesso. Immettere il valore di `<server_admin_password>` scelto quando viene chiesta la password.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Dopo aver eseguito la connessione al server, creare un database vuoto nel prompt.
```sql
CREATE DATABASE mypgsqldb;
```

3.  Nel prompt eseguire il comando seguente per cambiare la connessione e connettersi al nuovo database **mypgsqldb** appena creato:
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Connettersi al database PostgreSQL tramite pgAdmin

Per connettersi al server PostgreSQL Azure tramite lo strumento dell'interfaccia utente grafica _pgAdmin_
1.  Avviare l'applicazione _pgAdmin_ nel computer client. È possibile installare _pgAdmin_ da http://www.pgadmin.org/.
2.  Scegliere **Add New Server** (Aggiungi nuovo server) dal menu **Quick Links** (Collegamenti rapidi).
3.  Nella finestra di dialogo **Create - Server** (Creazione server), nella scheda **General** (Generale), immettere un nome descrittivo univoco per il server. Ad esempio, **Azure PostgreSQL Server**.
 ![Strumento pgAdmin - Finestra di creazione del server](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  Nella finestra di dialogo **Create - Server** (Creazione server) fare clic sulla scheda **Connection** (Connessione):
    - Immettere il nome del server completo, ad esempio **mypgserver-20170401.postgres.database.azure.com**, nella casella **Host Name/ Address** (Nome host/Indirizzo). 
    - Immettere la porta 5432 nella casella **Port** (Porta). 
    - Immettere l'**account di accesso dell'amministratore del server (user@mypgserver)** ottenuto in precedenza in questa guida di avvio rapido e la password immessa alla creazione del server rispettivamente nelle caselle **Username** (Nome utente) e **Password**.
    - Selezionare **SSL Mode** (Modalità SSL) e impostare **Require** (Richiedi). Per impostazione predefinita, tutti i server PostgreSQL Azure vengono creati con l'opzione di applicazione del protocollo SSL attivata. Per disattivare questa opzione, vedere le informazioni dettagliate in [Enforcing SSL](./concepts-ssl-connection-security.md) (Applicazione di SSL).

    ![pgAdmin - Finestra di creazione del server](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  Fare clic su **Save**.
6.  Nel riquadro sinistro Browser espandere i gruppi **Server**. Scegliere il server **Azure PostgreSQL Server**.
7.  In **Server** scegliere il server a cui si è connessi e quindi scegliere **Databases** (Database) sotto il server. 
8.  Fare clic con il pulsante destro del mouse su **Databases** (Database) per creare un database.
9.  Scegliere il nome di database **mypgsqldb** e il proprietario, rappresentato dall'account di accesso dell'amministratore del server **mylogin**.
10. Fare clic su **Save** (Salva) per creare un database vuoto.
11. In **Browser** espandere il gruppo **Servers** (Server). Espandere il server creato e visualizzare il database **mypgsqldb** al di sotto.
 ![pgAdmin - Finestra di creazione del database](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Pulire tutte le risorse create nella guida di avvio rapido eliminando il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Altre guide di avvio rapido di questa raccolta si basano sulla presente guida di avvio rapido. Se si prevede di continuare a usare le guide di avvio rapido successive, non pulire le risorse create in questa guida di avvio rapido. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa guida di avvio rapido nell'interfaccia della riga di comando di Azure.

```azurecli-interactive
az group delete --name myresourcegroup
```

Se si vuole eliminare solo il server appena creato, è possibile eseguire il comando [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)
