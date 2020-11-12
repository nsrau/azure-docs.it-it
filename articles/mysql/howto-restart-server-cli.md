---
title: Riavviare il server-interfaccia della riga di comando di Azure-database di Azure per MySQL
description: Questo articolo descrive come riavviare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77f20bba76ee3a37e6a72481e0b3d13c9904b106
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541828"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Riavviare database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per MySQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

Il tempo necessario per completare un riavvio dipende dal processo di ripristino di MySQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- È necessario un [database di Azure per il server MySQL](quickstart-create-server-up-azure-cli.md).
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede la versione 2,0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="restart-the-server"></a>Riavviare il server.

Riavviare il server con il comando seguente:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come impostare i parametri nel database di Azure per MySQL](howto-configure-server-parameters-using-cli.md)
