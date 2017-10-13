---
title: Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 6f081416dd7d78f0153b3fda21a340a8c1a70c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Le regole del firewall a livello di server consentono agli amministratori di gestire l'accesso a un'istanza di Database di Azure per il server PostgreSQL da uno specifico indirizzo IP o intervallo di indirizzi IP. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall per gestire il server. Per una panoramica dei firewall di Database di Azure per PostgreSQL, vedere [Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md) (Regole del firewall di Database di Azure per il server PostgreSQL)

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un [server e un database di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installare l'utilità della riga di comando [Azure CLI 2.0](/cli/azure/install-azure-cli) o usare Azure Cloud Shell nel browser.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurare le regole del firewall di Database di Azure per PostgreSQL
I comandi [az postgres server firewall-rule-](/cli/azure/postgres/server/firewall-rule) vengono usati per configurare le regole del firewall.

## <a name="list-firewall-rules"></a>Elencare le regole del firewall 
Per elencare le regole del firewall del server esistenti sul server, eseguire il comando [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#list).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
L'output elenca le eventuali regole presenti, per impostazione predefinita in formato JSON. È possibile usare l'opzione `--output table` per ottenere un formato di tabella più leggibile come output.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Creare la regola del firewall
Per creare una nuova regola del firewall sul server, eseguire il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create). 

Questo esempio consente un intervallo di tutti gli indirizzi IP per accedere al server **mypgserver-20170401.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Per consentire l'accesso a un solo indirizzo IP, specificare lo stesso valore come indirizzo IP iniziale e finale, come nell'esempio seguente.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall creata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

## <a name="update-firewall-rule"></a>Aggiornare la regola del firewall 
Aggiornare una regola del firewall esistente sul server usando il comando [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#update). Specificare il nome della regola del firewall esistente come input e gli attributi dell'indirizzo IP iniziale e finale per l'aggiornamento.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall aggiornata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.
> [!NOTE]
> Se la regola del firewall non esiste, viene creata dal comando di aggiornamento.

## <a name="show-firewall-rule-details"></a>Visualizzare i dettagli di una regola del firewall
È inoltre possibile visualizzare i dettagli di una regola del firewall esistente per un server eseguendo il comando [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#show).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall specificata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

## <a name="delete-firewall-rule"></a>Eliminare la regola del firewall
Per revocare l'accesso per un intervallo IP dal server, eliminare una regola del firewall esistente eseguendo il comando [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#delete). Specificare il nome della regola del firewall esistente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, viene restituito il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Analogamente, è possibile usare un Web browser per [creare e gestire le regole del firewall di Database di Azure per PostgreSQL usando il portale di Azure](howto-manage-firewall-using-portal.md)
- Altre informazioni sulle [regole del firewall di Database di Azure per il server PostgreSQL](concepts-firewall-rules.md)
- Per informazioni sulla connessione a un'istanza di Database di Azure per il server PostgreSQL, vedere [Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md) (Raccolte di connessioni per Database di Azure per PostgreSQL)
