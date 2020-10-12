---
title: Piano di migrazione della compilazione con Azure Migrate | Microsoft Docs
description: Vengono fornite informazioni aggiuntive sulla compilazione del piano di migrazione con Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: db1de363856fd560fea97f8f9cdf542717c4cca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090094"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Creare un piano di migrazione con Azure Migrate

Questo articolo fornisce una guida rapida per la creazione del piano di migrazione in Azure con [Azure migrate](migrate-services-overview.md).Per altre domande, vedere le risorse seguenti:

- [Domande generali](resources-faq.md) su Azure migrate
- Domande sull' [appliance Azure migrate](common-questions-appliance.md)
- Domande sulla [migrazione del server](common-questions-server-migration.md)
- Risposte alle domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum)

## <a name="define-the-goals-of-cloud-migration"></a>Definire gli obiettivi della migrazione cloud

Prima di creare un piano di migrazione, è importante comprendere e valutare la [motivazione](/azure/cloud-adoption-framework/strategy/motivations) per passare al cloud che può contribuire a produrre risultati aziendali più efficaci. Come illustrato nel [Framework di adozione del cloud per Azure](/azure/cloud-adoption-framework) , è possibile che siano presenti trigger e approcci di migrazione diversi idonei per l'azienda:  

**Eventi aziendali critici** | **Risultato della migrazione**
--- | ---
Uscita Data Center | Risparmi sui costi
Unione, acquisizione o dismissione | Riduzione della complessità tecnica o del fornitore
Riduzione delle spese di capitale | Ottimizzazione delle operazioni interne
Fine del supporto per le tecnologie mission-critical | Aumento della flessibilità aziendale
Risposta alle modifiche di conformità alle normative | Preparazione per nuove funzionalità tecniche
Nuovi requisiti di sovranità dei dati | Scalabilità per soddisfare le esigenze del mercato
Riduzione delle rotture e miglioramento della stabilità IT | Ridimensionamento per soddisfare le esigenze geografiche

La motivazione della migrazione può essere utile anche per riflettere gli obiettivi strategici e i risultati che si desidera ottenere eseguendo la migrazione ad Azure. Il passaggio successivo consiste nell'identificare e pianificare un percorso di migrazione ad Azure personalizzato per i carichi di lavoro. Azure Migrate: lo strumento Server Assessment consente di valutare i carichi di lavoro locali e fornisce linee guida e strumenti per facilitare la migrazione.

## <a name="understand-your-digital-estate"></a>Informazioni sulle proprietà digitali

Iniziare a comprendere l'infrastruttura, le applicazioni e le dipendenze locali per identificare i carichi di lavoro di cui si vuole eseguire la migrazione in Azure e ottenere proiezioni di costo ottimizzate. Lo strumento Server Assessment consente di rispondere alle domande seguenti:

### <a name="what-workloads-are-in-use"></a>Quali carichi di lavoro sono in uso?

Usare il dispositivo Azure Migrate Lightweight per eseguire un'individuazione senza agenti delle VM VMware locali, delle VM Hyper-V e dei server fisici. L'individuazione continua raccoglie i metadati relativi alla configurazione e alle prestazioni del computer e può essere usata anche per ottenere l'inventario delle applicazioni installate e i ruoli e le funzionalità in esecuzione nei computer locali. Il dispositivo Azure Migrate raccoglie:

- Dettagli dei metadati di macchine virtuali, dischi e schede di rete

- Applicazioni installate che includono applicazioni e ruoli/funzionalità  

- Dati sulle prestazioni, tra cui utilizzo della CPU e della memoria, IOPS del disco e velocità effettiva

Esportare quindi l'elenco inventario applicazioni per individuare tutte le istanze SQL Server in esecuzione nei carichi di lavoro e usare lo strumento di valutazione Azure Migrate: database per comprenderne la conformità.

 ![Inventario delle applicazioni nel portale](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Esportazione inventario applicazioni](./media/concepts-migration-planning/application-inventory-export.png)

Insieme ai dati di individuazione dello strumento Server Assessment, usare i dati CMDB esistenti per creare la visualizzazione del server e del database e comprendere la distribuzione del server tra le business unit, i proprietari delle applicazioni, le aree geografiche e così via, che possono a sua volta contribuire alla priorità dei carichi di lavoro da migrare.

### <a name="what-dependencies-exist-between-workloads"></a>Quali dipendenze esistono tra i carichi di lavoro?

Dopo aver individuato i server, usare il mapping delle dipendenze senza agente per visualizzare e identificare le dipendenze tra server e le strategie di ottimizzazione per lo trasferimento di server interdipendenti in Azure. La visualizzazione consente di comprendere se alcuni computer sono in uso o se è possibile rimuoverne le autorizzazioni anziché eseguire la migrazione.  Assicurarsi di analizzare le dipendenze per assicurarsi che non venga lasciato nulla ed evitare interruzioni delle sorprese durante la migrazione. Una volta eseguito l'inventario delle applicazioni e il mapping delle dipendenze, è possibile creare gruppi con attendibilità elevata e iniziare a valutare i server.

 ![Mapping delle dipendenze](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Sono ottimizzati e dimensionati correttamente?

Poiché Azure offre la flessibilità necessaria per ridimensionare la capacità del cloud nel tempo, la migrazione è un'opportunità per ottimizzare le risorse di CPU e memoria allocate ai server. Creare una valutazione del gruppo identificata prima di per comprendere la cronologia delle prestazioni dei carichi di lavoro, che sarà molto importante nei consigli relativi a SKU e dischi delle VM con diritti in Azure.

## <a name="assess-your-readiness-for-migration"></a>Valutare la conformità per la migrazione

### <a name="readiness-and-suitability-analysis-for-azure"></a>Analisi della conformità e dell'idoneità per Azure
Esportare il report di valutazione delle VM di Azure e filtrare in base alle categorie di conformità seguenti per comprendere la conformità delle VM per Azure:

- **Pronto per Azure**: è possibile eseguire la migrazione di questi computer così come sono in Azure senza modifiche  

- Idoneo **per Azure in modo condizionale**: è possibile eseguire la migrazione di questi computer in Azure, ma sono necessarie modifiche minime in questi server in base alle indicazioni di correzione fornite nella valutazione

- **Non pronto per Azure**: non è possibile eseguire la migrazione di questi computer in Azure così come sono ed è necessario risolvere i problemi in base alle indicazioni di monitoraggio e aggiornamento prima della migrazione

- **Conformità sconosciuta**: Azure migrate non è in grado di determinare la conformità del computer a causa di metadati insufficienti

Usando le valutazioni del database, è possibile valutare la conformità per la migrazione dei dati di SQL Server al database SQL di Azure o alle istanze gestite di SQL di Azure. È possibile visualizzare la percentuale dello stato di conformità della migrazione per ogni istanza di SQL Server. Inoltre, per ogni istanza è possibile visualizzare la destinazione consigliata in Azure, i blocchi di migrazione potenziali, il numero di modifiche di rilievo, la conformità per il database SQL di Azure o la VM SQL di Azure e il livello di compatibilità. È possibile esaminare più a fondo per comprendere l'effetto dei blocchi di migrazione e le raccomandazioni per correggerli.

 ![Valutazioni del database](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Indicazioni sul dimensionamento

Dopo che la macchina virtuale è stata contrassegnata come pronta per Azure, la valutazione del server esegue consigli sul dimensionamento per identificare la VM di Azure e lo SKU del disco per le VM. È possibile scegliere di visualizzare la raccomandazione di ridimensionamento in base alla cronologia delle prestazioni (in modo da ottimizzare le risorse durante la migrazione) o in base alla configurazione locale senza considerare la cronologia delle prestazioni. Per i database, è possibile visualizzare le raccomandazioni relative allo SKU del database, al piano tariffario e al livello di calcolo nella valutazione del database.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Valutazioni di calcolo per ottenere i costi stimati per l'esecuzione dei carichi di lavoro in Azure

L'opzione di ridimensionamento a destra *basata sulle prestazioni* nelle valutazioni consente di ottimizzare i carichi di lavoro per Azure. Oltre a rightsizing, sono disponibili poche altre vie per ridurre i costi:

- **Istanze riservate**: con istanze riservate, è possibile ridurre significativamente i costi rispetto ai prezzi con pagamento in base al consumo con termini di 1 anno o di tre anni in Windows e Linux (VM)

- **Vantaggi di Azure Hybrid**: è possibile portare le licenze locali di Windows Server con Software Assurance in Azure e combinarle con le opzioni di istanze riservate

- **Offerta Enterprise Agreement (EA)**: il Enterprise Agreement offre risparmi predefiniti applicabili alla sottoscrizione

- **Offerte**: sono disponibili più offerte di Azure, ad esempio Sviluppo/test con pagamento in base al consumo e sviluppo/test Enterprise che fornisce tariffe inferiori per le macchine virtuali di sviluppo o test

- **Tempo di esecuzione macchina virtuale**: è possibile menzionare la durata in giorni al mese e ore al giorno durante l'esecuzione delle macchine virtuali di Azure per ridurre i costi (non applicabile a ri)

- **Area di destinazione**: è possibile creare più valutazioni in aree diverse da confrontare se la migrazione a una determinata area geografica può risultare più economica

- **Raccomandazioni basate sulle prestazioni**: come procedura consigliata, provare a usare le raccomandazioni per le macchine virtuali di Azure con diritti che consentiranno di risparmiare sui costi del cloud

### <a name="visualize-data"></a>Visualizzare i dati

È possibile visualizzare il report di valutazione del server con conformità di Azure e la distribuzione dei costi mensili nel portale, nonché esportare la valutazione per applicare più visualizzazioni sui dati di individuazione e valutazione per rendere più completo il piano di migrazione. È possibile creare più valutazioni per diverse combinazioni di proprietà e scegliere il set di proprietà che funzionano meglio per l'azienda.  

 ![Panoramica delle valutazioni](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Valutare Gap e potenziali blocchi

Durante la determinazione delle applicazioni e dell'infrastruttura sottostante da migrare, identificare i vincoli di tempo di inattività per queste applicazioni e cercare eventuali dipendenze operative tra le applicazioni e l'infrastruttura sottostante. Questa analisi può aiutarti a pianificare migrazioni che soddisfano l'obiettivo del tempo di ripristino (RTO) e assicurano una perdita di dati minima o pari a zero. Prima di eseguire la migrazione, si consiglia di esaminare e mitigare eventuali problemi di compatibilità o funzionalità non supportate che potrebbero bloccare la migrazione dei server e dei database SQL con il supporto del report di valutazione del server e Azure Migrate: raccomandazioni per la valutazione del database.

### <a name="first-workloads-to-target-and-approach"></a>Primi carichi di lavoro per la destinazione e l'approccio

Ora che sono state apportate tutte le informazioni fondamentali per la decisione di migrazione, è necessario definire le priorità per le applicazioni e i carichi di lavoro che devono essere migrati per primi. Sviluppare un approccio di "applicazione e apprendimento" per eseguire la migrazione delle applicazioni desiderate in modo sistematico e controllabile, in modo che sia possibile stirare eventuali difetti in questa strategia prima di intraprendere una migrazione su larga scala. È anche possibile usare fattori strategici come la complessità e il tempo per la migrazione, l'urgenza aziendale, l'ambiente di produzione e non di produzione, la conformità e i requisiti di sicurezza, le informazioni sulle applicazioni e così via per stabilire la priorità dei gruppi di applicazioni da migrare.

Alcune strategie di migrazione consigliate sono:

- **Classificare in ordine di priorità le vittorie rapide**: è possibile usare i report di valutazione per identificare i frutti a bassa impiccagione, inclusi i server e i database pronti per la migrazione ad Azure:
    - Idonea per Azure: esportare il report di valutazione e filtrare tutti i computer "pronti per Azure". Può trattarsi del primo gruppo di computer che è possibile spostare con lo strumento di migrazione Azure Migrate: Server.
    - Fine del supporto del sistema operativo: esportare il report di valutazione e filtrare tutti i computer che eseguono i sistemi operativi Windows Server 2008 e Windows Server 2008 R2. Questi SKU sono la fine del supporto e solo Azure ti offre tre anni di aggiornamenti della sicurezza gratuiti quando Esegui la migrazione in Azure. Quando si combinano, si Vantaggio Azure Hybrid e si usano istanze riservate, il salvataggio potrebbe essere molto più alto.
    - Migrazione di SQL Server: usare le raccomandazioni per la valutazione del database per eseguire la migrazione dei database pronti per i database SQL di Azure usando i Azure Migrate: migrazione del database e i database pronti per la macchina virtuale SQL di Azure con il Azure Migrate: migrazione del server.
    - Fine del supporto software: esportare l'inventario delle applicazioni e filtrare il software o le estensioni che potrebbero raggiungere la fine del supporto. È necessario assegnare priorità a queste applicazioni.
    - VM con provisioning eccessivo: esportare il report di valutazione e filtrare i computer con un basso utilizzo della CPU (%) e utilizzo della memoria (%).  È possibile usare questa opportunità per eseguire la migrazione a una macchina virtuale con diritti in Azure e salvare ciò che si è pagato per le risorse sottoutilizzate.
    - Vincoli di capacità: esportare il report di valutazione e filtrare i computer con un utilizzo elevato della CPU (%) e utilizzo della memoria (%).  È possibile impedire che le macchine virtuali sovraccaricate si rompano e aumentino le prestazioni eseguendone la migrazione in Azure e sfruttando la funzionalità di scalabilità automatica per soddisfare la domanda. È anche possibile esaminare il report di valutazione per comprendere i vincoli di archiviazione analizzando l'IOPS e la velocità effettiva del disco e individuare il tipo di disco consigliato più adatto alle proprie esigenze.

- **Inizia con le dimensioni ridotte, quindi**inizia con lo spostamento di applicazioni e carichi di lavoro con rischi minimi e meno complessi, per creare confidenza nella tua strategia di migrazione. Puoi anche intersecare le tue raccomandazioni per la valutazione del Azure Migrate con il repository CMDB delle organizzazioni per trovare ed eseguire la migrazione dei carichi di lavoro di ambienti di sviluppo/test nelle tue migrazioni pilota. Quando si esegue la migrazione dei carichi di lavoro di produzione, è possibile usare le informazioni di questi progetti pilota.  

- **Conformità ai requisiti normativi**e di settore: Azure mantiene il più ampio portfolio di conformità in tutto il settore, in termini di ampiezza e profondità delle offerte. È possibile usarlo per classificare in ordine di priorità le migrazioni in Azure ed essere conformi agli standard e alle leggi nazionali, regionali e specifici del settore. Ciò vale soprattutto per le organizzazioni che si occupano di informazioni cruciali per l'azienda o che si trovano in industrie altamente regolamentate, in cui gli standard e le normative si abbondano e, in alcuni casi, possono cambiare spesso, rendendolo difficile da gestire.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Finalizzare il piano di migrazione e prepararsi per la migrazione

Prima di finalizzare il piano di migrazione, assicurarsi che queste considerazioni principali sulla migrazione non intralcino la pianificazione della migrazione:

- Valutare i vincoli di larghezza di banda e latenza di rete che possono causare ritardi imprevisti e la velocità della replica di migrazione.

- Buffer nel tempo per eseguire test delle prestazioni e dell'accettazione dell'utente nelle applicazioni migrate o eseguire qualsiasi modifica di app post-migrazione, ad esempio l'aggiornamento delle stringhe di connessione del database e delle configurazioni del server Web, l'esecuzione di cutover e pulizia e così via.

- Esaminare le autorizzazioni di Azure consigliate e il modello di autorizzazione e i ruoli di accesso al server e al database necessari per la migrazione.

- Preparare l'organizzazione e garantire l'allineamento della forza lavoro alla trasformazione digitale. Una solida base di formazione è importante per il successo dell'organizzazione. Scopri la formazione gratuita disponibile in [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), inclusi i corsi su concetti di base di Azure, l'architettura della soluzione e la sicurezza. Incoraggiare il team a esplorare anche la [certificazione di Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   .  

- Se necessario, ottenere supporto per l'implementazione. Molte organizzazioni scelgono di aiutare l'esterno a supportare la migrazione nel cloud. Per passare ad Azure in modo rapido e sicuro con assistenza personalizzata, prendere in considerazione un [provider di servizi gestiti di esperti di Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   o un [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Consente di creare un piano di migrazione cloud efficace che include informazioni dettagliate sull'elenco o sui gruppi di applicazioni di cui si vuole eseguire la migrazione, i vincoli di disponibilità dell'app e del database e i tempi di inattività e le attività cardine per la migrazione. Il piano di migrazione dovrebbe anche considerare la durata della copia dei dati e includere un buffer prevedibile per le attività di test e cutover post-migrazione. Il test di post-migrazione deve includere i casi di utilizzo dei test funzionali, di integrazione, della sicurezza e delle prestazioni per garantire che le applicazioni migrate funzionino come previsto e che tutte le relazioni tra oggetti di database e dati siano state trasferite nel cloud.  

Usare questa analisi per creare una roadmap per la migrazione e dichiarare una finestra di manutenzione per eseguire la migrazione delle applicazioni e dei database con tempi di inattività minimi fino a zero e limitare il potenziale impatto operativo/aziendale durante la migrazione.  

È consigliabile testare sempre e continuare a usare la funzionalità di *migrazione di test* di Azure migrate prima di avviare le migrazioni con scalabilità completa in Azure. Questi dati reali consentono di stimare il tempo effettivo richiesto e apportare le modifiche necessarie al piano di migrazione. La migrazione dei test consente inoltre di individuare eventuali problemi potenziali con il piano di migrazione e correggerli prima che avvenga la migrazione effettiva.  

Quando si è pronti per eseguire la migrazione, usare *lo strumento di migrazione del server* di Azure migrate e il *servizio di migrazione dei dati* Azure migrate per un'esperienza di migrazione perfetta e integrata con il rilevamento end-to-end. Lo strumento di migrazione server supporta la migrazione di macchine virtuali e server ospitati in locale nel data center dei clienti o in qualsiasi altro cloud privato o pubblico, inclusi AWS, GCP e così via, con un tempo di inattività pari a circa zero. Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi.  

> [!NOTE]
> Per le macchine virtuali VMware, server Assessment usa il sistema operativo specificato per la macchina virtuale in server vCenter per gestire l'analisi del sistema operativo guest. Per le macchine virtuali Linux in esecuzione su VMware, attualmente non identifica la versione del kernel esatta del sistema operativo guest.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare il [percorso di migrazione cloud](/azure/architecture/cloud-adoption/getting-started/migrate)   nel Framework di adozione del cloud di Azure.
- [Inizia a usare](https://youtu.be/wFfq3YPxYHE) Azure migrate.
- Creare una valutazione per le macchine virtuali [VMware](tutorial-assess-vmware.md) o [Hyper-V](tutorial-assess-hyper-v.md).
