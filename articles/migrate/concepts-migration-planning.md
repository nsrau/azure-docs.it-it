---
title: Piano di migrazione della compilazione con Azure Migrate | Microsoft Docs
description: Vengono fornite informazioni aggiuntive sulla compilazione del piano di migrazione con Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: rajosh
ms.openlocfilehash: 8f37814e29ce0089c26e235123768296efc2c0b0
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504909"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Creare un piano di migrazione con Azure Migrate

Seguire questo articolo per creare il piano di migrazione in Azure con [Azure migrate](migrate-services-overview.md). 

## <a name="define-cloud-migration-goals"></a>Definire gli obiettivi di migrazione cloud

Prima di iniziare, la comprensione e la valutazione della [motivazione](/azure/cloud-adoption-framework/strategy/motivations) per il passaggio al cloud possono contribuire a un risultato aziendale riuscito. Come illustrato nel [Framework di adozione del cloud](/azure/cloud-adoption-framework), esistono diversi trigger e risultati.   

**Evento di business** | **Risultato della migrazione**
--- | ---
Uscita Data Center | Cost 
Unione, acquisizione o dismissione | Riduzione della complessità tecnica o fornitore
Riduzione delle spese di capitale | Ottimizzazione delle operazioni interne
Fine del supporto per le tecnologie mission-critical | Aumento della flessibilità aziendale
Risposta alle modifiche di conformità alle normative | Preparazione per nuove funzionalità tecniche
Nuovi requisiti di sovranità dei dati | Scalabilità per soddisfare le esigenze del mercato
Riduzione delle problematiche e miglioramenti della stabilità IT | Ridimensionamento per soddisfare le esigenze geografiche

Identificare le motivazioni consente di bloccare gli obiettivi di migrazione strategici. Il passaggio successivo consiste nell'identificare e pianificare un percorso di migrazione personalizzato per i carichi di lavoro. Lo strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) consente di valutare i carichi di lavoro locali e fornisce linee guida e strumenti per facilitare la migrazione.

## <a name="understand-your-digital-estate"></a>Informazioni sulle proprietà digitali

Per iniziare, è necessario identificare l'infrastruttura, le applicazioni e le dipendenze locali. Ciò consente di identificare i carichi di lavoro per la migrazione ad Azure e di raccogliere le proiezioni di costo ottimizzate. Lo strumento Server Assessment consente di identificare i carichi di lavoro in uso, le dipendenze tra i carichi di lavoro e l'ottimizzazione del carico di lavoro.

### <a name="workloads-in-use"></a>Carichi di lavoro in uso

Azure Migrate usa un appliance Azure Migrate leggero per eseguire l'individuazione senza agenti di VM VMware locali, VM Hyper-V, altre macchine virtuali e server fisici. L'individuazione continua raccoglie le informazioni di configurazione del computer e i metadati delle prestazioni, oltre ai dati dell'applicazione. Ecco cosa raccoglie l'appliance dai computer locali: 

- Metadati del computer, del disco e della scheda di interfaccia di rete.

- Applicazioni, ruoli e funzionalità installati.

- Dati sulle prestazioni, tra cui utilizzo della CPU e della memoria, IOPS del disco e velocità effettiva.

Dopo aver raccolto i dati, è possibile esportare l'elenco inventario applicazioni per trovare le app e SQL Server istanze in esecuzione nei computer. È possibile utilizzare lo strumento Azure Migrate: database assessment per comprendere SQL Server conformità.

 ![Inventario delle applicazioni nel portale](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Esportazione inventario applicazioni](./media/concepts-migration-planning/application-inventory-export.png)

Insieme ai dati individuati con lo strumento Server Assessment, è possibile utilizzare i dati del database di gestione della configurazione (CMDB) per creare una visualizzazione del server e del database e per comprendere come vengono distribuiti i server tra le business unit, i proprietari delle applicazioni, le aree geografiche e così via. Ciò consente di decidere quali carichi di lavoro assegnare le priorità per la migrazione. 

### <a name="dependencies-between-workloads"></a>Dipendenze tra i carichi di lavoro

Dopo l'individuazione del server, è possibile [analizzare le dipendenze](concepts-dependency-visualization.md), visualizzare e identificare le dipendenze tra server e strategie di ottimizzazione per lo trasferimento di server interdipendenti in Azure. La visualizzazione consente di comprendere se alcuni computer sono in uso o se possono essere rimossi, anziché essere migrati.  L'analisi delle dipendenze consente di garantire che non venga lasciato nulla e di sorprendere le interruzioni durante la migrazione. Con l'inventario delle applicazioni e l'analisi delle dipendenze, è possibile creare gruppi di server con attendibilità elevata e iniziare a valutarli.

 ![Mapping delle dipendenze](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Ottimizzazione e dimensionamento

Azure offre la flessibilità necessaria per ridimensionare la capacità del cloud nel tempo e la migrazione offre la possibilità di ottimizzare le risorse di CPU e memoria allocate ai server. La creazione di una valutazione nei server di identità consente di comprendere la cronologia delle prestazioni del carico di lavoro. Si tratta di un'operazione fondamentale per dimensionare correttamente gli SKU di VM di Azure e le raccomandazioni sui dischi in Azure.

## <a name="assess-migration-readiness"></a>Valutazione della conformità della migrazione


### <a name="readinesssuitability-analysis"></a>Analisi della conformità/idoneità

È possibile esportare il report di valutazione e filtrare le categorie per comprendere la conformità di Azure:

- **Pronto per Azure** : è possibile eseguire la migrazione dei computer così come sono in Azure, senza alcuna modifica. 
- Idoneo in modo **condizionale per Azure** : è possibile eseguire la migrazione dei computer in Azure, ma sono necessarie modifiche minime, in base alle indicazioni per la correzione fornite nella valutazione.
- **Non pronto per Azure** : non è possibile eseguire la migrazione dei computer in Azure così come sono. Prima di eseguire la migrazione, è necessario correggere i problemi in base alle indicazioni di correzione. 
- **Conformità sconosciuta** : Azure migrate non è in grado di determinare la conformità del computer a causa di metadati insufficienti.

Con le valutazioni dei database è possibile valutare la conformità del SQL Server Data estate per la migrazione al database SQL di Azure o alle istanze gestite di SQL di Azure. La valutazione Mostra la percentuale dello stato di conformità della migrazione per ogni istanza di SQL Server. Per ogni istanza è inoltre possibile visualizzare la destinazione consigliata in Azure, i blocchi potenziali per la migrazione, il numero di modifiche di rilievo, la conformità per il database SQL di Azure o la macchina virtuale SQL di Azure e un livello di compatibilità. È possibile esaminare più a fondo per comprendere l'effetto dei blocchi di migrazione e i suggerimenti per correggerli.

 ![Valutazioni del database](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Indicazioni sul dimensionamento

Dopo che un computer è stato contrassegnato come pronto per Azure, la valutazione del server apporta consigli sul dimensionamento che identificano lo SKU di VM di Azure e il tipo di disco per i computer. È possibile ottenere consigli sul dimensionamento in base alla cronologia delle prestazioni (per ottimizzare le risorse durante la migrazione) o in base alle impostazioni del computer locale senza cronologia delle prestazioni. In una valutazione del database, è possibile visualizzare le raccomandazioni per lo SKU del database, il piano tariffario e il livello di calcolo.  

### <a name="get-compute-costs"></a>Ottenere i costi di calcolo

L'opzione di dimensionamento in base alle prestazioni in Azure Migrate Assessment consente di dimensionare correttamente le VM e deve essere usata come procedura consigliata per l'ottimizzazione dei carichi di lavoro in Azure. Oltre al dimensionamento corretto, sono disponibili altre opzioni per il salvataggio dei costi di Azure: 

- **Istanze riservate** : con [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/), è possibile ridurre significativamente i costi rispetto ai [prezzi con pagamento in base](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)al consumo.
- **Vantaggio Azure Hybrid** : con [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)è possibile usare le licenze di Windows Server locali con le sottoscrizioni di Software Assurance attive o Linux in Azure e combinarle con le opzioni di istanze riservate.
- **Enterprise Agreement** : Azure [Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) può offrire risparmi per le sottoscrizioni e i servizi di Azure.
- **Offerte** : sono disponibili più [offerte di Azure](https://azure.microsoft.com/support/legal/offer-details/). Ad esempio, [sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/pricing/dev-test/)o [sviluppo/test Enterprise offerta](https://azure.microsoft.com/offers/ms-azr-0148p/), per offrire tariffe inferiori per le macchine virtuali di sviluppo e test
- Tempo di esecuzione della **macchina virtuale** : è possibile esaminare i giorni al mese e le ore al giorno in cui vengono eseguite le macchine virtuali di Azure. L'arresto dei computer quando non sono in uso può ridurre i costi (non applicabile per RIs).
- **Area di destinazione** : è possibile creare valutazioni in aree diverse, per stabilire se la migrazione a un'area specifica potrebbe essere più conveniente. 

### <a name="visualize-data"></a>Visualizzare i dati

È possibile visualizzare i report di valutazione del server (con le informazioni di conformità di Azure e la distribuzione dei costi mensili) nel portale. È anche possibile esportare la valutazione e arricchire il piano di migrazione con visualizzazioni aggiuntive. È possibile creare più valutazioni, con diverse combinazioni di proprietà, e scegliere il set di proprietà che funzionano meglio per l'azienda.  

 ![Panoramica delle valutazioni](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Valuta Gap/blocchi

Quando si rilevano le app e i carichi di lavoro di cui si vuole eseguire la migrazione, si identificano i vincoli di tempo di inattività e si cercano eventuali dipendenze operative tra le app e l'infrastruttura sottostante. Questa analisi consente di pianificare le migrazioni che soddisfano l'obiettivo del tempo di ripristino (RTO) e assicurano la perdita di dati minima o pari a zero. Prima di eseguire la migrazione, è consigliabile rivedere e mitigare eventuali problemi di compatibilità o funzionalità non supportate, che potrebbero bloccare la migrazione del database SQL o del server. Il report di valutazione del server di Azure Migrate e la valutazione Azure Migrate database possono risultare utili per questa operazione. 

### <a name="prioritize-workloads"></a>Assegnare priorità ai carichi di lavoro

Dopo aver raccolto le informazioni relative all'inventario, è possibile identificare le app e i carichi di lavoro di cui eseguire la migrazione per primi. Sviluppare un approccio di "applicazione e apprendimento" per eseguire la migrazione delle app in modo sistematico e controllabile, in modo che sia possibile stirare eventuali difetti prima di avviare una migrazione su larga scala.

Per definire la priorità dell'ordine di migrazione, è possibile usare fattori strategici come la complessità, il time-to-migrate, l'urgenza aziendale, le considerazioni di produzione/non produzione, la conformità, i requisiti di sicurezza, le informazioni sulle applicazioni e così via. 

Alcuni consigli:

- **Assegnazione delle priorità a WINS rapido** : usare i report di valutazione per identificare i frutti a bassa impiccagione, inclusi i server e i database completamente pronti e richiedere il minimo sforzo per eseguire la migrazione ad Azure. La tabella riepiloga alcuni modi per eseguire questa operazione.

    **State** | **Azione**
    --- | ---
    **VM pronte per Azure** | Esportare il report di valutazione e filtrare tutti i computer con stato *pronto per Azure*. Potrebbe trattarsi del primo gruppo di computer che si sollevano e passano ad Azure, usando lo strumento di [migrazione Azure migrate: server](migrate-services-overview.md#azure-migrate-server-migration-tool) .
    **Sistemi operativi per la fine del supporto** | Esportare il report di valutazione e filtrare tutti i computer che eseguono Windows Server 2008 R2/Windows Server 2008. Questi sistemi operativi sono al termine del supporto e solo Azure fornisce tre anni di aggiornamenti della sicurezza gratuiti quando si esegue la migrazione in Azure. Se si combinano Vantaggio Azure Hybrid e si utilizza RIs, il risparmio potrebbe essere molto più alto.
    **Migrazione di SQL Server** | Usare le raccomandazioni per la valutazione del database per eseguire la migrazione dei database pronti per il database SQL di Azure, usando lo strumento di migrazione Azure Migrate: database. Eseguire la migrazione dei database pronti per la macchina virtuale SQL di Azure usando lo strumento di migrazione Azure Migrate: Server.
    **Software di fine supporto** | Esportare l'inventario delle applicazioni e filtrare eventuali software/estensioni che potrebbero raggiungere la fine del supporto. Assegnare priorità a queste applicazioni per la migrazione.
    **Computer sottoposti a provisioning** | Esportare il report di valutazione e filtrare per i computer con utilizzo CPU ridotto (%) e utilizzo della memoria (%).  Esegui la migrazione a una macchina virtuale di Azure di dimensioni appropriate e Risparmia sui costi per le risorse sottoutilizzate.
    **Computer con provisioning eccessivo** | Esportare il report di valutazione e il filtro per i computer con utilizzo elevato della CPU (%) e utilizzo della memoria (%).  Risolvere i vincoli di capacità, impedire l'esecuzione di interruzioni delle macchine virtuali sovraccaricate e migliorare le prestazioni eseguendo la migrazione di questi computer in Azure. In Azure usare le funzionalità di scalabilità automatica per soddisfare la domanda.<br/><br/> Analizzare i report di valutazione per esaminare i vincoli di archiviazione. Analizzare IOPS e velocità effettiva del disco e il tipo di disco consigliato.

- **Inizia da piccolo, quindi vai a grande** : per iniziare, spostando le app e i carichi di lavoro che presentano rischi e complessità minimi, è possibile creare confidenza nella strategia di migrazione. Analizza Azure Migrate raccomandazioni di valutazione insieme al repository CMDB per trovare ed eseguire la migrazione di carichi di lavoro di sviluppo/test che potrebbero essere candidati per le migrazioni pilota. Il feedback e le informazioni sulle migrazioni pilota possono essere utili quando si inizia a migrare i carichi di lavoro di produzione.  
- **Conformità** : Azure mantiene il più ampio portfolio di conformità nel settore, in termini di ampiezza e profondità delle offerte. Usare i requisiti di conformità per classificare in ordine di priorità le migrazioni, in modo che le app e i carichi di lavoro siano conformi agli standard e alle leggi nazionali, regionali e specifici del settore. Ciò vale soprattutto per le organizzazioni che gestiscono un processo cruciale per l'azienda, che contengono informazioni riservate o che si trovano in settori altamente regolamentati. In questi tipi di organizzazioni, gli standard e le normative si abbondano e possono cambiare spesso e risultare difficili da gestire.  

## <a name="finalize-the-migration-plan"></a>Finalizzare il piano di migrazione

Prima di finalizzare il piano di migrazione, assicurarsi di prendere in considerazione e mitigare gli altri blocchi potenziali, come indicato di seguito: 

- **Requisiti di rete** : valutare la larghezza di banda di rete e i vincoli di latenza, che potrebbero causare ritardi imprevisti e problemi di velocità di replica della migrazione.
- **Modifiche di test/post-migrazione** : consentono a un buffer di tempo di eseguire test delle prestazioni e di accettazione dell'utente per le app migrate oppure di configurare/modificare le app dopo la migrazione, ad esempio l'aggiornamento delle stringhe di connessione al database, la configurazione dei server Web, l'esecuzione di operazioni di troncamento/pulizia e così via.
- **Autorizzazioni** : esaminare le autorizzazioni di Azure consigliate e i ruoli di accesso server/database e le autorizzazioni necessarie per la migrazione.
- **Training** : preparare l'organizzazione per la trasformazione digitale. Una solida base di formazione è importante per il successo dell'organizzazione. Scopri la formazione gratuita su [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), inclusi i corsi sui concetti di base di Azure, le architetture delle soluzioni e la sicurezza. Invita il team a esplorare le [certificazioni di Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF).  
- **Supporto dell'implementazione** : se necessario, ottenere supporto per l'implementazione. Molte organizzazioni scelgono di aiutare l'esterno a supportare la migrazione nel cloud. Per passare ad Azure in modo rapido e sicuro con assistenza personalizzata, prendere in considerazione un [provider di servizi gestiti di esperti di Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)o [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Creare un piano di migrazione cloud efficace che includa informazioni dettagliate sulle app di cui si vuole eseguire la migrazione, disponibilità di app/database, vincoli di tempo di inattività e attività cardine di migrazione. Il piano prende in considerazione la durata della copia dei dati e include un buffer realistico per i test post-migrazione e le attività di taglio. 

Un piano di test di post-migrazione deve includere i test funzionali, di integrazione, della sicurezza e delle prestazioni e i casi di utilizzo, per garantire che le app migrate funzionino come previsto e che tutti gli oggetti di database e le relazioni tra i dati vengano trasferiti correttamente nel cloud.  

È possibile creare una roadmap per la migrazione e dichiarare una finestra di manutenzione per eseguire la migrazione delle app e dei database con tempi di inattività minimi o pari a zero e limitare il potenziale impatto operativo e aziendale durante la migrazione.  

## <a name="migrate"></a>Migrate

È consigliabile eseguire una migrazione di test in Azure Migrate, prima di avviare una migrazione su larga scala. Una migrazione di test consente di stimare il tempo necessario e ottimizzare il piano di migrazione. Offre l'opportunità di individuare eventuali problemi potenziali e risolverli prima della migrazione completa.

Quando si è pronti per la migrazione, usare lo strumento di migrazione Azure Migrate: server e il servizio di migrazione dei dati di Azure (DMS), per un'esperienza di migrazione semplice e integrata, con rilevamento end-to-end.

- Con lo strumento di migrazione server è possibile eseguire la migrazione di macchine virtuali e server locali o di macchine virtuali situate in un altro cloud privato o pubblico (incluso AWS, GCP) con un tempo di inattività di circa zero.
- Azure DMS fornisce un servizio completamente gestito progettato per consentire migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure, con tempi di inattività minimi.  

## <a name="next-steps"></a>Passaggi successivi

- Esaminare il [percorso di migrazione cloud](/azure/architecture/cloud-adoption/getting-started/migrate)   nel Framework di adozione del cloud di Azure.
- Ottenere una [rapida panoramica](migrate-services-overview.md) delle Azure migrate e guardare un [video](https://youtu.be/wFfq3YPxYHE)introduttivo.
- Altre informazioni sulla valutazione delle macchine virtuali per la migrazione alle [macchine virtuali di Azure](concepts-assessment-calculation.md).
