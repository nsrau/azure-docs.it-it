---
title: Riavviare il server-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come riavviare un database di Azure per PostgreSQL-singolo server usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: d25c8908b46608ac64bcf135404a070740413fe0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907381"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Riavviare database di Azure per PostgreSQL: singolo server usando il portale di Azure
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per PostgreSQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.
 
Il tempo necessario per completare un riavvio dipende dal processo di ripristino di PostgreSQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- [Database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Eseguire il riavvio del server

La procedura seguente consente di riavviare il server PostgreSQL:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server PostgreSQL.

2. Nella barra degli strumenti della pagina **Panoramica** del server fare clic su **Riavvia**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Database di Azure per PostgreSQL - Panoramica - Pulsante Riavvia":::

3. Fare clic su **Sì** per confermare il riavvio del server.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Database di Azure per PostgreSQL - Conferma di riavvio":::

4. Si noti che lo stato del server passa a "Riavvio in corso".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Database di Azure per PostgreSQL - Stato di riavvio":::

5. La conferma del riavvio del server ha esito positivo.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Database di Azure per PostgreSQL - Riavvio riuscito":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come impostare i parametri nel database di Azure per PostgreSQL](howto-configure-server-parameters-using-portal.md)