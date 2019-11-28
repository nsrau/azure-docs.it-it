---
title: Esercitazione - Configurare i database in Database di Azure per MySQL con Ansible
description: Informazioni su come usare Ansible per la creazione e la configurazione di un server Database di Azure per MySQL
keywords: ansible, azure, devops, bash, playbook, mysql, database
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d8c8fd361f37a9cb961012807fe40b905c10c047
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156521"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Esercitazione: Configurare i database in Database di Azure per MySQL tramite Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Database di Azure per MySQL](/azure/mysql/overview) è un servizio di database relazionale basato su MySQL Community Edition. Database di Azure per MySQL consente di gestire i database MySQL nelle app Web.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare un server MySQL
> * Creare un database MySQL
> * Configurare una regola del firewall in modo che un'app esterna possa connettersi al server
> * Connettersi al server MySql da Azure Cloud Shell
> * Eseguire una query sui server MySQL disponibili
> * Elencare tutti i database nei server connessi

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il codice del playbook in questa sezione crea un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.  

Salvare il playbook seguente come `rg.yml`:

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

Prima di eseguire il playbook, vedere le note seguenti:

* Viene creato un gruppo di risorse denominato `myResourceGroup`.
* Il gruppo di risorse viene creato nella posizione `eastus`:

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Creare un server MySQL e un database

Il codice del playbook in questa sezione crea un server MySQL e un'istanza di Database di Azure per MySQL. Il nuovo server MySQL è un server Basic di generazione 5 con un vCore, denominato `mysqlserveransible`. L'istanza del database è denominata `mysqldbansible`.

Per altre informazioni, vedere [Piani tariffari di Database di Azure per MySQL](/azure/mysql/concepts-pricing-tiers). 

Salvare il playbook seguente come `mysql_create.yml`:

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

Prima di eseguire il playbook, vedere le note seguenti:

* Nella sezione `vars` il valore di `mysqlserver_name` deve essere univoco.
* Nella sezione `vars` sostituire `<server_admin_password>` con una password.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurare una regola del firewall

Una regola del firewall a livello di server consente a un'app esterna di connettersi al server tramite il firewall del servizio MySQL di Azure. Sono esempi di app esterne lo strumento da riga di comando `mysql` e MySQL Workbench.

Il codice del playbook in questa sezione crea una regola del firewall denominata `extenalaccess` che consente le connessioni da qualsiasi indirizzo IP esterno. 

Salvare il playbook seguente come `mysql_firewall.yml`:

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

Prima di eseguire il playbook, vedere le note seguenti:

* Nella sezione vars sostituire `startIpAddress` e `endIpAddress`. Usare l'intervallo di indirizzi IP corrispondente all'intervallo da cui si effettuerà la connessione.
* Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In tal caso, non sarà possibile connettersi al server a meno che il reparto IT non apra la porta 3306.
* Il playbook usa il modulo `azure_rm_resource`, che consente l'uso diretto dell'API REST.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Connettersi al server

In questa sezione si usa Azure Cloud Shell per connettersi al server creato in precedenza.

1. Selezionare il pulsante **Prova** nel codice seguente:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Al prompt, immettere il comando seguente per richiedere lo stato del server:

    ```sql
    mysql> status
    ```
    
    Se il comando viene eseguito correttamente, viene visualizzato un output simile ai risultati seguenti:
    
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
    
## <a name="query-mysql-servers"></a>Recuperare i server MySQL

Il codice del playbook in questa sezione recupera i server MySQL in `myResourceGroup` ed elenca i database nei server trovati.

Salvare il playbook seguente come `mysql_query.yml`:

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

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook mysql_query.yml
```

Dopo avere eseguito il playbook, viene visualizzato un output simile ai risultati seguenti:

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

Viene inoltre visualizzato l'output seguente per il database MySQL:

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

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Salvare il playbook seguente come `cleanup.yml`:

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

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)