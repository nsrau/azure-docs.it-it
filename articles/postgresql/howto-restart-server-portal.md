---
title: Riavviare un server di Database di Azure per PostgreSQL usando il portale di Azure
description: In questo articolo viene descritta la procedura per riavviare un server di Database di Azure per PostgreSQL usando il portale di Azure.
services: postgresql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: 9a60f46f71a3d8e6f6d218c9a0ebd3194b6ab39f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52166872"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Riavviare un server di Database di Azure per PostgreSQL usando il portale di Azure
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per PostgreSQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.
 
Il tempo necessario per completare un riavvio dipende dal processo di ripristino di PostgreSQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [server e un database di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Eseguire il riavvio del server

La procedura seguente consente di riavviare il server PostgreSQL:

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL.

2. Nella barra degli strumenti della pagina **Panoramica** del server fare clic su **Riavvia**.

   ![Database di Azure per PostgreSQL - Panoramica - Pulsante Riavvia](./media/howto-restart-server-portal/2-server.png)

3. Fare clic su **Sì** per confermare il riavvio del server.

   ![Database di Azure per PostgreSQL - Conferma di riavvio ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Si noti che lo stato del server passa a "Riavvio in corso".

   ![Database di Azure per PostgreSQL - Stato di riavvio ](./media/howto-restart-server-portal/4-restarting-status.png)

5. La conferma del riavvio del server ha esito positivo.

   ![Database di Azure per PostgreSQL - Riavvio riuscito ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido: Creare un server di Database di Azure per PostgreSQL usando il portale di Azure](./quickstart-create-server-database-portal.md)