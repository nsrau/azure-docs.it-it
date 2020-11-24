---
title: Aggiungere strumenti di migrazione in Azure Migrate
description: Informazioni su come aggiungere strumenti di migrazione in Azure Migrate.
ms.topic: article
ms.date: 11/23/2020
ms.openlocfilehash: 5ff5fb54e077896fb6169ad53ce29483cd2c2f89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545335"
---
# <a name="add-migration-tools"></a>Aggiungere strumenti di migrazione

Questo articolo descrive come aggiungere strumenti di migrazione in [Azure migrate](./migrate-services-overview.md).

- Se si vuole aggiungere uno strumento di migrazione e non è stato ancora configurato un progetto di Azure Migrate, seguire questo [articolo](create-manage-projects.md).
- Se è stato aggiunto uno strumento ISV per la migrazione, [attenersi alla procedura](prepare-isv-movere.md)per prepararsi a usare lo strumento.

## <a name="select-a-migration-scenario"></a>Selezionare uno scenario di migrazione

1. Nel progetto di Azure Migrate fare clic su **Panoramica**.
2. Selezionare lo scenario di migrazione che si vuole usare:

    - Per eseguire la migrazione di computer e carichi di lavoro in Azure, selezionare **valuta ed Esegui la migrazione dei server**.
    - Per eseguire la migrazione dei database locali, selezionare **valuta ed Esegui la migrazione dei database**.
    - Per eseguire la migrazione di app Web locali, selezionare **Esplora altre**  >  **app Web**.
    - Per eseguire la migrazione dei dati in Azure con la casella dati, selezionare la casella **Esplora altri**  >  **dati**.

    ![Opzioni per la selezione di uno scenario di migrazione](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Selezionare uno strumento di migrazione server

1. Aggiungere uno strumento:

    - Se è stato creato un progetto Azure Migrate usando l'opzione **valuta e Esegui migrazione server** nel portale, lo strumento di migrazione Azure migrate server viene aggiunto automaticamente al progetto. Per aggiungere altri strumenti di migrazione, in **Server**, accanto a **strumenti di migrazione**, selezionare **Aggiungi altri strumenti**.
    
         ![Pulsante per aggiungere altri strumenti di migrazione](./media/how-to-migrate/add-migration-tools.png)

    - Se è stato creato un progetto con un'opzione diversa e non sono ancora disponibili strumenti di migrazione, **Servers** in  >  **strumenti di migrazione** server selezionare **fare clic qui**.

    ![Pulsante per aggiungere primi strumenti di migrazione](./media/how-to-migrate/no-migration-tool.png)

2. In **Azure migrate**  >  **Aggiungi strumenti** selezionare gli strumenti che si desidera aggiungere. Quindi selezionare **Aggiungi strumento**.

    ![Selezionare gli strumenti di valutazione nell'elenco](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Selezionare uno strumento di migrazione del database

Se è stato creato un progetto Azure Migrate usando l'opzione **valuta e migra database** nel portale, lo strumento di migrazione del database viene aggiunto automaticamente al progetto. 

1. Se lo strumento di migrazione del database non è presente nel progetto, in **database**  >  **Assessment Tools** selezionare **fare clic qui**.
    
    ![Aggiungere strumenti di migrazione del database](./media/how-to-migrate/no-database-migration-tool.png)


2. In **Azure migrate**  >  **Aggiungi strumenti** selezionare lo strumento di migrazione del database. Quindi selezionare **Aggiungi strumento**.

    ![Selezionare lo strumento di migrazione del database dall'elenco](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Selezionare uno strumento di migrazione di app Web

Se è stato creato un progetto Azure migrate usando l'opzione **Esplora altre**  >  **app** Web nel portale, lo strumento di migrazione delle app Web viene aggiunto automaticamente al progetto. 

1. Se lo strumento di migrazione di app Web non è incluso nel progetto, in strumenti di valutazione di app **Web**  >  **Assessment tools** selezionare **fare clic qui**.

    ![Aggiungere strumenti di migrazione delle app Web](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. In **Azure migrate**  >  **Aggiungi strumenti** selezionare lo strumento Migrazione App Web. Quindi selezionare **Aggiungi strumento**.

    ![Selezionare gli strumenti di valutazione di webapp dall'elenco](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Ordinare un Azure Data Box

Per eseguire la migrazione di grandi quantità di dati in Azure, è possibile ordinare un Azure Data Box per il trasferimento di dati offline.

1. In **Panoramica** selezionare **Esplora altro**.
2. In **Esplora altro** selezionare **Data Box**.
3. In **Introduzione a data box** selezionare la sottoscrizione e il gruppo di risorse che si vuole usare per l'ordinamento di un data box.
4. Il **tipo di trasferimento** è un'importazione in Azure. Specificare il paese in cui risiedono i dati e l'area di Azure in cui si desidera trasferire i dati. 
5. Fare clic su **applica** per salvare le impostazioni.

## <a name="next-steps"></a>Passaggi successivi

Provare a eseguire una migrazione usando lo strumento di migrazione Azure Migrate server per le macchine virtuali [Hyper-V](tutorial-migrate-hyper-v.md) o [VMware](tutorial-migrate-vmware.md) .
