---
title: Creare e gestire regole del firewall di Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure
description: Questo articolo descrive come creare e gestire regole del firewall di Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: a2f0d775f061ab221092c9354d9d04b9241c9430
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543887"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Creare e gestire le regole del firewall di Database di Azure per MariaDB con l'interfaccia della riga di comando di Azure
Le regole del firewall a livello di server consentono agli amministratori di gestire l'accesso a un'istanza del server di Database di Azure per MariaDB da uno specifico indirizzo IP o un intervallo di indirizzi IP. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall per gestire il server. Per una panoramica dei firewall di Database di Azure per MariaDB, vedere [Azure Database for MariaDB server firewall rules](./concepts-firewall-rules.md) (Regole del firewall del server di Database di Azure per MariaDB)

## <a name="prerequisites"></a>Prerequisiti
* [Installare l'interfaccia da riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Un [server e un database di Database di Azure per MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Comandi per le regole del firewall:
Il comando **az mariadb server firewall-rule** viene usato dall'interfaccia della riga di comando di Azure per creare, eliminare, elencare, visualizzare e aggiornare le regole del firewall.

Comandi:
- **create**: Creare una regola del firewall del server MariaDB di Azure.
- **delete**: Eliminare una regola del firewall del server MariaDB di Azure.
- **list**: Elencare le regole del firewall del server MariaDB di Azure.
- **show**: Visualizzare i dettagli di una regola del firewall del server MariaDB di Azure.
- **update**: Aggiornare una regola del firewall del server MariaDB di Azure.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Accedere ad Azure ed elencare le istanze del server di Database di Azure per MariaDB
Connettere in modo sicuro l'interfaccia della riga di comando di Azure all'account Azure usando il comando **az login**.

1. Eseguire il comando seguente dalla riga di comando:
```azurecli
az login
```
Questo comando restituisce un codice da usare nel passaggio successivo.

2. Usare un Web browser per aprire la pagina [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e quindi immettere il codice.

3. Al prompt dei comandi, accedere usando le credenziali di Azure.

4. Dopo che l'accesso è stato autorizzato, nella console viene visualizzato un elenco di sottoscrizioni. Copiare l'ID della sottoscrizione desiderata per impostare la sottoscrizione corrente da usare. Usare il comando [az account set](/cli/azure/account#az-account-set).
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Elencare le istanze dei server di Database di Azure per MariaDB per la sottoscrizione e il gruppo di risorse, se non si è certi dei nomi. Usare il comando [az mariadb server list](/cli/azure/mariadb/server#az-mariadb-server-list).

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Prendere nota dell'attributo del nome nell'elenco, necessario per specificare il server MariaDB da usare. Se necessario, verificare i dettagli per il server usando l'attributo del nome per assicurarsi che sia corretto. Usare il comando [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show).

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Elencare le regole del firewall per un'istanza del server di Database di Azure per MariaDB 
Usando il nome del server e il nome del gruppo di risorse, elencare le regole del firewall esistenti nel server. Usare il comando [az mariadb server firewall list](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list).  Tenere presente che l'attributo del nome del server è specificato nell'opzione **--server** e non nell'opzione **--name**. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
L'output elenca le eventuali regole presenti in formato JSON (per impostazione predefinita). Usando l'opzione **--output table**, i risultati vengono restituiti in un formato di tabella più leggibile.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Creare una regola del firewall per un'istanza del server di Database di Azure per MariaDB
Usando il nome del server MariaDB di Azure e il nome del gruppo di risorse, creare una nuova regola del firewall nel server. Usare il comando [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). Specificare un nome per la regola, oltre all'indirizzo IP iniziale e all'indirizzo IP finale (per coprire l'accesso a un intervallo di indirizzi IP) relativi alla regola.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Per consentire l'accesso a un solo indirizzo IP, specificare lo stesso indirizzo come indirizzo IP iniziale e finale, come nell'esempio seguente.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Per consentire alle applicazioni da indirizzi IP di Azure di connettersi al server di Database di Azure per MariaDB, specificare l'indirizzo IP 0.0.0.0 come indirizzo IP iniziale e indirizzo IP finale, come in questo esempio.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

Al termine dell'operazione, l'output di ogni comando create elenca i dettagli della regola del firewall creata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Aggiornare una regola del firewall per il server di Database di Azure per MariaDB 
Usando il nome del server MariaDB di Azure e il nome del gruppo di risorse, aggiornare una regola del firewall esistente nel server. Usare il comando [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update). Specificare il nome della regola del firewall esistente come input, oltre agli attributi dell'indirizzo IP iniziale e finale da aggiornare.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall aggiornata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

> [!NOTE]
> Se la regola del firewall non esiste, viene creata dal comando di aggiornamento.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Visualizzare i dettagli di una regola del firewall nel server di Database di Azure per MariaDB
Usando il nome del server MariaDB di Azure e il nome del gruppo di risorse, visualizzare i dettagli di una regola del firewall esistente nel server. Usare il comando [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show). Specificare il nome della regola del firewall esistente come input.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall specificata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Eliminare una regola del firewall per il server di Database di Azure per MariaDB
Usando il nome del server MariaDB di Azure e il nome del gruppo di risorse, rimuovere una regola del firewall esistente dal server. Usare il comando [az mariadb server firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete). Specificare il nome della regola del firewall esistente.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, viene visualizzato il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [regole del firewall del server di Database di Azure per MariaDB](./concepts-firewall-rules.md).
- [Creare e gestire le regole del firewall di Database di Azure per MariaDB con il portale di Azure](./howto-manage-firewall-portal.md).
