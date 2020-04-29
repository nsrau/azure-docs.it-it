---
title: Riavviare il server-interfaccia della riga di comando di Azure-database di Azure per MariaDB
description: Questo articolo descrive come riavviare un database di Azure per il server MariaDB usando l'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f5572611b99245fd62b4e0a9d73e6ed728e42f5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530649"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Riavviare il database di Azure per il server MariaDB usando l'interfaccia della riga di comando
Questo argomento descrive come riavviare un server di Database di Azure per MariaDB. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

Il tempo necessario per completare un riavvio dipende dal processo di ripristino di MariaDB. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Riavviare il server

Riavviare il server con il comando seguente:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come impostare i parametri nel database di Azure per MariaDB](howto-configure-server-parameters-cli.md)