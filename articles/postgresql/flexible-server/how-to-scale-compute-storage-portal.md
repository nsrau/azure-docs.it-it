---
title: Operazioni di ridimensionamento-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive come eseguire operazioni di ridimensionamento nel database di Azure per PostgreSQL tramite il portale di Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939245"
---
# <a name="scale-operations-in-flexible-server"></a>Ridimensionare le operazioni nel server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questo articolo illustra la procedura per eseguire operazioni di ridimensionamento per il calcolo e l'archiviazione. Potrai modificare i livelli di calcolo tra gli SKU con espansione, utilizzo generico e con ottimizzazione per la memoria, inclusa la scelta del numero di Vcore adatto per l'esecuzione dell'applicazione. È anche possibile aumentare il livello di archiviazione. Gli IOPS previsti vengono visualizzati in base al livello di calcolo, Vcore e alla capacità di archiviazione. La stima dei costi viene inoltre mostrata in base alla selezione effettuata.

> [!IMPORTANT]
> Non è possibile ridurre il livello di archiviazione.

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server di database di Azure per PostgreSQL-flexible. La stessa procedura è applicabile anche per il server flessibile configurato con la ridondanza della zona.
> [!IMPORTANT]
> Se configurata con la disponibilità elevata, non è possibile scegliere lo SKU a scaricabile. Durante l'operazione di ridimensionamento, la modalità standby viene innanzitutto ridimensionata in base alle dimensioni desiderate, viene eseguito il failover del server primario e il database primario viene ridimensionato. 

## <a name="scaling-the-compute-tier-and-size"></a>Ridimensionamento del livello di calcolo e delle dimensioni

Per scegliere il livello di calcolo, seguire questa procedura.
 
1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile da cui si desidera ripristinare il backup.

2.  Fare clic su **calcolo e archiviazione**.

3.  Viene visualizzata una pagina con le impostazioni correnti.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="calcolo + visualizzazione archiviazione":::

4.  È possibile scegliere la classe di calcolo tra i livelli di espansione, utilizzo generico e con ottimizzazione per la memoria.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="calcolo + visualizzazione archiviazione":::


5.  Se si è bravi con le dimensioni predefinite di Vcore e memoria, è possibile ignorare il passaggio successivo.

6.  Se si vuole modificare il numero di Vcore, è possibile fare clic sull'elenco a discesa delle **dimensioni di calcolo** e fare clic sul numero desiderato di Vcore/memoria dall'elenco.
    
    - Livello di calcolo a espansione: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="calcolo + visualizzazione archiviazione":::

    - Livello di calcolo per utilizzo generico: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="calcolo + visualizzazione archiviazione":::

    - Livello di calcolo con ottimizzazione per la memoria: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="calcolo + visualizzazione archiviazione"::: per la memoria

7.  Fare clic su **Save**. 
8.  Verrà visualizzato un messaggio di conferma. Se si desidera continuare, fare clic su **OK** . 
9.  Notifica sull'operazione di ridimensionamento in corso.


## <a name="scaling-storage-size"></a>Ridimensionamento delle dimensioni di archiviazione

Per aumentare le dimensioni di archiviazione, seguire questa procedura.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile per il quale si desidera aumentare le dimensioni di archiviazione.
2.  Fare clic su **calcolo e archiviazione**.

3.  Viene visualizzata una pagina con le impostazioni correnti.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="calcolo + visualizzazione archiviazione":::
4.  Le **dimensioni di archiviazione dei campi in Gib** con una barra di scorrimento vengono visualizzate con le dimensioni correnti.

5.  Far scorrere la barra fino alle dimensioni desiderate. Viene visualizzato il numero IOPS corrispondente. Gli IOPS dipendono dal livello di calcolo e dalle dimensioni. Vengono visualizzate anche le informazioni sui costi. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="calcolo + visualizzazione archiviazione":::

6.  Se le dimensioni di archiviazione sono valide, fare clic su **Salva**. 
7.  Verrà visualizzato un messaggio di conferma. Se si desidera continuare, fare clic su **OK** . 
8.  Notifica sull'operazione di ridimensionamento in corso.

## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata](./concepts-high-availability.md)
-   Informazioni su [backup e ripristino](./concepts-backup-restore.md)
