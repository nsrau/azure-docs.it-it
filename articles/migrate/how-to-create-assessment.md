---
title: Creare una valutazione con Azure Migrate server Assessment | Microsoft Docs
description: Viene descritto come creare una valutazione con lo strumento di valutazione di Azure Migrate server
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229102"
---
# <a name="create-an-assessment"></a>Creare una valutazione

Questo articolo descrive come creare una valutazione per macchine virtuali VMware locali o macchine virtuali Hyper-V con Azure Migrate: Valutazione del server.

[Azure migrate](migrate-services-overview.md) consente di eseguire la migrazione ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione dell'infrastruttura, delle applicazioni e dei dati locali in Azure. L'hub fornisce strumenti di Azure per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti. 

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato](how-to-add-tool-first-time.md) un progetto Azure migrate.
- Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) la Azure migrate: Strumento di valutazione server.
- Per creare una valutazione, è necessario configurare un'appliance Azure Migrate per [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: Valutazione del server. [Altre informazioni](migrate-appliance.md)


## <a name="assessment-overview"></a>Panoramica della valutazione
Esistono due tipi di valutazione che è possibile creare usando Azure Migrate: Valutazione del server.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basato sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni macchina virtuale consigliate**: In base ai dati di utilizzo della CPU e della memoria.<br/><br/> **Tipo di disco consigliato (disco gestito standard o Premium)** : In base ai IOPS e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni macchina virtuale consigliate**: In base alle dimensioni della macchina virtuale locale<br/><br> **Tipo di disco consigliato**: In base all'impostazione del tipo di archiviazione selezionata per la valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione come segue:

1. Esaminare le [procedure consigliate](best-practices-assessment.md) per la creazione di valutazioni.
2. Nella scheda **Server** , in **Azure migrate: Riquadro Server** Assessment, fare clic su **valuta**.

    ![Valutare](./media/how-to-create-assessment/assess.png)

2. In **valuta Server**specificare un nome per la valutazione.
3. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.

    ![Proprietà valutazione](./media/how-to-create-assessment//view-all.png)

3. In **selezionare o creare un gruppo**selezionare **Crea nuovo**e specificare un nome per il gruppo. Un gruppo raccoglie una o più macchine virtuali insieme per la valutazione.
4. In **Aggiungi computer al gruppo**selezionare le macchine virtuali da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

    ![Creare una valutazione](./media/how-to-create-assessment//assessment-create.png)

6. Una volta creata la valutazione, visualizzarla nei **Server** > **Azure migrate: **Valutazioni**della**valutazione > del server.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.



## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Conformità di Azure**: Indica se le macchine virtuali sono adatte per la migrazione ad Azure.
- **Stima dei costi mensili**: I costi di calcolo e archiviazione mensili stimati per l'esecuzione delle macchine virtuali in Azure.
- **Stima dei costi di archiviazione mensile**: Costi stimati per l'archiviazione su disco dopo la migrazione.

### <a name="view-an-assessment"></a>Visualizza una valutazione

1. In **Server** degli **obiettivi** >  **di migrazione fare clic su  **valutazioni**in Azure migrate: Valutazione**del server.
2. In **valutazioni**fare clic su una valutazione per aprirla.

    ![Riepilogo valutazione](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Verificare la conformità di Azure

1. In **Azure conformità**verificare se le macchine virtuali sono pronte per la migrazione ad Azure.
2. Verificare lo stato della macchina virtuale:
    - **Pronto per Azure**: Azure Migrate consiglia le dimensioni della macchina virtuale e le stime dei costi per le macchine virtuali nella valutazione.
    - **Pronto con condizioni**: Mostra i problemi e la correzione consigliata.
    - **Non pronto per Azure**: Mostra i problemi e la correzione consigliata.
    - **Conformità sconosciuta**: Utilizzato quando Azure Migrate non è in grado di valutare la conformità a causa di problemi di disponibilità dei dati.

2. Fare clic su uno stato di **conformità di Azure** . È possibile visualizzare i dettagli di conformità della macchina virtuale ed eseguire il drill-down per visualizzare i dettagli della VM, incluse le impostazioni di calcolo, archiviazione e rete.



### <a name="review-cost-details"></a>Dettagli sui costi della verifica

Questa visualizzazione Mostra il costo di calcolo e di archiviazione stimato per l'esecuzione di macchine virtuali in Azure.

1. Esaminare i costi di calcolo e archiviazione mensili. I costi vengono aggregati per tutte le macchine virtuali del gruppo valutato.

    - Le stime dei costi si basano sulle raccomandazioni relative alle dimensioni per un computer e sui relativi dischi e proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi prevede l'esecuzione delle macchine virtuali locali come VM IaaS. Azure Migrate server Assessment non considera i costi PaaS o SaaS.

2. È possibile esaminare le stime dei costi di archiviazione mensili. Questa visualizzazione Mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi su diversi tipi di dischi di archiviazione.
3. È possibile eseguire il drill-down per visualizzare i dettagli di macchine virtuali specifiche.


### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnata una classificazione di attendibilità.

![Classificazione di attendibilità](./media/how-to-create-assessment/confidence-rating.png)

- Viene assegnata una classificazione da 1 stella (più bassa) a 5 stelle (più alta).
- La classificazione di attendibilità consente di stimare l'affidabilità delle indicazioni relative alla dimensione fornite dalla valutazione.
- La classificazione di attendibilità è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.

Le classificazioni di attendibilità per una valutazione sono le seguenti.

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle




## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare il [mapping](how-to-create-group-machine-dependencies.md) delle dipendenze per creare gruppi con attendibilità elevata.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
