---
title: Restore-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive come eseguire operazioni di ripristino nel database di Azure per PostgreSQL tramite il portale di Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938865"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Ripristino temporizzato di un server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

In questo articolo viene fornita una procedura dettagliata per eseguire i recuperi temporizzati in server flessibili tramite backup. È possibile eseguire l'esecuzione in un punto di ripristino più recente o in un punto di ripristino personalizzato entro il periodo di memorizzazione.

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server di database di Azure per PostgreSQL-flexible. La stessa procedura è applicabile anche per il server flessibile configurato con la ridondanza della zona.

## <a name="restoring-to-the-earliest-restore-point"></a>Ripristino fino al punto di ripristino più recente

Attenersi alla procedura seguente per ripristinare il server flessibile utilizzando un backup esistente più recente.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile da cui si desidera ripristinare il backup.

2.  Fare clic su **Panoramica** nel pannello a sinistra e fare clic su **Ripristina** .
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Panoramica sul ripristino":::

3.  Verrà visualizzata la pagina Ripristina con un'opzione che consente di scegliere tra il primo punto di ripristino e il punto di ripristino personalizzato.

4.  Selezionare il **punto di ripristino meno recente** e specificare un nuovo nome del server nel campo Ripristina nel **nuovo server** . Viene visualizzato il timestamp meno recente in cui è possibile eseguire il ripristino. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="Panoramica sul ripristino":::

5.  Fare clic su **OK**.

6.  Verrà visualizzata una notifica che l'operazione di ripristino è stata avviata.

## <a name="restoring-to-a-custom-restore-point"></a>Ripristino a un punto di ripristino personalizzato

Attenersi alla procedura seguente per ripristinare il server flessibile utilizzando un backup esistente più recente.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile da cui si desidera ripristinare il backup.

2.  Nella pagina Panoramica fare clic su **Ripristina**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Panoramica sul ripristino":::
    
3.  Verrà visualizzata la pagina Ripristina con un'opzione che consente di scegliere tra il primo punto di ripristino e il punto di ripristino personalizzato.

4.  Scegliere **punto di ripristino personalizzato**.

5.  Selezionare data e ora e specificare un nuovo nome del server nel campo **Ripristina nel nuovo server** . 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Panoramica sul ripristino":::
 
6.  Fare clic su **OK**.

7.  Verrà visualizzata una notifica che l'operazione di ripristino è stata avviata.

## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
-   Informazioni su [backup e ripristino](./concepts-backup-restore.md)
