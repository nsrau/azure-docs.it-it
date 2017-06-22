---
title: Esempi dell&quot;interfaccia della riga di comando di Azure per scalare un database di Azure per il server MySQL | Documentazione Microsoft
description: Questo esempio di script dell&quot;interfaccia della riga di comando di Azure scala un database di Azure per il MySQL a un diverso livello di prestazioni dopo le query sulle metriche.
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
ms.openlocfilehash: c54262218e6174dc85905cb067c334cc6a401946
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Monitorare a scalare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure
Questo esempio di script dell'interfaccia della riga di comando di Azure scala un singolo database di Azure per il server MySQL a un diverso livello di prestazioni dopo le query sulle metriche.

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

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az mysql server update \
--resource-group myresource \
--name mysqlserver4demo \
--compute-units 100
```

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.
```azurecli
az group delete --name myresource
```

## <a name="next-steps"></a>Passaggi successivi
[Esempi dell'interfaccia della riga di comando di Azure per il database di Azure per MySQL](../sample-scripts-azure-cli.md)

