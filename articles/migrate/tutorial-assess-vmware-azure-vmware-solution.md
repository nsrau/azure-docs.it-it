---
title: Valutare le macchine virtuali VMware per la migrazione alla soluzione Azure VMware con Azure Migrate
description: Informazioni su come valutare le macchine virtuali VMware per la migrazione alla soluzione Azure VMware con Valutazione server di Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 29f7f824d96aedd80e490ba84c390be4d9493683
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604241"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Esercitazione: Valutare le VM VMware per la migrazione alla soluzione Azure VMware

Il percorso di migrazione ad Azure prevede la valutazione dei carichi di lavoro locali per misurare l'idoneità del cloud, identificare i rischi e stimare costi e complessità.

Questo articolo illustra come valutare macchine virtuali VMware individuate per la migrazione alla soluzione Azure VMware con lo strumento Azure Migrate: Valutazione server. La soluzione Azure VMware è un servizio gestito che consente di eseguire la piattaforma VMware in Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
- Eseguire una valutazione in base alle informazioni relative a metadati e configurazioni delle macchine virtuali.
- Eseguire una valutazione in base ai dati sulle prestazioni.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.



## <a name="prerequisites"></a>Prerequisiti

Prima di seguire questa esercitazione per valutare le macchine virtuali per la migrazione alla soluzione Azure VMware, assicurarsi di aver individuato le macchine virtuali che si intende valutare:

- Per individuare le macchine virtuali usando l'appliance di Azure Migrate, [seguire questa esercitazione](tutorial-discover-vmware.md). 
- Per individuare le macchine virtuali usando un file CSV importato, [seguire questa esercitazione](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Decidere la valutazione da eseguire


Decidere se si vuole eseguire una valutazione usando criteri di dimensionamento in base a dati/metadati di configurazione delle macchine virtuali raccolti in locale o a dati dinamici relativi alle prestazioni.

**Valutazione** | **Dettagli** | **Consiglio**
--- | --- | ---
**Come in locale** | Eseguire la valutazione in base ai dati/metadati di configurazione delle macchine virtuali.  | Le dimensioni consigliate dei nodi nella soluzione Azure VMware sono basate sulle dimensioni delle macchine virtuali locali, unitamente alle impostazioni specificate nella valutazione per il tipo di nodo, il tipo di archiviazione e l'impostazione di tolleranza errori.
**Basata sulle prestazioni** | Eseguire la valutazione in base ai dati dinamici relativi alle prestazioni. | Le dimensioni consigliate dei nodi nella soluzione Azure VMware sono basate sui dati relativi all'utilizzo della CPU e della memoria, unitamente alle impostazioni specificate nella valutazione per il tipo di nodo, il tipo di archiviazione e l'impostazione di tolleranza errori.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Nella pagina **Server** selezionare **Server Windows e Linux** e fare clic su **Valutare ed eseguire la migrazione dei server**.

   ![Posizione del pulsante Valutare ed eseguire la migrazione dei server](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

2. In **Azure Migrate: Valutazione server** fare clic su **Valuta**.

3. In **Valuta server** > **Tipo di valutazione** selezionare **Soluzione Azure VMware (AVS) (anteprima)** .
4. In **Origine individuazione**:

    - Se le macchine virtuali sono state individuate usando l'appliance, selezionare **Macchine virtuali individuate dall'appliance di Azure Migrate**.
    - Se le macchine virtuali sono state individuate usando un file CSV importato, selezionare **Macchine virtuali importate**. 
    
5. Specificare un nome per la valutazione. 
6. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.

    ![Pagina per la selezione delle impostazioni di valutazione](./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png)


7. In **Proprietà valutazione** > **Proprietà destinazione**:

    - In **Località di destinazione** specificare l'area di Azure in cui eseguire la migrazione.
       - Le raccomandazioni relative alle dimensioni e ai costi si basano sulla località specificata.
       - Attualmente è possibile eseguire la valutazione per tre aree (Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale)
   - In **Tipo di archiviazione** lasciare impostato **vSAN**. Questo è il tipo di archiviazione predefinito per un cloud privato della soluzione Azure VMware.
   - Le **istanze riservate** non sono attualmente supportate per i nodi della soluzione Azure VMware.
8. In **Dimensioni macchina virtuale**:
    - In **Tipo di nodo** selezionare un tipo di nodo in base ai carichi di lavoro in esecuzione nelle macchine virtuali locali.
        - Il tipo dei nodi necessari per eseguire la migrazione delle macchine virtuali alla soluzione Azure VMware viene consigliato da Azure Migrate.
        - Il tipo di nodo predefinito è AV36.
    - In **Impostazione FTT (tolleranza errori), livello RAID** selezionare la combinazione di tolleranza errori e RAID.  L'opzione FTT selezionata, combinata con il requisito del disco della macchina virtuale locale, determina il lo spazio di archiviazione totale vSAN richiesto nella soluzione Azure VMware.
    - In **Oversubscription della CPU** specificare il rapporto di core virtuali associati a un core fisico nel nodo della soluzione Azure VMware. Un'oversubscription maggiore di 4:1 potrebbe causare una riduzione del livello delle prestazioni, ma può essere usata per i carichi di lavoro di tipo server Web.

9. In **Dimensioni nodo**: 
    - In **Criterio di dimensionamento** scegliere se si vuole basare la valutazione sui metadati statici o sui dati relativi alle prestazioni. Se si usano i dati relativi alle prestazioni:
        - In **Cronologia delle prestazioni** indicare la durata dei dati in base alla quale basare la valutazione
        - In **Utilizzo percentile** specificare il valore percentile da usare per il campione delle prestazioni. 
    - In **Fattore di comfort** indicare il buffer da usare durante la valutazione. Questa opzione tiene conto di aspetti quali l'utilizzo stagionale, una cronologia ridotta delle prestazioni e il probabile aumento dell'utilizzo futuro. Se ad esempio si usa un fattore di comfort di due:
    
        **Componente** | **Utilizzo effettivo** | **Aggiungere fattore di comfort (2.0)**
        --- | --- | ---  
        Core | 2 | 4
        Memoria | 8 GB | 16 GB     

10. In **Prezzi**:
    - In **Offerta** viene visualizzata l'[offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta per cui Valutazione server stima il costo.
    - In **Valuta** selezionare la valuta di fatturazione per l'account.
    - In **Sconto (%)** aggiungere eventuali sconti specifici della sottoscrizione ricevuti oltre all'offerta di Azure. L'impostazione predefinita è 0%.

11. Se sono state apportate modifiche, fare clic su **Salva**.

    ![Proprietà valutazione](./media/tutorial-assess-vmware-azure-vmware-solution/view-all.png)

12. In **Valuta server** fare clic su **Avanti**.
13. In **Valuta server** > **Selezionare le macchine virtuali da valutare**, per creare un nuovo gruppo di server per la valutazione, selezionare **Crea nuovo** e specificare un nome per il gruppo. 
14. Selezionare l'appliance e quindi le macchine virtuali da aggiungere al gruppo. Quindi fare clic su **Next**.
15. In **Rivedi e crea valutazione** esaminare i dettagli della valutazione e fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

    > [!NOTE]
    > Per le valutazioni basate sulle prestazioni, prima di creare una valutazione è consigliabile attendere almeno un giorno dopo aver avviato l'individuazione. In questo modo i dati sulle prestazioni raccolti saranno maggiormente attendibili. Per una classificazione più attendibile, se possibile, dopo l'avvio dell'individuazione attendere un tempo pari alla durata delle prestazioni specificata (giorno/settimana/mese).

## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione della soluzione Azure VMware descrive:

- Idoneità per AVS: indica se le macchine virtuali locali sono idonee per la migrazione alla soluzione Azure VMware.
- Numero di nodi AVS: numero stimato dei nodi della soluzione Azure VMware necessari per eseguire le macchine virtuali.
- Utilizzo tra nodi AVS: utilizzo previsto della CPU, della memoria e delle risorse di archiviazione in tutti i nodi.
- Stima dei costi mensili: costi mensili stimati per tutti i nodi della soluzione Azure VMware in esecuzione nelle macchine virtuali locali.

## <a name="view-an-assessment"></a>Visualizzare una valutazione

Per visualizzare una valutazione:

1. In **Server** > **Azure Migrate: Valutazione server**, fare clic sul numero accanto a **Valutazioni**.
2. In **Valutazioni** selezionare una valutazione per aprirla. 
3. Esaminare il riepilogo della valutazione. È anche possibile modificare le proprietà della valutazione o ricalcolare la valutazione.
 

### <a name="review-readiness"></a>Esaminare l'idoneità

1. Fare clic su **Idoneità per Azure**.
2. In **Idoneità per Azure** esaminare lo stato delle macchine virtuali.

    - **Idoneo/a per AVS?** : è possibile eseguire la migrazione della macchina virtuale alla soluzione Azure VMware così com'è, senza modifiche. La macchina virtuale viene avviata nella soluzione Azure VMware con il supporto completo per la soluzione Azure VMware.
    - **Idonea con condizioni**: la macchina virtuale potrebbe presentare problemi di compatibilità con la versione corrente di vSphere. Potrebbe essere necessario installare strumenti VMware o configurare altre impostazioni prima che sia completamente funzionante nella soluzione Azure VMware.
    - **Non idoneo/a per AVS?** : la macchina virtuale non verrà avviata nella soluzione Azure VMware. Ad esempio, se una macchina virtuale VMware locale include un dispositivo esterno (ad esempio un CD-ROM) collegato e si usa VMware VMotion, l'operazione VMotion non riesce.
 - **Idoneità sconosciuta**: Azure Migrate non è riuscito a determinare l'idoneità della macchina virtuale perché i metadati raccolti dall'ambiente locale sono insufficienti.

3. Esaminare lo strumento suggerito.

    - VMware HCX o Enterprise: per i computer VMware, la soluzione VMWare Hybrid Cloud Extension (HCX) è lo strumento di migrazione suggerito per eseguire la migrazione del carico di lavoro locale al cloud privato della soluzione Azure VMware. Per ulteriori informazioni, .
    - Sconosciuto: Per i computer importati tramite un file con estensione csv, lo strumento di migrazione predefinito è sconosciuto. Tuttavia, per i computer VMware è consigliabile usare la soluzione VMware Hybrid Cloud Extension (HCX).
4. Fare clic su uno stato di idoneità per la soluzione Azure VMware. È possibile visualizzare i dettagli sull'idoneità delle VM ed eseguire il drill-down per visualizzare i dettagli delle VM, incluse le impostazioni di calcolo, archiviazione e rete.

### <a name="review-cost-estimates"></a>Verificare le stime dei costi

Il riepilogo della valutazione mostra i costi di calcolo e archiviazione stimati per l'esecuzione delle macchine virtuali in Azure. 

1. Esaminare i costi totali mensili. I costi sono aggregati per tutte le VM nel gruppo valutato.

    - Le stime dei costi si basano sul numero di nodi della soluzione Azure VMware necessari considerando i requisiti delle risorse di tutte le macchine virtuali in totale.
    - Dal momento che i prezzi per la soluzione Azure VMware vengono calcolati in base ai nodi, il costo totale non prevede la distribuzione dei costi di calcolo e dei costi di archiviazione.
    - La stima dei costi è relativa all'esecuzione delle macchine virtuali locali nella soluzione Azure VMware. Valutazione server di Azure Migrate non considera i costi per PaaS o SaaS.

2. Esaminare le stime di archiviazione mensili. La visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione. 
3. È possibile eseguire il drill-down per visualizzare i dettagli dei costi per macchine virtuali specifiche.

### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Valutazione server assegna una classificazione di attendibilità alle valutazioni basate sulle prestazioni. La classificazione è compresa tra una stella (più bassa) e cinque stelle (più alta).

![Classificazione di attendibilità](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

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
- Configurare il mapping delle dipendenze [senza agente](how-to-create-group-machine-dependencies-agentless.md) o [basato sull'agente](how-to-create-group-machine-dependencies.md).
