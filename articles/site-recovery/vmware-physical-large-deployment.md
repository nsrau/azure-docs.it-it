---
title: Configurare il ripristino di emergenza in Azure per un numero elevato di macchine virtuali VMware o server fisici con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure per un numero elevato di macchine virtuali VMware locali o server fisici con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 7ef4a9d5f63282736b010e67b467f82474bcf409
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782666"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurare il ripristino di emergenza su larga scala per macchine virtuali VMware/server fisici

Questo articolo descrive come configurare il ripristino di emergenza in Azure per numeri elevati (> 1000) di macchine virtuali VMware locali o server fisici nell'ambiente di produzione, usando il servizio [Azure Site Recovery](site-recovery-overview.md) .


## <a name="define-your-bcdr-strategy"></a>Definire la strategia di BCDR

Come parte della strategia di continuità aziendale e ripristino di emergenza (BCDR), si definiscono gli obiettivi del punto di ripristino (RPOs) e gli obiettivi del tempo di ripristino (RTO) per le app e i carichi di lavoro aziendali. RTO misura la durata del tempo e del livello di servizio entro i quali un'app o un processo aziendale deve essere ripristinato e disponibile per evitare problemi di continuità.
- Site Recovery fornisce la replica continua per le macchine virtuali VMware e i server fisici e un [contratto di contratto](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) per RTO.
- Quando si pianifica il ripristino di emergenza su larga scala per le macchine virtuali VMware e si calcolano le risorse di Azure necessarie, è possibile specificare un valore RTO che verrà usato per i calcoli della capacità.


## <a name="best-practices"></a>Procedure consigliate

Alcune procedure consigliate generali per il ripristino di emergenza su larga scala. Queste procedure consigliate sono descritte più dettagliatamente nelle sezioni successive del documento.

- **Identificare i requisiti di destinazione**: Stimare la capacità e le esigenze delle risorse in Azure prima di configurare il ripristino di emergenza.
- **Pianificare i componenti di Site Recovery**: Individuare i componenti Site Recovery (server di configurazione, server di elaborazione) necessari per soddisfare la capacità stimata.
- **Configurare uno o più server di elaborazione con scalabilità orizzontale**: Non usare il server di elaborazione in esecuzione per impostazione predefinita nel server di configurazione. 
- **Eseguire gli aggiornamenti più recenti**: Il Team Site Recovery rilascia periodicamente le nuove versioni dei componenti Site Recovery ed è necessario assicurarsi di eseguire le versioni più recenti. A questo scopo, è possibile tenere traccia delle [novità](site-recovery-whats-new.md) degli aggiornamenti e [abilitare e installare gli aggiornamenti](service-updates-how-to.md) quando vengono rilasciati.
- **Monitorare**in modo proattivo: Quando si ottiene il ripristino di emergenza operativo, è necessario monitorare in modo proattivo lo stato e l'integrità delle macchine replicate e delle risorse dell'infrastruttura.
- **Esercitazioni sul ripristino di emergenza**: È consigliabile eseguire esercitazioni sul ripristino di emergenza a intervalli regolari. Questi non hanno alcun impatto sull'ambiente di produzione, ma garantiscono che il failover in Azure funzioni come previsto, quando necessario.



## <a name="gather-capacity-planning-information"></a>Raccogliere informazioni sulla pianificazione della capacità

Raccogliere informazioni sull'ambiente locale, per valutare e stimare le esigenze di capacità di destinazione (Azure).
- Per VMware, eseguire il Deployment Planner per le macchine virtuali VMware.
- Per i server fisici, raccogliere manualmente le informazioni.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Eseguire l'Deployment Planner per le macchine virtuali VMware

Il Deployment Planner consente di raccogliere informazioni sull'ambiente VMware locale.

- Eseguire il Deployment Planner durante un periodo che rappresenta la varianza tipica per le macchine virtuali. Questa operazione genererà stime e indicazioni più accurate.
- Si consiglia di eseguire il Deployment Planner nel computer server di configurazione, poiché il pianificatore calcola la velocità effettiva dal server in cui è in esecuzione. [Altre](site-recovery-vmware-deployment-planner-run.md#get-throughput) informazioni sulla misurazione della velocità effettiva.
- Se non è ancora stato configurato un server di configurazione:
    - [Ottenere una panoramica](vmware-physical-azure-config-process-server-overview.md) dei componenti di Site Recovery.
    - [Configurare un server di configurazione](vmware-azure-deploy-configuration-server.md)per eseguire il Deployment Planner.

Eseguire quindi il planner come indicato di seguito:

1. Informazioni [sul](site-recovery-deployment-planner.md) Deployment Planner. È possibile scaricare la versione più recente dal portale o [scaricarla direttamente](https://aka.ms/asr-deployment-planner).
2. Esaminare i [prerequisiti](site-recovery-deployment-planner.md#prerequisites) e [gli aggiornamenti più recenti](site-recovery-deployment-planner-history.md) per il Deployment Planner, quindi [scaricare ed estrarre](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) lo strumento.
3. [Eseguire il Deployment Planner](site-recovery-vmware-deployment-planner-run.md) nel server di configurazione.
4. [Generare un report](site-recovery-vmware-deployment-planner-run.md#generate-report) per riepilogare le stime e le raccomandazioni.
5. Analizzare le [raccomandazioni](site-recovery-vmware-deployment-planner-analyze-report.md) e le [stime dei costi](site-recovery-vmware-deployment-planner-cost-estimation.md)del report.

>[!NOTE]
> Per impostazione predefinita, lo strumento è configurato per profilare e generare report per un massimo di 1000 VM. È possibile modificare questo limite aumentando il valore della chiave MaxVMsSupported nel file ASRDeploymentPlanner. exe. config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Requisiti e capacità per la destinazione del piano (Azure)

Usando le stime e le raccomandazioni raccolte, è possibile pianificare le risorse e la capacità di destinazione. Se è stato eseguito il Deployment Planner per le macchine virtuali VMware, è possibile usare una serie di raccomandazioni per il [report](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) .

- **VM compatibili**: Usare questo numero per identificare il numero di macchine virtuali pronte per il ripristino di emergenza in Azure. Le raccomandazioni relative alla larghezza di banda di rete e ai core di Azure si basano su questo numero.
- **Larghezza di banda di rete necessaria**: Prendere nota della larghezza di banda necessaria per delta replication di VM compatibili. 
    - Quando si esegue il pianificatore, si specifica il RPO desiderato in minuti. I consigli indicano che la larghezza di banda necessaria per soddisfare il RPO 100% e il 90% del tempo. 
    - Le raccomandazioni relative alla larghezza di banda di rete prendono in considerazione la larghezza di banda necessaria per il numero totale di server di configurazione e server di elaborazione consigliati in Microsoft Planner.
- Componenti di **base di Azure necessari**: Prendere nota del numero di core necessari nell'area di Azure di destinazione, in base al numero di VM compatibili. Se non si dispone di un numero sufficiente di core, al failover Site Recovery non sarà in grado di creare le macchine virtuali di Azure necessarie.
- **Dimensioni consigliate per il batch di macchine virtuali**: Le dimensioni consigliate per il batch si basano sulla possibilità di completare la replica iniziale per il batch entro 72 ore per impostazione predefinita, soddisfacendo al contempo un RPO del 100%. Il valore dell'ora può essere modificato.

È possibile usare questi consigli per pianificare le risorse di Azure, la larghezza di banda di rete e l'invio in batch delle macchine virtuali.

## <a name="plan-azure-subscriptions-and-quotas"></a>Pianificare le sottoscrizioni e le quote di Azure

È necessario assicurarsi che le quote disponibili nella sottoscrizione di destinazione siano sufficienti per gestire il failover.

**Attività** | **Dettagli** | **Azione**
--- | --- | ---
**Controlla Core** | Se i core nella quota disponibile non equivalgono o superano il numero di destinazioni totali al momento del failover, i failover avranno esito negativo. | Per le macchine virtuali VMware, verificare di avere un numero sufficiente di core nella sottoscrizione di destinazione per soddisfare la raccomandazione Deployment Planner core.<br/><br/> Per i server fisici, verificare che i core di Azure soddisfino le stime manuali.<br/><br/> Per controllare le quote, nella **sottoscrizione**portale di Azure > fare clic su **utilizzo e quote**.<br/><br/> [Altre](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) informazioni su come aumentare le quote.
**Verifica limiti failover** | Il numero di failover non deve superare Site Recovery limiti di failover. |  Se i failover superano i limiti, è possibile aggiungere sottoscrizioni ed eseguire il failover a più sottoscrizioni o aumentare la quota per una sottoscrizione. 


### <a name="failover-limits"></a>Limiti di failover

I limiti indicano il numero di failover supportati da Site Recovery entro un'ora, supponendo tre dischi per computer.

Cosa si intende per conformità? Per avviare una macchina virtuale di Azure, Azure richiede che alcuni driver siano in stato di avvio avvio e che i servizi come DHCP siano impostati per l'avvio automatico.
- I computer che soddisfano queste impostazioni avranno già le impostazioni.
- Per i computer che eseguono Windows, è possibile verificare in modo proattivo la conformità e renderli conformi, se necessario. [Altre informazioni](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)
- I computer Linux vengono resi conformi solo al momento del failover.

**Il computer è conforme ad Azure?** | **Limiti delle macchine virtuali di Azure (failover del disco gestito)**
--- | --- 
Sì | 2000
No | 1000

- I limiti presuppongono che siano in corso altri processi nell'area di destinazione per la sottoscrizione.
- Alcune aree di Azure sono più piccole e possono avere limiti leggermente inferiori.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Pianificare l'infrastruttura e la connettività delle macchine virtuali

Dopo il failover in Azure, è necessario che i carichi di lavoro funzionino come in locale e per consentire agli utenti di accedere ai carichi di lavoro in esecuzione nelle VM di Azure.

- [Altre](site-recovery-active-directory.md#test-failover-considerations) informazioni sul failover dell'infrastruttura locale di Active Directory o DNS in Azure.
- [Altre](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) informazioni sulla preparazione per la connessione alle macchine virtuali di Azure dopo il failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Pianificare la capacità e i requisiti di origine

È importante disporre di server di configurazione e server di elaborazione con scalabilità orizzontale sufficienti per soddisfare i requisiti di capacità. Quando si inizia la distribuzione su larga scala, iniziare con un singolo server di configurazione e un singolo server di elaborazione con scalabilità orizzontale. Quando si raggiungono i limiti previsti, aggiungere altri server.

>[!NOTE]
> Per le macchine virtuali VMware, il Deployment Planner apporta alcune raccomandazioni sui server di configurazione e di elaborazione necessari. È consigliabile utilizzare le tabelle incluse nelle procedure riportate di seguito, anziché seguire la raccomandazione Deployment Planner. 


## <a name="set-up-a-configuration-server"></a>Configurare un server di configurazione
 
La capacità del server di configurazione è interessata dal numero di computer che eseguono la replica e non dalla frequenza della varianza dei dati. Per determinare se sono necessari server di configurazione aggiuntivi, usare questi limiti di VM definiti.

**CPU** | **Memoria** | **Disco cache** | **Limite di computer replicati**
 --- | --- | --- | ---
8 vCPU<br> 2 socket * 4 core a 2,5 GHz | 16 GB | 600 GB | Fino a 550 computer<br> Presuppone che ogni computer disponga di tre dischi di 100 GB ciascuno.

- Questi limiti sono basati su un server di configurazione configurato usando un modello OVF.
- I limiti presuppongono che non si stia usando il server di elaborazione in esecuzione per impostazione predefinita nel server di configurazione.

Se è necessario aggiungere un nuovo server di configurazione, seguire queste istruzioni:

- [Configurare un server di configurazione per il](vmware-azure-deploy-configuration-server.md) ripristino di emergenza di macchine virtuali VMware usando un modello OVF.
- [Configurare manualmente un server di configurazione](physical-azure-set-up-source.md) per i server fisici o per le distribuzioni VMware che non possono usare un modello OVF.

Quando si configura un server di configurazione, tenere presente quanto segue:

- Quando si configura un server di configurazione, è importante prendere in considerazione la sottoscrizione e l'insieme di credenziali in cui risiede, perché questi non devono essere modificati dopo l'installazione. Se è necessario modificare l'insieme di credenziali, è necessario annullare l'associazione del server di configurazione dall'insieme di credenziali e registrarlo nuovamente. Questa operazione interrompe la replica delle VM nell'insieme di credenziali.
- Se si desidera configurare un server di configurazione con più schede di rete, è consigliabile eseguire questa operazione durante la configurazione. Non è possibile eseguire questa operazione dopo la registrazione del server di configurazione nell'insieme di credenziali.

## <a name="set-up-a-process-server"></a>Configurare un server di elaborazione

La capacità del server di elaborazione è interessata dalle percentuali di varianza dei dati e non dal numero di macchine virtuali abilitate per la replica.

- Per distribuzioni di grandi dimensioni è necessario disporre sempre di almeno un server di elaborazione con scalabilità orizzontale.
- Per determinare se sono necessari server aggiuntivi, usare la tabella seguente.
- Si consiglia di aggiungere un server con la specifica più elevata. 


**CPU** | **Memoria** | **Disco cache** | **Frequenza di varianza**
 --- | --- | --- | --- 
12 vCPU<br> 2 socket * 6 core a 2,5 GHz | 24 GB | 1 GB | Fino a 2 TB al giorno

Configurare il server di elaborazione come indicato di seguito:

1. Esaminare i [prerequisiti](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installare il server nel [portale](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)o dalla [riga di comando](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configurare i computer replicati per l'utilizzo del nuovo server. Se sono già presenti computer che eseguono la replica:
    - È possibile [spostare](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) un intero carico di lavoro del server di elaborazione nel nuovo server di elaborazione.
    - In alternativa, è possibile [spostare](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) macchine virtuali specifiche nel nuovo server di elaborazione.



## <a name="enable-large-scale-replication"></a>Abilitare la replica su larga scala

Dopo aver pianificato la capacità e aver distribuito i componenti e l'infrastruttura necessari, abilitare la replica per un numero elevato di macchine virtuali.

1. Ordinare i computer in batch. Abilitare la replica per le macchine virtuali all'interno di un batch e quindi passare al batch successivo.

    - Per le macchine virtuali VMware, è possibile usare le dimensioni consigliate per il [batch di macchine](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) virtuali nel report Deployment Planner.
    - Per i computer fisici, è consigliabile identificare i batch in base ai computer con dimensioni e quantità di dati simili e sulla velocità effettiva di rete disponibile. Lo scopo è quello di eseguire il batch dei computer che probabilmente completano la replica iniziale in quasi la stessa quantità di tempo.
    
2. Se la varianza del disco per un computer è elevata o supera i limiti di thePlanner di distribuzione, è possibile spostare i file non critici che non è necessario replicare, ad esempio i dump dei log o i file temporanei, dal computer. Per le macchine virtuali VMware, è possibile spostare i file in un disco separato, quindi [escludere il disco](vmware-azure-exclude-disk.md) dalla replica.
3. Prima di abilitare la replica, verificare che i computer soddisfino i [requisiti di replica](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configurare un criterio di replica per [macchine virtuali VMware](vmware-azure-set-up-replication.md#create-a-policy) o [server fisici](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Abilitare la replica per [macchine virtuali VMware](vmware-azure-enable-replication.md) o [server fisici](physical-azure-disaster-recovery.md#enable-replication). Viene avviata la replica iniziale per i computer selezionati.

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Dopo aver avviato la replica per il primo batch di macchine virtuali, avviare il monitoraggio della distribuzione come segue:  

1. Assegnare un amministratore del ripristino di emergenza per monitorare lo stato di integrità dei computer replicati.
2. [Monitorare gli eventi](site-recovery-monitor-and-troubleshoot.md) per gli elementi replicati e l'infrastruttura.
3. [Monitorare l'integrità](vmware-physical-azure-monitor-process-server.md) dei server di elaborazione con scalabilità orizzontale.
4. Iscriversi per ricevere [notifiche tramite posta elettronica](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) per gli eventi, per semplificare il monitoraggio.
5. Eseguire esercitazioni regolari per il [ripristino di emergenza](site-recovery-test-failover-to-azure.md), per assicurarsi che tutto funzioni come previsto.


## <a name="plan-for-large-scale-failovers"></a>Pianificare i failover su larga scala

In caso di emergenza, potrebbe essere necessario eseguire il failover di un numero elevato di computer/carichi di lavoro in Azure. Prepararsi per questo tipo di evento come indicato di seguito.

È possibile preparare in anticipo il failover nel modo seguente:

- [Preparare l'infrastruttura e le macchine virtuali](#plan-infrastructure-and-vm-connectivity) in modo che i carichi di lavoro saranno disponibili dopo il failover e in modo che gli utenti possano accedere alle macchine virtuali di Azure.
- Si notino i [limiti di failover](#failover-limits) più indietro in questo documento. Assicurarsi che i failover rientrino in questi limiti.
- Eseguire esercitazioni regolari per il [ripristino di emergenza](site-recovery-test-failover-to-azure.md). Consente di eseguire il drill-down per:
    - Individuare i gap nella distribuzione prima del failover.
    - Stimare RTO end-to-end per le app.
    - Stimare RPO end-to-end per i carichi di lavoro.
    - Identificare i conflitti di intervallo di indirizzi IP.
    - Quando si eseguono le esercitazioni, si consiglia di non usare le reti di produzione per le esercitazioni, evitare di usare gli stessi nomi di subnet nelle reti di produzione e di test e pulire i failover di test dopo ogni drill-through.

Per eseguire un failover su larga scala, si consiglia quanto segue:

1. Creare piani di ripristino per il failover del carico di lavoro.
    - Ogni piano di ripristino può attivare il failover di un massimo di 50 computer.
    - [Ulteriori informazioni](recovery-plan-overview.md) sui piani di ripristino.
2. Aggiungere gli script Runbook di automazione di Azure ai piani di ripristino per automatizzare le attività manuali in Azure. Le attività tipiche includono la configurazione di bilanciamento del carico, l'aggiornamento di DNS e così via. [Altre informazioni](site-recovery-runbook-automation.md)
2. Prima del failover, preparare i computer Windows in modo che siano conformi all'ambiente Azure. I [limiti di failover](#plan-azure-subscriptions-and-quotas) sono maggiori per i computer conformi. [Altre](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) informazioni su manuali operativi.
4.  Attivare il failover con il cmdlet [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) di PowerShell, insieme a un piano di ripristino.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Site Recovery di monitoraggio](site-recovery-monitor-and-troubleshoot.md)
