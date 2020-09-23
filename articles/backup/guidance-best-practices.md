---
title: Materiale sussidiario e procedure consigliate
description: Scopri le procedure consigliate e le linee guida per il backup del carico di lavoro cloud e locale nel cloud
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: f999c568dda6eae60f3060cc4672eccaf06541c1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985516"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Eseguire il backup di carichi di lavoro cloud e locali nel cloud

## <a name="introduction"></a>Introduzione

Backup di Azure protegge completamente gli asset di dati in Azure tramite una soluzione semplice, sicura ed economica che richiede l'infrastruttura zero. Si tratta di una soluzione di protezione dei dati incorporata Azure's per un'ampia gamma di carichi di lavoro. Consente di proteggere i carichi di lavoro mission-critical in esecuzione nel cloud e garantisce che i backup siano sempre disponibili e gestiti su larga scala nell'intero patrimonio di backup.

### <a name="intended-audience"></a>Destinatari

I destinatari primari di questo articolo sono gli amministratori IT e delle applicazioni, nonché gli implementatori di organizzazioni di grandi e medie dimensioni che desiderano apprendere le funzionalità della tecnologia di protezione dei dati incorporata di Azure, backup di Azure e come implementare in modo efficiente soluzioni che proteggono meglio le distribuzioni. L'articolo presuppone che l'utente abbia familiarità con le tecnologie di base di Azure, i concetti relativi alla protezione dei dati e abbia esperienza con una soluzione di backup. Le linee guida descritte in questo articolo possono semplificare la progettazione della soluzione di backup in Azure usando modelli stabiliti ed evitare problemi noti.

### <a name="how-this-article-is-organized"></a>Organizzazione di questo articolo

Sebbene sia facile iniziare a proteggere l'infrastruttura e le applicazioni in Azure, quando si garantisce che le risorse di Azure sottostanti siano configurate correttamente e utilizzate in modo ottimale, è possibile accelerare il time-to-value. Questo articolo illustra una breve panoramica delle considerazioni sulla progettazione e delle linee guida per la configurazione ottimale della distribuzione di backup di Azure. Esamina i componenti di base (ad esempio, l'insieme di credenziali dei servizi di ripristino, i criteri di backup) e i concetti (ad esempio, la governance) e come pensarli e le relative funzionalità con collegamenti alla documentazione dettagliata del prodotto.

## <a name="architecture"></a>Architettura

![Architettura di Backup di Azure](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Carichi di lavoro

Backup di Azure consente la protezione dei dati per diversi carichi di lavoro (in locale e nel cloud). Si tratta di un meccanismo di protezione dati incorporato sicuro e affidabile in Azure. Può ridimensionare senza problemi la protezione tra più carichi di lavoro senza alcun sovraccarico di gestione. Sono disponibili anche più canali di automazione per abilitare questa operazione (tramite PowerShell, l'interfaccia della riga di comando, i modelli di Azure Resource Manager e le API REST).

* **Archiviazione scalabile, durevole e sicura-** Backup di Azure Usa archiviazione BLOB affidabile con funzionalità di sicurezza e disponibilità elevata integrate. È possibile scegliere le archiviazioni con ridondanza locale, GRS o RA-GRS per i dati di backup.  

* **Integrazione del carico di lavoro nativo-** Backup di Azure offre l'integrazione nativa con i carichi di lavoro di Azure (VM, SAP HANA, SQL in macchine virtuali di Azure e persino File di Azure) senza la necessità di gestire l'automazione o l'infrastruttura per distribuire gli agenti, scrivere nuovi script o eseguire il provisioning dell'archiviazione.

### <a name="data-plane"></a>Piano dati

* **Gestione automatica dell'archiviazione** : backup di Azure automatizza il provisioning e la gestione degli account di archiviazione per i dati di backup per garantire la scalabilità Man mano che i dati di backup aumentano.

* **Protezione da eliminazioni dannose:** Proteggi da eventuali tentativi accidentali e dannosi di eliminare i backup tramite l'eliminazione temporanea dei backup. I dati di backup eliminati vengono archiviati per 14 giorni senza costi aggiuntivi e consentono il ripristino da questo stato.

* **Backup crittografati sicuri-** Backup di Azure garantisce che i dati di backup vengano archiviati in modo sicuro, sfruttando le funzionalità di sicurezza predefinite della piattaforma Azure, ad esempio RBAC e la crittografia.

* **Gestione del ciclo di vita dei dati di backup-** Backup di Azure pulisce automaticamente i dati di backup meno recenti per conformarsi ai criteri di conservazione. È anche possibile eseguire la suddivisione in livelli dei dati dall'archiviazione operativa all'archiviazione dell'insieme di credenziali.

### <a name="management-plane"></a>Piano di gestione

* **Controllo degli accessi** : gli insiemi di credenziali (servizi di ripristino e insiemi di credenziali di backup) forniscono le funzionalità di gestione e sono accessibili tramite il portale di Azure, il centro di backup, i dashboard dell'insieme di credenziali, l'SDK, l'interfaccia della riga di comando e persino Si tratta anche di un limite RBAC, che consente di limitare l'accesso ai backup solo agli amministratori di backup autorizzati.

* **Gestione dei criteri** : i criteri di backup di Azure in ogni insieme di credenziali definiscono quando devono essere attivati i backup e per quanto tempo devono essere conservati. È anche possibile gestire questi criteri e applicarli tra più elementi.

* **Monitoraggio e creazione di report** : backup di Azure si integra con log Analytics e offre la possibilità di visualizzare i report anche tramite cartelle di lavoro.

* **Gestione snapshot** : backup di Azure acquisisce snapshot per alcuni carichi di lavoro nativi di Azure (vm e file di Azure), gestisce questi snapshot e consente ripristini veloci da essi. Questa opzione riduce drasticamente il tempo necessario per ripristinare i dati nella risorsa di archiviazione originale.

## <a name="vault-considerations"></a>Considerazioni sull'insieme di credenziali

Backup di Azure usa gli insiemi di credenziali (servizi di ripristino e insiemi di credenziali di backup) per orchestrare e gestire i backup. nonché per archiviare i dati sottoposti a backup. La progettazione di insiemi di credenziali efficace consente alle organizzazioni di stabilire una struttura per organizzare e gestire asset di backup in Azure per supportare le priorità aziendali. Quando si crea un insieme di credenziali, tenere presenti le linee guida seguenti:  

### <a name="align-to-subscription-design-strategy"></a>Allinea alla strategia di progettazione della sottoscrizione

Dato che l'insieme di credenziali è limitato a una sottoscrizione, adattarsi alla progettazione dell'insieme di credenziali per soddisfare la strategia di progettazione della sottoscrizione, ad esempio la *strategia di categoria dell'applicazione* , in cui le sottoscrizioni sono separate in base a applicazioni o servizi specifici o lungo le linee degli archetipi delle applicazioni. Per altre informazioni, vedere questo [articolo](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Insieme di credenziali singolo o multiplo

È possibile usare un singolo insieme di credenziali o più insiemi di credenziali per organizzare e gestire il backup. Considerare le linee guida seguenti:

* Se i carichi di lavoro sono tutti gestiti da una singola sottoscrizione e una singola risorsa, è possibile usare un unico insieme di credenziali per monitorare e gestire il proprio patrimonio di backup.

* Se i carichi di lavoro vengono distribuiti tra le sottoscrizioni, è possibile creare più insiemi di credenziali, uno o più per sottoscrizione.
  * Backup Center consente di avere un unico riquadro di vetro per gestire tutte le attività correlate al backup. [Altre informazioni]()sono disponibili qui.
  * È possibile personalizzare le visualizzazioni con i modelli di cartella di lavoro. Esplora backup è un modello di questo tipo per le macchine virtuali di Azure. [Altre informazioni](monitor-azure-backup-with-backup-explorer.md)sono disponibili qui.
  * Se sono necessari criteri coerenti tra gli insiemi di credenziali, è possibile usare i criteri di Azure per propagare i criteri di backup tra più insiemi di credenziali. È possibile scrivere una [definizione di criteri di Azure](../governance/policy/concepts/definition-structure.md) personalizzata che usa l'effetto ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) per propagare un criterio di backup tra più insiemi di credenziali. È possibile assegnare questa definizione di criteri di Azure a un particolare ambito (sottoscrizione o RG), in modo che distribuisca una risorsa "criterio di backup" in tutti gli insiemi di [credenziali](../governance/policy/assign-policy-portal.md) dei servizi di ripristino nell'ambito dell'assegnazione di criteri di Azure. Le impostazioni dei criteri di backup (ad esempio frequenza di backup, conservazione e così via) devono essere specificate dall'utente come parametri nell'assegnazione di criteri di Azure.

* Con l'aumentare del footprint aziendale, è possibile spostare i carichi di lavoro tra le sottoscrizioni per i motivi seguenti: allinea in base ai criteri di backup, consolida gli insiemi di credenziali, compromessi con ridondanza inferiore per risparmiare sui costi (passa da GRS a con ridondanza locale).  Backup di Azure supporta lo trasferimento di un insieme di credenziali di servizi di ripristino tra sottoscrizioni di Azure o a un altro gruppo di risorse nella stessa sottoscrizione. [Altre informazioni](backup-azure-move-recovery-services-vault.md)sono disponibili qui.

### <a name="review-default-settings"></a>Verifica impostazioni predefinite

Verificare le impostazioni predefinite per il tipo di replica di archiviazione e le impostazioni di sicurezza per soddisfare i requisiti prima di configurare i backup nell'insieme di credenziali.

* Per impostazione predefinita, il *tipo di replica di archiviazione* è impostato su con ridondanza geografica (GRS). Dopo aver configurato il backup, l'opzione per la modifica è disabilitata. Per esaminare e modificare le impostazioni, seguire [questa](backup-create-rs-vault.md#set-storage-redundancy) procedura.

* Per impostazione predefinita, l' *eliminazione* temporanea è abilitata negli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose. Per esaminare e modificare le impostazioni, seguire [questa](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) procedura.

* Il *ripristino tra aree* consente di ripristinare le macchine virtuali di Azure in un'area secondaria, ovvero un'area abbinata ad Azure. Questa opzione consente di eseguire esercitazioni per soddisfare i requisiti di controllo o conformità e di ripristinare la macchina virtuale o il relativo disco in caso di emergenza nell'area primaria. CRR è una funzionalità di consenso esplicito per qualsiasi insieme di credenziali GRS. [Altre informazioni](backup-create-rs-vault.md#set-cross-region-restore)sono disponibili qui.

* Prima di completare la progettazione dell'insieme di credenziali, esaminare le [matrici di supporto](backup-support-matrix.md#vault-support) dell'insieme di credenziali per comprendere i fattori che potrebbero influenzare o limitare le scelte di progettazione.

## <a name="backup-policy-considerations"></a>Considerazioni sui criteri di backup

Il criterio di backup di Azure prevede due componenti: *pianificazione* (quando eseguire il backup) e conservazione (tempo di *conservazione* del backup). È possibile definire i criteri in base al tipo di dati di cui viene eseguito il backup, ai requisiti di RTO/RPO, alle esigenze di conformità operativa o normativa e al tipo di carico di lavoro (ad esempio, VM, database, file). [Altre informazioni](backup-architecture.md#backup-policy-essentials)sono disponibili qui.

Quando si creano i criteri di backup, tenere presenti le linee guida seguenti:

### <a name="schedule-considerations"></a>Considerazioni sulla pianificazione

* Si consiglia di raggruppare le macchine virtuali che richiedono la stessa ora di inizio della pianificazione, la frequenza e le impostazioni di conservazione all'interno di un singolo criterio.

* Verificare che l'ora di inizio pianificata per il backup sia durante il periodo di tempo applicazione di produzione non Peak.

* Per distribuire il traffico di backup, provare a eseguire il backup di macchine virtuali diverse in momenti diversi del giorno e verificare che i tempi non si sovrappongano.

### <a name="retention-considerations"></a>Considerazioni sulla conservazione

* La conservazione a breve termine può essere "minutes" o "Daily". La conservazione per i punti di backup "settimanali", "mensili" o "annuali" viene definita conservazione a lungo termine.

* Conservazione a lungo termine:
  * Pianificato (requisiti di conformità): se si sa in anticipo che i dati sono necessari anni dall'ora corrente, usare la conservazione a lungo termine.
  * Non pianificato (requisito su richiesta): se non si conosce in anticipo, usare è possibile usare su richiesta con impostazioni di conservazione personalizzate specifiche. queste impostazioni di conservazione personalizzate non sono interessate dalle impostazioni dei criteri.

* Backup su richiesta con conservazione personalizzata: se è necessario eseguire un backup non pianificato tramite criteri di backup, è possibile usare un backup su richiesta. Questo può essere utile per l'esecuzione di backup che non rientrano nel backup pianificato o per l'esecuzione di backup granulari (ad esempio, più backup di macchine virtuali IaaS al giorno perché il backup pianificato consente un solo backup al giorno). È importante notare che i criteri di conservazione definiti nei criteri pianificati non si applicano ai backup su richiesta.

### <a name="optimize-backup-policy"></a>Ottimizzare i criteri di backup

* Con la modifica dei requisiti aziendali, potrebbe essere necessario estendere o ridurre la durata della conservazione. Quando si esegue questa operazione, è possibile prevedere quanto segue:  
  * Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati per essere mantenuti conformi al nuovo criterio.
  * Se la conservazione è ridotta, i punti di ripristino vengono contrassegnati per l'eliminazione nel processo di pulizia successivo e successivamente eliminati.
  * Le regole di conservazione più recenti si applicano a tutti i punti di conservazione (esclusi i punti di conservazione su richiesta). Quindi, se il periodo di memorizzazione viene esteso (ad esempio a 100 giorni), quando viene eseguito il backup, seguito dalla riduzione della conservazione (ad esempio da 100 a sette giorni), tutti i dati di backup verranno conservati in base all'ultimo periodo di conservazione specificato (ovvero 7 giorni).

* Backup di Azure offre la flessibilità necessaria per *arrestare la protezione e la gestione dei backup*:
  * *Arrestare la protezione e conservare i dati di backup*. Se si sta disattivando o rimuovendo le autorizzazioni per l'origine dati (macchina virtuale, applicazione), ma è necessario conservare i dati a scopo di controllo o di conformità, è possibile usare questa opzione per arrestare tutti i processi di backup futuri che proteggono l'origine dati e conservano i punti di ripristino di cui è stato eseguito il backup. È quindi possibile ripristinare o riprendere la protezione della macchina virtuale.
  * *Arrestare la protezione ed eliminare i dati di backup*. Questa opzione consente di arrestare tutti i processi di backup futuri dalla protezione della macchina virtuale ed eliminare tutti i punti di ripristino. Non sarà possibile ripristinare la macchina virtuale né usare l'opzione Riprendi backup.

  * Se si riprende la protezione (di un'origine dati arrestata con Mantieni dati), verranno applicate le regole di conservazione. Tutti i punti di ripristino scaduti verranno rimossi (all'ora pianificata).

* Prima di completare la progettazione dei criteri, è importante tenere presente i fattori seguenti che potrebbero influenzare le scelte di progettazione.
  * Un criterio di backup è limitato a un insieme di credenziali.
  * È previsto un limite per il numero di elementi per criterio (ad esempio, 100 VM). Per la scalabilità, è possibile creare criteri duplicati con le stesse pianificazioni o con diverse pianificazioni.
  * Non è possibile eliminare selettivamente specifici punti di ripristino.
  * Non è possibile disabilitare completamente il backup pianificato e proteggere l'origine dati in uno stato protetto. Il backup meno frequente che è possibile configurare con i criteri prevede un backup pianificato settimanale. Un'alternativa consiste nell'arrestare la protezione con Mantieni dati e abilitare la protezione ogni volta che si vuole eseguire un backup, eseguire un backup su richiesta e quindi disabilitare la protezione, ma mantenere i dati di backup. [Altre informazioni](backup-azure-manage-vms.md#stop-protecting-a-vm)sono disponibili qui.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Per semplificare la protezione dei dati di backup e soddisfare le esigenze di sicurezza dell'azienda, backup di Azure fornisce garanzie di riservatezza, integrità e disponibilità contro attacchi intenzionali e abusi di dati e sistemi preziosi. Prendere in considerazione le seguenti linee guida di sicurezza per la soluzione di backup di Azure:

### <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

* Il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente la gestione degli accessi con granularità fine, la separazione dei compiti all'interno del team e la concessione della quantità di accesso agli utenti necessari per svolgere i propri processi. [Altre informazioni](backup-rbac-rs-vault.md)sono disponibili qui.

* Backup di Azure offre tre ruoli predefiniti per controllare le operazioni di gestione dei backup: collaboratori, operatori e lettori di backup. [Altre informazioni](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)sono disponibili qui.

* Backup di Azure include diversi controlli di sicurezza incorporati nel servizio per prevenire, rilevare e rispondere alle vulnerabilità di sicurezza (altre informazioni)

* Gli account di archiviazione usati dagli insiemi di credenziali dei servizi di ripristino sono isolati e non è possibile accedervi dagli utenti per scopi dannosi. L'accesso è consentito solo tramite le operazioni di gestione di Backup di Azure, ad esempio il ripristino.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Crittografia dei dati in transito e inattivi

La crittografia protegge i dati e consente di soddisfare gli obblighi di sicurezza e conformità dell'organizzazione.

* All'interno di Azure i dati in transito tra Archiviazione di Azure e l'insieme di credenziali sono protetti con HTTPS. Questi dati rimangono nella rete di Azure.

* I dati di backup vengono crittografati automaticamente usando chiavi gestite da Microsoft. In alternativa, è possibile usare le proprie chiavi, note anche come [chiavi gestite dal cliente](encryption-at-rest-with-cmk.md).

* Backup di Azure supporta il backup e il ripristino delle VM di Azure i cui dischi del sistema operativo e dei dati sono crittografati con Crittografia dischi di Azure. [Altre informazioni](backup-azure-vms-encryption.md)sono disponibili qui.

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Protezione dei dati di backup da eliminazioni accidentali

Backup di Azure offre funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione. Con l'eliminazione temporanea, se un utente elimina il backup (di una macchina virtuale, SQL Server database, la condivisione file di Azure SAP HANA database), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione dei dati di backup nello stato "eliminazione temporanea" non comportano alcun costo. [Altre informazioni](backup-azure-security-feature-cloud.md)sono disponibili qui.

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoraggio e avvisi di attività sospette

Backup di Azure offre funzionalità di monitoraggio e avviso predefinite per visualizzare e configurare le azioni per gli eventi correlati a Backup di Azure. [Altre informazioni](security-overview.md#monitoring-and-alerts-of-suspicious-activity)sono disponibili qui.

### <a name="security-features-to-help-protect-hybrid-backups"></a>Funzionalità di sicurezza per la protezione dei backup ibridi

Il servizio Backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS, Microsoft Azure Recovery Services) per eseguire il backup e il ripristino di file, cartelle e dello stato del volume o del sistema da un computer locale ad Azure. MARS offre ora funzionalità di sicurezza: una passphrase da crittografare prima di caricare e decrittografare dopo il download da backup di Azure, i dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione e l'operazione critica (es. la modifica di una passphrase può essere eseguita solo dagli utenti che hanno credenziali di Azure valide. [Altre informazioni](backup-azure-security-feature.md)sono disponibili qui.

## <a name="network-considerations"></a>Considerazioni per la rete

Backup di Azure richiede lo spostamento dei dati dal carico di lavoro all'insieme di credenziali dei servizi di ripristino. Backup di Azure offre diverse funzionalità per proteggere i dati di backup dall'esposizione involontaria, ad esempio un attacco man-in-the-Middle sulla rete. Considerare le linee guida seguenti:

### <a name="internet-connectivity"></a>Connettività Internet

* *Backup di macchine* virtuali di Azure: tutte le comunicazioni e il trasferimento dei dati necessari tra archiviazione e il servizio backup di Azure avvengono all'interno della rete di Azure senza dover accedere alla rete virtuale. Quindi, il backup delle macchine virtuali di Azure inserite all'interno di reti protette non richiede l'accesso a qualsiasi IP o FQDN.

* *SAP Hana database in una macchina virtuale di Azure, SQL Server database in una macchina virtuale di Azure* : richiede la connettività al servizio backup di Azure, archiviazione di azure e Azure Active Directory. Questa operazione può essere eseguita usando endpoint privati oppure consentendo l'accesso agli indirizzi IP pubblici o FQDN richiesti. Non consentire una connettività corretta ai servizi di Azure necessari può causare errori nelle operazioni quali l'individuazione del database, la configurazione del backup, l'esecuzione di backup e il ripristino dei dati. Per informazioni complete sulla rete quando si usano i tag NSG, il firewall di Azure e il proxy HTTP, vedere questi articoli relativi a [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) e [SAP Hana](./backup-azure-sap-hana-database.md#establish-network-connectivity) .

* *Ibrido* : l'agente MARS (servizi di ripristino di Microsoft Azure) richiede l'accesso alla rete per tutte le operazioni critiche, ovvero installazione, configurazione, backup e ripristino. L'agente MARS può connettersi al servizio backup di Azure tramite [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) usando il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft, usando [endpoint privati](install-mars-agent.md#private-endpoints) o tramite [proxy/firewall con controlli di accesso appropriati](install-mars-agent.md#verify-internet-access).

### <a name="private-endpoints-for-azure-backup"></a>Endpoint privati per backup di Azure

[Endpoint privato](../private-link/private-endpoint-overview.md) di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio basato sul collegamento privato di Azure. Backup di Azure consente di eseguire il backup e il ripristino in modo sicuro dei dati dagli insiemi di credenziali dei servizi di ripristino usando endpoint privati.

* Quando si abilitano endpoint privati per l'insieme di credenziali, vengono usati solo per il backup e il ripristino dei carichi di lavoro SQL e SAP HANA in una macchina virtuale di Azure e i backup dell'agente MARS.  È anche possibile usare l'insieme di credenziali per il backup di altri carichi di lavoro (non richiedono endpoint privati). Oltre al backup di carichi di lavoro SQL e SAP HANA e al backup con l'agente MARS, gli endpoint privati vengono usati anche per eseguire il ripristino dei file nel caso di backup di macchine virtuali di Azure. [Altre informazioni](private-endpoints.md#recommended-and-supported-scenarios)sono disponibili qui.

* Azure Active Directory attualmente non supporta endpoint privati. Per questo motivo, gli IP e i nomi di dominio completi necessari per Azure Active Directory dovranno essere consentiti per l'accesso in uscita dalla rete protetta durante l'esecuzione del backup di database in macchine virtuali di Azure e di backup con l'agente MARS. È anche possibile usare i tag NSG e i tag del firewall di Azure per consentire l'accesso ai Azure AD, come applicabile. Altre informazioni sui prerequisiti sono disponibili [qui](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Considerazioni sulla governance

La governance in Azure viene implementata principalmente con [criteri di Azure](../governance/policy/overview.md) e [Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md). [Criteri di Azure](../governance/policy/overview.md) consente di creare, assegnare e gestire le definizioni dei criteri per applicare le regole alle risorse. Questa funzionalità consente di mantenere tali risorse conformi agli standard dell'azienda. [Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md) consente di tenere traccia dell'uso del cloud e delle spese per le risorse di Azure e altri provider di servizi cloud. Inoltre, gli strumenti seguenti, ad esempio [Azure Price Calculator](https://azure.microsoft.com/pricing/calculator/) e [Azure Advisor](../advisor/advisor-overview.md)  svolgono un ruolo importante nel processo di gestione dei costi.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Backup di Azure supporta due scenari principali tramite criteri predefiniti di Azure

* Assicurarsi che i computer aziendali critici appena creati vengano sottoposti a backup automatico con le impostazioni di conservazione corrette. Backup di Azure fornisce un criterio predefinito (usando criteri di Azure) che può essere assegnato a tutte le macchine virtuali di Azure in una posizione specificata all'interno di una sottoscrizione o di un gruppo di risorse. Quando questo criterio viene assegnato a un determinato ambito, tutte le nuove macchine virtuali create in tale ambito vengono automaticamente configurate per il backup in un insieme di credenziali esistente nello stesso percorso e nella stessa sottoscrizione. L'utente può specificare l'insieme di credenziali e i criteri di conservazione a cui devono essere associate le macchine virtuali di cui è stato eseguito il backup. [Altre informazioni](backup-azure-auto-enable-backup.md)sono disponibili qui.

* Assicurarsi che la diagnostica sia abilitata per gli insiemi di credenziali appena creati per supportare i report. Spesso, l'aggiunta manuale di un'impostazione di diagnostica per ogni insieme di credenziali può essere un'attività complessa. Inoltre, per qualsiasi nuovo insieme di credenziali creato è necessario abilitare le impostazioni di diagnostica, in modo che sia possibile visualizzare i report per questo insieme di credenziali. Per semplificare la creazione di impostazioni di diagnostica su larga scala (con Log Analytics come destinazione), backup di Azure fornisce un criterio predefinito di Azure. Questo criterio aggiunge un'impostazione di diagnostica LA a tutti gli insiemi di credenziali in ogni sottoscrizione o gruppo di risorse. Nelle sezioni seguenti vengono fornite istruzioni su come utilizzare questo criterio. [Altre informazioni](azure-policy-configure-diagnostics.md)sono disponibili qui.

### <a name="azure-backup-cost-considerations"></a>Considerazioni sui costi di backup di Azure

Le funzionalità del servizio backup di Azure offrono la flessibilità necessaria per gestire in modo efficiente i costi e soddisfare i requisiti aziendali di BCDR (continuità aziendale e ripristino di emergenza). Considerare le linee guida seguenti:

* Usare il calcolatore dei prezzi per valutare e ottimizzare i costi regolando varie leve. [Per altre informazioni, vedi qui](azure-backup-pricing.md)

* Esplora backup: usare Esplora backup o i report di backup per comprendere e ottimizzare la crescita dell'archiviazione dei backup, arrestando i backup per carichi di lavoro non critici o macchine virtuali eliminate. È possibile ottenere una visualizzazione aggregata dell'intera proprietà dal punto di vista del backup. Sono incluse non solo le informazioni sugli elementi di backup, ma anche le risorse che non sono configurate per il backup. In questo modo si garantisce che la protezione dei dati critici non venga mai persa e che i backup siano ottimizzati per carichi di lavoro non critici o per carichi di lavoro eliminati.

* Ottimizzare i criteri di backup
  * Ottimizzare le impostazioni di pianificazione e conservazione in base agli archetipi del carico di lavoro (ad esempio, cruciale, non critico)
  * Ottimizzare le impostazioni di conservazione per il ripristino immediato
  * Scegliere il tipo di backup appropriato per soddisfare i requisiti, accettando i tipi di backup supportati (completo, incrementale, log, differenziale) in base al carico di lavoro in backup di Azure.

* Dischi di backup selettiva: Escludi disco (funzionalità di anteprima) offre una scelta efficace ed economica per eseguire il backup in modo selettivo dei dati critici. Ad esempio, eseguire il backup di un solo disco quando non si vuole eseguire il backup dei restanti dischi collegati a una macchina virtuale. Questa operazione è utile anche quando si dispone di più soluzioni di backup. Ad esempio, quando si esegue il backup di database o dati con una soluzione di backup del carico di lavoro (SQL Server database nel backup di macchine virtuali di Azure) e si vuole usare il backup a livello di VM di Azure per i dischi selezionati.

* Backup di Azure acquisisce snapshot delle macchine virtuali di Azure e le archivia insieme ai dischi per aumentare la creazione dei punti di ripristino e velocizzare le operazioni di ripristino. Questa operazione viene definita ripristino istantaneo. Per impostazione predefinita, gli snapshot di ripristino istantaneo vengono conservati per due giorni. Questa funzionalità consente di eseguire un'operazione di ripristino da questi snapshot riducendo i tempi di ripristino. Riduce il tempo necessario per trasformare e copiare i dati dall'insieme di credenziali. Verranno pertanto addebitati costi di archiviazione corrispondenti agli snapshot creati durante questo periodo. [Altre informazioni](backup-instant-restore-capability.md#configure-snapshot-retention)sono disponibili qui.

* Il tipo di replica di archiviazione dell'insieme di credenziali di backup di Azure per impostazione predefinita è con ridondanza geografica (GRS). Questa opzione non può essere modificata dopo la protezione degli elementi. L'archiviazione con ridondanza geografica (GRS) offre un livello più elevato di durabilità dei dati rispetto all'archiviazione con ridondanza locale (con ridondanza locale), consente a un consenso esplicito di usare il ripristino tra aree e i costi. Esaminare i compromessi tra i costi più bassi e la durabilità dei dati più elevata e decidere cosa è migliore per lo scenario. [Per altre informazioni, vedi qui](backup-create-rs-vault.md#set-storage-redundancy)

* Se si sta proteggendo sia il carico di lavoro in esecuzione all'interno di una macchina virtuale che la macchina virtuale, verificare se è necessaria la doppia protezione.

## <a name="monitoring-and-alerting-considerations"></a>Considerazioni sul monitoraggio e sugli avvisi

Un utente o un amministratore di backup deve essere in grado di monitorare tutte le soluzioni di backup e ricevere notifiche relative a scenari importanti. Questa sezione illustra in dettaglio le funzionalità di monitoraggio e notifica fornite dal servizio backup di Azure.

### <a name="monitoring"></a>Monitoraggio

* Backup di Azure fornisce il **monitoraggio dei processi incorporato** per operazioni quali la configurazione di backup, backup, ripristino, eliminazione del backup e così via. Questo ambito è limitato all'insieme di credenziali e è ideale per il monitoraggio di un singolo insieme di credenziali. [Altre informazioni](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)sono disponibili qui.

* Se è necessario monitorare le attività operative su larga scala, **Backup Explorer** fornisce una visualizzazione aggregata dell'intero patrimonio di backup, abilitando l'analisi dettagliata e la risoluzione dei problemi. Si tratta di una cartella di lavoro di monitoraggio di Azure integrata che offre un'unica posizione centralizzata che consente di monitorare le attività operative nell'intero patrimonio di backup in Azure, in cui vengono distribuiti tenant, località, sottoscrizioni, gruppi di risorse e insiemi di credenziali. [Altre informazioni](monitor-azure-backup-with-backup-explorer.md)sono disponibili qui.
  * È possibile usarlo per identificare le risorse che non sono configurate per il backup e assicurarsi di non perdere la protezione dei dati critici nella propria azienda in crescita.
  * Il dashboard fornisce attività operative per gli ultimi sette giorni (massimo). Se è necessario conservare questi dati, è possibile esportarli come file di Excel e conservarli.
  * Se si è un utente di Azure Lighthouse, è possibile visualizzare le informazioni su più tenant, abilitando il monitoraggio senza limiti.

* Se è necessario mantenere e visualizzare le attività operative per il lungo termine, usare i **report**. Un requisito comune per gli amministratori di backup è ottenere informazioni dettagliate sui backup in base ai dati che si estendono a un lungo periodo di tempo. I casi d'uso per una soluzione di questo tipo includono:
  * Allocazione e previsione dello spazio di archiviazione cloud utilizzato.
  * Controllo delle attività dii backup e ripristino.
  * Identificazione delle tendenze principali a diversi livelli di granularità.

* Inoltre,
  * È possibile inviare dati (ad esempio, processi, criteri e così via) all'area di lavoro **log Analytics** . In questo modo le funzionalità dei log di monitoraggio di Azure consentono di correlare i dati con altri dati di monitoraggio raccolti da monitoraggio di Azure, consolidare le voci di log da più sottoscrizioni e tenant di Azure in un'unica posizione per l'analisi, usare query di log per eseguire analisi complesse e ottenere informazioni approfondite sulle voci di log. [Altre informazioni](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)sono disponibili qui.
  * È possibile inviare dati a hub eventi per inviare voci all'esterno di Azure, ad esempio a una soluzione SIEM di terze parti (informazioni di sicurezza e gestione degli eventi) o ad altre soluzioni di log Analytics. [Altre informazioni](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs)sono disponibili qui.
  * È possibile inviare dati a un account di archiviazione di Azure se si desidera conservare i dati di log più lunghi di 90 giorni per il controllo, l'analisi statica o il backup. Se è necessario conservare gli eventi solo per 90 giorni, non è necessario configurare archivi in un account di archiviazione, perché gli eventi del log attività vengono conservati nella piattaforma Azure per 90 giorni. [Altre informazioni](../azure-monitor/platform/activity-log.md#send-to--azure-storage)

### <a name="alerting"></a>Creazione di avvisi

* Gli avvisi sono principalmente un modo per ricevere una notifica per intraprendere un'azione pertinente. La sezione avvisi di backup Mostra gli avvisi generati dal servizio backup di Azure.

* Backup di Azure fornisce un meccanismo di notifica degli **avvisi incorporato** tramite posta elettronica per errori, avvisi e operazioni critiche. È possibile specificare singoli indirizzi di posta elettronica o liste di distribuzione per ricevere una notifica quando viene generato un avviso. È anche possibile scegliere se ricevere una notifica per ogni singolo avviso o raggrupparli in un digest orario e quindi ricevere una notifica.
  * Questi avvisi sono definiti dal servizio e forniscono supporto per scenari limitati: errori di backup/ripristino, arresto della protezione con Mantieni dati/Interrompi protezione con Elimina dati e così via. [Altre informazioni](backup-azure-monitoring-built-in-monitor.md#alert-scenarios)sono disponibili qui.
  * Se viene eseguita un'operazione distruttiva, ad esempio arresta la protezione con l'eliminazione dei dati, viene generato un avviso e viene inviato un messaggio di posta elettronica ai proprietari, agli amministratori e ai coamministratori della sottoscrizione anche se le notifiche **non** sono configurate per l'insieme di credenziali di servizi di ripristino.
  * Alcuni carichi di lavoro possono generare una frequenza elevata di errori, ad esempio SQL Server ogni 15 minuti. Per evitare che vengano sovraccaricati gli avvisi generati per ogni occorrenza di errore, gli avvisi vengono consolidati. [Altre informazioni](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts)sono disponibili qui.
  * Gli avvisi incorporati non possono essere personalizzati e sono limitati ai messaggi di posta elettronica definiti nel portale di Azure.

* Se è necessario **creare avvisi personalizzati** (ad esempio, avvisi di processi riusciti), usare log Analytics. In Monitoraggio di Azure è possibile creare avvisi personalizzati in un'area di lavoro Log Analytics. I carichi di lavoro ibridi (DPM/MAB) possono anche inviare dati a LA e usare LA per fornire avvisi comuni tra i carichi di lavoro supportati da backup di Azure.

* È anche possibile ottenere le notifiche tramite i **log attività**predefiniti dell'insieme di credenziali di servizi di ripristino. Supporta tuttavia scenari limitati e non è adatto per operazioni come il backup pianificato, che si allinea meglio con i log delle risorse rispetto ai log attività. Per altre informazioni su queste limitazioni e su come usare Log Analytics area di lavoro per il monitoraggio e l'invio di avvisi su larga scala per tutti i carichi di lavoro protetti da backup di Azure, fare riferimento a questo [articolo](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Passaggi successivi

È consigliabile leggere gli articoli seguenti come punti di partenza per l'uso di backup di Azure:

* [Panoramica di backup di Azure](backup-overview.md)
* [Domande frequenti](backup-azure-backup-faq.md)
