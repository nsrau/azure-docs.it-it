---
title: Progettare il primo Database di Azure per PostgreSQL tramite l&quot;interfaccia della riga di comando di Azure | Documentazione Microsoft
description: In questa esercitazione viene illustrato come progettare il primo Database di Azure per PostgreSQL tramite l&quot;interfaccia della riga di comando di Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Progettare il primo Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure 
In questa esercitazione, si usano l'interfaccia della riga di comando di Azure e altre utilità per informazioni su come:
> [!div class="checklist"]
> * Creare un database di Azure per PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.
```azurecli
az login
```

Se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui la risorsa esiste o per cui è configurata. Selezionare un ID di sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account#set).
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Nell'esempio seguente viene creato un gruppo di risorse denominato `myresourcegroup` nella posizione `westus`.
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un [Database di Azure per il server PostgreSQL](overview.md) tramite il comando **az postgres server create**. Un server contiene un gruppo di database gestiti come gruppo. 

Nell'esempio seguente viene creato un server denominato `mypgserver-20170401` nel gruppo di risorse `myresourcegroup` con account di accesso dell'amministratore del server `mylogin`. Il nome di un server esegue il mapping al nome DNS e pertanto deve essere univoco a livello globale in Azure. Sostituire `<server_admin_password>` con il proprio valore.
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida introduttiva. Prendere nota di queste informazioni per usarle in seguito.

Per impostazione predefinita, il database **postgres** viene creato nel server. Il database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) è un database predefinito che può essere usato da utenti, utilità e applicazioni di terze parti. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Creare una regola del firewall a livello di server di PostgreSQL di Azure con il comando **az postgres server firewall-rule create**. Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) o [PgAdmin](https://www.pgadmin.org/) di connettersi al server tramite il firewall del servizio PostgreSQL di Azure. 

È possibile impostare una regola firewall che copra un intervallo IP che sia in grado di connettersi dalla rete. L'esempio seguente usa **az postgres server firewall-rule create** per creare una regola del firewall `AllowAllIps` per l'intervallo di indirizzi IP. Per aprire tutti gli indirizzi IP, usare 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo finale.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Il server PostgreSQL di Azure comunica sulla porta 5432. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 5432 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
>

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario fornire le informazioni sull'host e le credenziali di accesso.
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

Il risultato è in formato JSON. Annotare l'**administratorLogin** e il **fullyQualifiedDomainName**.
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

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Connettersi al Database di Azure per il database PostgreSQL tramite psql

Se il computer client ha PostgreSQL installato, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) per connettersi a un server PostgreSQL di Azure. Usiamo ora l'utilità della riga di comando psql per connettersi al server PostgreSQL di Azure.

1. Eseguire il comando psql seguente per connettersi a un Database di Azure per il server PostgreSQL
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Ad esempio, il comando seguente consente di connettersi al database predefinito denominato **postgres** sul server PostgreSQL **mypgserver 20170401.postgres.database.azure.com** usando le credenziali di accesso. Inserire `<server_admin_password>` scelto quando viene richiesta la password.
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  Una volta connessi al server, creare un database vuoto nel prompt.
```bash
CREATE DATABASE mypgsqldb;
```

3.  Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato **mypgsqldb**:
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al Database di Azure per PostgreSQL, si può passare al completamento di alcune attività di base.

In primo luogo, è possibile creare una tabella e caricarla con alcuni dati. Creare una tabella che tiene traccia delle informazioni riguardanti l'inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

È possibile visualizzare ora la tabella appena creata nell'elenco di tabelle digitando:
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Ora che abbiamo una tabella, possiamo inserire alcuni dati al suo interno. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, sono presenti due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella del database. 
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare i dati nelle tabelle
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata di conseguenza quando si recuperano dati.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente
Si supponga di aver eliminato accidentalmente una tabella. Si tratta di un elemento che non è facile da ripristinare. Il Database di Azure per PostgreSQL consente di tornare in qualsiasi punto nel tempo (negli ultimi 7 giorni (Basic) e 35 giorni (Standard)) e ripristinare questo punto nel tempo in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il server di esempio in un punto precedente all'aggiunta della tabella.

Per il ripristino sono necessarie le informazioni seguenti:
- **Punto di ripristino:** selezionare un punto nel tempo precedente alla modifica del server. Il punto deve essere maggiore o equivalente al valore del backup meno recente del database di origine.
- **Server di destinazione:** fornire un nuovo nome del server che si desidera ripristinare
- **Server di origine:** fornire il nome del server che si desidera ripristinare
- **Posizione:** non è possibile selezionare l'area, per impostazione predefinita è la stessa del server di origine
- **Piano tariffario:**: non è possibile modificare questo valore quando si ripristina un server. È uguale al server di origine. 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

Per ripristinare il server per [, ripristinare in un punto nel tempo ](./howto-restore-server-portal.md) precedente all'eliminazione delle tabelle. Il ripristino di un server in un altro punto nel tempo crea un duplicato del nuovo server come il server originale nel punto nel tempo specificato, purché sia entro il periodo di conservazione per il [livello di servizio](./concepts-service-tiers.md) applicato.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come usare l'interfaccia della riga di comando di Azure e altre utilità per:
> [!div class="checklist"]
> * Creare un database di Azure per PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

Successivamente, per informazioni su come usare il portale di Azure per eseguire attività simili, esaminare questa esercitazione: [Progettare il primo Database di Azure per PostgreSQL tramite il portale di Azure](tutorial-design-database-using-azure-portal.md)

