---
title: Architettura di Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811427"
---
# <a name="azure-migrate-architecture-and-processes"></a>I processi e architettura di azure Migrate

[Azure Migrate](migrate-overview.md) fornisce un hub di strumenti che consentono di individuare, valutare e la migrazione di App, infrastruttura e i carichi di lavoro in Microsoft Azure. L'hub include strumenti di Azure Migrate e le offerte di fornitori di software indipendenti di terze parti. 

 Questo articolo riepiloga la valutazione e l'architettura di migrazione e i processi per la valutazione di Azure Migrate Server e la migrazione di Server eseguire la migrazione di Azure.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Valutazione con Azure di eseguire la migrazione Server Assessment

Valutazione di Server eseguire la migrazione di Azure può valutare le macchine virtuali VMware e Hyper-V le macchine virtuali per la migrazione ad Azure. Valutazione funziona nel modo seguente:

1. **Preparare la valutazione delle**: In locale, è possibile impostare un leggero Azure Migrate appliance in locale come VM VMware o macchine Virtuali Hyper-V e registrare il dispositivo con Azure Migrate.
2. **Individuare le macchine virtuali**: L'appliance di eseguire la migrazione di Azure viene eseguita per individuare le macchine virtuali locali. 
    - Non deve essere installato nelle macchine virtuali individuate.
    - I metadati di macchina virtuale includono informazioni sui core, memoria, dischi, dimensioni dei dischi e schede di rete.
    - I dati sulle prestazioni sono incluse informazioni su CPU e memoria, utilizzo IOPS, velocità effettiva del disco (MBps), disco e rete output (MBps)
- **Raccogliere e valutare le macchine virtuali**: Al termine dell'individuazione, nel portale di Azure portal che è raccogliere individuato le macchine virtuali in gruppi che in genere costituiti da macchine virtuali che si vuole eseguire la migrazione insieme. Eseguire una valutazione su un gruppo.


## <a name="vmware-assessment"></a>Valutazione di VMware 

Nel diagramma vengono riepilogati come VM VMware assessment è compatibile con Azure eseguire la migrazione di Server Assessment.

![Architettura di valutazione VMware](./media/migrate-architecture/sas-architecture-vmware.png)

Il processo è il seguente:

1. **Distribuisce il dispositivo di Azure Migrate**:

    a. Scaricare il modello (con estensione OVA) dal portale di Azure.

    b. Importarlo al computer del Server vCenter per creare la macchina virtuale.

    c. Connettersi a questa macchina virtuale, configurare le impostazioni di base per tale e registrarlo con Azure Migrate.

2. **Avvio individuazione**:

    a. Connettersi all'app dell'agente di raccolta in esecuzione nell'appliance per avviare l'individuazione.

    b. L'appliance invia continuamente i dati di metadati e le prestazioni dalle macchine virtuali in Azure.

    - L'appliance è sempre connesso al servizio Azure Migrate.
    - Vengono acquisite le modifiche dell'ambiente durante l'individuazione di continuo. Ad esempio l'aggiunta di macchine virtuali nell'ambito di individuazione o aggiunta di dischi di macchine Virtuali o schede di rete.

3. **Valutare le macchine virtuali**:

    a. Al termine dell'individuazione, nel portale di Azure portal che è raccogliere individuato le macchine virtuali in gruppi.  In genere, un gruppo è costituito da macchine virtuali che si vuole eseguire la migrazione insieme.

    b. Eseguire una valutazione per ogni gruppo.

4. **Esaminare assessment**: 

    a. Al termine della valutazione, visualizzarla nel portale.

    b. Per un'ulteriore analisi, scaricare la valutazione in formato Excel.



### <a name="vmware-ports"></a>Porte VMware
Azure Migrate Usa le porte di connessione seguenti per VMware:

**Origine** | **Destinazione** | **Porta** | **Dettagli**
--- | --- | --- | ---
Appliance Azure Migrate | Servizio Azure Migrate | Target-TCP 443 | Inviare i dati dei metadati e delle prestazioni per eseguire la migrazione di Azure.
Appliance Azure Migrate | Server vCenter | Target-TCP 443 | Connettersi al Server vCenter per i dati dei metadati e delle prestazioni. valore predefinito è 443, ma può essere modificato con vCenter in ascolto su una porta diversa. 
Client RDP | Appliance Azure Migrate | Target-TCP3389 | Connessione RDP all'individuazione di trigger dall'appliance.

### <a name="collected-vmware-metadata"></a>Metadati raccolti VMware

L'appliance invia i metadati e i dati relativi alle prestazioni dalle macchine virtuali in Azure.

**Azione** | **Dettagli**
--- | ---
**Metadati raccolti** | nome della macchina virtuale di vCenter<br/> percorso VM vCenter (cartella host/cluster)<br/> Indirizzi IP e MAC<br/> Sistema operativo<br/> Numero di dischi/core/schede<br/> Dimensioni di memoria e disco.
**Dati sulle prestazioni raccolti** | Utilizzo della CPU/memoria<br/> Per i dati su disco (velocità effettiva di lettura/scrittura disco; letture/scritture disco al secondo)<br/> Dati di interfaccia di rete (network, in rete in uscita).<br/><br/> I dati sulle prestazioni vengono raccolti continuamente dopo che il dispositivo si connette al Server vCenter. Non vengono raccolti i dati cronologici.

## <a name="hyper-v-assessment"></a>Valutazione di Hyper-V

Nel diagramma vengono riepilogati come works di valutazione di Hyper-V so di valutazione di Azure Migrate Server.

![Architettura di valutazione Hyper-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

Il processo è il seguente:

1. **Creare l'appliance Azure Migrate**:

    a. Scaricare la macchina virtuale in formato compresso dal portale di Azure.

    b. Importarlo in un host Hyper-V.

    c. Connettersi all'appliance di macchina virtuale. Configurare le impostazioni di base per tale e registrarlo con Azure Migrate.

2. **Avvio individuazione**:

    a. Connettersi all'appliance Azure Migrate per avviare l'individuazione. Non deve essere installato nelle macchine virtuali individuate.

    b. L'appliance invia continuamente i dati dei metadati e le prestazioni della macchina virtuale per eseguire la migrazione di Azure.

    - L'appliance è sempre connesso al servizio Azure Migrate (tramite sessioni CIM).
    - Vengono acquisite le modifiche dell'ambiente durante l'individuazione di continuo. Ad esempio l'aggiunta di macchine virtuali nell'ambito di individuazione o aggiunta di dischi di macchine Virtuali o schede di rete.


3. **Valutare le macchine virtuali**:

    a. Al termine dell'individuazione, nel portale di Azure portal che è raccogliere individuato le macchine virtuali in gruppi.  In genere, un gruppo è costituito da macchine virtuali che si vuole eseguire la migrazione insieme.

    b. Eseguire una valutazione per ogni gruppo.

4. **Esaminare assessment**:

    a. Al termine della valutazione, visualizzarla nel portale.

    b. Per un'ulteriore analisi, scaricare la valutazione in formato Excel.

### <a name="hyper-v-ports"></a>Porte Hyper-V

Servizio Azure Migrate Usa le porte di connessione seguenti per Hyper-v:

**Origine** | **Destinazione** | **Porta** | **Dettagli**
--- | --- | --- | ---
Appliance Azure Migrate | Servizio Azure Migrate | Target-TCP 443 | Inviare i dati dei metadati e delle prestazioni per eseguire la migrazione di Azure.
Appliance Azure Migrate | Host/cluster Hyper-V | Target-Default WinRM ports-HTTP:5985; HTTPS:5986 | Connettersi all'host per i dati dei metadati e delle prestazioni. Sessione CIM utilizzata per la connessione
Client RDP | Appliance Azure Migrate | Target-TCP3389 | Connessione RDP all'individuazione di trigger dall'appliance.

## <a name="collected-hyper-v-vm-metadata"></a>Raccogliere i metadati di macchina virtuale Hyper-V

L'appliance invia i metadati e i dati relativi alle prestazioni dalle macchine virtuali in Azure.


**Azione** | **Dettagli**
--- | ---
**Metadati raccolti** | Nome della VM.<br/> Percorso della macchina virtuale (cartella host/cluster)<br/> Indirizzi IP e MAC<br/> Sistema operativo<br/> Numero di dischi/core/schede<br/> Dimensioni dei dischi e memoria<br/> Output su disco IOPS, velocità effettiva del disco (MBps), rete (MBps)
**Dati sulle prestazioni raccolti** |  Utilizzo della CPU/memoria<br/> Per i dati su disco (velocità effettiva di lettura/scrittura disco; letture/scritture disco al secondo)<br/> Dati di interfaccia di rete (network, in rete in uscita).<br/><br/> I dati sulle prestazioni vengono raccolti continuamente dopo che il dispositivo si connette all'host Hyper-V. Non vengono raccolti i dati cronologici.




## <a name="migration-with-azure-migrate-server-migration"></a>La migrazione con Azure applicata alla migrazione del Server

Utilizzando la migrazione di Server eseguire la migrazione di Azure, è possibile migrare le seguenti in Azure:

- Macchine virtuali VMware locali
- VM Hyper-V in locale
- Server fisici in locale
- Istanze di macchine Virtuali Windows di AWS
- Istanze di macchine Virtuali Windows di GCP

Il processo di migrazione è leggermente diversa, a seconda di ciò che si sta eseguendo la migrazione.


## <a name="migrate-vmware-vms"></a>Eseguire la migrazione di macchine virtuali VMware

Migrazione del Server eseguire la migrazione di Azure offre due metodi per la migrazione di VM VMware in locale:

- **La replica senza agente**: Eseguire la migrazione di macchine virtuali senza dover installare nulla su di essi.
- **Replica basata su agente**. Installare un agente nella macchina virtuale per la replica.

Anche se la replica senza agente è più semplice dal punto di vista della distribuzione, ha attualmente alcune limitazioni. [Altre informazioni](server-migrate-overview.md) su queste limitazioni.
 

### <a name="agent-based-migration"></a>Migrazione basata su agente

Migrazione basata su agente di macchine virtuali VMware è necessario un numero di componenti da distribuire, come riepilogato nella tabella.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Computer server di configurazione** | Un singolo VMware VM locale che viene distribuita da un modello OVF scaricato.<br/><br/> La macchina esegue tutti i componenti di Site Recovery in locale, tra cui il server di configurazione e server di elaborazione. | **Server di configurazione**: coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.<br/><br/> **Server di elaborazione** Installato per impostazione predefinita nel server di configurazione. Riceve dati di replica. li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Azure. Inoltre, il server di elaborazione installa il servizio Mobility di Azure Site Recovery nelle macchine virtuali ed esegue l'individuazione automatica delle macchine virtuali locali.
**Servizio Mobility** | Il servizio Mobility deve essere installato in ogni VM VMware da replicare. | È consigliabile consentire l'installazione automatica dal server di elaborazione. In alternativa, è possibile installare manualmente il servizio o usare un metodo di distribuzione automatico, ad esempio System Center Configuration Manager.





### <a name="agent-based-replication-process"></a>Processo di replica basata su agente

![Processo di replica](./media/migrate-architecture/v2a-architecture-henry.png)

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale in Azure. 
    - La replica è a livello di blocco, quasi continua, usando l'agente del servizio Mobility in esecuzione nella macchina virtuale.
    - Vengono applicate le impostazioni dei criteri di replica:
        - **Soglia RPO**. Questa impostazione non influisce sulla replica. È utile con il monitoraggio. Viene generato un evento e, facoltativamente, viene inviato un messaggio di posta elettronica, se l'obiettivo RPO corrente supera la soglia specificata.
        - **Conservazione del punto di ripristino**. Questa impostazione specifica quanto indietro nel tempo si vuole andare quando si verifica un'interruzione. Il periodo massimo di conservazione per l'archiviazione Premium è di 24 ore. Per l'archiviazione standard è 72 ore. 
        - **Snapshot coerenti con l'app**. È possibile acquisire snapshot coerenti con l'app a intervalli compresi tra 1 e 12 ore, a seconda delle esigenze dell'app. Si tratta di snapshot BLOB di Azure standard. L'agente di mobilità in esecuzione in una macchina virtuale richiede uno snapshot VSS conforme a questa impostazione e fa riferimento a quel momento come punto coerente con l'applicazione nel flusso di replica.

2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet.

    - In alternativa, è possibile usare Azure ExpressRoute con [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering).
    - La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate per un computer vengono inviate al server di elaborazione.
2. Le comunicazioni avvengono nel modo seguente:

    - Le macchine virtuali comunicano con il server di configurazione locale sulla porta HTTPS 443 in ingresso, per la gestione della replica.
    - Il server di configurazione orchestra la replica con Azure attraverso la porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.
5. I dati di replica registra ' s land prima in un account di archiviazione della cache di Azure. Questi log vengono elaborati e i dati vengono archiviati in Azure il disco gestito (il disco di valore di inizializzazione di Azure Site Recovery). I punti di ripristino vengono creati sul disco.







## <a name="next-steps"></a>Passaggi successivi

- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
- Per assistenza dalla community, visitare il [forum MSDN di eseguire la migrazione di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) oppure [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate).

