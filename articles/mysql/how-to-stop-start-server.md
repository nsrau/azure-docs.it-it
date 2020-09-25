---
title: Arrestare/avviare portale di Azure-database di Azure per il server MySQL
description: Questo articolo descrive come arrestare/avviare le operazioni nel database di Azure per MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: f09b6d48e8a98b0995c882769d6c978996324dad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343807"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Arrestare/avviare un database di Azure per MySQL

> [!IMPORTANT]
> La funzionalità di arresto/avvio per database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

In questo articolo viene fornita una procedura dettagliata per eseguire l'arresto e l'avvio del server flessibile.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server flessibile per database di Azure per MySQL.

> [!NOTE]
> Vedere la limitazione dell'uso di [Stop/Start](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Come arrestare/avviare il database di Azure per MySQL usando portale di Azure

### <a name="stop-a-running-server"></a>Arrestare un server in esecuzione

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server MySQL che si desidera arrestare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Arresta** sulla barra degli strumenti.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Server di arresto per database di Azure per MySQL":::

    > [!NOTE]
    > Una volta arrestato il server, le altre operazioni di gestione non sono disponibili per il server flessibile.

### <a name="start-a-stopped-server"></a>Avviare un server arrestato

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile che si desidera avviare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Avvia** sulla barra degli strumenti.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Server di avvio del database di Azure per MySQL":::

    > [!NOTE]
    > Una volta avviato il server, tutte le operazioni di gestione sono ora disponibili per il server flessibile.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Come arrestare/avviare il database di Azure per MySQL tramite l'interfaccia della riga di comando

### <a name="stop-a-running-server"></a>Arrestare un server in esecuzione

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server MySQL che si desidera arrestare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Arresta** sulla barra degli strumenti.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Una volta arrestato il server, le altre operazioni di gestione non sono disponibili per il server flessibile.

### <a name="start-a-stopped-server"></a>Avviare un server arrestato

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile che si desidera avviare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Avvia** sulla barra degli strumenti.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Una volta avviato il server, tutte le operazioni di gestione sono ora disponibili per il server flessibile.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
