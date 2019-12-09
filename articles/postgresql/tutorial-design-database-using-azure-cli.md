---
title: 'Esercitazione: Progettare un database di Azure per PostgreSQL - Server singolo - Interfaccia della riga di comando di Azure'
description: Questa esercitazione illustra come creare e configurare il primo Database di Azure per PostgreSQL - Server singolo ed eseguire query usando l'interfaccia della riga di comando di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 0f1f4c07f3dc694bcae9b540c71a11e53a00eb7f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773681"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Esercitazione: Progettare un Database di Azure per PostgreSQL - Server singolo usando l'interfaccia della riga di comando di Azure 
In questa esercitazione, si usano l'interfaccia della riga di comando di Azure e altre utilità per informazioni su come:
> [!div class="checklist"]
> * Creare un database di Azure per il server PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

È possibile usare Azure Cloud Shell nel browser oppure [installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli) nel proprio computer per eseguire i comandi di questa esercitazione.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

Se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui la risorsa esiste o per cui è configurata. Selezionare un ID di sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Nell'esempio seguente viene creato un gruppo di risorse denominato `myresourcegroup` nella posizione `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL
Creare un [database di Azure per il server PostgreSQL](overview.md) tramite il comando [az postgres server create](/cli/azure/postgres/server). Un server contiene un gruppo di database gestiti come gruppo. 

Nell'esempio seguente viene creato un server denominato `mydemoserver` nel gruppo di risorse `myresourcegroup` con account di accesso dell'amministratore del server `myadmin`. Poiché viene eseguito il mapping del nome di un server a un nome DNS, il nome deve essere univoco a livello globale in Azure. Sostituire `<server_admin_password>` con il proprio valore. Si tratta di un server per utilizzo generico di quinta generazione con due vCore.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
Il valore del parametro sku-name segue la convenzione {piano tariffario}\_{generazione calcolo}\_{vCore} come illustrato nell'esempio seguente:
+ `--sku-name B_Gen5_2` esegue il mapping a Basic, Gen 5 e 2 vCore.
+ `--sku-name GP_Gen5_32` esegue il mapping a utilizzo generico, Gen 5 e 32 vCore.
+ `--sku-name MO_Gen5_2` esegue il mapping a ottimizzazione per la memoria, Gen 5 e 2 vCore.

Vedere la documentazione dei [piani tariffari](./concepts-pricing-tiers.md) per comprendere i valori validi per area e livello.

> [!IMPORTANT]
> L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida di avvio rapido. Prendere nota di queste informazioni per usarle in seguito.

Per impostazione predefinita, il database **postgres** viene creato al di sotto del server. Il database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) è un database predefinito che può essere usato da utenti, utilità e applicazioni di terze parti. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Creare una regola del firewall a livello di server per PostgreSQL Azure tramite il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) o [PgAdmin](https://www.pgadmin.org/), di connettersi al server tramite il firewall del servizio PostgreSQL Azure. 

È possibile impostare una regola del firewall che copra un intervallo di indirizzi IP per consentire la connessione dalla rete in uso. L'esempio seguente usa [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) per creare una regola del firewall `AllowMyIP` che consenta la connessione da un singolo indirizzo IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Per limitare l'accesso al server Azure PostgreSQL a una sola rete, impostare una regola del firewall che copra l'intervallo di indirizzi IP della rete aziendale.

> [!NOTE]
> Il server PostgreSQL Azure comunica sulla porta 5432. Quando si esegue la connessione da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. Richiedere al reparto IT di aprire la porta 5432 per connettersi al server di database SQL di Azure.
>

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

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Connettersi al Database di Azure per il database PostgreSQL tramite psql
Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) o la console Cloud di Azure per connettersi a un server PostgreSQL di Azure. Si usi ora l'utilità della riga di comando psql per connettersi al Database di Azure per il server PostgreSQL.

1. Eseguire il comando psql seguente per connettersi a un Database di Azure per PostgreSQL:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Ad esempio, il comando seguente consente di connettersi al database predefinito denominato **postgres** nel server PostgreSQL **mydemoserver.postgres.database.azure.com** usando le credenziali di accesso. Immettere il valore di `<server_admin_password>` scelto quando viene chiesta la password.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se si preferisce usare un percorso URL per connettersi a Postgres, l'URL codifica il segno @ nel nome utente con `%40`. Ad esempio la stringa di connessione per psql sarà
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Dopo aver eseguito la connessione al server, creare un database vuoto al prompt:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato **mypgsqldb**:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al Database di Azure per PostgreSQL, si possono completare alcune attività di base:

In primo luogo, creare una tabella e caricarvi alcuni dati. Creare ad esempio una tabella che tenga traccia delle informazioni riguardanti l'inventario:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

È possibile visualizzare ora la tabella appena creata nell'elenco di tabelle digitando:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Caricare i dati nella tabella
Ora che è stata creata una tabella, inserirvi alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, sono presenti due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella di inventario: 
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare query e dati nella tabella:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Quando si recuperano i dati, è possibile visualizzare i valori aggiornati:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente
Si supponga di aver eliminato accidentalmente una tabella. Si tratta di un elemento che non è facile da ripristinare. Database di Azure per PostgreSQL consente di tornare a qualsiasi momento specifico per cui sono disponibili backup del server (in base al periodo di conservazione dei backup configurato) ed eseguirne il ripristino in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. 

La procedura seguente consente di ripristinare il server di esempio in un punto precedente all'aggiunta della tabella:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Il comando `az postgres server restore` richiede i parametri seguenti:

| Impostazione | Valore consigliato | DESCRIZIONE  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| name | mydemoserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selezionare un punto di ripristino temporizzato. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. È possibile usare il fuso orario locale, ad esempio `2017-04-13T05:59:00-08:00`, o il formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Il nome o l'ID del server di origine da cui eseguire il ripristino. |

Il ripristino di un server in un punto nel tempo crea un nuovo server, ovvero una copia del server originale nel momento specificato. I valori relativi al percorso e al piano tariffario del server ripristinato sono gli stessi del server di origine.

Il comando è sincrono e il risultato verrà restituito dopo il ripristino del server. Una volta terminato il ripristino, individuare il nuovo server creato. Verificare che i dati siano stati ripristinati come previsto.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come usare l'interfaccia della riga di comando di Azure e altre utilità per:
> [!div class="checklist"]
> * Creare un database di Azure per il server PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

Per apprendere come usare il portale di Azure per eseguire attività simili, vedere quindi questa esercitazione: [Progettare la prima istanza di Database di Azure per PostgreSQL tramite il portale di Azure](tutorial-design-database-using-azure-portal.md)
