---
title: "Guida introduttiva: Configurare l'isolamento dei carichi di lavoro - Portale"
description: Usare il portale di Azure per configurare l'isolamento dei carichi di lavoro.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 70ac4942c397e8ca5db2d1b5041d0d9d43ae7222
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82797291"
---
# <a name="quickstart-configure-synapse-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Guida introduttiva: Configurare l'isolamento del carico di lavoro del pool Synapse SQL usando un gruppo di carico di lavoro nel portale di Azure

Questa guida di avvio rapido descrive come configurare l'[isolamento dei carichi di lavoro](sql-data-warehouse-workload-isolation.md) creando un gruppo di carico di lavoro per prenotare le risorse.  Ai fini di questa esercitazione, verrà creato il gruppo di carico di lavoro per il caricamento dei dati denominato `DataLoads`. Il gruppo di carico di lavoro prenoterà il 20% delle risorse del sistema.  Con un isolamento del 20% per il caricamento dei dati, si tratta di risorse garantite per soddisfare i contratti di servizio.  Dopo aver creato il gruppo di carico di lavoro, [creare un classificatore del carico di lavoro](quickstart-create-a-workload-classifier-portal.md) per assegnare query a questo gruppo di carico di lavoro.


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.


## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

> [!NOTE]
> La creazione di un'istanza del pool SQL in Azure Synapse Analytics può tradursi in un nuovo servizio fatturabile.  Per altre informazioni, vedere [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Prerequisiti

Questa guida di avvio rapido presuppone che l'utente abbia già un'istanza del pool SQL in Synapse SQL con autorizzazioni CONTROL DATABASE. Se è necessario crearne uno, fare riferimento a [Creare e connettere - portale](create-data-warehouse-portal.md) per creare un data warehouse denominato **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Per configurare la gestione del carico di lavoro, il pool SQL deve essere online. 

## <a name="configure-workload-isolation"></a>Configurare l'isolamento dei carichi di lavoro
Le risorse del pool SQL possono essere isolate e riservate per carichi di lavoro specifici tramite la creazione gruppi di carico di lavoro.  Per informazioni dettagliate su come gestire il carico di lavoro tramite i gruppi di carico di lavoro, vedere la documentazione relativa ai concetti di [isolamento dei carichi di lavoro](sql-data-warehouse-workload-isolation.md).  Nella guida di avvio rapido [Creare e connettere - Portale](create-data-warehouse-portal.md) è stato creato il data warehouse **mySampleDataWarehouse** ed è stato inizializzato con 400 unità Data Warehouse. Nella procedura seguente viene creato un gruppo di carico di lavoro in **mySampleDataWarehouse**.

Per creare un gruppo di carico di lavoro con un isolamento del 20%:
1.  Fare clic su **Azure Synapse Analytics (in precedenza SQL DW)** nella pagina sinistra del portale di Azure.
2.  Selezionare **mySampleDataWarehouse** nella pagina **Azure Synapse Analytics (in precedenza SQL DW)** . Verrà aperto il pool SQL.
3.  Fare clic su **Gestione del carico di lavoro**.
4.  Fare clic su **Nuovo gruppo di carico di lavoro**.
5.  Fare clic su **Personalizzato**.

    ![Fare clic su personalizzato](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Immettere `DataLoads` per **Gruppo di carico di lavoro**.
7.  Immettere `20` per **% risorse minima**.
8.  Immettere `5` per **Percentuale di risorse minima per richiesta**.
9.  Immettere `100` per **% massima di risorse**.
10.   Fare clic su **Salva**.

   ![Fare clic su Salva.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Verrà visualizzata una notifica del portale non appena viene completata la creazione del gruppo di carico di lavoro.  Le risorse del gruppo di carico di lavoro vengono visualizzate nei grafici sotto i valori configurati.

   ![Fare clic su Fine](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare il gruppo di carico di lavoro `DataLoads` creato in questa esercitazione:
1. Fare clic su **`...`** a destra del gruppo di carico di lavoro `DataLoads`.
2. Fare clic su **Elimina gruppo di carico di lavoro**.
3. Fare clic su **Sì** quando viene richiesto di confermare l'eliminazione del gruppo di carico di lavoro.
4. Fare clic su **Save**.

   ![Fare clic su Elimina](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Per le unità del data warehouse e i dati archiviati vengono addebitati costi. Le risorse di calcolo e archiviazione vengono fatturate separatamente.

- Se si vogliono mantenere i dati nelle risorse di archiviazione, è possibile sospendere il calcolo quando il data warehouse non è in uso. In questo modo, vengono addebitati solo i costi per l'archiviazione dei dati. Quando si è pronti a lavorare con i dati, riprendere il calcolo.
- Per evitare di ricevere addebiti in futuro, è possibile eliminare il data warehouse.

Seguire questa procedura per pulire le risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il proprio data warehouse.

    ![Pulire le risorse](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Per sospendere il calcolo, selezionare il pulsante **Pausa**. Quando si sospende il data warehouse, viene visualizzato il pulsante **Avvia**.  Per riprendere il calcolo, selezionare **Avvia**.

3. Per rimuovere il data warehouse in modo da non ricevere addebiti per operazioni di calcolo o archiviazione, selezionare **Elimina**.

4. Per rimuovere il server SQL creato, selezionare **sqlpoolservername.database.windows.net** nell'immagine precedente e quindi selezionare **Elimina**.  Fare attenzione quando si esegue questa operazione perché l'eliminazione del server comporta anche quella di tutti i database assegnati al server.

5. Per rimuovere il gruppo di risorse, selezionare **myResourceGroup** e quindi **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Per usare il gruppo di carico di lavoro `DataLoads`, è necessario creare un [classificatore del carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per instradare le richieste al gruppo di carico di lavoro.  Procedere all'esercitazione [Creare un classificatore del carico di lavoro](quickstart-create-a-workload-classifier-portal.md) per creare un classificatore del carico di lavoro per `DataLoads`.

## <a name="see-also"></a>Vedere anche
Per informazioni dettagliate su come monitorare i carichi di lavoro per la gestione dei carichi di lavoro, vedere l'articolo [Gestire e monitorare Gestione del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
