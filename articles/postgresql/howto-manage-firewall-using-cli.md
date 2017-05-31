---
title: Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Descrive come creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l&quot;interfaccia della riga di comando di Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Le regole del firewall a livello di server consentono agli amministratori di gestire l'accesso a un'istanza di Database di Azure per il server PostgreSQL da uno specifico indirizzo IP o intervallo di indirizzi IP. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall per gestire il server. Per una panoramica dei firewall di Database di Azure per PostgreSQL, vedere [Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md) (Regole del firewall di Database di Azure per il server PostgreSQL)

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un [server e un database di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Utilità della riga di comando dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installata

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurare le regole del firewall di Database di Azure per PostgreSQL
Il comando **az postgres server firewall-rule** viene usato dall'interfaccia della riga di comando di Azure per creare, eliminare, elencare, visualizzare e aggiornare le regole del firewall. Questo 

## <a name="login-to-azure-and-list-servers"></a>Accedere ad Azure ed elencare i server
Connettere in modo sicuro l'interfaccia della riga di comando di Azure all'account Azure. A tale scopo, usare il comando **az login**.
1. Eseguire il comando seguente dalla riga di comando.
```azurecli
az login
```
Questo comando restituirà un codice da usare nel passaggio successivo.

2. Usare un Web browser per aprire la pagina [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice.

3. Al prompt dei comandi, accedere usando le credenziali di Azure.

4. Dopo che l'accesso è stato autorizzato, nella console verrà visualizzato un elenco di sottoscrizioni.
Copiare l'ID della sottoscrizione desiderata per impostare la sottoscrizione corrente da usare per procedere.
```azurecli
az account set --subscription {your subscription id}
```
5. Elencare le istanze di Database di Azure per i server PostgreSQL per la sottoscrizione e il gruppo di risorse, se non si è certi dei nomi.

```azurecli
az postgres server list --resource-group myresourcegroup
```
Prendere nota dell'attributo del nome nell'elenco, che verrà usato per specificare il server PostgreSQL. Se necessario, verificare i dettagli del server usando l'attributo del nome per verificare che il nome sia corretto:

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>Elencare le regole del firewall 
Usando il nome del server e il nome del gruppo di risorse, elencare le regole del firewall esistenti nel server. Si noti che l'attributo del nome server è specificato nell'opzione **--server** e non nell'opzione **--name**.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
L'output elencherà le eventuali regole presenti, per impostazione predefinita in formato JSON. È possibile usare l'opzione **--output table** per ottenere un formato di tabella più leggibile come output.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Creare la regola del firewall
Usando il nome dell'istanza di Database di Azure per il server PostgreSQL e il nome del gruppo di risorse, creare una nuova regola del firewall nel server. Specificare un nome per la regola, oltre all'indirizzo IP iniziale e all'indirizzo IP finale per coprire un intervallo di indirizzi IP cui consentire l'accesso.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Per consentire l'accesso a un solo indirizzo IP, specificare lo stesso valore come indirizzo IP iniziale e finale, come nell'esempio seguente.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
Al termine dell'operazione, l'output del comando elencherà i dettagli della regola del firewall creata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizzerà invece il testo di un messaggio di errore.

## <a name="update-firewall-rule"></a>Aggiornare la regola del firewall 
Usando il nome dell'istanza di Database di Azure per il server PostgreSQL e il nome del gruppo di risorse, aggiornare una regola del firewall esistente nel server. Specificare il nome della regola del firewall esistente come input e gli attributi dell'indirizzo IP iniziale e finale per l'aggiornamento.
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Al termine dell'operazione, l'output del comando elencherà i dettagli della regola del firewall aggiornata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizzerà invece il testo di un messaggio di errore.
> [!NOTE]
> Se la regola del firewall non esiste, verrà creata dal comando di aggiornamento.

## <a name="show-firewall-rule-details"></a>Visualizzare i dettagli di una regola del firewall
Usando il nome dell'istanza di Database di Azure per il server PostgreSQL e il nome del gruppo di risorse, visualizzare i dettagli di una regola del firewall esistente nel server. Specificare il nome della regola del firewall esistente come input.
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
Al termine dell'operazione, l'output del comando elencherà i dettagli della regola del firewall specificata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizzerà invece il testo di un messaggio di errore.

## <a name="delete-firewall-rule"></a>Eliminare la regola del firewall
Usando il nome dell'istanza di Database di Azure per il server PostgreSQL e il nome del gruppo di risorse, rimuovere una regola del firewall esistente dal server. Specificare il nome della regola del firewall esistente.
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, verrà restituito il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Analogamente, è possibile usare un Web browser per [creare e gestire le regole del firewall di Database di Azure per PostgreSQL usando il portale di Azure](howto-manage-firewall-using-portal.md)
- Altre informazioni sulle [regole del firewall di Database di Azure per il server PostgreSQL](concepts-firewall-rules.md)
- Per informazioni sulla connessione a un'istanza di Database di Azure per il server PostgreSQL, vedere [Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md) (Raccolte di connessioni per Database di Azure per PostgreSQL)

