---
title: Creare e configurare un Database di Azure per MySQL con Ansible (anteprima)
description: Informazioni su come usare Ansible per la creazione e la configurazione di un Database di Azure per MySQL
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, mysql, database
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 508274d11a9693d28a9b3a01bd6ebbd7198e8711
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586666"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-using-ansible-preview"></a>Creare e configurare un database di Azure per MySQL con Ansible (anteprima)
[Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/) è un servizio gestito che consente di eseguire, gestire e ridimensionare database MySQL a disponibilità elevata nel cloud. Questa guida introduttiva illustra come creare un server Database di Azure per MySQL in circa cinque minuti usando il portale di Azure. 

Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. Questo articolo illustra come usare Ansible per creare un Database di Azure per MySQL e configurarne la regola del firewall in cinque minuti. 

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> In questa esercitazione, per eseguire i playbook di esempio seguenti è necessario Ansible 2.7. È possibile installare la versione RC 2.7 di Ansible eseguendo `sudo pip install ansible[azure]==2.7.0rc2`. Ansible 2.7 verrà rilasciata a ottobre 2018. Successivamente, non sarà necessario specificare la versione qui perché la versione predefinita sarà 2.7.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.  

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroup** nella località **stati uniti orientali**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Salvare il sopracitato playbook come *rg.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:
```bash
ansible-playbook rg.yml
```

## <a name="create-mysql-server-and-database"></a>Creare un server MySQL e un database
L'esempio seguente crea un server MySQL denominato **mysqlserveransible** e un Database di Azure per MySQL denominato **mysqldbansible**. Questo è un server per utilizzo base di generazione 5 con 1 vCore. Il valore di **mysqlserver_name** deve essere univoco. Vedere la [documentazione dei piani tariffari](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) per comprendere i valori validi per area e livello. 

Immettere la propria `<server_admin_password>`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Salvare il sopracitato playbook come *mysql_create.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-firewall-rule"></a>Configurare una regola del firewall
Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento della riga di comando **mysql** o MySQL Workbench, di connettersi al server tramite il firewall del servizio MySQL di Azure. L'esempio seguente crea una regola del firewall denominata **externalaccess** che consente connessioni da qualsiasi indirizzo IP esterno. 

Immettere il proprio **startIpAddress** e **endIpAddress** con un intervallo di indirizzi IP corrispondenti alla posizione da cui si effettuerà la connessione: 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

> [!NOTE]
> Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.
> 

Per eseguire quest'attività, qui viene usato il modulo **azure_rm_resource** che consente l'uso diretto dell'API REST.

Salvare il sopracitato playbook come *mysql_firewall.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-using-command-line-tool"></a>Connettersi al server con lo strumento da riga di comando
È possibile scaricare MySQL da [qui](https://dev.mysql.com/downloads/) e installarlo nel computer in uso. È anche possibile fare clic sul pulsante **Prova** negli esempi di codice o sul pulsante `>_` nella barra degli strumenti in alto a destra nel portale di Azure e avviare **Azure Cloud Shell**.

Digitare i comandi seguenti: 

1. Connettersi al server tramite lo strumento da riga di comando **mysql**:
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. Visualizzare lo stato del server:
```sql
 mysql> status
```

Se è tutto corretto, lo strumento da riga di comando deve restituire il testo seguente:

```
demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65233
Server version: 5.6.39.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          65233
Current database:
Current user:           mysqladmin@13.76.42.93
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 36 min 21 sec

Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
--------------
```

## <a name="using-facts-to-query-mysql-servers"></a>Uso di fact per eseguire query su server MySQL
L'esempio seguente esegue una query su uno o più server MySQL in **myResourceGroup** e successivamente su tutti i database sul server:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Salvare il sopracitato playbook come *mysql_query.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook mysql_query.yml
```

Viene quindi visualizzato l'output seguente per il server MySQL: 
```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Viene anche visualizzato l'output seguente per il database MySQL:
```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non sono necessarie queste risorse, è possibile eliminarle eseguendo l'esempio seguente. Viene cancellato un gruppo di risorse denominato **myResourceGroup**. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Salvare il sopracitato playbook come *rg_delete*.yml. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:
```bash
ansible-playbook rg_delete.yml
```

Se si vuole eliminare solo il server MySQL appena creato, è possibile farlo eseguendo lo script nell'esempio seguente:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Salvare il sopracitato playbook come *mysql_delete.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Ansible in Azure](https://docs.microsoft.com/azure/ansible/)