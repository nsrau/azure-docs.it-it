---
title: Architettura di migrazione basato su agente nella migrazione di Server di eseguire la migrazione di Azure
description: Fornisce una panoramica della migrazione basata su agente di VM VMware con la migrazione di Server eseguire la migrazione di Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811154"
---
# <a name="agent-based-migration-architecture"></a>Architettura di migrazione basato su agente

Questo articolo offre una panoramica dell'architettura e i processi usati per la replica basata su agente con lo strumento di migrazione del Server eseguire la migrazione di Azure.

[Azure Migrate](migrate-services-overview.md) fornisce un hub centrale per tenere traccia di individuazione, valutazione e migrazione delle App in locale e i carichi di lavoro e istanze di macchina virtuale di AWS/GCP, in Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e migrazione, nonché le offerte di fornitori di software indipendenti di terze parti.

## <a name="agent-based-replication"></a>Replica basata su agente

La replica basata su agenti nella replica Server eseguire la migrazione di Azure viene usato per eseguire la migrazione in locale le macchine virtuali VMware e server fisici in Azure. Può anche essere utilizzato per eseguire la migrazione di altri server virtualizzati locali, nonché macchine virtuali, incluse le istanze AWS e macchine virtuali di GCP di cloud privati e pubblici.

Per la migrazione di VMware, lo strumento di migrazione del Server eseguire la migrazione di Azure offre due opzioni:

- Migrazione mediante replica basata su agente, come descritto in questo articolo.
- Replica senza agente, eseguire la migrazione di macchine virtuali senza dover installare nulla su di essi.

Altre informazioni sulle [selezionando un metodo di migrazione per VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migrazione del server e Azure Site Recovery

Migrazione del Server eseguire la migrazione di Azure è uno strumento per la migrazione in locale e i carichi di lavoro di cloud pubblico di Azure. È ottimizzato per la migrazione. Site Recovery è uno strumento di ripristino di emergenza. Migrazione del Server e Site Recovery di Azure condividono alcuni componenti con tecnologia comune usati per la replica dei dati, ma per scopi diversi.

## <a name="architectural-components"></a>Componenti dell'architettura

![Architettura](./media/agent-based-replication-architecture/architecture.png)

La tabella riepiloga i componenti usati per la migrazione basata su agente.

**Componente** | **Dettagli** | **Installazione**
--- | --- | ---
**Appliance di replica** | L'appliance di replica (server di configurazione) è un computer locale che funge da ponte tra l'ambiente locale e lo strumento di migrazione del Server eseguire la migrazione di Azure. L'appliance individua l'inventario della macchina virtuale in locale, in modo che la migrazione di Server di Azure può orchestrare la replica e la migrazione. L'appliance presenta due componenti:<br/><br/> **Server di configurazione**: Si connette alla migrazione del Server eseguire la migrazione di Azure e coordina la replica.<br/> **Server di elaborazione** Gestisce la replica dei dati. Riceve i dati della macchina virtuale, comprime e crittografa e invia alla sottoscrizione di Azure. Migrazione del Server, scrive i dati in dischi gestiti. | Per impostazione predefinita è installato il server di elaborazione con il server di configurazione nell'appliance di replica.
**Servizio Mobility** | Il servizio Mobility è un agente installato in ogni computer da replicare ed eseguire la migrazione. Invia i dati di replica dal computer al server di elaborazione. Sono disponibili numerosi diversi agenti del servizio Mobility. | Nell'appliance di replica si trovano i file di installazione per il servizio Mobility. Scaricare e installare l'agente che è necessario, in conformità con il sistema operativo e la versione del computer da replicare.

### <a name="mobility-service-installation"></a>Installazione del servizio Mobility

È possibile distribuire il servizio Mobility tramite i metodi seguenti:

- **Installazione push**: Il servizio Mobility viene installato dal server di elaborazione quando si abilita la protezione per una macchina. 
- **Eseguire l'installazione manuale**: È possibile installare manualmente il servizio Mobility in ogni computer tramite interfaccia utente o il prompt dei comandi.

Il servizio Mobility comunica con l'appliance di replica e le macchine replicate. Se si dispone di un software antivirus in esecuzione nel dispositivo di replica, server di elaborazione o le macchine replicate, le cartelle seguenti devono essere escluse dall'analisi:


- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Programmi (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (su computer Windows con il servizio Mobility installato)

## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale in Azure.
2. Durante la replica iniziale, il servizio Mobility legge i dati da dischi delle macchine e li invia al server di elaborazione.
3. Questi dati vengono utilizzati per il seeding di una copia del disco nella sottoscrizione di Azure. 
4. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. La replica è a livello di blocco e quasi continua.
4. Le scritture intercetta il servizio Mobility per la memoria del disco della macchina virtuale, grazie all'integrazione con il sottosistema di archiviazione del sistema operativo. Questo metodo consente di evitare operazioni dei / o su disco nella macchina di replica per la replica incrementale. 
5. Le modifiche rilevate per un computer vengono inviate al server di elaborazione sulla porta HTTPS 9443 in ingresso. La porta può essere modificata. Il server di elaborazione vengono compressi e li crittografa e li invia ad Azure. 

## <a name="ports"></a>Porte

**Dispositivo** | **Connection**
--- | --- 
VM | Il servizio Mobility in esecuzione nelle macchine virtuali comunica con l'appliance di replica in locale sulla porta HTTPS 443 in ingresso, per la gestione delle repliche.<br/><br/> Le macchine virtuali inviano i dati di replica al server di elaborazione (in esecuzione nell'appliance di replica per impostazione predefinita) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance replica Orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.


## <a name="performance-and-scaling"></a>Prestazioni e ridimensionamento

Per impostazione predefinita, si distribuisce un'appliance di replica singolo che esegue sia il server di configurazione e il server di elaborazione. Se si replicano solo poche macchine, questa distribuzione è sufficiente. Tuttavia, se si esegue la replica e la migrazione di centinaia di computer, un singolo server di elaborazione potrebbe non essere in grado di gestire tutto il traffico di replica. In questo caso, è possibile distribuire server di elaborazione aggiuntivi, scalabilità orizzontale.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery Deployment Planner per VMware

Se si replicano VM VMware, è possibile usare la [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) per VMware consentire di determinare i requisiti relativi alle prestazioni, tra cui i dati giornalieri modificare frequenza e i server di elaborazione è necessario.

### <a name="replication-appliance-capacity"></a>Capacità di appliance di replica

I valori in questa tabella sono utilizzabile per determinare se è necessario un server di elaborazione aggiuntivo nella distribuzione.

- Se la frequenza di modifica giornaliera (varianza) è superiore a 2 TB, distribuire un server di elaborazione aggiuntivo.
- Se si esegue la replica di più di 200 computer, distribuire un'appliance di replica aggiuntivo.

**CPU** | **Memoria** | **Spazio disponibile per la memorizzazione nella cache di dati** | **Della frequenza di varianza** | **Limiti di replica**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | < 100 computer 
12 vCPU (2 socket * 6 core \@ 2,5 GHz) | 18 GB | 600 GB | Da 501 GB a 1 TB | 100 e 150 computer.
16 vCPU (2 socket * 8 core \@ 2,5 GHz) | 32 G1 |  1 TB | Da 1 TB a 2 TB | 151 a 200 computer.

### <a name="scale-out-process-server-sizing"></a>Dimensioni del server di elaborazione scale-out

Se è necessario distribuire un server di elaborazione con scalabilità orizzontale, questa tabella consente di scoprire ridimensionamento del server.

**Server di elaborazione** | **Spazio disponibile per la memorizzazione nella cache di dati** | **Della frequenza di varianza** | **Limiti di replica**
--- | --- | --- | --- 
4 vCPU (2 socket * 2 core \@ 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | Un massimo di 85 macchine 
8 vCPU (2 socket * 4 core \@ 2,5 GHz), 12 GB di memoria | 600 GB | Da 251 GB a 1 TB    | 86 e 150 computer.
12 Vcpu (2 socket * 6 core \@ a 2,5 GHz), 24 GB di memoria | 1 TB | 1-2 TB | 151 e 225 computer.

## <a name="control-upload-throughput"></a>Velocità effettiva di caricamento di controllo

È possibile limitare la quantità di larghezza di banda usata per caricare dati in Azure in ogni host Hyper-V. Stai attento. Se si impostano i valori troppo basso che un impatto negativo sulle replica e la migrazione di ritardo.


1. Accedere al nodo host o cluster Hyper-V.
2. Eseguire **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**per aprire lo snap-in MMC Backup di Windows Azure.
3. Nello snap-in selezionare **Modifica proprietà**.
4. Nelle **limitazione**, selezionare **abilita la limitazione delle richieste per operazioni di backup all'utilizzo della larghezza di banda di internet**. Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Influenza l'efficienza di caricamento

Se si dispone di larghezza di banda disponibile per la replica e vogliono aumentare caricamenti, è possibile aumentare il numero di thread allocati per l'attività di caricamento, come indicato di seguito:

1. Aprire il Registro di sistema con Regedit.
2. Passare alla chiave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Aumentare il valore per il numero di thread usati per il caricamento dei dati per ogni macchina virtuale di replica. Il valore predefinito è 4 e il valore massimo è 32. 

## <a name="next-steps"></a>Passaggi successivi

Prova basata su agente [migrazione di VM VMware](tutorial-migrate-vmware-agent.md) utilizzando la migrazione di Server eseguire la migrazione di Azure.
