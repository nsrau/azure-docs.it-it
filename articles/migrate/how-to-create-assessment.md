---
title: Creare una valutazione delle VM di Azure con Azure Migrate server Assessment | Microsoft Docs
description: Viene descritto come creare una valutazione delle VM di Azure con lo strumento di valutazione di Azure Migrate server
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 534619ace09b4e11934062a591adf8d9ab6f77ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500802"
---
# <a name="create-an-azure-vm-assessment"></a>Creare una valutazione per le macchine virtuali di Azure

Questo articolo descrive come creare una valutazione delle VM di Azure per macchine virtuali VMware locali o macchine virtuali Hyper-V con Azure Migrate: server assessment.

[Azure migrate](migrate-services-overview.md) consente di eseguire la migrazione ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali. L'hub fornisce gli strumenti di Azure per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti. 

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato](./create-manage-projects.md) un progetto Azure migrate.
- Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server assessment.
- Per creare una valutazione, è necessario configurare un'appliance Azure Migrate per [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment. [Altre informazioni](migrate-appliance.md)


## <a name="azure-vm-assessment-overview"></a>Panoramica della valutazione delle VM di Azure
Esistono due tipi di criteri di ridimensionamento che è possibile usare per creare una valutazione delle VM di Azure usando Azure Migrate: server assessment.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni VM consigliate**: in base ai dati sull'utilizzo di CPU e memoria.<br/><br/> **Tipo di disco consigliato (disco gestito Standard o Premium)** : in base alle operazioni di I/O al secondo e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni VM consigliate**: in base alle dimensioni delle VM locali<br/><br> **Tipo di disco consigliato**: in base all'impostazione del tipo di archiviazione selezionata per la valutazione.

[Altre informazioni](concepts-assessment-calculation.md) sulle valutazioni.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Rivedere le [procedure consigliate](best-practices-assessment.md) per la creazione di valutazioni.
2. Nella scheda **Server**, nel riquadro **Azure Migrate: Valutazione server**, fare clic su **Valuta**.

    ![Screenshot mostra i server Azure Migrate con valutazione selezionata in strumenti di valutazione.](./media/how-to-create-assessment/assess.png)

3. In **Valuta server** selezionare "Macchina virtuale di Azure" come tipo di valutazione, selezionare l'origine di individuazione e specificare il nome della valutazione.

    ![Informazioni di base sulla valutazione](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.

    ![Proprietà valutazione](./media/how-to-create-assessment//view-all.png)

5. Fare clic su **Avanti** per **selezionare i computer da valutare**. In **Selezionare o creare un gruppo** selezionare **Crea nuovo** e specificare un nome per il gruppo. Un gruppo raccoglie una o più VM per la valutazione.
6. In **Aggiungere le macchine virtuali al gruppo** selezionare le VM da aggiungere al gruppo.
7. Fare clic su **Avanti** per passare a **Rivedi e crea valutazione** e rivedere i dettagli della valutazione.
8. Fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

    ![Creare una valutazione](./media/how-to-create-assessment//assessment-create.png)

9. Dopo aver creato la valutazione, visualizzarla in **Server** > **Azure Migrate: Valutazione server** > **Valutazioni**.
10. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.



## <a name="review-an-azure-vm-assessment"></a>Rivedere una valutazione di macchine virtuali di Azure

Una valutazione di VM di Azure descrive:

- **Idoneità per Azure**: se le VM sono idonee per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione delle VM in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

### <a name="view-an-azure-vm-assessment"></a>Visualizzare una valutazione della VM di Azure

1. In **Obiettivi della migrazione** >  **Server** fare clic su **Valutazioni** in **Azure Migrate: Valutazione server**.
2. In **Valutazioni** fare clic su una valutazione per aprirla.

    ![Riepilogo della valutazione](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Esaminare l'idoneità per Azure

1. In **Idoneità per Azure** verificare se le VM sono pronte per la migrazione ad Azure.
2. Verificare lo stato delle VM:
    - **Idonea per Azure**: Azure Migrate consiglia le dimensioni e le stime dei costi per le VM nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: stato usato quando Azure Migrate non è in grado di valutare l'idoneità a causa di problemi di disponibilità dei dati.

3. Fare clic su uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità delle VM ed eseguire il drill-down per visualizzare i dettagli delle VM, incluse le impostazioni di calcolo, archiviazione e rete.



### <a name="review-cost-details"></a>Esaminare i dettagli dei costi

Questa visualizzazione mostra il costo stimato di calcolo e archiviazione associato all'esecuzione delle macchine virtuali in Azure.

1. Esaminare i costi mensili di calcolo e archiviazione. I costi sono aggregati per tutte le VM nel gruppo valutato.

    - Le stime dei costi sono basate sulle dimensioni consigliate per una VM e sui relativi dischi e proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi è relativa all'esecuzione delle VM locali come VM IaaS. Valutazione server di Azure Migrate non considera i costi per PaaS o SaaS.

2. È possibile esaminare le stime dei costi di archiviazione mensile. Questa visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione.
3. È possibile eseguire il drill-down per visualizzare i dettagli relativi a VM specifiche.


### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnata una classificazione di attendibilità.

![Classificazione di attendibilità](./media/how-to-create-assessment/confidence-rating.png)

- La classificazione è compresa tra 1 stella (minima) e 5 stelle (massima).
- La classificazione di attendibilità aiuta a stimare l'affidabilità delle indicazioni relative alle dimensioni fornite dalla valutazione.
- La classificazione di attendibilità è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.

Di seguito sono elencate le classificazioni di attendibilità per una valutazione.

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle




## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare il [mapping delle dipendenze](how-to-create-group-machine-dependencies.md) per creare gruppi con attendibilità elevata.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.