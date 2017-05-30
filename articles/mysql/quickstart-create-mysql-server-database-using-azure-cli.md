---
title: 'Avvio rapido: Creare un database di Azure per il server MySQL - Interfaccia della riga di comando di Azure | Microsoft Docs'
description: Questa guida di avvio rapido descrive come usare l&quot;interfaccia della riga di comando di Azure per creare un database di Azure per il server MySQL in un gruppo di risorse di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/24/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9f78163e4ff1166a2abd94150d686256ee338286
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure
Questa guida di avvio rapido descrive come usare l'interfaccia della riga di comando di Azure per creare un database di Azure per il server MySQL in un gruppo di risorse di Azure in circa cinque minuti. L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script.

Per completare questa guida di avvio rapido, verificare di aver installato la versione più aggiornata dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

```azurecli
az login
```
Seguire le istruzioni del prompt dei comandi per aprire l'URL https://aka.ms/devicelog nel browser e quindi immettere il codice generato nel **prompt dei comandi**.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) con il comando [az group create](https://docs.microsoft.com/cli/azure/group#create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Nell'esempio seguente viene creato un gruppo di risorse denominato `mycliresource` nella posizione `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un database di Azure per il server MySQL
Creare un database di Azure per il server MySQL con il comando **az mysql server create**. Un server può gestire più database. In genere, viene usato un database separato per ogni progetto o per ogni utente.

L'esempio seguente crea un database di Azure per il server MySQL disponibile in `westus` nel gruppo di risorse `mycliresource` con nome `mycliserver`. Il server ha un account di accesso amministratore chiamato `myadmin` e la password `Password01!`. Il server viene creato con livello di prestazioni **Basic** e **50** unità di calcolo condivise tra tutti i database nel server. È possibile aumentare o ridurre la capacità di calcolo e quella di archiviazione a seconda delle esigenze dell'applicazione.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Configurare una regola del firewall
Creare una regola del firewall a livello di server per il database di Azure per il server MySQL con il comando **az mysql server firewall-rule create**. Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento della riga di comando **mysql.exe** o MySQL Workbench, di connettersi al server tramite il firewall del servizio MySQL Azure. 

L'esempio seguente crea una regola del firewall per un intervallo di indirizzi predefinito, che in questo esempio corrisponde all'intero intervallo possibile di indirizzi IP.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Configurare le impostazioni SSL
Per impostazione predefinita, tra il server e le applicazioni client vengono applicate connessioni SSL.  Questo garantisce la sicurezza dei dati "in movimento" tramite la crittografia del flusso di dati su Internet.  Per semplificare questa guida di avvio rapido, le connessioni SSL verranno disabilitate per il server.  Questo comportamento non è consigliato per i server di produzione.  Per altre informazioni, vedere [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurare la connettività SSL nell'applicazione per connettersi in modo sicuro al database di Azure per MySQL).

L'esempio seguente disabilita l'applicazione di SSL nel server MySQL.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

Il risultato è in formato JSON. Annotare i valori di **fullyQualifiedDomainName** e **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
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
Per connettersi al server tramite lo strumento da riga di comando **mysql.exe**, assicurarsi che nel computer sia installato MySQL.  È possibile scaricare MySQL da [qui](https://dev.mysql.com/downloads/).

Aprire il prompt dei comandi e immettere il comando seguente: 

1. Connettersi al server tramite lo strumento da riga di comando **mysql**:
```dos
 mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
```

2. Visualizzare lo stato del server:
```dos
 mysql> status
```
Se è tutto corretto, lo strumento da riga di comando deve restituire il codice seguente:

```dos
C:\Users\v-chenyh>mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
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
Connection:             mycliserver.mysql.database.azure.com via TCP/IP
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
> Per altri comandi, vedere [MySQL 5.6 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) (Manuale di riferimento di MySQL 5.6 - Capitolo 4.5.1).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connettersi al server tramite lo strumento dell'interfaccia utente grafica MySQL Workbench
1.    Avviare l'applicazione MySQL Workbench nel computer client. È possibile scaricare e installare MySQL Workbench da [qui](https://dev.mysql.com/downloads/workbench/).

2.    Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni seguenti nella scheda **Parameters** (Parametri):

| **Parameters** | **Descrizione** |
|----------------|-----------------|
|    *Connection Name* (Nome connessione) | Specificare un nome per la connessione. Può essere qualsiasi nome. |
| *Connection Method* (Metodo di connessione) | Scegliere Standard (TCP/IP) |
| *Hostname* (Nome host) | mycliserver.mysql.database.azure.com (Nome del server annotato in precedenza) |
| *Porta* | 3306 |
| *Nome utente* | myadmin@mycliserver (Account di accesso amministratore server annotato in precedenza) |
| *Password* | Archiviare la password dell'account amministratore |

   ![Setup New Connection (Configura nuova connessione)](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

Fare clic su **Test Connection** (Test connessione) per verificare che tutti i parametri siano configurati correttamente.
È ora possibile fare clic sulla connessione appena creata per connettersi al server.

## <a name="clean-up-resources"></a>Pulire le risorse

Se queste risorse non sono necessarie per un'altra guida di avvio rapido/esercitazione, è possibile eliminarle eseguendo questi passaggi: 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare un database MySQL con l'interfaccia della riga di comando di Azure](./tutorial-design-database-using-cli.md)

