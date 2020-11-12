---
title: Riavviare il server portale di Azure-database di Azure per MySQL-server flessibile
description: Questo articolo descrive come riavviare un server flessibile di database di Azure per MySQL usando il portale di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 88a1524875f168b49f50f1684c650d5bc178bf38
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542661"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Riavviare il server flessibile del database di Azure per MySQL usando portale di Azure
Questo argomento descrive come riavviare un database di Azure per il server flessibile MySQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

Il tempo necessario per completare un riavvio dipende dal processo di ripristino di MySQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [server flessibile per database di Azure per MySQL](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Eseguire il riavvio del server

La procedura seguente consente di riavviare il server MySQL:

1. Nella portale di Azure selezionare il server di database di Azure per MySQL flessibile.

2. Nella barra degli strumenti della pagina **Panoramica** del server fare clic su **Riavvia**.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Database di Azure per MySQL: Panoramica - Pulsante Riavvia":::

3. Fare clic su **Sì** per confermare il riavvio del server.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Database di Azure per MySQL - Conferma di riavvio":::

4. Si noti che lo stato del server passa a "Riavvio in corso".

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Database di Azure per MySQL - Stato di riavvio":::

5. La conferma del riavvio del server ha esito positivo.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Database di Azure per MySQL - Riavvio riuscito":::

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva: creare un server flessibile per database di Azure per MySQL con portale di Azure](quickstart-create-server-portal.md)
