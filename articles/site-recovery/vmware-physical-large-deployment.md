---
title: Configurare il ripristino di emergenza in Azure per un numero elevato di macchine virtuali VMware o server fisici con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure per un numero elevato di macchine virtuali VMware locali o server fisici con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: e96aafe61c0d8547ffca9e97bfd9e90c9529155f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237270"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurare il ripristino di emergenza su vasta scala per VM VMware/server fisici

Questo articolo descrive come configurare il ripristino di emergenza in Azure per un numero elevato (> 1000) di macchine virtuali VMware locali o server fisici nell'ambiente di produzione, usando il [Azure Site Recovery](site-recovery-overview.md) servizio.


## <a name="define-your-bcdr-strategy"></a>Definire la strategia di BCDR

Come parte della strategia di ripristino di emergenza e continuità aziendale, si definisce gli obiettivi del punto di ripristino (RPO) e gli obiettivi di tempo di ripristino (RTO) per le app aziendali e i carichi di lavoro. RTO misura la durata di tempo e del servizio a livello in cui un'app aziendale o un processo deve essere ripristinato ed è disponibile, per evitare i problemi di continuità.
- Site Recovery offre la replica continua per le macchine virtuali VMware e server fisici e un [contratto di servizio](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) per RTO.
- Durante la pianificazione per il ripristino di emergenza su vasta scala per le macchine virtuali VMware e individuare le risorse di Azure che è necessario, è possibile specificare un valore RTO che verrà usato per calcoli di capacità.


## <a name="best-practices"></a>Procedure consigliate

Alcune procedure consigliate generali per il ripristino di emergenza su vasta scala. Queste procedure consigliate sono descritti più dettagliatamente nelle sezioni successive del documento.

- **Identificare i requisiti di destinazione**: Stimare le esigenze di capacità e risorse in Azure prima di configurare il ripristino di emergenza.
- **Piano per i componenti di Site Recovery**: Valutare quali componenti di Site Recovery (server di configurazione, server di elaborazione) sono necessarie soddisfare la capacità stimata.
- **Configurare uno o più server di elaborazione scale-out**: Non usare il server di elaborazione che esegue per impostazione predefinita nel server di configurazione. 
- **Eseguire gli aggiornamenti più recenti**: Il team di Site Recovery rilascia nuove versioni dei componenti di Site Recovery a intervalli regolari e verificare che si sta eseguendo le versioni più recenti. A che scopo, tenere traccia [quali sono le novità](site-recovery-whats-new.md) per gli aggiornamenti, e [per abilitare e installare gli aggiornamenti](service-updates-how-to.md) durante il rilascio.
- **Monitorare in modo proattivo**: Poiché si otterrebbe il ripristino di emergenza sia operativa, è necessario monitorare in modo proattivo lo stato e integrità dei computer replicati e risorse di infrastruttura.
- **Esercitazioni sul ripristino di emergenza**: È consigliabile eseguire esercitazioni per il ripristino di emergenza a intervalli regolari. Questi non ha ripercussioni sull'ambiente di produzione, ma contribuiscono a garantire che il failover in Azure funzionerà come previsto quando necessario.



## <a name="gather-capacity-planning-information"></a>Raccogliere informazioni sulla pianificazione della capacità

Raccogliere le informazioni sull'ambiente locale, per aiutare a valutare e stimare che la capacità di destinazione (Azure).
- Per VMware, eseguire Deployment Planner per le macchine virtuali VMware eseguire questa operazione.
- Per i server fisici, raccogliere le informazioni manualmente.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Eseguire Deployment Planner per le macchine virtuali VMware

Deployment Planner consente di raccogliere informazioni sull'ambiente VMware in locale.

- Eseguire Deployment Planner durante un periodo che rappresenta la varianza del tipico per le macchine virtuali. Ciò genererà più accurata di stime e indicazioni.
- È consigliabile eseguire Deployment Planner nel server di configurazione, poiché lo strumento di pianificazione calcola la velocità effettiva dal server in cui è in esecuzione. [Altre informazioni](site-recovery-vmware-deployment-planner-run.md#get-throughput) sulla misurazione della velocità effettiva.
- Se non è ancora necessario configurare un server di configurazione:
    - [Ottenere una panoramica](vmware-physical-azure-config-process-server-overview.md) dei componenti di Site Recovery.
    - [Configurare un server di configurazione](vmware-azure-deploy-configuration-server.md), in modo da eseguire Deployment Planner su di esso.

Eseguire quindi lo strumento di pianificazione come segue:

1. [Informazioni su](site-recovery-deployment-planner.md) Deployment Planner. È possibile scaricare la versione più recente dal portale, oppure [scaricarlo direttamente](https://aka.ms/asr-deployment-planner).
2. Rivedere le [prerequisiti](site-recovery-deployment-planner.md#prerequisites) e [gli aggiornamenti più recenti](site-recovery-deployment-planner-history.md) per lo strumento Deployment Planner, e [scaricare ed estrarre](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) lo strumento.
3. [Eseguire Deployment Planner](site-recovery-vmware-deployment-planner-run.md) nel server di configurazione.
4. [Generare un report](site-recovery-vmware-deployment-planner-run.md#generate-report) per riepilogare le stime e indicazioni.
5. Analizzare il [segnalano le raccomandazioni](site-recovery-vmware-deployment-planner-analyze-report.md) e [stime dei costi](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Per impostazione predefinita, lo strumento è configurato per il profiling e genera report per le macchine virtuali fino a 1000. È possibile modificare questo limite, aumentando il valore della chiave MaxVMsSupported nel file Asrdeploymentplanner.

## <a name="plan-target-azure-requirements-and-capacity"></a>Pianificare la capacità e requisiti di destinazione (Azure)

Utilizzando il proprio stime raccolte e le raccomandazioni, è possibile pianificare la capacità e le risorse di destinazione. Se è stato eseguito lo strumento Deployment Planner per le macchine virtuali VMware, è possibile usare un numero dei [raccomandazioni del report](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) per aiutarti.

- **Le VM compatibili**: Questo numero può essere utilizzato per identificare il numero di macchine virtuali che sono pronte per il ripristino di emergenza in Azure. Indicazioni sulla larghezza di banda di rete e i core di Azure si basano su questo numero.
- **Larghezza di banda necessaria**: Si noti la larghezza di banda che necessaria per la replica differenziale delle macchine virtuali compatibili. 
    - Quando si esegue lo strumento di pianificazione è specificare il valore RPO desiderato in pochi minuti. Le raccomandazioni illustrano la larghezza di banda necessaria per soddisfare tale valore RPO il 100% e del 90% del tempo. 
    - Le raccomandazioni di larghezza di banda di rete prendono in considerazione la larghezza di banda necessaria per il numero totale di server di configurazione e i server di elaborazione consigliati in Planner.
- **Core Azure necessari**: Si noti il numero di core è necessario nell'area di Azure di destinazione in base al numero di VM compatibili. Se non si dispone di sufficienti memorie centrali, al momento del failover Site Recovery sarà in grado di creare macchine virtuali di Azure necessari.
- **Dimensioni batch della macchina virtuale consigliate**: La dimensione batch consigliata è basata sulla possibilità di completare la replica iniziale per il batch entro 72 ore per impostazione predefinita, soddisfacendo un RPO pari a 100%. Il valore dell'ora può essere modificato.

È possibile usare queste indicazioni per pianificare le risorse di Azure, della larghezza di banda di rete e l'invio in batch di macchine Virtuali.

## <a name="plan-azure-subscriptions-and-quotas"></a>Pianificare le quote e le sottoscrizioni di Azure

Desideriamo accertarci che le quote disponibili nella sottoscrizione di destinazione siano sufficienti per gestire il failover.

**Attività** | **Dettagli** | **Azione**
--- | --- | ---
**Controllo core** | Se core della quota disponibile di non essere uguale o superare il numero totale di destinazione al momento del failover, i failover avrà esito negativo. | Per le macchine virtuali VMware, controllare che nella sottoscrizione di destinazione per soddisfare l'indicazione di core di Deployment Planner disponibili sufficienti memorie centrali.<br/><br/> Per i server fisici, verificare che Azure Core soddisfino le stime manuale.<br/><br/> Per controllare le quote, nel portale di Azure > **abbonamento**, fare clic su **utilizzo + quote**.<br/><br/> [Altre informazioni](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) sull'aumento delle quote.
**Verificare i limiti failover** | Il numero di failover non deve superare i limiti failover di Site Recovery. |  Se i failover superano i limiti, è possibile aggiungere sottoscrizioni e il failover su più sottoscrizioni o aumentare la quota per una sottoscrizione. 


### <a name="failover-limits"></a>Limiti failover

I limiti di indicano il numero di failover supportate da Site Recovery entro un'ora, supponendo di tre dischi per ogni macchina.

Che cos'è conforme a mean? Per avviare una VM di Azure, Azure richiede alcuni driver sia nello stato iniziale di avvio e dei servizi, ad esempio DHCP da impostare per l'avvio automatico.
- Le macchine che siano conformi avranno già tali impostazioni posto.
- Per i computer che eseguono Windows, è in modo proattivo possibile controllare la conformità e renderli conformi, se necessario. [Altre informazioni](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)
- Macchine Linux sono solo risulta conforme al momento del failover.

**Computer sia conforme con Azure?** | **Limiti di macchina virtuale di Azure (failover di disco gestito)**
--- | --- 
Yes | 2000
No  | 1000

- Limiti presuppongono che minimi che altri processi sono in corso nell'area di destinazione per la sottoscrizione.
- Alcune aree di Azure sono più piccoli e potrebbero essere leggermente più bassi limiti.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Pianificare l'infrastruttura e connettività delle macchine Virtuali

Dopo il failover in Azure è necessario i carichi di lavoro per funzionare come in locale e per consentire agli utenti di accedere ai carichi di lavoro in esecuzione nelle VM di Azure.

- [Altre informazioni](site-recovery-active-directory.md#test-failover-considerations) circa il failover l'infrastruttura locale di Active Directory o DNS in Azure.
- [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sulla preparazione per la connessione alle macchine virtuali di Azure dopo il failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Pianificare la capacità di origine e requisiti

È importante avere sufficienti server di configurazione e i server di elaborazione scale-out per soddisfare i requisiti di capacità. Quando si inizia la distribuzione su larga scala, iniziare con un server di configurazione e un server singolo processo di scalabilità orizzontale. Come si raggiungono i limiti prescritti, aggiungere altri server.

>[!NOTE]
> Per le macchine virtuali VMware, Deployment Planner fornisce alcuni consigli sui server di configurazione e di elaborazione che è necessario. È consigliabile usare tabelle incluse nelle procedure seguenti, anziché seguire le indicazioni di Deployment Planner. 


## <a name="set-up-a-configuration-server"></a>Configurare un server di configurazione
 
Capacità del server di configurazione è influenzata dal numero di macchine eseguono la replica e non dai dati della frequenza di varianza. Per determinare se sono necessari server di configurazione aggiuntive, usare questi limiti delle macchine Virtuali definite.

**CPU** | **Memoria** | **Disco cache** | **Limite del computer replicati**
 --- | --- | --- | ---
8 vCPU<br> 2 socket * 4 core a 2,5 GHz | 16 GB | 600 TB | Fino a 550 macchine<br> Si presuppone che ogni computer disponga di tre dischi da 100 GB.

- Questi limiti si basano su un server di configurazione usando un modello OVF.
- I limiti presuppongono che non si usa il server di elaborazione che esegue per impostazione predefinita nel server di configurazione.

Se è necessario aggiungere un nuovo server di configurazione, seguire queste istruzioni:

- [Configurare un server di configurazione](vmware-azure-deploy-configuration-server.md) per il ripristino di emergenza di VM VMware, usando un modello OVF.
- [Configurare un server di configurazione](physical-azure-set-up-source.md) manualmente per i server fisici o per le distribuzioni di VMware che non è possibile usare un modello OVF.

Come si configura un server di configurazione, si noti che:

- Quando si configura un server di configurazione, è importante prendere in considerazione la sottoscrizione e l'insieme di credenziali entro il quale si trova, poiché questi non devono essere modificati dopo l'installazione. Se è necessario modificare l'insieme di credenziali, è necessario dissociare il server di configurazione dall'insieme di credenziali e per registrarla di nuovo. Viene arrestata la replica di macchine virtuali nell'insieme di credenziali.
- Se si desidera configurare un server di configurazione con più schede di rete, eseguire questa operazione durante il set di. Non è possibile farlo dopo la registrazione del server di configurazione nell'insieme di credenziali.

## <a name="set-up-a-process-server"></a>Configurare un server di elaborazione

Capacità del server di processo dipende dalla frequenza di varianza dei dati e non per il numero di computer abilitato per la replica.

- Per le distribuzioni di grandi dimensioni deve avere sempre almeno un server di elaborazione scale-out.
- Per determinare se sono necessari server aggiuntivo, usare la tabella seguente.
- È consigliabile aggiungere un server con la specifica più elevata. 


**CPU** | **Memoria** | **Disco cache** | **Della frequenza di varianza**
 --- | --- | --- | --- 
12 vCPU<br> 2 socket * 6 core a 2,5 GHz | 24 GB | 1 GB | Fino a 2 TB al giorno

Configurare il server di elaborazione come segue:

1. Esaminare i [prerequisiti](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installare il server nel [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), o dal [della riga di comando](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configurare le macchine replicate per usare il nuovo server. Se si hanno già macchine eseguono la replica:
    - È possibile [spostare](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) un carico di lavoro server intero processo per il nuovo server di elaborazione.
    - In alternativa, è possibile [spostare](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) macchine virtuali specifiche per il nuovo server di elaborazione.



## <a name="enable-large-scale-replication"></a>Abilitare la replica su larga scala

Dopo la pianificazione della capacità e la distribuzione dell'infrastruttura e i componenti necessari, abilitare la replica per un numero elevato di macchine virtuali.

1. Ordinare le macchine in batch. Abilitare la replica per le macchine virtuali all'interno di un batch e quindi passare al batch successivo.

    - Per le macchine virtuali VMware, è possibile usare la [dimensioni batch della macchina virtuale consigliate](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) nel report di Deployment Planner.
    - Per le macchine fisiche, è consigliabile che identificare batch basati su macchine che dispongono di una dimensione e quantità di dati simili e sulla velocità effettiva di rete disponibile. L'obiettivo è batch macchine che sono probabile che completare la replica iniziale nella stessa quantità di tempo.
    
2. Se la varianza del disco per una macchina è elevato o supera i limiti in thePlanner distribuzione, è possibile spostare i file non critici che non è necessario eseguire la replica (ad esempio, i dump di log o i file temporanei) dal computer. Per le macchine virtuali VMware, è possibile spostare questi file su un disco separato e quindi [escludere tale disco](vmware-azure-exclude-disk.md) dalla replica.
3. Prima di abilitare la replica, verificare che le macchine soddisfino [requisiti di replica](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configurare un criterio di replica per [macchine virtuali VMware](vmware-azure-set-up-replication.md#create-a-policy) oppure [server fisici](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Abilitare la replica [macchine virtuali VMware](vmware-azure-enable-replication.md) oppure [server fisici](physical-azure-disaster-recovery.md#enable-replication). Questo comando avvia la replica iniziale per i computer selezionati.

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Dopo che viene avviata la replica per il primo batch di macchine virtuali, avviare il monitoraggio della distribuzione come indicato di seguito:  

1. Assegna un amministratore di ripristino di emergenza per monitorare lo stato di integrità delle macchine replicate.
2. [Monitorare gli eventi](site-recovery-monitor-and-troubleshoot.md) per gli elementi replicati e l'infrastruttura.
3. [Monitorare l'integrità](vmware-physical-azure-monitor-process-server.md) dei server di elaborazione scale-out.
4. Iscriviti per ricevere [notifiche tramite posta elettronica](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) per gli eventi, per facilitare il monitoraggio.
5. Condurre regular [esercitazioni sul ripristino di emergenza](site-recovery-test-failover-to-azure.md), per assicurare che tutto funzioni come previsto.


## <a name="plan-for-large-scale-failovers"></a>Piano per i failover su vasta scala

In caso di emergenza, potrebbe essere necessario eseguire il failover di un numero elevato di computer/carichi di lavoro in Azure. Preparare per questo tipo di evento come indicato di seguito.

È possibile preparare in anticipo per il failover come indicato di seguito:

- [Preparare l'infrastruttura e le macchine virtuali](#plan-infrastructure-and-vm-connectivity) in modo che i carichi di lavoro saranno disponibili dopo il failover e in modo che gli utenti possono accedere le VM di Azure.
- Si noti il [limiti failover](#failover-limits) più indietro in questo documento. Assicurarsi che il failover siano compresi questi limiti.
- Eseguire normali [esercitazioni sul ripristino di emergenza](site-recovery-test-failover-to-azure.md). Guida di esercitazioni per:
    - Individuare le lacune nella distribuzione prima del failover.
    - Stimare l'obiettivo RTO end-to-end per le app.
    - Stimare il valore RPO di end-to-end per i carichi di lavoro.
    - Identificare i conflitti di intervallo di indirizzi IP.
    - Quando si eseguono l'analisi, è consigliabile non usare le reti di produzione per le esercitazioni sul, evitare di usare gli stessi nomi di subnet nelle reti di produzione e test e pulizia failover di test dopo ogni drill.

Per eseguire un failover su vasta scala, si consiglia quanto segue:

1. Creare piani di ripristino per il failover del carico di lavoro.
    - Ogni piano di ripristino può attivare il failover delle macchine fino a 50.
    - [Ulteriori informazioni](recovery-plan-overview.md) sui piani di ripristino.
2. Aggiungere gli script di runbook di automazione di Azure a piani di ripristino per automatizzare le attività manuali in Azure. Attività più comuni includono la configurazione di servizi di bilanciamento del carico, l'aggiornamento di DNS e così via. [Altre informazioni](site-recovery-runbook-automation.md)
2. Prima del failover, preparare i computer Windows in modo da renderli conformi con l'ambiente Azure. [Limiti failover](#plan-azure-subscriptions-and-quotas) sono superiori per i computer conformi. [Altre informazioni](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) sui runbook.
4.  Attivare il failover con la [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) cmdlet di PowerShell, insieme a un piano di ripristino.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitoraggio di Site Recovery](site-recovery-monitor-and-troubleshoot.md)
