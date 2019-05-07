---
title: Riavviare un server di Database di Azure per MariaDB usando il portale di Azure
description: Questo articolo illustra come riavviare un server di Database di Azure per MariaDB usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 232037562c4a84ee9217e2e89a0da2ffdc37d560
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "60745791"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Riavviare un server di Database di Azure per MariaDB usando il portale di Azure
Questo argomento descrive come riavviare un server di Database di Azure per MariaDB. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

Il tempo necessario per completare un riavvio dipende dal processo di ripristino di MariaDB. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [Database di Azure per il server di MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Eseguire il riavvio del server

La procedura seguente consente di riavviare il server MariaDB:

1. Nel portale di Azure, selezionare il server Database di Azure per MariaDB.

2. Nella barra degli strumenti della pagina **Panoramica** del server fare clic su **Riavvia**.

   ![Database di Azure per MariaDB: Panoramica - Pulsante di riavvio](./media/howto-restart-server-portal/2-server.png)

3. Fare clic su **Sì** per confermare il riavvio del server.

   ![Database di Azure per MariaDB - Conferma di riavvio](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Si noti che lo stato del server passa a "Riavvio in corso".

   ![Database di Azure per MariaDB - Stato di riavvio](./media/howto-restart-server-portal/4-restarting-status.png)

5. La conferma del riavvio del server ha esito positivo.

   ![Database di Azure per MariaDB - Riavvio riuscito](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva: Creare un server di Database di Azure per MariaDB usando il portale di Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)