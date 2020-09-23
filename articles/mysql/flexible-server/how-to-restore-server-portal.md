---
title: Restore-portale di Azure-database di Azure per MySQL-server flessibile
description: Questo articolo descrive come eseguire operazioni di ripristino nel database di Azure per MySQL tramite il portale di Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 1c81ddad8a11cbad361ff84caf6f7200a0c010d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937291"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Ripristino temporizzato di un database di Azure per MySQL-server flessibile (anteprima)


> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

In questo articolo viene fornita una procedura dettagliata per eseguire i recuperi temporizzati in server flessibili tramite backup.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server flessibile per database di Azure per MySQL.

## <a name="restore-to-the-latest-restore-point"></a>Ripristina fino al punto di ripristino più recente

Attenersi alla procedura seguente per ripristinare il server flessibile utilizzando un backup esistente più recente.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile da cui si desidera ripristinare il backup.

2.  Fare clic su **Panoramica** nel pannello di sinistra.

3.  Nella pagina Panoramica fare clic su **Ripristina**.

    Segnaposto

4.  Verrà visualizzata la pagina Ripristina con un'opzione che consente di scegliere tra il **punto di ripristino più recente** e il punto di ripristino personalizzato.

5.  Selezionare il **punto di ripristino più recente**.


6.  Consente di specificare un nuovo nome del server nel campo **Ripristina nel nuovo server** .

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Tempo di ripristino più recente":::

8.  Fare clic su **OK**.

9.  Verrà visualizzata una notifica che l'operazione di ripristino è stata avviata.

## <a name="restoring-to-a-custom-restore-point"></a>Ripristino a un punto di ripristino personalizzato

Attenersi alla procedura seguente per ripristinare il server flessibile utilizzando un backup esistente più recente.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile da cui si desidera ripristinare il backup.

2.  Nella pagina Panoramica fare clic su **Ripristina**.

    Segnaposto

3.  Verrà visualizzata la pagina Ripristina con un'opzione che consente di scegliere tra il primo punto di ripristino e il punto di ripristino personalizzato.

4.  Scegliere **punto di ripristino personalizzato**.

5.  Selezionare data e ora.

6.  Consente di specificare un nuovo nome del server nel campo **Ripristina nel nuovo server** .

6.  Consente di specificare un nuovo nome del server nel campo **Ripristina nel nuovo server** . 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="Visualizza panoramica":::
 
7.  Fare clic su **OK**.

8.  Verrà visualizzata una notifica che l'operazione di ripristino è stata avviata.

## <a name="next-steps"></a>Passaggi successivi

Segnaposto
