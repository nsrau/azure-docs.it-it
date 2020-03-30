---
title: Migrazione basata su agenti nella migrazione del server di migrazione di Azure MigrateAgent-based migration in Azure Migrate Server Migration
description: Fornisce una panoramica della migrazione di macchine virtuali VMware basata su agente in Azure Migrate.Provides an overview of agent-based VMware VM migration in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425859"
---
# <a name="agent-based-migration-architecture"></a>Architettura di migrazione basata su agente

Questo articolo offre una panoramica dell'architettura e dei processi usati per la replica basata su agenti delle macchine virtuali VMware con lo strumento [Azure Migrate: Server Migration.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Usando Azure Migrate: server Migration, è possibile replicare le macchine virtuali VMware con un paio di opzioni:Using Azure Migrate: Server Migration, you can replicate VMware VMs with a couple of options:

- Eseguire la migrazione delle macchine virtuali usando la replica basata su agente, come descritto in questo articolo.
- Eseguire la migrazione di macchine virtuali VMware utilizzando la replica senza agente. In questo modo viene eseguita la migrazione delle macchine virtuali senza dover installare alcun elemento in esse.

Altre informazioni sulla [selezione e](server-migrate-overview.md) il confronto dei metodi di migrazione per le macchine virtuali VMware.Learn more about selecting and comparing migration methods for VMware VMs. 


## <a name="agent-based-migration"></a>Migrazione basata su agente

La migrazione basata su agente viene usata per eseguire la migrazione delle macchine virtuali VMware locali e dei server fisici in Azure.Agent-based migration is used to migrate on-premises VMware VMs and physical servers to Azure. Può anche essere usato per eseguire la migrazione di altri server virtualizzati locali, nonché di macchine virtuali private e pubbliche cloud, incluse le istanze AWS e le macchine virtuali GCP. La migrazione basata su agente in Azure Migrate usa alcune funzionalità di back-end del servizio Azure Site Recovery.Agent-based migration in Azure Migrate uses some backend functionality from the [Azure Site Recovery](../site-recovery/site-recovery-overview.md) service.


## <a name="architectural-components"></a>Componenti dell'architettura

Il diagramma illustra i componenti coinvolti nella migrazione basata su agenti.

![Architecture](./media/agent-based-replication-architecture/architecture.png)

Nella tabella sono riepilogati i componenti utilizzati per la migrazione basata su agenti.

**Componente** | **Dettagli** | **Installazione**
--- | --- | ---
**Appliance di replica** | L'appliance di replica (server di configurazione/server di elaborazione) è un computer locale che funge da ponte tra l'ambiente locale e la migrazione del server. L'appliance individua l'inventario del computer locale, in modo che la migrazione del server possa orchestrare la replica e la migrazione. L'apparecchio è dotato di due componenti:<br/><br/> **Server di configurazione**: Si connette alla migrazione del server e coordina la replica.<br/> **Server di elaborazione**: gestisce la replica dei dati. Il server di elaborazione riceve i dati del computer, li comprime e li crittografa e li invia ad Azure.The process server receives machine data, compresse s compresss and encrypts, and sends to Azure. In Azure la migrazione del server scrive i dati nei dischi gestiti. | Per impostazione predefinita, il server di elaborazione viene installato insieme al server di configurazione nell'appliance di replica.
**Servizio Mobility** | Il servizio Mobility è un agente installato in ogni computer di cui si desidera eseguire la replica e la migrazione. Invia i dati di replica dal computer al server di elaborazione. | I file di installazione per versioni diverse del servizio Mobility si trovano nell'appliance di replica. Si scarica e si installa l'agente necessario, in base al sistema operativo e alla versione del computer che si desidera replicare.

## <a name="mobility-service-installation"></a>Installazione del servizio Mobility

È possibile distribuire il servizio Mobility tramite i metodi seguenti:

- **Installazione push**: il servizio Mobility viene installato dal server di elaborazione quando si abilita la protezione per un computer. 
- **Installa manualmente**: È possibile installare manualmente il servizio Mobility in ogni computer tramite l'interfaccia utente o il prompt dei comandi.

Il servizio Mobility comunica con l'appliance di replica e i computer replicati. Se si dispone di un software antivirus in esecuzione sull'appliance di replica, sui server di elaborazione o sui computer replicati, le cartelle seguenti devono essere escluse dall'analisi:


- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Programmi (x86)\Microsoft Azure Site Recovery
- (su computer Windows in cui è installato il servizio Mobility)

## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina, inizia la replica iniziale in Azure.When you enable replication for a machine, initial replication to Azure begins.
2. Durante la replica iniziale, il servizio Mobility legge i dati dai dischi del computer e li invia al server di elaborazione.
3. Questi dati vengono usati per eseguire il seeding di una copia del disco nella sottoscrizione di Azure.This data is used to seed a copy of the disk in your Azure subscription. 
4. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. La replica è a livello di blocco e quasi continua.
4. Il servizio Mobility intercetta le scritture nella memoria su disco, integrandosi con il sottosistema di archiviazione del sistema operativo. Questo metodo consente di evitare le operazioni di I/O del disco nel computer di replica, per la replica incrementale. 
5. Le revisioni di un computer vengono inviate al server di elaborazione sulla porta in ingresso HTTPS 9443. La porta può essere modificata. Il server di elaborazione lo comprime e lo crittografa e lo invia ad Azure.The process server compresse and encrypts it, and sends it to Azure. 

## <a name="ports"></a>Porte

**Dispositivo** | **Connessione**
--- | --- 
**Macchine di replica** | Il servizio Mobility in esecuzione nelle macchine virtuali comunica con l'appliance di replica locale sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> I computer inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
**Appliance di replica** | L'appliance di replica orchestra la replica con Azure sulla porta HTTPS 443 in uscita.
**Server di elaborazione** | Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.


## <a name="performance-and-scaling"></a>Prestazioni e ridimensionamento

Per impostazione predefinita, si distribuisce una singola appliance di replica che esegue sia il server di configurazione che il server di elaborazione. Se si replicano solo alcuni computer, questa distribuzione è sufficiente. Tuttavia, se si esegue la replica e la migrazione di centinaia di computer, un singolo server di elaborazione potrebbe non essere in grado di gestire tutto il traffico di replica. In questo caso, è possibile distribuire server di elaborazione con scalabilità orizzontale aggiuntivi.

### <a name="plan-vmware-deployment"></a>Pianificare la distribuzione di VMware

Se si replicano macchine virtuali VMware, è possibile utilizzare [Site Recovery Planner per VMware](../site-recovery/site-recovery-deployment-planner.md)per determinare i requisiti di prestazioni, inclusa la frequenza giornaliera di modifica dei dati e i server di elaborazione necessari.

### <a name="replication-appliance-capacity"></a>Capacità dell'appliance di replica

Utilizzare i valori in questa tabella per determinare se è necessario un server di elaborazione aggiuntivo nella distribuzione.

- Se la frequenza di modifica giornaliera (frequenza di varianza) supera i 2 TB, distribuire un server di elaborazione aggiuntivo.
- Se si esegue la replica di più di 200 computer, distribuire un'appliance di replica aggiuntiva.

**CPU** | **Memoria** | **Memorizzazione nella cache dei dati di spazio liberi** | **Tasso di varianza** | **Limiti di replica**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | < 100 computer 
12 vCPU (2 socket * 6 core \@ 2,5 GHz) | 18 GB | 600 GB | Da 501 GB a 1 TB | 100-150 macchine.
16 vCPU (2 socket * 8 core \@ 2,5 GHz) | 32 G1 |  1 TB | Da 1 TB a 2 TB | 151-200 macchine.

### <a name="sizing-scale-out-process-servers"></a>Dimensionamento dei server di elaborazione con scalabilità orizzontale

Se è necessario distribuire un server di elaborazione con scalabilità orizzontale, utilizzare questa tabella per determinare il dimensionamento del server.

**Server di elaborazione** | **Spazio libero per la memorizzazione nella cache dei dati** | **Tasso di varianza** | **Limiti di replica**
--- | --- | --- | --- 
4 vCPU (2 socket * 2 core \@ 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | Fino a 85 macchine 
8 vCPU (2 socket * 4 core \@ 2,5 GHz), 12 GB di memoria | 600 GB | Da 251 GB a 1 TB    | Macchine da 86 a 150 macchine.
12 vCPU (2 socket e \@ 6 core 2,5 GHz), 24 GB di memoria | 1 TB | 1-2 TB | 151-225 macchine.

## <a name="throttle-upload-bandwidth"></a>Limitare la larghezza di banda di caricamento.

il traffico VMware che viene replicato in Azure passa attraverso un server di elaborazione specifico. È possibile limitare la velocità effettiva di caricamento limitando la larghezza di banda nei computer in esecuzione come server di elaborazione. È possibile influenzare la larghezza di banda utilizzando questa chiave del Registro di sistema:You can influence bandwidth using this registry key:

- Il valore del Registro di sistema HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM specifica il numero di thread usati per il trasferimento dati di un disco, durante la replica iniziale o differenziale. Un valore più elevato aumenta la larghezza di banda di rete usata per la replica. Il valore predefinito è quattro. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.
- Inoltre, è possibile limitare la larghezza di banda sul computer del server di elaborazione come segue:

    1. Nel computer server di elaborazione aprire lo snap-in MMC Backup di Azure. C'è un collegamento sul desktop o nella cartella C: . 
    2. Nello snap-in selezionare **Modifica proprietà**.
    3. In **Limitazione**selezionare **Abilita limitazione utilizzo larghezza di banda Internet per le operazioni**di backup . Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 1.023 Mbps.


## <a name="next-steps"></a>Passaggi successivi

Provare la [migrazione basata su agenti](tutorial-migrate-vmware-agent.md) per [VMware](tutorial-migrate-vmware-agent.md) o [server fisici.](tutorial-migrate-physical-virtual-machines.md)
