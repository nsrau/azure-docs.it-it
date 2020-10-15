---
title: Valutare i server fisici per la migrazione ad Azure con Valutazione server di Azure Migrate
description: Questo articolo descrive come valutare i server fisici locali per la migrazione ad Azure usando Valutazione server di Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 3669658100681d08e754c19377b82faff5bce1ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090450"
---
# <a name="tutorial-assess-physical-servers-for-migration-to-azure"></a>Esercitazione: Valutare i server fisici per la migrazione ad Azure

Il percorso di migrazione ad Azure prevede la valutazione dei carichi di lavoro locali per misurare l'idoneità del cloud, identificare i rischi e stimare costi e complessità.

Questo articolo descrive come valutare i server fisici locali per la migrazione ad Azure con lo strumento Azure Migrate: Valutazione server.


In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
- Eseguire una valutazione in base alle informazioni relative a metadati e configurazioni delle macchine virtuali.
- Eseguire una valutazione in base ai dati sulle prestazioni.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

- Prima di seguire questa esercitazione per valutare le macchine virtuali per la migrazione alle macchine virtuali di Azure, assicurarsi di aver individuato le macchine virtuali che si intende valutare:
    - Per individuare le macchine virtuali usando l'appliance di Azure Migrate, [seguire questa esercitazione](tutorial-discover-physical.md). 
    - Per individuare le macchine virtuali usando un file CSV importato, [seguire questa esercitazione](tutorial-discover-import.md).
- Assicurarsi che nei computer fisici che si vogliono valutare non sia in esecuzione Windows Server 2003 o SUSE Linux. La valutazione non è supportata per queste macchine virtuali.


## <a name="decide-which-assessment-to-run"></a>Decidere la valutazione da eseguire


Decidere se si vuole eseguire una valutazione usando criteri di dimensionamento in base a dati/metadati di configurazione delle macchine virtuali raccolti in locale o a dati dinamici relativi alle prestazioni.

**Valutazione** | **Dettagli** | **Consiglio**
--- | --- | ---
**Come in locale** | Eseguire la valutazione in base ai dati/metadati di configurazione delle macchine virtuali.  | Le dimensioni consigliate per le macchine virtuali di Azure sono basate sulle dimensioni delle macchine virtuali locali.<br/><br> Il tipo di disco di Azure consigliato è basato sull'opzione selezionata nella valutazione per l'impostazione del tipo di archiviazione.
**Basata sulle prestazioni** | Eseguire la valutazione in base ai dati dinamici relativi alle prestazioni. | Le dimensioni consigliate per le macchine virtuali di Azure sono basate sui dati di utilizzo della CPU e della memoria.<br/><br/> Il tipo di disco consigliato è basato sulle operazioni di I/O al secondo e sulla velocità effettiva dei dischi locali.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Nella pagina **Server** selezionare **Server Windows e Linux** e fare clic su **Valutare ed eseguire la migrazione dei server**.

   ![Posizione del pulsante Valutare ed eseguire la migrazione dei server](./media/tutorial-assess-physical/assess.png)

2. In **Azure Migrate: Valutazione server** fare clic su **Valuta**.

    ![Posizione del pulsante Valuta](./media/tutorial-assess-physical/assess-servers.png)

3. In **Valuta server** > **Tipo di valutazione** selezionare **Macchina virtuale di Azure**.
4. In **Origine individuazione**:

    - Se le macchine virtuali sono state individuate usando l'appliance, selezionare **Macchine virtuali individuate dall'appliance di Azure Migrate**.
    - Se le macchine virtuali sono state individuate usando un file CSV importato, selezionare **Macchine virtuali importate**. 
    
5. Specificare un nome per la valutazione. 
6. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.

    ![Posizione del pulsante Visualizza tutto per esaminare le proprietà della valutazione](./media/tutorial-assess-physical/assessment-name.png)

7. In **Proprietà valutazione** > **Proprietà destinazione**:
    - In **Località di destinazione** specificare l'area di Azure in cui eseguire la migrazione.
        - Le raccomandazioni relative alle dimensioni e ai costi si basano sulla località specificata.
        - In Azure per enti pubblici è possibile specificare [queste aree](migrate-support-matrix.md#supported-geographies-azure-government) come destinazione delle valutazioni
    - In **Tipo di archiviazione**
        - Se nella valutazione si vogliono usare i dati basati sulle prestazioni, selezionare **Automatico** in modo che sia Azure Migrate a consigliare un tipo di archiviazione, sulla base dei dati relativi a base alle operazioni di I/O al secondo e alla velocità effettiva del disco.
        - In alternativa, selezionare il tipo di archiviazione da usare per la macchina virtuale quando si esegue la migrazione.
    - In **Istanze riservate** specificare se si vogliono usare istanze riservate per la macchina virtuale quando si esegue la migrazione.
        - Se si sceglie di usare un'istanza riservata, non è possibile specificare **Sconto (%)** o **Tempo di attività macchina virtuale**. 
        - [Altre informazioni](https://aka.ms/azurereservedinstances)
8. In **Dimensioni macchina virtuale**:
 
    - In **Criterio di dimensionamento** scegliere se si vuole basare la valutazione sui dati/metadati di configurazione della macchina virtuale o sui dati relativi alle prestazioni. Se si usano i dati relativi alle prestazioni:
        - In **Cronologia delle prestazioni** indicare la durata dei dati in base alla quale basare la valutazione
        - In **Utilizzo percentile** specificare il valore percentile da usare per il campione delle prestazioni. 
    - In **Serie macchina virtuale** specificare la serie di macchine virtuali di Azure che si vuole prendere in considerazione.
        - Se si usa la valutazione basata sulle prestazioni, il valore viene suggerito da Azure Migrate.
        - Perfezionare le impostazioni in base alle esigenze. Se, ad esempio, non si ha un ambiente di produzione in cui sono richieste le macchine virtuali della serie A in Azure, si può escludere la serie A dall'elenco delle serie.
    - In **Fattore di comfort** indicare il buffer da usare durante la valutazione. Questa opzione tiene conto di aspetti quali l'utilizzo stagionale, una cronologia ridotta delle prestazioni e il probabile aumento dell'utilizzo futuro. Se ad esempio si usa un fattore di comfort di due: **Componente** | **Utilizzo effettivo** | **Aggiungere fattore di comfort (2.0)** Core | 2 | 4 Memoria | 8 GB | 16 GB    
   
9. In **Prezzi**:
    - In **Offerta** specificare l'[offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Valutazione server stima il costo di tale offerta.
    - In **Valuta** selezionare la valuta di fatturazione per l'account.
    - In **Sconto (%)** aggiungere eventuali sconti specifici della sottoscrizione ricevuti oltre all'offerta di Azure. L'impostazione predefinita è 0%.
    - In **Tempo di attività macchina virtuale** specificare la durata (giorni al mese/ora al giorno) in cui le macchine virtuali sono in esecuzione.
        - Questa opzione è utile per le macchine virtuali di Azure che non vengono eseguite in modo continuativo.
        - Le stime dei costi sono basate sulla durata specificata.
        - Il valore predefinito è 31 giorni al mese e 24 ore al giorno.

    - In **Sottoscrizione con contratto Enterprise** specificare se prendere in considerazione uno sconto per la sottoscrizione con contratto Enterprise per la stima dei costi. 
    - In **Vantaggio Azure Hybrid** specificare se si ha già una licenza di Windows Server. Se si ha già una licenza coperta da un contratto Software Assurance per le sottoscrizioni di Windows Server, è possibile richiedere il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/) quando si importano licenze in Azure.

10. Se sono state apportate modifiche, fare clic su **Salva**.

    ![Proprietà valutazione](./media/tutorial-assess-physical/assessment-properties.png)

11. In **Valuta server** fare clic su **Avanti**.
12. In **Selezionare le macchine virtuali da valutare** selezionare **Crea nuovo** e specificare un nome per il gruppo. 
13. Selezionare l'appliance e quindi le macchine virtuali da aggiungere al gruppo. Quindi fare clic su **Next**.
14. In **Rivedi e crea valutazione esaminare i dettagli della valutazione e fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.


    > [!NOTE]
    > Per le valutazioni basate sulle prestazioni, prima di creare una valutazione è consigliabile attendere almeno un giorno dopo aver avviato l'individuazione. In questo modo i dati sulle prestazioni raccolti saranno maggiormente attendibili. Per una classificazione più attendibile, se possibile, dopo l'avvio dell'individuazione attendere un tempo pari alla durata delle prestazioni specificata (giorno/settimana/mese).

## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Idoneità per Azure**: se le VM sono idonee per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione delle VM in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

Per visualizzare una valutazione:

1. In **Server** > **Azure Migrate: Valutazione server**, fare clic sul numero accanto a **Valutazioni**.
2. In **Valutazioni** selezionare una valutazione per aprirla. A titolo di esempio (stime e costi sono solo esemplificativi): 

    ![Riepilogo della valutazione](./media/tutorial-assess-physical/assessment-summary.png)

3. Esaminare il riepilogo della valutazione. È anche possibile modificare le proprietà della valutazione o ricalcolare la valutazione.
 
 
### <a name="review-readiness"></a>Esaminare l'idoneità

1. Fare clic su **Idoneità per Azure**.
2. In **Idoneità per Azure** esaminare lo stato delle macchine virtuali:
    - **Idonea per Azure**: questo stato viene usato quando Azure Migrate consiglia le dimensioni e le stime dei costi per le macchine virtuali nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: questo stato viene usato quando Azure Migrate non può valutare l'idoneità a causa di problemi di disponibilità dei dati.

3. Selezionare uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità delle macchine virtuali ed eseguire il drill-down per visualizzare i dettagli delle macchine virtuali, incluse le impostazioni di calcolo, archiviazione e rete.

### <a name="review-cost-estimates"></a>Verificare le stime dei costi

Il riepilogo della valutazione mostra i costi di calcolo e archiviazione stimati per l'esecuzione delle macchine virtuali in Azure. 

1. Esaminare i costi totali mensili. I costi sono aggregati per tutte le VM nel gruppo valutato.

    - Le stime dei costi sono basate sulle dimensioni consigliate per una macchina virtuale e sui relativi dischi e proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi è relativa all'esecuzione delle macchine virtuali locali in macchine virtuali di Azure. La stima non considera i costi per PaaS o SaaS.

2. Esaminare i costi di archiviazione mensili. La visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione. 
3. È possibile eseguire il drill-down per visualizzare i dettagli dei costi per macchine virtuali specifiche.

### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Valutazione server assegna una classificazione di attendibilità alle valutazioni basate sulle prestazioni. La classificazione è compresa tra una stella (più bassa) e cinque stelle (più alta).

![Classificazione di attendibilità](./media/tutorial-assess-physical/confidence-rating.png)

La classificazione di attendibilità è utile per stimare l'affidabilità delle raccomandazioni sulle dimensioni nella valutazione. La classificazione è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.

> [!NOTE]
> Le classificazioni di attendibilità non vengono assegnate se si crea una valutazione basata su un file CSV.

Le classificazioni di attendibilità sono elencate di seguito.

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle

[Altre informazioni](concepts-assessment-calculation.md#confidence-ratings-performance-based) sulle classificazioni di attendibilità.

## <a name="next-steps"></a>Passaggi successivi

- Per trovare le dipendenze delle macchine virtuali, usare il [mapping delle dipendenze](concepts-dependency-visualization.md).
- Configurare il mapping delle dipendenze [basato sull'agente](how-to-create-group-machine-dependencies.md).
