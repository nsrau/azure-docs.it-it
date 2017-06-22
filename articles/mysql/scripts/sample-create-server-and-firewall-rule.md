---
title: Esempi dell&quot;interfaccia della riga di comando di Azure per creare un singolo database di Azure per il server MySQL e configurare una regola di firewall | Documentazione Microsoft
description: Questo script dell&quot;interfaccia della riga di comando di Azure di esempio crea un singolo database di Azure per il server MySQL e configura una regola di firewall a livello di server.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creare un server MySQL e configurare una regola di firewall tramite l'interfaccia della riga di comando di Azure
Questo script dell'interfaccia della riga di comando di Azure di esempio crea un singolo database di Azure per il server MySQL e configura una regola di firewall a livello di server. Una volta che lo script è stato eseguito correttamente, il server MySQL è accessibile da tutti i servizi di Azure e dall'indirizzo IP configurato.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Pulire la distribuzione
 Dopo l'esecuzione dello script di esempio, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse associate a esso.
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| **Comando** | **Note** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| az mysql server create | Crea un server MySQL che ospita i database. |
| az mysql server firewall create | Crea una regola di firewall per consentire l'accesso al server e ai database presenti sul server dall'intervallo di indirizzi IP immesso. |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
[Esempi dell'interfaccia della riga di comando di Azure per il database di Azure per MySQL](../sample-scripts-azure-cli.md)

