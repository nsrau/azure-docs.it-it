---
title: Migrazione basata su agente in Azure Migrate migrazione del server
description: Viene fornita una panoramica della migrazione di macchine virtuali VMware basate su agente in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: 90e499b436a3ae44fa29cec1138d939a106a4db7
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91357166"
---
# <a name="agent-based-migration-architecture"></a>Architettura di migrazione basata su agente

Questo articolo fornisce una panoramica dell'architettura e dei processi usati per la replica basata su agenti delle VM VMware con lo strumento di [migrazione Azure migrate: server](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Utilizzando Azure Migrate: migrazione del server, è possibile eseguire la replica di macchine virtuali VMware con due opzioni:

- Eseguire la migrazione di macchine virtuali tramite la replica basata su agente, come descritto in questo articolo.
- Eseguire la migrazione di macchine virtuali VMware con la replica senza agente. Viene eseguita la migrazione delle macchine virtuali senza dover installare alcun elemento.

Altre informazioni su come [selezionare e confrontare](server-migrate-overview.md) i metodi di migrazione per le macchine virtuali VMware. 


## <a name="agent-based-migration"></a>Migrazione basata su agenti

La migrazione basata su agenti viene usata per eseguire la migrazione di server fisici e macchine virtuali VMware locali in Azure. Può anche essere usato per eseguire la migrazione di altri server virtualizzati locali, nonché per le macchine virtuali di cloud privato e pubblico, incluse le istanze di AWS e le macchine virtuali GCP. La migrazione basata su agente in Azure Migrate usa alcune funzionalità di back-end del servizio [Azure Site Recovery](../site-recovery/site-recovery-overview.md) .


## <a name="architectural-components"></a>Componenti dell'architettura

Il diagramma illustra i componenti necessari per la migrazione basata su agenti.

![Il diagramma mostra i componenti per la migrazione basata su agenti, illustrati in una tabella.](./media/agent-based-replication-architecture/architecture.png)

Nella tabella vengono riepilogati i componenti utilizzati per la migrazione basata su agenti.

**Componente** | **Informazioni dettagliate** | **Installazione**
--- | --- | ---
**Appliance di replica** | Il dispositivo di replica (server di configurazione/server di elaborazione) è un computer locale che funge da Bridge tra l'ambiente locale e la migrazione del server. L'appliance individua l'inventario del computer locale, in modo che la migrazione del server possa orchestrare la replica e la migrazione. Il dispositivo è costituito da due componenti:<br/><br/> **Server di configurazione**: si connette alla migrazione del server e coordina la replica.<br/> **Server di elaborazione**: gestisce la replica dei dati. Il server di elaborazione riceve i dati del computer, li comprime e li crittografa e li invia ad Azure. In Azure, la migrazione del server scrive i dati in Managed Disks. | Per impostazione predefinita, il server di elaborazione viene installato insieme al server di configurazione nell'appliance di replica.
**Servizio Mobility** | Il servizio Mobility è un agente installato in ogni computer di cui si vuole eseguire la replica e la migrazione. Invia i dati di replica dal computer al server di elaborazione. | I file di installazione per versioni diverse del servizio Mobility si trovano nell'appliance di replica. Scaricare e installare l'agente necessario, in base al sistema operativo e alla versione del computer che si vuole replicare.

## <a name="mobility-service-installation"></a>Installazione del servizio Mobility

È possibile distribuire il servizio Mobility tramite i metodi seguenti:

- **Installazione push**: il servizio Mobility viene installato dal server di elaborazione quando si Abilita la protezione per un computer. 
- **Installare manualmente**: è possibile installare manualmente il servizio Mobility in ogni computer tramite l'interfaccia utente o il prompt dei comandi.

Il servizio Mobility comunica con l'appliance di replica e i computer replicati. Se si dispone di software antivirus in esecuzione nell'appliance di replica, i server di elaborazione o i computer replicati, è necessario escludere le cartelle seguenti dall'analisi:


- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Programmi (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (nei computer Windows con il servizio Mobility installato)

## <a name="replication-process"></a>Processo di replica

1. Quando si Abilita la replica per un computer, viene avviata la replica iniziale in Azure.
2. Durante la replica iniziale, il servizio Mobility legge i dati dai dischi del computer e li invia al server di elaborazione.
3. Questi dati vengono usati per eseguire il seeding di una copia del disco nella sottoscrizione di Azure. 
4. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. La replica è a livello di blocco e quasi continua.
4. Il servizio Mobility intercetta le Scritture nella memoria del disco, integrando il sottosistema di archiviazione del sistema operativo. Questo metodo consente di evitare operazioni di I/O su disco nel computer di replica, per la replica incrementale. 
5. Le modifiche rilevate per un computer vengono inviate al server di elaborazione sulla porta di ingresso HTTPS 9443. La porta può essere modificata. Il server di elaborazione comprime e ne esegue la crittografia e lo invia ad Azure. 

## <a name="ports"></a>Porte

**Dispositivo** | **Connection**
--- | --- 
**Replica di computer** | Il servizio Mobility in esecuzione sulle VM comunica con l'appliance di replica locale sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> I computer inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
**Appliance di replica** | L'appliance di replica orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
**Server di elaborazione** | Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.


## <a name="performance-and-scaling"></a>Prestazioni e ridimensionamento

Per impostazione predefinita, si distribuisce una singola appliance di replica che esegue il server di configurazione e il server di elaborazione. Se si esegue la replica solo di alcuni computer, questa distribuzione è sufficiente. Tuttavia, se si esegue la replica e la migrazione di centinaia di computer, un singolo server di elaborazione potrebbe non essere in grado di gestire tutto il traffico di replica. In questo caso, è possibile distribuire server di elaborazione aggiuntivi e con scalabilità orizzontale.

### <a name="plan-vmware-deployment"></a>Pianificare la distribuzione di VMware

Se si esegue la replica di macchine virtuali VMware, è possibile usare la [Site Recovery Deployment Planner per VMware](../site-recovery/site-recovery-deployment-planner.md), per determinare i requisiti di prestazioni, tra cui la frequenza di modifica dei dati giornaliera e i server di elaborazione necessari.

### <a name="replication-appliance-capacity"></a>Capacità appliance di replica

Usare i valori in questa tabella per determinare se è necessario un server di elaborazione aggiuntivo nella distribuzione.

- Se la frequenza di modifica giornaliera (varianza) è superiore a 2 TB, distribuire un server di elaborazione aggiuntivo.
- Se si esegue la replica di più di 200 computer, distribuire un'altra appliance di replica.

**CPU** | **Memoria** | **Spazio disponibile-memorizzazione nella cache dei dati** | **Frequenza di varianza** | **Limiti di replica**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | < 100 computer 
12 vCPU (2 socket * 6 core \@ 2,5 GHz) | 18 GB | 600 GB | Da 501 GB a 1 TB | 100-150 computer.
16 vCPU (2 socket * 8 core \@ 2,5 GHz) | 32 G1 |  1 TB | Da 1 TB a 2 TB | 151-200 computer.

### <a name="sizing-scale-out-process-servers"></a>Ridimensionamento di server di elaborazione con scalabilità orizzontale

Se è necessario distribuire un server di elaborazione con scalabilità orizzontale, usare questa tabella per determinare il dimensionamento del server.

**Server di elaborazione** | **Spazio disponibile per la memorizzazione nella cache dei dati** | **Frequenza di varianza** | **Limiti di replica**
--- | --- | --- | --- 
4 vCPU (2 socket * 2 core \@ 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | Fino a 85 computer 
8 vCPU (2 socket * 4 core \@ 2,5 GHz), 12 GB di memoria | 600 GB | Da 251 GB a 1 TB    | 86-150 computer.
12 vCPU (2 socket * 6 core \@ 2,5 GHz), 24 GB di memoria | 1 TB | 1-2 TB | 151-225 computer.

## <a name="throttle-upload-bandwidth"></a>Limitazione della larghezza di banda di caricamento.

il traffico VMware che viene replicato in Azure passa attraverso un server di elaborazione specifico. È possibile limitare la velocità effettiva di caricamento limitando la larghezza di banda nei computer che eseguono come server di elaborazione. Con questa chiave del registro di sistema è possibile influenzare la larghezza di banda:

- Il valore del Registro di sistema HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM specifica il numero di thread usati per il trasferimento dati di un disco, durante la replica iniziale o differenziale. Un valore più elevato aumenta la larghezza di banda di rete usata per la replica. Il valore predefinito è quattro. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.
- Inoltre, è possibile limitare la larghezza di banda nel computer del server di elaborazione, come indicato di seguito:

    1. Nel computer del server di elaborazione aprire lo snap-in di MMC backup di Azure. È disponibile un collegamento sul desktop o nella cartella C:\Programmi\Microsoft Azure Recovery Services Agent\bin. 
    2. Nello snap-in selezionare **Modifica proprietà**.
    3. In **limitazione**selezionare **Abilita limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**. Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 1.023 Mbps.


## <a name="next-steps"></a>Passaggi successivi

Provare la [migrazione basata su agente](tutorial-migrate-vmware-agent.md) per i [server fisici](tutorial-migrate-physical-virtual-machines.md)o [VMware](tutorial-migrate-vmware-agent.md) .
