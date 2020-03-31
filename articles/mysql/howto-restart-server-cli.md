---
title: Riavviare il server - interfaccia della riga di comando di Azure - Database di Azure per MySQLRestart server - Azure CLI - Azure Database for MySQL
description: Questo articolo descrive come è possibile riavviare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure.This article describes how you can restart an Azure Database for MySQL server using the Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c1fd688fbfd892e3d0dfc3ebb1712dd931e0ed39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063506"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Riavviare il database di Azure per il server MySQL usando l'interfaccia della riga di comando di AzureRestart Azure Database for MySQL server using the Azure CLI
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per MySQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

Il tempo necessario per completare un riavvio dipende dal processo di ripristino di MySQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MySQLAn Azure Database for MySQL server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Riavviare il server

Riavviare il server con il seguente comando:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come impostare i parametri nel database di [Azure per MySQLLearn about how to set parameters in Azure Database for MySQL](howto-configure-server-parameters-using-cli.md)