---
title: Utilizzare la versione precedente di Azure Migrate | Microsoft Docs
description: Informazioni di riepilogo per l'utilizzo della versione precedente di Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b3607f0b462efceab322e6eaf616268a34b02fb0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142090"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Utilizzare la versione precedente di Azure Migrate

Questo articolo fornisce informazioni sull'utilizzo della versione precedente di Azure Migrate.


Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: usare questa versione per creare progetti di Azure Migrate, individuare i computer locali e orchestrare valutazioni e migrazioni. [Vedere altre informazioni](whats-new.md) sulle novità di questa versione.
- **Versione precedente**: se si usa la versione precedente di Azure Migrate (è supportata solo la valutazione delle macchine virtuali VMware locali), è ora consigliabile passare alla versione corrente. Se è ancora necessario usare progetti di Azure Migrate creati nella versione precedente, di seguito sono elencate le operazioni consentite e quelle non più consentite:
    - Non è più possibile creare progetti di migrazione.
    - Si consiglia di non eseguire nuove individuazioni.
    - È ancora possibile accedere ai progetti esistenti.
    - È ancora possibile eseguire valutazioni.
    

## <a name="upgrade-between-versions"></a>Aggiornamento della versione

Non è possibile aggiornare i progetti o i componenti della versione precedente alla nuova versione. È necessario [creare un nuovo progetto di Azure Migrate](how-to-add-tool-first-time.md) e aggiungervi strumenti di valutazione e migrazione.

## <a name="find-projects-from-previous-version"></a>Trovare progetti dalla versione precedente

Per trovare i progetti della versione precedente, procedere come indicato di seguito:

1. Nella portale di Azure > **Tutti i servizi** cercare e selezionare **Azure Migrate**. 
2. Nel dashboard di Azure Migrate sono disponibili una notifica e un collegamento per accedere ai progetti di Azure Migrate precedenti.
3. Fare clic sul collegamento per aprire i progetti della versione 1.


## <a name="create-an-assessment"></a>Creare una valutazione

Dopo aver individuato le macchine virtuali nel portale, è possibile raggrupparle e creare valutazioni.

- Le valutazioni di tipo 'Come in locale' possono essere create immediatamente, non appena vengono individuate le macchine virtuali nel portale.
- È invece consigliabile attendere almeno un giorno prima di creare valutazioni basate sulle prestazioni per ottenere raccomandazioni affidabili relative alle dimensioni.

Creare una valutazione nel modo seguente:

1. Nella pagina **Panoramica** del progetto fare clic su **+Crea valutazione**.
2. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.
3. Creare il gruppo e specificarne il nome.
4. Selezionare le macchine virtuali da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo e la valutazione.
6. Dopo aver creato la valutazione, visualizzarla in **Panoramica** > **Dashboard**.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.

Se si desidera aggiornare una valutazione esistente con dati sulle prestazioni più recenti, è possibile usare il comando **Ricalcola** sulla valutazione per aggiornarla.

## <a name="review-an-assessment"></a>Esaminare una valutazione 

Una valutazione si articola in tre fasi:

- Una valutazione inizia con un'analisi di idoneità per determinare se i computer sono compatibili in Azure.
- Calcolo delle dimensioni delle stime.
- Stima dei costi mensili.

Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità, viene contrassegnato come non idoneo per Azure e dimensioni e costi non vengono calcolati.


### <a name="review-azure-readiness"></a>Valutare l'idoneità per Azure

La visualizzazione Idoneità per Azure nella valutazione mostra lo stato di idoneità di ogni macchina virtuale.

**Idoneità** | **State** | **Dettagli**
--- | --- | ---
Idoneo per Azure | Nessun problema di compatibilità. È possibile eseguire la migrazione del computer così com'è in Azure e il computer verrà avviato in Azure con il supporto completo di Azure. | Per le macchine virtuali idonee, Azure Migrate offre un consiglio riguardo alle dimensioni delle macchine in Azure.
Idoneo per Azure con condizioni | Il computer potrebbe essere avviato in Azure, ma potrebbe non avere il supporto completo di Azure. Ad esempio un computer con una versione precedente di Windows Server non supportata in Azure. | Azure Migrate spiega i problemi di idoneità e indica le procedure di correzione.
Non idonea per Azure |  La macchina virtuale non verrà avviata in Azure. Ad esempio, il disco di una macchina virtuale locale che supera i 4 TB non può essere ospitato in Azure. | Azure Migrate spiega i problemi di idoneità e indica le procedure di correzione.
Idoneità sconosciuta | Azure Migrate non è in grado di identificare l'idoneità per Azure, in genere perché i dati non sono disponibili.


#### <a name="azure-vm-properties"></a>Proprietà della macchina virtuale di Azure
Per l'idoneità si tiene conto di un certo numero di proprietà della macchina virtuale, per determinare se la macchina virtuale può essere eseguita in Azure.


**Proprietà** | **Dettagli** | **Idoneità**
--- | --- | ---
**Tipo di avvio** | BIOS supportato. UEFI non supportato. | Idoneo con condizioni se il tipo di avvio è UEFI.
**Core** | Core della macchina virtuale < = numero massimo di core (128) supportati per una macchina virtuale di Azure.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione i core utilizzati.<br/>Se <br/>nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> In mancanza di una cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Idoneo se è minore o uguale ai limiti.
**Memoria** | La dimensione della memoria del computer deve essere minore o uguale alla memoria massima per una macchina virtuale di Azure, ovvero 3892 GB per standard_M128m&nbsp;<sup>2</sup> di Azure serie M. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione la memoria utilizzata.<br/><br/>Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> In mancanza di una cronologia, si usa la memoria allocata senza applicare il fattore di comfort.<br/><br/> | Idoneo se rientra nei limiti.
**Disco di archiviazione** | Le dimensioni allocate di un disco non devono superare i 4 TB (4096 GB).<br/><br/> I dischi collegati al computer non devono essere più di 65, incluso il disco del sistema operativo. | Idoneo se rientra nei limiti.
**Rete** | A un computer non devono essere collegati più di 32 NIC. | Idoneo se rientra nei limiti.

#### <a name="guest-operating-system"></a>Sistema operativo guest

Insieme alle proprietà della macchina virtuale, Azure Migrate esamina anche il sistema operativo guest della macchina virtuale locale per stabilire se la macchina virtuale può essere eseguita in Azure.

- Azure Migrate considera il sistema operativo specificato nel server vCenter.
- Poiché il processo di individuazione eseguito da Azure Migrate è basato su appliance, non dispone di un modo per verificare se il sistema operativo eseguito all'interno della VM è uguale a quello specificato nel server vCenter.

Viene usata la logica seguente.

**Sistema operativo** | **Dettagli** | **Idoneità**
--- | --- | ---
Windows Server 2016 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 R2 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2008 R2 e tutti i Service Pack | Azure offre supporto completo.| Idoneo per Azure
Windows Server 2008 (32 bit e 64 bit) | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2003, 2003 R2 | Non più supportato ed è necessario un [contratto di supporto personalizzato](https://aka.ms/WSosstatement) per il supporto in Azure. | Idoneo per Azure con condizioni, provare ad aggiornare il sistema operativo prima della migrazione in Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Non più supportato. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di aggiornare il sistema operativo prima della migrazione in Azure.
Client Windows 7, 8 e 10 | Azure offre supporto solo con [la sottoscrizione di Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Idoneo per Azure con condizioni
Windows 10 Pro Desktop | Azure fornisce supporto con [i diritti di hosting multi-tenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Idoneo per Azure con condizioni
Windows Vista, XP Professional | Non più supportato. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di aggiornare il sistema operativo prima della migrazione in Azure.
Linux | Azure approva questi [sistemi operativi Linux](../virtual-machines/linux/endorsed-distros.md). Altri sistemi operativi Linux potrebbero supportare l'avvio in Azure, ma è consigliabile aggiornare il sistema operativo a una versione approvata prima della migrazione ad Azure. | Idoneo per Azure se la versione è approvata.<br/><br/>Idoneo per Azure con condizioni se la versione non è approvata.
Altri sistemi operativi<br/><br/> Ad esempio Oracle Solaris, Apple Mac OS, FreeBSD e così via. | Azure non approva questi sistemi operativi. Il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di installare un sistema operativo supportato prima della migrazione in Azure.  
Sistema operativo specificato come **Altro** nel server vCenter | In questo caso Azure Migrate non può identificare il sistema operativo. | Idoneità sconosciuta. Verificare che il sistema operativo eseguito nella VM sia supportato in Azure.
Sistemi operativi a 32 bit | Il computer può eseguire l'avvio in Azure, ma Azure non offre il supporto completo. | Idoneo per Azure con condizioni, provare ad aggiornare il sistema operativo della macchina virtuale da 32 bit a 64 bit prima di eseguire la migrazione in Azure.


### <a name="review-sizing"></a>Verificare le dimensioni

 La raccomandazione relativa alle dimensioni di Azure Migrate dipende dal criterio di definizione delle dimensioni specificato nelle proprietà della valutazione.

- Se la definizione delle dimensioni è basata sulle prestazioni, la raccomandazione relativa alle dimensioni prende in considerazione la cronologia delle prestazioni delle macchine virtuali (CPU e memoria) e dei dischi (operazioni di I/O al secondo e velocità effettiva).
- Se il criterio di definizione delle dimensioni è 'Come in locale', la raccomandazione relativa alle dimensioni in Azure si basa sulle dimensioni della macchina virtuale in locale. La definizione delle dimensioni del disco è basata sul tipo di archiviazione specificato nelle proprietà della valutazione (l'impostazione predefinita prevede dischi Premium). Azure Migrate non considera i dati sulle prestazioni per la macchina virtuale e i dischi.

### <a name="review-cost-estimates"></a>Verificare le stime dei costi

Le stime dei costi mostrano il costo totale di calcolo e di archiviazione associato all'esecuzione delle macchine virtuali in Azure, con i dettagli per ogni macchina.

- Le stime dei costi vengono calcolate usando la raccomandazione per le dimensioni per una macchina virtuale e i relativi dischi, oltre alle proprietà della valutazione.
- I costi mensili stimati per il calcolo e l'archiviazione vengono aggregati per tutte le macchine virtuali del gruppo.
- La stima dei costi si riferisce all'esecuzione delle macchine virtuali locali come macchine virtuali dell'infrastruttura distribuita come servizio (IaaS) di Azure. In Azure Migrate non vengono considerati i costi relativi alla piattaforma distribuita come servizio (PaaS, Platform as a Service) o al software come un servizio (SaaS, Software as a Service).

### <a name="review-confidence-rating-performance-based-assessment"></a>Verificare la classificazione di attendibilità (valutazione basata sulle prestazioni)

Ogni valutazione basata sulle prestazioni è associata a una classificazione di attendibilità.

- La classificazione di attendibilità è compresa tra una e cinque stelle (in ordine crescente).
- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate.
- La classificazione di attendibilità non è disponibile per le valutazioni di tipo "Come in locale".

Per definire le dimensioni in base alle prestazioni, Azure Migrate necessita delle informazioni seguenti:
- Dati di utilizzo per la CPU.
- Dati sulla memoria della macchina virtuale.
- Per ogni disco collegato alla macchina virtuale, il servizio deve avere i dati relativi alle operazioni di I/O al secondo e alla velocità effettiva del disco.
- Per ogni scheda di rete collegata a una macchina virtuale, Azure Migrate deve disporre dei dati su input/output di rete.
- Se uno di questi elementi non è disponibile, le raccomandazioni per le dimensioni (e quindi le classificazioni di attendibilità) potrebbero non essere affidabili.


Nella tabella sono riepilogate le classificazioni di attendibilità possibili in base alla percentuale dei punti dati disponibili.

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Problemi di valutazione che influiscono sulle classificazioni di attendibilità

Una valutazione potrebbe non avere a disposizione tutti i punti dati per svariati motivi:

- L'ambiente non è stato sottoposto a profilatura per la durata della valutazione. Ad esempio, se si crea la valutazione con il periodo di tempo delle prestazioni impostato su un giorno, è necessario attendere almeno un giorno dopo avere avviato l'individuazione perché siano raccolti tutti i punti dati.
- Durante il periodo di calcolo della valutazione sono state arrestate alcune macchine virtuali. Se una o più macchine virtuali sono state spente per parte della durata, Azure Migrate non può raccogliere i dati sulle prestazioni per questo periodo.
- Alcune macchine virtuali sono state create durante il periodo di calcolo della valutazione. Ad esempio, se si crea una valutazione usando la cronologia delle prestazioni dell'ultimo mese, ma una settimana prima viene creato un certo numero di macchine virtuali nell'ambiente, la cronologia delle prestazioni delle nuove macchine virtuali non corrisponderà all'intera durata.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, attendere almeno un giorno per consentire all'appliance di profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, la definizione delle dimensioni in base alle prestazioni potrebbe non essere affidabile. Se non si vuole ripetere il calcolo, è consigliabile passare alla definizione delle dimensioni di tipo Come in locale, modificando le proprietà della valutazione.



## <a name="create-groups-using-dependency-visualization"></a>Creare gruppi tramite la visualizzazione delle dipendenze

Oltre a creare manualmente i gruppi, è possibile creare gruppi usando la visualizzazione delle dipendenze.
- Questo metodo viene in genere usato quando si vogliono valutare gruppi con livelli di attendibilità più elevati controllando in modo incrociato le dipendenze dei computer prima di eseguire una valutazione.
- La visualizzazione delle dipendenze è utile per pianificare in modo efficace la migrazione a Azure. Garantisce inoltre una migrazione completa e senza interruzioni a sorpresa.
- È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire contemporaneamente la migrazione e determinare se un sistema in esecuzione è ancora utile o se è un candidato alla rimozione anziché alla migrazione.
- Azure Migrate usa la soluzione Mapping dei servizi in Monitoraggio di Azure per abilitare la visualizzazione delle dipendenze.

> [!NOTE]
> La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

Per configurare la visualizzazione delle dipendenze, associare un'area di lavoro Log Analytics a un progetto di Azure Migrate, installare gli agenti nei computer per i quali si vogliono visualizzare le dipendenze e quindi creare gruppi usando le informazioni sulle dipendenze. 



### <a name="associate-a-log-analytics-workspace"></a>Associare un'area di lavoro Log Analytics

Per usare la visualizzazione delle dipendenze, è necessario associare un'area di lavoro Log Analytics a un progetto di migrazione. È possibile creare o collegare solo un'area di lavoro nella stessa sottoscrizione in cui viene creato il progetto di migrazione.

1. Per associare un'area di lavoro Log Analytics a un progetto, in **Panoramica** passare a **Informazioni di base** e fare clic su **Configurazione richiesta**.
2. È possibile creare una nuova area di lavoro o associarne una esistente:
  - Per creare una nuova area di lavoro, specificare un nome. L'area di lavoro viene creata in un'area nella stessa [area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) del progetto di migrazione.
  - Quando si collega un'area di lavoro esistente, è possibile sceglierla tra tutte le aree di lavoro disponibili nella stessa sottoscrizione del progetto di migrazione. Sono elencate solo le aree di lavoro create in un'[area di Mapping dei servizi supportata](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Per associare un'area di lavoro, assicurarsi di avere l'accesso in lettura all'area di lavoro.

> [!NOTE]
> Non è possibile modificare l'area di lavoro associata a un progetto di migrazione.

### <a name="download-and-install-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

Dopo aver configurato un'area di lavoro, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole valutare. Se si hanno computer senza accesso a Internet, è necessario scaricare e installare il [Gateway Log Analytics](../azure-monitor/platform/gateway.md).

1. In **Panoramica** fare clic su **Gestisci** > **Computer** e selezionare il computer necessario.
2. Nella colonna **Dipendenze** fare clic su **Installa agenti**.
3. Nella pagina **Dipendenze** scaricare e installare Microsoft Monitoring Agent (MMA) e Dependency Agent in ogni macchina virtuale da valutare.
4. Copiare l'ID e la chiave dell'area di lavoro. Questi dati sono necessari quando si installa MMA nel computer locale.

> [!NOTE]
> Per automatizzare l'installazione degli agenti è possibile usare uno strumento di distribuzione come System Center Configuration Manager o uno strumento dei partner come [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), che offre una soluzione di distribuzione degli agenti per Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Installare l'agente MMA in un computer Windows

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.

È possibile installare l'agente dalla riga di comando o usando un metodo automatizzato, ad esempio System Center Configuration Manager. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sull'uso di questi metodi per installare l'agente MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Installare l'agente MMA in un computer Linux

Per installare l'agente in un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Altre informazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) sull'elenco dei sistemi operativi Linux supportati da MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Installare l'agente MMA in un computer monitorato tramite Operations Manager

Per i computer monitorati da System Center Operations Manager 2012 R2 o versioni successive, non è necessario installare l'agente MMA. La soluzione Mapping dei servizi si integra con MMA di Operations Manager per raccogliere i dati necessari sulle dipendenze. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) È necessario installare Dependency Agent.

### <a name="install-the-dependency-agent"></a>Installare Dependency Agent

1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

- Altre informazioni sul [supporto di Dependency Agent](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) per sistemi operativi Windows e Linux.
- [Altre informazioni](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) sul modo in cui è possibile usare gli script per installare l'agente di dipendenza.

>[!NOTE]
> L'articolo sul Monitoraggio di Azure per le macchine virtuali a cui si fa riferimento per fornire una panoramica dei prerequisiti di sistema e dei metodi per la distribuzione di Dependency Agent è applicabile anche alla soluzione Mapping dei servizi.

### <a name="create-a-group-with-dependency-mapping"></a>Creare un gruppo con il mapping delle dipendenze

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Gestisci** > **Computer**.
2. Cercare il computer in cui sono stati installati gli agenti.
3. Nella colonna **Dipendenze** relativa al computer viene ora visualizzata la dicitura **Visualizza dipendenze**. Fare clic sulla colonna per visualizzare le dipendenze del computer.
4. La mappa delle dipendenze del computer mostra i dettagli seguenti:
    - Le connessioni TCP in ingresso (client) e in uscita (server) verso o dal computer
        - I computer dipendenti in cui non sono installati l'agente MMA e Dependency Agent sono raggruppati in base ai numeri di porta.
        - I computer dipendenti in cui sono installati l'agente MMA e Dependency Agent sono visualizzati in caselle separate.
    - I processi in esecuzione sul computer: è possibile espandere ogni casella di computer per visualizzare i processi
    - Vengono visualizzate le proprietà del computer, inclusi il nome di dominio completo, il sistema operativo e l'indirizzo MAC. È possibile fare clic su ogni casella del computer per visualizzare i dettagli.

4. È possibile visualizzare le dipendenze per intervalli di tempo diversi facendo clic sulla durata nell'etichetta dell'intervallo di tempo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.

   > [!NOTE]
   >    È supportato un intervallo di tempo di massimo un'ora. Usare i log di Monitoraggio di Azure per [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo più lungo.

5. Dopo aver identificato i computer dipendenti da raggruppare, fare clic tenendo premuto CTRL per selezionare più computer nella mappa e quindi fare clic su **Raggruppa macchine virtuali**.
6. Specificare un nome di gruppo. Verificare che i computer dipendenti siano stati individuati da Azure Migrate.

    > [!NOTE]
    > Se un computer dipendente non viene individuato da Azure Migrate, non è possibile aggiungerlo al gruppo. Per aggiungere tali computer al gruppo, è necessario eseguire nuovamente il processo di individuazione con l'ambito corretto nel server vCenter e verificare che il computer venga individuato da Azure Migrate.  

7. Se si vuole creare una valutazione per questo gruppo, selezionare la casella di controllo per creare una nuova valutazione per il gruppo.
8. Fare clic su **OK** per salvare il gruppo.

Una volta creato il gruppo, è consigliabile installare gli agenti in tutti i computer del gruppo e perfezionare il gruppo visualizzandone tutte le dipendenze.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Eseguire query sui dati delle dipendenze dai log di Monitoraggio di Azure

I dati sulle dipendenze acquisiti da Mapping dei servizi sono disponibili per le query nell'area di lavoro Log Analytics associata al progetto di Azure Migrate. [Vedere altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sulle tabelle dati di Mapping dei servizi per l'esecuzione di query nei log di Monitoraggio di Azure. 

Per eseguire le query Kusto:

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Panoramica**.
2. In **Panoramica** , andare alla sezione **Elementi fondamentali** del progetto e fare clic sul nome dell'area di lavoro accanto a **Spazio di lavoro OMS** .
3. Nella pagina dell'area di lavoro Log Analytics, fare clic su **Generale** > **Log**.
4. Scrivere la query per raccogliere dati sulle dipendenze usando i log di Monitoraggio di Azure. Nella sezione successiva sono disponibili query di esempio.
5. Eseguire la query facendo clic su Esegui. 

[Vedere altre informazioni](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) su come scrivere query Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Query di esempio sui log di Monitoraggio di Azure

Di seguito sono riportate alcune query di esempio che è possibile usare per estrarre i dati sulle dipendenze. È possibile modificare le query per estrarre i punti dati preferiti. Un elenco completo dei campi nei record dei dati sulle dipendenze è disponibile [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Altre query di esempio sono disponibili [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Riepilogare le connessioni in ingresso in un set di computer

I record nella tabella per le metriche di connessione, VMConnection, non rappresentano le singole connessioni di rete fisiche. Più connessioni di rete fisiche vengono raggruppate in una connessione logica. [Vedere altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sul modo in cui i dati sulle connessioni di rete fisiche vengono aggregati in un singolo record logico in VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Riepilogare il volume di dati inviati e ricevuti su connessioni in ingresso tra un set di computer

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Passaggi successivi
[Vedere altre informazioni](migrate-services-overview.md) sulla versione più recente di Azure Migrate.
