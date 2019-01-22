---
title: 'Guida introduttiva: Creare un server di Database di Azure per MariaDB - Interfaccia della riga di comando di Azure'
description: Questa guida introduttiva descrive come usare l'interfaccia della riga di comando di Azure per creare un server di Database di Azure per MariaDB in un gruppo di risorse di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: 7d45c11345312fe48312bd4e744433397a17a62d
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359324"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Creare un server di Database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare e gestire le risorse di Azure dalla riga di comando o tramite script. Questa guida introduttiva illustra come usare l'interfaccia della riga di comando di Azure per creare un server di Database di Azure per MariaDB in un gruppo di risorse di Azure in circa cinque minuti. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si installa e usa l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario eseguire la versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

Se si hanno più sottoscrizioni, scegliere quella che contiene la risorsa o in cui viene fatturata la risorsa. Per selezionare un ID di sottoscrizione specifico nell'account, usare il comando [az account set](/cli/azure/account#az-account-set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Nell'esempio seguente viene creato un gruppo di risorse denominato `myresourcegroup` nella località `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Creare un database di Azure per un server MariaDB

Creare un server di Database di Azure per MariaDB con il comando [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create). Un server può gestire più database. In genere, viene usato un database separato per ogni progetto o per ogni utente.

Impostazione | Valore di esempio | DESCRIZIONE
---|---|---
name | **mydemoserver** | Immettere un nome univoco per identificare il server di Database di Azure per MariaDB. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) Deve contenere tra 3 e 63 caratteri.
resource-group | **myresourcegroup** | Immettere il nome del gruppo di risorse di Azure.
sku-name | **GP_Gen5_2** | Il nome della SKU. Segue la convenzione *piano tariffario*\_*generazione di calcolo*\_*vCore* in sintassi abbreviata. Per altre informazioni sul parametro **sku-name**, vedere la sezione dopo questa tabella.
backup-retention | **7** | Specifica per quanto tempo deve essere conservato un backup. L'unità è giorni. Intervallo: da 7 a 35. 
geo-redundant-backup | **Disabilitato** | Indica se abilitare i backup con ridondanza geografica per questo server. Valori consentiti: **Enabled**, **Disabled**.
location | **westus** | Località di Azure per il server.
ssl-enforcement | **Enabled** | Indica se abilitare SSL per questo server. Valori consentiti: **Enabled**, **Disabled**.
storage-size | **51200** | Capacità di archiviazione del server (l'unità è MB). Le dimensioni di archiviazione valide partono da un minimo di 5120 MB e aumentano con incrementi di 1024 MB. Per altre informazioni sui limiti delle dimensioni di archiviazione, vedere i [Piani tariffari](./concepts-pricing-tiers.md). 
version | **10.2** | La versione principale del motore MariaDB.
admin-user | **myadmin** | Nome utente per l'account di accesso amministratore. Il parametro **admin-user** non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**.
admin-password | *password* | Password dell'utente amministratore. La password deve contenere da 8 a 128 caratteri. Deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici.

Il valore del parametro sku-name segue la convenzione {piano tariffario}\_{generazione calcolo}\_{vCore} come illustrato nell'esempio seguente:
+ `--sku-name B_Gen5_1` esegue il mapping a Basic, Gen 5 e 1 vCore. Questa opzione corrisponde allo SKU più piccolo disponibile.
+ `--sku-name GP_Gen5_32` esegue il mapping a utilizzo generico, Gen 5 e 32 vCore.
+ `--sku-name MO_Gen5_2` esegue il mapping a ottimizzazione per la memoria, Gen 5 e 2 vCore.

Per informazioni sui valori validi per aree e livelli, vedere [Piani tariffari](./concepts-pricing-tiers.md).

L'esempio seguente crea un server denominato **mydemoserver** nell'area Stati Uniti occidentali. Il server è nel gruppo di risorse **myresourcegroup** e il nome di accesso dell'amministratore server è **myadmin**. Il server è un server di generazione 5 nel piano tariffario Utilizzo generico e ha 2 vCore. Il nome del server è associato a un nome DNS e deve essere univoco a livello globale. Sostituire `<server_admin_password>` con la password dell'amministratore del server.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni I/O sono adeguate per il carico di lavoro. Si noti che i server creati nel piano tariffario Basic non possono essere scalati in un secondo momento per utilizzo generico o ottimizzati per la memoria. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mariadb/).
> 

## <a name="configure-a-firewall-rule"></a>Configurare una regola del firewall

Creare una regola del firewall a livello del server di Database di Azure per MariaDB con il comando [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento da riga di comando mysql o MySQL Workbench, di connettersi al server tramite il firewall del servizio Database di Azure per MariaDB. 

L'esempio seguente crea una regola firewall denominata `AllowMyIP` che consente connessioni da un indirizzo IP specifico, 192.168.0.1. Sostituire con un indirizzo IP o un intervallo di indirizzi IP corrispondente alla posizione da cui ci si connette. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Le connessioni al database di Azure per MariaDB comunicano sulla porta 3306. Se si prova a connettersi dall'interno di una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.
> 

## <a name="configure-ssl-settings"></a>Configurare le impostazioni SSL

Per impostazione predefinita, tra il server e le applicazioni client vengono applicate connessioni SSL. Questa impostazione predefinita garantisce la sicurezza dei dati "in movimento" tramite la crittografia del flusso di dati su Internet. Per questa guida introduttiva, disabilitare le connessioni SSL per il server. Si sconsiglia di disabilitare le connessioni SSL per i server di produzione. Per altre informazioni, vedere [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MariaDB](./howto-configure-ssl.md).

L'esempio seguente disabilita l'applicazione di SSL nel server di Database di Azure per MariaDB:
 
```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso. Per ottenere le informazioni di connessione, eseguire il comando seguente:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Prendere nota dei valori per **fullyQualifiedDomainName** e **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Connettersi al server usando lo strumento da riga di comando mysql

Connettersi al server usando lo strumento da riga di comando mysql. È possibile [scaricare](https://dev.mysql.com/downloads/) lo strumento da riga di comando e installarlo nel computer in uso. Si può accedere allo strumento da riga di comando anche selezionando il pulsante **Prova** in un esempio di codice in questo articolo. Un altro modo per accedere allo strumento da riga di comando consiste nel selezionare il pulsante **>_** sulla barra degli strumenti in alto a destra nel portale di Azure per aprire **Azure Cloud Shell**.

Per connettersi al server usando lo strumento da riga di comando mysql:

1. Connettersi al server:

  ```azurecli-interactive
  mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
  ```

2. Visualizzare lo stato del server al prompt `mysql>`:

  ```sql
  status
  ```
  Verrà visualizzato un testo simile al seguente:

  ```bash
  C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
  Enter password: ***********
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 65512
  Server version: 5.6.39.0 MariaDB Server

  Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

  mysql> status
  --------------
  mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

  Connection id:          64681
  Current database:
  Current user:           myadmin@40.118.201.21
  SSL:                    Cipher in use is AES256-SHA
  Current pager:          stdout
  Using outfile:          ''
  Using delimiter:        ;
  Server version:         5.6.39.0 MariaDB Server
  Protocol version:       10
  Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
  Server characterset:    latin1
  Db     characterset:    latin1
  Client characterset:    utf8
  Conn.  characterset:    utf8
  TCP port:               3306
  Uptime:                 1 day 3 hours 28 min 50 sec

  Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
  --------------

  mysql>
  ```

> [!TIP]
> Per altri comandi, vedere il capitolo 4.5.1 di [MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manuale di riferimento di MySQL 5.7).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Connettersi al server con MySQL Workbench

1.  Aprire MySQL Workbench nel computer client. Se non è già installata, [scaricare](https://dev.mysql.com/downloads/workbench/) e installare l'applicazione.

2.  Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni seguenti nella scheda **Parameters** (Parametri):

 ![Configurare una nuova connessione](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

  | Impostazione | Valore consigliato | DESCRIZIONE |
  |---|---|---|
  | Connection Name (Nome connessione) | **Demo Connection** | Immettere un'etichetta per la connessione (può essere qualsiasi nome) |
  | Connection Method (Metodo di connessione) | **Standard (TCP/IP)** | Usare il protocollo TCP/IP per connettersi a Database di Azure per MariaDB |
  | Nome host | **mydemoserver.mariadb.database.azure.com** | Il nome del server annotato in precedenza. |
  | Porta | **3306** | Porta predefinita per il Database di Azure per MariaDB. |
  | Username | **myadmin@mydemoserver** | Il nome di accesso dell'amministratore server annotato in precedenza. |
  | Password | *password* | Usare la password dell'account amministratore configurata in precedenza. |

3. Per verificare che tutti i parametri siano configurati correttamente, selezionare **Test Connection** (Test connessione).

4. Selezionare la connessione per connettersi al server.

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse usate in questa guida introduttiva non sono necessarie per un'altra guida o esercitazione, è possibile eliminarle eseguendo il comando seguente: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se si vuole eliminare solo il server creato in questa guida introduttiva, eseguire il comando [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete):

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare un database MariaDB con l'interfaccia della riga di comando di Azure](./tutorial-design-database-cli.md)
