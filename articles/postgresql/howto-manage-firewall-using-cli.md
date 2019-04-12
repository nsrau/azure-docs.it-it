---
title: Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
description: Questo articolo descrive come creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9a96361d3fb155ea5b400990690e3c2b1f65f819
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492448"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Le regole del firewall a livello di server sono utilizzabile per gestire l'accesso a un Database di Azure per il PostgreSQL Server da uno specifico indirizzo IP o intervallo di indirizzi IP. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall per gestire il server. Per una panoramica del Database di Azure per le regole del firewall di PostgreSQL, vedere [Database di Azure per regole firewall del PostgreSQL Server](concepts-firewall-rules.md).

Le regole della rete virtuale sono anche utilizzabile per proteggere l'accesso al server. Altre informazioni sulle [creazione e gestione di rete virtuale del servizio endpoint e regole tramite la CLI di Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Installare l'utilità della riga di comando [Azure CLI](/cli/azure/install-azure-cli) o usare Azure Cloud Shell nel browser.
- [Server e database di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurare le regole del firewall di Database di Azure per PostgreSQL
I comandi [az postgres server firewall-rule-](/cli/azure/postgres/server/firewall-rule) vengono usati per configurare le regole del firewall.

## <a name="list-firewall-rules"></a>Elencare le regole del firewall 
Per elencare le regole firewall del server esistenti, eseguire il comando [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
L'output elenca le eventuali regole firewall presenti, per impostazione predefinita in formato JSON. È possibile usare l'opzione `--output table` per ottenere un formato di tabella più leggibile come output.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Creare la regola del firewall
Per creare una nuova regola del firewall sul server, eseguire il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Per consentire alle applicazioni da indirizzi IP di Azure di connettersi al server di Database di Azure per PostgreSQL, specificare l'indirizzo IP 0.0.0.0 come indirizzo IP iniziale e indirizzo IP finale, come in questo esempio.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall creata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece un messaggio di errore.

## <a name="update-firewall-rule"></a>Aggiornare la regola del firewall 
Aggiornare una regola del firewall esistente sul server usando il comando [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule). Specificare il nome della regola del firewall esistente come input e gli attributi dell'indirizzo IP iniziale e finale per l'aggiornamento.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall aggiornata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece un messaggio di errore.
> [!NOTE]
> Se la regola del firewall non esiste, viene creata dal comando di aggiornamento.

## <a name="show-firewall-rule-details"></a>Visualizzare i dettagli di una regola del firewall
Si può anche visualizzare i dettagli di una regola firewall esistente a livello di server eseguendo il comando [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall specificata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece un messaggio di errore.

## <a name="delete-firewall-rule"></a>Eliminare la regola del firewall
Per revocare l'accesso al server per un intervallo IP, eliminare una regola firewall esistente eseguendo il comando [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule). Specificare il nome della regola del firewall esistente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, viene restituito il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Analogamente, è possibile usare un Web browser per [creare e gestire le regole firewall di Database di Azure per PostgreSQL usando il portale di Azure](howto-manage-firewall-using-portal.md).
- Altre informazioni sulle [regole firewall di Database di Azure per il server PostgreSQL](concepts-firewall-rules.md).
- Proteggere ulteriormente l'accesso al server dal [creazione e gestione di rete virtuale del servizio endpoint e regole tramite la CLI di Azure](howto-manage-vnet-using-cli.md).
- Per informazioni sulla connessione a un'istanza di Database di Azure per il server PostgreSQL, vedere [Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md) (Raccolte di connessioni per Database di Azure per PostgreSQL).
