---
title: 'Avvio rapido: Creare un database di Azure per il server MySQL - Interfaccia della riga di comando di Azure | Microsoft Docs'
description: Questa guida di avvio rapido descrive come usare l'interfaccia della riga di comando di Azure per creare un database di Azure per il server MySQL in un gruppo di risorse di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: hero-article
ms.date: 11/29/2017
ms.custom: mvc
ms.openlocfilehash: f2b9df09135ae922f617c21cc5b9e32556d515f6
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure
Questa guida di avvio rapido descrive come usare l'interfaccia della riga di comando di Azure per creare un database di Azure per il server MySQL in un gruppo di risorse di Azure in circa cinque minuti. L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

Se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui la risorsa esiste o per cui è configurata. Selezionare un ID di sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Nell'esempio seguente viene creato un gruppo di risorse denominato `myresourcegroup` nella posizione `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un database di Azure per il server MySQL
Creare un database di Azure per il server MySQL con il comando **[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)**. Un server può gestire più database. In genere, viene usato un database separato per ogni progetto o per ogni utente.

L'esempio seguente crea un database di Azure per il server MySQL disponibile in `westus` nel gruppo di risorse `myresourcegroup` con nome `myserver4demo`. Il server ha un account di accesso amministratore chiamato `myadmin` e la password `Password01!`. Il server viene creato con livello di prestazioni **Basic** e **50** unità di calcolo condivise tra tutti i database nel server. È possibile aumentare o ridurre le capacità di calcolo e archiviazione a seconda delle esigenze dell'applicazione.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name myserver4demo --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Configurare una regola del firewall
Creare una regola del firewall a livello di server per il database di Azure per il server MySQL con il comando **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)**. Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento della riga di comando **mysql.exe** o MySQL Workbench, di connettersi al server tramite il firewall del servizio MySQL Azure. 

L'esempio seguente crea una regola del firewall per un intervallo di indirizzi predefinito, che in questo esempio corrisponde all'intero intervallo possibile di indirizzi IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server myserver4demo --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Configurare le impostazioni SSL
Per impostazione predefinita, tra il server e le applicazioni client vengono applicate connessioni SSL. Questa impostazione predefinita garantisce la sicurezza dei dati "in movimento" tramite la crittografia del flusso di dati su Internet. Per semplificare questa guida introduttiva, disabilitare le connessioni SSL per il server. Si sconsiglia di disabilitare le connessioni SSL per i server di produzione. Per altre informazioni, vedere [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MySQL](./howto-configure-ssl.md).

L'esempio seguente disabilita l'applicazione di SSL nel server MySQL.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name myserver4demo --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name myserver4demo
```

Il risultato è in formato JSON. Annotare i valori di **fullyQualifiedDomainName** e **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "myserver4demo.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/myserver4demo",
  "location": "westus",
  "name": "myserver4demo",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Connettersi al server tramite lo strumento da riga di comando mysql.exe
Connettersi al server tramite lo strumento da riga di comando **mysql.exe**. È possibile scaricare MySQL da [qui](https://dev.mysql.com/downloads/) e installarlo nel computer in uso. È anche possibile fare clic sul pulsante **Prova** negli esempi di codice o sul pulsante `>_` nella barra degli strumenti in alto a destra nel portale di Azure e avviare **Azure Cloud Shell**.

Digitare i comandi seguenti: 

1. Connettersi al server tramite lo strumento da riga di comando **mysql**:
```azurecli-interactive
 mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. Visualizzare lo stato del server:
```sql
 mysql> status
```
Se è tutto corretto, lo strumento da riga di comando deve restituire il testo seguente:

```dos
C:\Users\>mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             myserver4demo.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Per altri comandi, vedere il capitolo 4.5.1 di [MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manuale di riferimento di MySQL 5.7).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connettersi al server tramite lo strumento dell'interfaccia utente grafica MySQL Workbench
1.  Avviare l'applicazione MySQL Workbench nel computer client. È possibile scaricare e installare MySQL Workbench da [qui](https://dev.mysql.com/downloads/workbench/).

2.  Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni seguenti nella scheda **Parameters** (Parametri):

   ![Setup New Connection (Configura nuova connessione)](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Impostazione** | **Valore consigliato** | **Descrizione** |
|---|---|---|
|   Connection Name (Nome connessione) | Connessione in uso | Specificare un'etichetta per la connessione (può essere qualsiasi nome) |
| Connection Method (Metodo di connessione) | Scegliere Standard (TCP/IP) | Usare il protocollo TCP/IP per connettersi a Database di Azure per MySQL> |
| Nome host | myserver4demo.mysql.database.azure.com | Nome del server annotato in precedenza. |
| Porta | 3306 | Viene usata la porta predefinita per MySQL. |
| Username | myadmin@myserver4demo | Account di accesso amministratore server annotato in precedenza. |
| Password | **** | Usare la password dell'account amministratore configurata in precedenza. |

Fare clic su **Test Connection** (Test connessione) per verificare che tutti i parametri siano configurati correttamente.
È ora possibile fare clic sulla connessione per connettersi al server.

## <a name="clean-up-resources"></a>Pulire le risorse
Se queste risorse non sono necessarie per un'altra guida introduttiva/esercitazione, è possibile eliminarle eseguendo il comando seguente: 

```azurecli-interactive
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare un database MySQL con l'interfaccia della riga di comando di Azure](./tutorial-design-database-using-cli.md)
