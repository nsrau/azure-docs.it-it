---
title: Riavviare un server di Database di Azure per PostgreSQL usando il portale di Azure
description: Questo articolo descrive come è possibile riavviare un Database di Azure per il server PostgreSQL tramite il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: bf73120e462b740de5d2245f8a647896ac61f2c8
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621825"
---
# <a name="restart-azure-database-for-postgresql-server-using-the-azure-portal"></a>Riavviare il Database di Azure per il server PostgreSQL tramite il portale di Azure
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per PostgreSQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.
 
Il tempo necessario per completare un riavvio dipende dal processo di ripristino di PostgreSQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- [Database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Eseguire il riavvio del server

La procedura seguente consente di riavviare il server PostgreSQL:

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il Database di Azure per il server PostgreSQL.

2. Nella barra degli strumenti della pagina **Panoramica** del server fare clic su **Riavvia**.

   ![Database di Azure per PostgreSQL - Panoramica - Pulsante Riavvia](./media/howto-restart-server-portal/2-server.png)

3. Fare clic su **Sì** per confermare il riavvio del server.

   ![Database di Azure per PostgreSQL - Conferma di riavvio](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Si noti che lo stato del server passa a "Riavvio in corso".

   ![Database di Azure per PostgreSQL - Stato di riavvio](./media/howto-restart-server-portal/4-restarting-status.png)

5. La conferma del riavvio del server ha esito positivo.

   ![Database di Azure per PostgreSQL - Riavvio riuscito](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come impostare i parametri nel Database di Azure per PostgreSQL](howto-configure-server-parameters-using-portal.md)