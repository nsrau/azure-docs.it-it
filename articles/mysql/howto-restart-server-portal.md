---
title: Riavviare il server portale di Azure-database di Azure per MySQL
description: Questo articolo descrive come riavviare un database di Azure per il server MySQL usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: d885cc64eeebd4873ad5993b39b48845d1365c23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902751"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Riavviare un server di Database di Azure per MySQL usando il portale di Azure
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per MySQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

Il tempo necessario per completare un riavvio dipende dal processo di ripristino di MySQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Eseguire il riavvio del server

La procedura seguente consente di riavviare il server MySQL:

1. Nel portale di Azure selezionare il server del Database di Azure per MySQL.

2. Nella barra degli strumenti della pagina **Panoramica** del server fare clic su **Riavvia**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Database di Azure per MySQL: Panoramica - Pulsante Riavvia":::

3. Fare clic su **Sì** per confermare il riavvio del server.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Database di Azure per MySQL: Panoramica - Pulsante Riavvia":::

4. Si noti che lo stato del server passa a "Riavvio in corso".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Database di Azure per MySQL: Panoramica - Pulsante Riavvia":::

5. La conferma del riavvio del server ha esito positivo.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Database di Azure per MySQL: Panoramica - Pulsante Riavvia":::

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva: Creare Database di Azure per il server MySQL con il portale di Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
