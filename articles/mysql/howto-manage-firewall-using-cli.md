---
title: Creare e gestire le regole del firewall di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire regole del firewall di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 01/18/2018
ms.openlocfilehash: 1738fdd85391135357d34fefa878538866f21b91
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Creare e gestire regole del firewall di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure
Le regole del firewall a livello di server consentono agli amministratori di gestire l'accesso a un'istanza di Database di Azure per il server MySQL da uno specifico indirizzo IP o un intervallo di indirizzi IP. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall per gestire il server. Per una panoramica dei firewall di Database di Azure per MySQL, vedere [Azure Database for MySQL server firewall rules](./concepts-firewall-rules.md) (Regole del firewall di Database di Azure per il server MySQL)

## <a name="prerequisites"></a>prerequisiti
* [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Installare Azure Python SDK per PostgreSQL e i servizi MySQL.
* Installare il componente dell'interfaccia della riga di comando di Azure per PostgreSQL e i servizi MySQL.
* Creare un database di Azure per il server MySQL.

## <a name="firewall-rule-commands"></a>Comandi per le regole del firewall:
Il comando **az mysql server firewall-rule** viene usato dall'interfaccia della riga di comando di Azure per creare, eliminare, elencare, visualizzare e aggiornare le regole del firewall.

Comandi:
- **create**: creare una regola del firewall del server MySQL di Azure.
- **delete**: eliminare una regola del firewall del server MySQL di Azure.
- **list**: elencare le regole del firewall del server MySQL di Azure.
- **show**: visualizzare i dettagli di una regola del firewall del server MySQL di Azure.
- **update**: aggiornare una regola del firewall del server MySQL di Azure.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Accedere ad Azure ed elencare le istanze di Database di Azure per server MySQL
Connettere in modo sicuro l'interfaccia della riga di comando di Azure all'account Azure usando il comando **az login**.

1. Eseguire il comando seguente dalla riga di comando:
```azurecli
az login
```
Questo comando restituisce un codice da usare nel passaggio successivo.

2. Usare un Web browser per aprire la pagina [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e quindi immettere il codice.

3. Al prompt dei comandi, accedere usando le credenziali di Azure.

4. Dopo che l'accesso è stato autorizzato, nella console viene visualizzato un elenco di sottoscrizioni. Copiare l'ID della sottoscrizione desiderata per impostare la sottoscrizione corrente da usare. Usare il comando [az account set](/cli/azure/account#az_account_set).
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Elencare le istanze di Database di Azure per i server MySQL per la sottoscrizione e il gruppo di risorse, se non si è certi dei nomi. Usare il comando [az mysql server list](/cli/azure/mysql/server#az_mysql_server_list).

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   Prendere nota dell'attributo del nome nell'elenco, necessario per specificare il server MySQL da usare. Se necessario, verificare i dettagli per il server usando l'attributo del nome per assicurarsi che sia corretto. Usare il comando [az mysql server show](/cli/azure/mysql/server#az_mysql_server_show).

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Elencare le regole del firewall per un'istanza di Database di Azure per il server MySQL 
Usando il nome del server e il nome del gruppo di risorse, elencare le regole del firewall esistenti nel server. Usare il comando [az mysql server firewall list](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_list).  Tenere presente che l'attributo del nome del server è specificato nell'opzione **--server** e non nell'opzione **--name**. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server-name mysqlserver4demo
```
L'output elenca le eventuali regole presenti in formato JSON (per impostazione predefinita). Usando l'opzione **--output table**, i risultati vengono restituiti in un formato di tabella più leggibile.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server-name mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Creare una regola del firewall nell'istanza di Database di Azure per il server MySQL
Usando il nome del server MySQL di Azure e il nome del gruppo di risorse, creare una nuova regola del firewall nel server. Usare il comando [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create). Specificare un nome per la regola, oltre all'indirizzo IP iniziale e all'indirizzo IP finale (per coprire l'accesso a un intervallo di indirizzi IP) relativi alla regola.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Per consentire l'accesso a un solo indirizzo IP, specificare lo stesso indirizzo come indirizzo IP iniziale e finale, come nell'esempio seguente.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Per consentire alle applicazioni da indirizzi IP di Azure di connettersi al server di Database di Azure per MySQL, specificare l'indirizzo IP 0.0.0.0 come indirizzo IP iniziale e indirizzo IP finale, come in questo esempio.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

Al termine dell'operazione, l'output di ogni comando create elenca i dettagli della regola del firewall creata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aggiornare una regola del firewall in Database di Azure per il server MySQL 
Usando il nome del server MySQL di Azure e il nome del gruppo di risorse, aggiornare una regola del firewall esistente nel server. Usare il comando [az mysql server firewall update](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_update). Specificare il nome della regola del firewall esistente come input, oltre agli attributi dell'indirizzo IP iniziale e finale da aggiornare.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall aggiornata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

> [!NOTE]
> Se la regola del firewall non esiste, viene creata dal comando di aggiornamento.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Visualizzare i dettagli di una regola del firewall in Database di Azure per il server MySQL
Usando il nome del server MySQL di Azure e il nome del gruppo di risorse, visualizzare i dettagli di una regola del firewall esistente nel server. Usare il comando [az mysql server firewall show](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_show). Specificare il nome della regola del firewall esistente come input.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall specificata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Eliminare una regola del firewall in Database di Azure per il server MySQL
Usando il nome del server MySQL di Azure e il nome del gruppo di risorse, rimuovere una regola del firewall esistente dal server. Usare il comando [az mysql server firewall delete](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_delete). Specificare il nome della regola del firewall esistente.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, viene visualizzato il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [regole del firewall di Database di Azure per il server MySQL](./concepts-firewall-rules.md).
- [Creare e gestire regole del firewall di Database di Azure per MySQL con il portale di Azure](./howto-manage-firewall-using-portal.md).
