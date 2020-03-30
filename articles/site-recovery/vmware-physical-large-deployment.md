---
title: Scalabilità di VMware/ripristino di emergenza fisico con Azure Site RecoveryScale VMware/physical disaster recovery with Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza in Azure per un numero elevato di macchine virtuali VMware locali o server fisici con Azure Site Recovery.Learn how to set up disaster recovery to Azure for large numbers of on-premises VMware VMs or physical servers with Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 36cc63721fe003934aabfb3ae2a03a4113937ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256939"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurare il ripristino di emergenza su larga scala per le macchine virtuali/server fisici VMwareSet up disaster recovery at scale for VMware VMs/physical servers

Questo articolo descrive come configurare il ripristino di emergenza in Azure per un numero elevato (> 1000) di macchine virtuali VMware locali o server fisici nell'ambiente di produzione, usando il servizio [Azure Site Recovery.This](site-recovery-overview.md) article describes how to set up disaster recovery to Azure for large numbers (> 1000) of on-premises VMware VMs or physical servers in your production environment, using the Azure Site Recovery service.


## <a name="define-your-bcdr-strategy"></a>Definisci la tua strategia BCDR

Nell'ambito della strategia di continuità aziendale e ripristino di emergenza (BCDR), si definiscono gli obiettivi del punto di ripristino (RPO) e gli obiettivi del tempo di ripristino (RTO) per le app aziendali e i carichi di lavoro. RTO misura la durata del tempo e il livello di servizio entro il quale un'app o un processo aziendale deve essere ripristinato e disponibile, al fine di evitare problemi di continuità.
- Site Recovery fornisce la replica continua per le macchine virtuali VMware e i server fisici e un servizio di controllo di accesso rapido per [RTO.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)
- Durante la pianificazione del ripristino di emergenza su larga scala per le macchine virtuali VMware e la definizione delle risorse di Azure necessarie, è possibile specificare un valore RTO che verrà usato per i calcoli della capacità.


## <a name="best-practices"></a>Procedure consigliate

Alcune procedure consigliate generali per il ripristino di emergenza su larga scala. Queste procedure consigliate vengono descritte in modo più dettagliato nelle sezioni successive del documento.

- **Identificare i requisiti di destinazione:** stimare le esigenze di capacità e risorse in Azure prima di configurare il ripristino di emergenza.
- **Pianificare i componenti**di Site Recovery : individuare i componenti di Site Recovery (server di configurazione, server di elaborazione) necessari per soddisfare la capacità stimata.
- **Configurare uno o più server di elaborazione con scalabilità orizzontale:** non utilizzare il server di elaborazione in esecuzione per impostazione predefinita nel server di configurazione. 
- **Eseguire gli aggiornamenti più recenti:** il team di Site Recovery rilascia regolarmente nuove versioni dei componenti di Site Recovery ed è necessario assicurarsi di eseguire le versioni più recenti. Per risolvere questo tipo, tenere traccia delle [novità](site-recovery-whats-new.md) per gli aggiornamenti e [abilitare e installare gli aggiornamenti](service-updates-how-to.md) non appena vengono rilasciati.
- **Monitorare in modo proattivo:** durante l'esecuzione del ripristino di emergenza, è necessario monitorare in modo proattivo lo stato e l'integrità dei computer replicati e le risorse dell'infrastruttura.
- **Esercitazioni**per il ripristino di emergenza: è consigliabile eseguire esercitazioni di ripristino di emergenza su base regolare. Questi non influiscono sull'ambiente di produzione, ma contribuiscono a garantire che il failover in Azure funzioni come previsto quando necessario.



## <a name="gather-capacity-planning-information"></a>Raccogliere informazioni sulla pianificazione della capacità

Raccogliere informazioni sull'ambiente locale per valutare e stimare le esigenze di capacità di destinazione (Azure).
- Per VMware, eseguire La pianificazione della distribuzione per le macchine virtuali VMware per eseguire questa operazione.
- Per i server fisici, raccogliere le informazioni manualmente.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Eseguire la pianificazione della distribuzione per le macchine virtuali VMwareRun the Deployment Planner for VMware VMs

Pianificazione distribuzione consente di raccogliere informazioni sull'ambiente vMware locale.

- Eseguire Deployment Planner durante un periodo che rappresenta la varianza tipica per le macchine virtuali. Ciò genererà stime e raccomandazioni più accurate.
- È consigliabile eseguire Deployment Planner nel computer del server di configurazione, poiché Planner calcola la velocità effettiva dal server in cui è in esecuzione. [Ulteriori informazioni](site-recovery-vmware-deployment-planner-run.md#get-throughput) sulla misurazione della velocità effettiva.
- Se non è ancora configurato un server di configurazione:
    - [Panoramica](vmware-physical-azure-config-process-server-overview.md) dei componenti di Site Recovery.
    - [Impostare un server](vmware-azure-deploy-configuration-server.md)di configurazione per eseguire La Pianificazione distribuzione su di esso.

Quindi eseguire il Planner come segue:

1. [Informazioni su](site-recovery-deployment-planner.md) Deployment Planner. È possibile scaricare la versione più recente dal portale o [scaricarla direttamente.](https://aka.ms/asr-deployment-planner)
2. Esaminare [i prerequisiti](site-recovery-deployment-planner.md#prerequisites) e [gli aggiornamenti più recenti](site-recovery-deployment-planner-history.md) per Deployment Planner e scaricare ed [estrarre](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) lo strumento.
3. [Eseguire Deployment Planner](site-recovery-vmware-deployment-planner-run.md) nel server di configurazione.
4. [Generare un report](site-recovery-vmware-deployment-planner-run.md#generate-report) per riepilogare le stime e i suggerimenti.
5. Analizzare le [raccomandazioni](site-recovery-vmware-deployment-planner-analyze-report.md) del report e le [stime dei costi](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Per impostazione predefinita, lo strumento è configurato per il profilo e genera report per un massimo di 1000 macchine virtuali. È possibile modificare questo limite aumentando il valore della chiave MaxVMsSupported nel file ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Pianificare i requisiti e la capacità dell'obiettivo (Azure)

Utilizzando le stime e i consigli raccolti, è possibile pianificare le risorse e la capacità di destinazione. Se è stato eseguito il Pianificatore di distribuzione per le macchine virtuali VMware, è possibile usare una serie di suggerimenti per i [report.](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)

- Macchine virtuali compatibili: usare questo numero per identificare il numero di macchine virtuali pronte per il ripristino di emergenza in **Azure.Compatible VMs**: Use this number to identify the number of VMs that are ready for disaster recovery to Azure. I suggerimenti sulla larghezza di banda di rete e sui core di Azure si basano su questo numero.
- **Larghezza di banda di rete necessaria:** notare la larghezza di banda necessaria per la replica delta delle macchine virtuali compatibili. 
    - Quando si esegue il Planner si specifica l'RPO desiderato in minuti. I consigli mostrano la larghezza di banda necessaria per soddisfare tale RPO 100% e 90% del tempo. 
    - I suggerimenti per la larghezza di banda di rete tengono conto della larghezza di banda necessaria per il numero totale di server di configurazione e server di elaborazione consigliati in Planner.
- **Core di Azure necessari:** notare il numero di core necessari nell'area di Azure di destinazione, in base al numero di macchine virtuali compatibili. Se non si dispone di abbastanza core, al failover Site Recovery non sarà in grado di creare le macchine virtuali di Azure necessarie.
- **Dimensioni consigliate del batch di macchine virtuali:** la dimensione del batch consigliata si basa sulla possibilità di completare la replica iniziale per il batch entro 72 ore per impostazione predefinita, soddisfacendo un RPO del 100%. Il valore dell'ora può essere modificato.

È possibile usare questi consigli per pianificare le risorse di Azure, la larghezza di banda di rete e l'invio in batch delle macchine virtuali.

## <a name="plan-azure-subscriptions-and-quotas"></a>Pianificare le sottoscrizioni e le quote di AzurePlan Azure subscriptions and quotas

È necessario assicurarsi che le quote disponibili nella sottoscrizione di destinazione siano sufficienti per gestire il failover.

**Attività** | **Dettagli** | **Azione**
--- | --- | ---
**Controllare i core** | Se i core nella quota disponibile non corrispondono o superano il numero totale di destinazione al momento del failover, i failover avranno esito negativo. | Per le macchine virtuali VMware, verificare di disporre di un numero sufficiente di core nella sottoscrizione di destinazione per soddisfare la raccomandazione di base di Deployment Planner.For VMware VMs, check you have enough cores in the target subscription to meet the Deployment Planner core recommendation.<br/><br/> Per i server fisici, verificare che i core di Azure soddisfino le stime manuali.<br/><br/> Per controllare le quote, nel portale di Azure > **sottoscrizione**fare clic su **Utilizzo , quote**.<br/><br/> [Ulteriori informazioni](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) sull'aumento delle quote.
**Controllare i limiti di failover** | Il numero di failover non deve superare i limiti di failover di Site Recovery. |  Se i failover superano i limiti, è possibile aggiungere sottoscrizioni ed eseguire il failover a più sottoscrizioni oppure aumentare la quota per una sottoscrizione. 


### <a name="failover-limits"></a>Limiti di failover

I limiti indicano il numero di failover supportati da Site Recovery entro un'ora, presupponendo tre dischi per computer.

Che cosa significa rispetto? Per avviare una macchina virtuale di Azure, Azure richiede che alcuni driver siano nello stato di avvio e che servizi come DHCP siano impostati per l'avvio automatico.
- Le macchine conformi avranno già queste impostazioni in atto.
- Per i computer che eseguono Windows, è possibile verificare in modo proattivo la conformità e renderli conformi se necessario. [Scopri di più](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Le macchine Linux vengono rese conformi solo al momento del failover.

**La macchina è conforme ad Azure?** | **Limiti delle macchine virtuali di Azure (failover del disco gestito)Azure VM limits (managed disk failover)**
--- | --- 
Sì | 2000
No | 1000

- I limiti presuppongono che nell'area di destinazione siano in corso almeno altri processi.
- Alcune aree di Azure sono più piccole e potrebbero avere limiti leggermente inferiori.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Pianificare la connettività dell'infrastruttura e delle macchine virtualiPlan infrastructure and VM connectivity

Dopo il failover in Azure, è necessario che i carichi di lavoro funzionino come in locale e consentano agli utenti di accedere ai carichi di lavoro in esecuzione nelle macchine virtuali di Azure.After failover to Azure you need your workloads to operate as they did on-premises, and to enable users to access workloads running on the Azure VMs.

- [Altre informazioni](site-recovery-active-directory.md#test-failover-considerations) sul failover dell'infrastruttura locale di Active Directory o DNS in Azure.Learn more about failing ing Active Directory or DNS on-premises infrastructure to Azure.
- [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sulla preparazione per la connessione alle macchine virtuali di Azure dopo il failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Pianificare la capacità e i requisiti di origine

È importante disporre di server di configurazione sufficienti e server di elaborazione con scalabilità orizzontale per soddisfare i requisiti di capacità. Quando si inizia la distribuzione su larga scala, iniziare con un singolo server di configurazione e un singolo server di elaborazione con scalabilità orizzontale. Quando si raggiungono i limiti prescritti, aggiungere altri server.

>[!NOTE]
> Per le macchine virtuali VMware, Deployment Planner fornisce alcuni suggerimenti sui server di configurazione e di elaborazione necessari. Si consiglia di utilizzare le tabelle incluse nelle procedure seguenti, anziché seguire la raccomandazione di Deployment Planner. 


## <a name="set-up-a-configuration-server"></a>Configurare un server di configurazione
 
La capacità del server di configurazione è influenzata dal numero di computer che vengono replicati e non dalla velocità di varianza dei dati. Per determinare se sono necessari server di configurazione aggiuntivi, usare questi limiti definiti per le macchine virtuali.

**CPU** | **Memoria** | **Disco cache** | **Limite macchina replicata**
 --- | --- | --- | ---
8 vCPU<br> 2 prese - 4 core - 2,5 Ghz | 16 GB | 600 GB | Fino a 550 macchine<br> Si supponga che ogni computer disponga di tre dischi da 100 GB ciascuno.

- Questi limiti sono basati su un server di configurazione configurato utilizzando un modello OVF.
- I limiti presuppongono che non si utilizzi il server di elaborazione in esecuzione per impostazione predefinita nel server di configurazione.

Se è necessario aggiungere un nuovo server di configurazione, seguire queste istruzioni:If you need to add a new configuration server, follow these instructions:

- [Configurare un server](vmware-azure-deploy-configuration-server.md) di configurazione per il ripristino di emergenza della macchina virtuale VMware usando un modello OVF.
- [Configurare un server](physical-azure-set-up-source.md) di configurazione manualmente per i server fisici o per le distribuzioni VMware che non possono utilizzare un modello OVF.

Durante l'impostazione di un server di configurazione, tenere presente che:

- Quando si configura un server di configurazione, è importante considerare la sottoscrizione e l'insieme di credenziali all'interno del quale si trova, poiché questi non devono essere modificati dopo l'installazione. Se è necessario modificare l'insieme di credenziali, è necessario dissociarlo dall'insieme di credenziali e registrarlo nuovamente. In questo modo si interrompe la replica delle macchine virtuali nell'insieme di credenziali.
- Se si desidera configurare un server di configurazione con più schede di rete, è necessario eseguire questa operazione durante la configurazione. Non è possibile eseguire questa operazione dopo la registrazione del server di configurazione nell'insieme di credenziali.

## <a name="set-up-a-process-server"></a>Configurare un server di elaborazione

La capacità del server di elaborazione è influenzata dalla frequenza di varianza dei dati e non dal numero di computer abilitati per la replica.

- Per le distribuzioni di grandi dimensioni è sempre necessario disporre di almeno un server di elaborazione con scalabilità orizzontale.
- Per determinare se sono necessari server aggiuntivi, utilizzare la tabella seguente.
- Si consiglia di aggiungere un server con la specifica più alta. 


**CPU** | **Memoria** | **Disco cache** | **Tasso di varianza**
 --- | --- | --- | --- 
12 vCPU<br> 2 prese: 6 core e 2,5 Ghz | 24 GB | 1 GB | Fino a 2 TB al giorno

Impostare il server di elaborazione come segue:

1. Esaminare i [prerequisiti](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installare il server nel [portale](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)o dalla riga di [comando](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configurare i computer replicati per l'utilizzo del nuovo server. Se si dispone già di macchine che replicano:
    - È possibile [spostare](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) un intero carico di lavoro del server di elaborazione nel nuovo server di elaborazione.
    - In alternativa, è possibile [spostare](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) macchine virtuali specifiche nel nuovo server di elaborazione.



## <a name="enable-large-scale-replication"></a>Abilitare la replica su larga scalaEnable large-scale replication

Dopo aver pianificato la capacità e distribuito i componenti e l'infrastruttura necessari, abilitare la replica per un numero elevato di macchine virtuali.

1. Ordinare le macchine in batch. Attivare la replica per le macchine virtuali all'interno di un batch e quindi passare al batch successivo.

    - Per le macchine virtuali VMware, è possibile usare la [dimensione batch della macchina virtuale consigliata](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) nel report Pianificazione distribuzione.
    - Per i computer fisici, è consigliabile identificare i batch in base a computer con dimensioni e quantità di dati simili e alla velocità effettiva di rete disponibile. L'obiettivo è quello di macchine batch che probabilmente termineranno la replica iniziale nello stesso lasso di tempo.
    
2. Se la varianza del disco per un computer è elevata o supera i limiti nella distribuzione di Deployment thePlanner, è possibile spostare i file non critici che non è necessario replicare (ad esempio dump di log o file temporanei) dal computer. Per le macchine virtuali VMware, è possibile spostare questi file in un disco separato e quindi [escludere tale disco](vmware-azure-exclude-disk.md) dalla replica.
3. Prima di abilitare la replica, verificare che i computer [soddisfino i requisiti di replica.](vmware-physical-azure-support-matrix.md#replicated-machines)
4. Configurare un criterio di replica per [le macchine virtuali VMware](vmware-azure-set-up-replication.md#create-a-policy) o [i server fisici.](physical-azure-disaster-recovery.md#create-a-replication-policy)
5. Abilitare la replica per [le macchine virtuali VMware](vmware-azure-enable-replication.md) o [i server fisici.](physical-azure-disaster-recovery.md#enable-replication) In questo modo viene evitata la replica iniziale per i computer selezionati.

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Dopo aver avviato la replica per il primo batch di macchine virtuali, avviare il monitoraggio della distribuzione come segue:After you kick off replication for the first batch of VMs, start monitoring your deployment as follows:  

1. Assegnare un amministratore di ripristino di emergenza per monitorare lo stato di integrità dei computer replicati.
2. [Monitorare gli eventi](site-recovery-monitor-and-troubleshoot.md) per gli elementi replicati e l'infrastruttura.
3. [Monitorare l'integrità](vmware-physical-azure-monitor-process-server.md) dei server di elaborazione con scalabilità orizzontale.
4. Iscriviti per ricevere [notifiche e-mail](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) per gli eventi, per un monitoraggio più semplice.
5. Eseguire [esercitazioni](site-recovery-test-failover-to-azure.md)regolari di ripristino di emergenza per assicurarsi che tutto funzioni come previsto.


## <a name="plan-for-large-scale-failovers"></a>Pianificare failover su larga scala

In an event of disaster, you might need to fail over a large number of machines/workloads to Azure. Prepararsi per questo tipo di evento come indicato di seguito.

È possibile prepararsi in anticipo per il failover come indicato di seguito:You can prepare in advance for failover as follows:

- [Preparare l'infrastruttura e](#plan-infrastructure-and-vm-connectivity) le macchine virtuali in modo che i carichi di lavoro siano disponibili dopo il failover e in modo che gli utenti possano accedere alle macchine virtuali di Azure.Prepare your infrastructure and VMs so that your workloads will be available after failover, and so that users can access the Azure VMs.
- Prendere nota dei limiti di [failover](#failover-limits) più indietro in questo documento. Assicurarsi che i failover rientrino in questi limiti.
- Eseguire esercitazioni regolari per [il ripristino di emergenza.](site-recovery-test-failover-to-azure.md) I trapani aiutano a:
    - Individuare le lacune nella distribuzione prima del failover.
    - Stimare l'RTO end-to-end per le app.
    - Stimare RPO end-to-end per i carichi di lavoro.
    - Identificare i conflitti tra gli intervalli di indirizzi IP.
    - Durante l'esecuzione delle esercitazioni, è consigliabile non usare le reti di produzione per le esercitazioni, evitare di usare gli stessi nomi di subnet nelle reti di produzione e di test e pulire i failover di test dopo ogni esercitazione.

Per eseguire un failover su larga scala, è consigliabile eseguire le operazioni seguenti:To run a large-scale failover, we recommend the following:

1. Creare piani di ripristino per il failover del carico di lavoro.
    - Ogni piano di ripristino può attivare il failover di un massimo di 50 computer.
    - [Ulteriori informazioni](recovery-plan-overview.md) sui piani di ripristino.
2. Aggiungere script di runbook di Automazione di Azure ai piani di ripristino per automatizzare eventuali attività manuali in Azure.Add Azure Automation runbook scripts to recovery plans, to automate any manual tasks on Azure. Le attività tipiche includono la configurazione dei servizi di bilanciamento del carico, l'aggiornamento del DNS e così via. [Scopri di più](site-recovery-runbook-automation.md)
2. Prima del failover, preparare i computer Windows in modo che siano conformi all'ambiente Azure.Before failover, prepare Windows machines so that they comply with the Azure environment. [I limiti](#plan-azure-subscriptions-and-quotas) di failover sono superiori per i computer conformi. [Ulteriori informazioni](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) sui runbook.
4.  Attivare il failover con il cmdlet [PowerShell Start-AzRecoveryServicesAsrPlannedFailoverJob,](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) insieme a un piano di ripristino.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare Site Recovery](site-recovery-monitor-and-troubleshoot.md)
