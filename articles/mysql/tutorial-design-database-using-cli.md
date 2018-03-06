---
title: Progettare il primo database di Database di Azure per MySQL - Interfaccia della riga di comando di Azure
description: In questa esercitazione viene illustrato come creare e gestire il database di Azure per il server e il database MySQL tramite l'interfaccia della riga di comando di Azure 2.0 dalla riga di comando.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 779e6b48a20dd49967a189293ed37b07bc5e1cda
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Progettare il primo database di Azure per il database MySQL

Il database di Azure per MySQL è un servizio di database relazionale in Microsoft Cloud basato sul motore di database MySQL Community Edition. In questa esercitazione, si usano l'interfaccia della riga di comando di Azure e altre utilità per informazioni su come:

> [!div class="checklist"]
> * Creare un database di Azure per MySQL
> * Configurare il firewall del server
> * Usare lo [strumento della riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

È possibile usare Azure Cloud Shell nel browser o [installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli) nel computer in uso per eseguire i blocchi di codice di questa esercitazione.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

Se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui la risorsa esiste o per cui è configurata. Selezionare un ID di sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) con il comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Nell'esempio seguente viene creato un gruppo di risorse denominato `myresourcegroup` nella posizione `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>Aggiungere l'estensione
Aggiungere l'estensione di gestione di Database di Azure per MySQL aggiornata con il comando seguente:
```azurecli-interactive
az extension add --name rdbms
``` 
## <a name="create-an-azure-database-for-mysql-server"></a>Creare un'istanza di Database di Azure per il server MySQL
Creare un database di Azure per il server MySQL con il comando mysql server create. Un server può gestire più database. In genere, viene usato un database separato per ogni progetto o per ogni utente.

L'esempio seguente crea un database di Azure per il server MySQL disponibile in `westus` nel gruppo di risorse `myresourcegroup` con nome `mydemoserver`. Il server ha un account di accesso amministratore denominato `myadmin`. Questo è un server per utilizzo generico di generazione 4 con 2 vCore. Sostituire `<server_admin_password>` con il proprio valore.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida di avvio rapido. Prendere nota di queste informazioni per usarle in seguito.


## <a name="configure-firewall-rule"></a>Configurare una regola del firewall
Creare una regola del firewall a livello di server del Database SQL di Azure con il comando az mysql server firewall-rule create. Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento della riga di comando **mysql** o MySQL Workbench, di connettersi al server tramite il firewall del servizio MySQL di Azure. 

Nell'esempio seguente viene creata una regola del firewall per un intervallo di indirizzi predefinito. In questo esempio viene illustrato l'intero intervallo possibile di indirizzi IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare il **fullyQualifiedDomainName** e l'**administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Connettersi al server usando mysql
Usare lo [strumento da riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per stabilire una connessione al server Database di Azure per MySQL. In questo esempio, il comando è:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Creazione di un database vuoto
Una volta connessi al server, creare un database vuoto.
```sql
mysql> CREATE DATABASE mysampledb;
```

Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al database di Database di Azure per MySQL, completare alcune attività di base.

In primo luogo, creare una tabella e caricarvi alcuni dati. Creare una tabella che contenga le informazioni riguardanti l'inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Dopo aver creato una tabella, inserire alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, ci sono due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella del database.
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare query e aggiornare i dati nelle tabelle.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata di conseguenza quando si recuperano dati.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente
Si supponga di aver eliminato accidentalmente questa tabella. Si tratta di un elemento che non è facile da ripristinare. Il database di Azure per MySQL consente di tornare in qualsiasi punto degli ultimi 35 giorni e di ripristinare questo punto nel tempo in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il server di esempio in un punto precedente all'aggiunta della tabella.

Per il ripristino sono necessarie le informazioni seguenti:

- Punto di ripristino: selezionare un punto nel tempo precedente alla modifica del server. Il punto deve essere maggiore o equivalente al valore del backup meno recente del database di origine.
- Server di destinazione: fornire un nuovo nome del server che si desidera ripristinare.
- Server di origine: fornire il nome del server che si desidera ripristinare
- Posizione: non è possibile selezionare l'area, per impostazione predefinita è la stessa del server di origine

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Il comando `az mysql server restore` richiede i parametri seguenti:
| Impostazione | Valore consigliato | DESCRIZIONE  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| name | mydemoserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selezionare un punto di ripristino temporizzato. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. È possibile usare il fuso orario locale, ad esempio `2017-04-13T05:59:00-08:00`, o il formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Il nome o l'ID del server di origine da cui eseguire il ripristino. |

Il ripristino di un server in un punto nel tempo crea un nuovo server, ovvero una copia del server originale nel momento specificato. I valori relativi al percorso e al piano tariffario del server ripristinato sono gli stessi del server di origine.

Il comando è sincrono e il risultato verrà restituito dopo il ripristino del server. Una volta terminato il ripristino, individuare il nuovo server creato. Verificare che i dati siano stati ripristinati come previsto.

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:
> [!div class="checklist"]
> * Creare un'istanza di Database di Azure per il server MySQL
> * Configurare il firewall del server
> * Usare lo [strumento della riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

> [!div class="nextstepaction"]
> [Database di Azure per MySQL - Esempi di interfaccia della riga di comando di Azure](./sample-scripts-azure-cli.md)
