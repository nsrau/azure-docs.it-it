---
title: Riavviare il server portale di Azure-database di Azure per MariaDB
description: Questo articolo illustra come riavviare un server di Database di Azure per MariaDB usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb4e56a3f40573a65b679ee026c22dfc5d6e5fa2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769422"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Riavviare un server di Database di Azure per MariaDB usando il portale di Azure
Questo argomento descrive come riavviare un server di Database di Azure per MariaDB. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

Il tempo necessario per completare un riavvio dipende dal processo di ripristino di MariaDB. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

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

[Guida introduttiva: creare database di Azure per il server MariaDB usando portale di Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)