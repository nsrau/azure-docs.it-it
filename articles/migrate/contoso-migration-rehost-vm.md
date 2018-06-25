---
title: Eseguire il rehosting di un'app Contoso con la migrazione alle VM di Azure mediante Azure Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il rehosting di un'app locale con una migrazione lift-and-shift ad Azure per la migrazione di macchine locali mediante il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 13b36398afdf8eb4db3adeee4ebb821411d813f5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300787"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migrazione Contoso: eseguire il rehosting di un'app in locale in VM di Azure


Questo articolo spiega in che modo Contoso esegue il rehosting dell'app SmartHotel in Azure, migrando le VM dell'app alle VM di Azure.


Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso migra le risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. Gli scenari aumentano di complessità e nel tempo verranno aggiunti altri articoli.


**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli scenari di migrazione Contoso. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app locale a due livelli locale SmartHotel in esecuzione in VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting nella macchina virtuale di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel ad Azure. Esegue la migrazione della macchina virtuale front-end dell'app mediante [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e del database dell'app mediante il servizio [Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione a un'istanza gestita di SQL. | Disponibile
Articolo 5: Eseguire il rehosting in macchine virtuali (questo articolo) | Spiega in che modo Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel solo con Site Recovery.
[Articolo 6: Eseguire il rehosting nelle macchine virtuali di Azure e nei gruppi di disponibilità di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un gruppo di disponibilità di SQL Server. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso migra l'app osTicket di Linux alle VM di Azure mediante Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux nelle VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso migra l'app osTicket di Linux alle VM di Azure mediante Site Recovery e il database dell'app a un'istanza del server MySQL di Azure mediante MySQL Workbench. | Disponibile

In questo articolo Contoso esegue la migrazione dell'app a due livelli Windows. App SmartHotel NET in esecuzione in VM di VMware ad Azure. Questa app è disponibile per l'uso in modalità open source ed è possibile scaricarla da [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Driver di business

Il team di leadership IT collabora attivamente con i partner commerciali per capire gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita del business**: Contoso è in espansione, con una conseguente pressione sui sistemi e sull'infrastruttura locali.
- **Limitare i rischi**: l'app SmartHotel è fondamentale per il business di Contoso. L'azienda intende migrarla ad Azure con zero rischi.
- **Estendere**: Contoso non intende modificare l'app, ma semplicemente garantirne la stabilità.


## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale:

- Dopo la migrazione, l'app in Azure dovrà avere le stesse caratteristiche prestazionali riscontrate oggi in VMware.  L'app manterrà la stessa importanza critica nel cloud come la corrispondente versione in locale. 
- Contoso non intende investire in questa app.  È importante per l'azienda, ma nella sua forma corrente intende semplicemente migrarla in modo sicuro al cloud.
- Contoso non intende modificare il modello operativo dell'app. Punta all'interazione nel cloud esattamente come avviene ora.
- Contoso non intende modificare le funzionalità dell'app. Cambierà solo la posizione.

## <a name="proposed-architecture"></a>Architettura proposta

Ecco l'ambiente corrente

- L'app è suddivisa a livelli tra due VM (**WEBVM** e **SQLVM**).
- Le macchine virtuali si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.

![Architettura dello scenario](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>Processo di migrazione

Contoso eseguirà la migrazione delle VM front-end dell'app e del database alle VM di Azure mediante Site Recovery:

- In primo luogo, preparerà e configurerà i componenti di Azure per Site Recovery e predisporrà l'infrastruttura VMware locale.
- Poiché l'[infrastruttura di Azure](contoso-migration-infrastructure.md) è già configurata, dovrà solo aggiungere un paio di componenti di Azure in modo specifico per Site Recovery.
- A questo punto potrà iniziare a replicare le VM.
-Una volta abilitata la replica, migrerà la VM effettuandone il failover in Azure.

![Processo di migrazione](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.


## <a name="prerequisites"></a>prerequisiti

Ecco gli elementi che Contoso dovrà avere a disposizione per l'esecuzione di questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Dovrebbe essere già stata creata una sottoscrizione nei primi articoli di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.<br/><br/> Sono disponibili altre informazioni sui requisiti specifici di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) per Site Recovery.
**Server locali** | I server vCenter locali devono eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Gli host ESXi devono eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Nell'host ESXi devono essere in esecuzione una o più VM VMware.
**VM locali** | Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Preparare Azure per Site Recovery**. Crea un account di archiviazione di Azure per contenere i dati replicati e un insieme di credenziali dei Servizi di ripristino.
> * **Passaggio 2: Preparare VMware in locale per Site Recovery**. Prepara gli account per l'individuazione di VM e l'installazione dell'agente, poi prepara la connessione alle VM di Azure dopo il failover.
> * **Passaggio 3: Replicare le VM**. Configura la replica e inizia a replicare le VM in Archiviazione di Azure.
> * **Passaggio 4: Eseguire la migrazione delle macchine virtuali con Site Recovery**. Effettua un failover di test per verificare che tutto funzioni come previsto e un failover completo per migrare le VM ad Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passaggio 1: Preparare Azure per il servizio Site Recovery

Ecco i componenti di Azure necessari a Contoso per la migrazione delle VM ad Azure:

- Una rete virtuale in cui verranno collocate le VM di Azure al momento della creazione, durante il failover.
- Un account di archiviazione di Azure per i dati replicati. 
- Un insieme di credenziali dei Servizi di ripristino in Azure.

Questi elementi vengono configurati come segue:

1. Contoso ha già configurato una rete che può usare per Site Recovery quando ha [distribuito l'infrastruttura di Azure](contoso-migration-infrastructure.md)

    - SmartHotel è un'app di produzione e le VM verranno migrate alla rete di produzione di Azure (VNET-PROD-EUS2) nell'area primaria degli Stati Uniti orientali 2.
    - Entrambe le VM verranno inserite nel gruppo di risorse ContosoRG, usato per le risorse di produzione.
    - La VM front-end dell'app (WEBVM) verrà migrata alla subnet front-end (PROD-FE-EUS2) nella rete di produzione.
    - La VM del database dell'app (SQLVM) verrà migrata alla subnet del database (PROD-DB-EUS2) nella rete di produzione.

2. Contoso crea un account di archiviazione di Azure (contosovmsacc20180528) nell'area primaria.
    - L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.
    - Usa un account generico con archiviazione standard e la replica dell'archiviazione con ridondanza locale. 

    ![Archiviazione di Site Recovery](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Una volta configurati rete e account di archiviazione, Contoso crea un insieme di credenziali di Servizi di ripristino (ContosoMigrationVault) e lo inserisce nel gruppo di risorse ContosoFailoverRG nell'area primaria degli Stati Uniti orientali 2.

    ![Insieme di credenziali dei servizi di ripristino](./media/contoso-migration-rehost-vm/asr-vault.png)

**Servono altre informazioni?**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) sulla configurazione di Azure per Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 2: Preparare l'ambiente VMware locale per Site Recovery

Ecco gli elementi che Contoso prepara in locale:

- Un account nel server vCenter o nell'host ESXi vSphere per l'individuazione automatica delle VM.
- Un account per l'installazione automatica del servizio Mobility nelle VM VMware. 
- Impostazioni delle VM locali, in modo che Contoso possa connettersi alle VM replicate di Azure dopo il failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. 
- Orchestrare la replica, il failover e il failback per le VM.
- È necessario almeno un account di sola lettura. L'account deve essere in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di VM.

Contoso configura l'account come segue:

1. Crea un ruolo a livello di vCenter.
2. Assegna al ruolo le autorizzazioni necessarie.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in ogni VM.

- Site Recovery è in grado di eseguire un'installazione push automatica del servizio Mobility quando è abilitata la replica della VM.
- È necessario un account, in modo che Site Recovery possa accedere alle VM per l'installazione push. Questo account viene specificato quando si configura la replica.
- L'account può essere di dominio o locale, con le autorizzazioni per l'installazione nelle VM.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover, Contoso intende connettersi alle VM di Azure. A tale scopo esegue le operazioni descritte di seguito prima della migrazione:

1. Per l'accesso tramite Internet:

 - Abilita RDP nella VM locale prima del failover.
 - Verifica che per il profilo **Pubblico** siano aggiunte le regole TCP e UDP.
 - Verifica che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.
 
2. Per l'accesso tramite VPN da sito a sito:

 - Abilitano RDP nella macchina virtuale locale.
 - Consentono RDP in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
 - Imposta i criteri SAN del sistema operativo nelle VM locali su **OnlineAll**.

Inoltre, quando effettua un failover, deve verificare quanto segue:

- Quando si attiva un failover, nella VM non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla VM fino al completamento.
- Dopo il failover, può selezionare **Diagnostica di avvio** per visualizzare uno screenshot della VM. Se l'operazione non funziona, verificano che la VM sia in esecuzione e rivedono questi [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) sulla creazione e sull'assegnazione di un ruolo per l'individuazione automatica.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) sulla creazione di un account per l'installazione push del servizio Mobility.


## <a name="step-3-replicate-the-on-premises-vms"></a>Passaggio 3: Replicare le macchine virtuali locali

Prima che possa eseguire una migrazione ad Azure, Contoso deve configurare e abilitare la replica.

### <a name="set-a-replication-goal"></a>Impostare un obiettivo di replica

1. Nel nome dell'insieme di credenziali (ContosoVMVault) selezionano un obiettivo di replica (**Introduzione** > **Site Recovery** > **Preparare l'infrastruttura**).
2. Specifica che le macchine sono in locale, in esecuzione in VMware e replicano in Azure.

    ![Obiettivo di replica](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, conferma di aver completato la pianificazione della distribuzione selezionando **Operazione completata**. In questo scenario Contoso migra solo due VM e non ha bisogno di pianificare la distribuzione.


### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Contoso deve configurare l'ambiente di origine. A tale scopo, scarica un modello OVF e lo usa per distribuire il server di configurazione di Site Recovery come macchina virtuale VMware locale a disponibilità elevata. Dopo aver attivato il server di configurazione, lo registra nell'insieme di credenziali.

Il server di configurazione esegue una serie di componenti:

- Il componente server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.



Contoso esegue questi passaggi come indicato di seguito:

1. Nell'insieme di credenziali scarica il modello OVF da **Preparare l'infrastruttura** > **Origine** > **Server di configurazione**.
    
    ![Scaricare OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Importa il modello in VMware per creare e distribuire la macchina virtuale.

    ![Modello OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  Alla prima accensione, la macchina virtuale viene avviata con un'esperienza di installazione di Windows Server 2016. Accetta il contratto di licenza e immette una password amministratore.
4. Al termine dell'installazione, accede alla macchina virtuale come amministratore. Per impostazione predefinita, al primo accesso viene avviato lo strumento di configurazione di Azure Site Recovery.
5. Nello strumento specifica un nome per registrare il server di configurazione nell'insieme di credenziali.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, esegue l'accesso alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si registra il server di configurazione.

    [Registrare il server di configurazione](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Contoso accede nuovamente alla macchina. Viene avviata automaticamente la gestione guidata del server di configurazione.
9. Nella procedura guidata seleziona la scheda NIC per ricevere il traffico di replica. Questa impostazione non può essere modificata dopo che è stata configurata.
10. Seleziona la sottoscrizione, il gruppo di risorse e l'insieme di credenziali in cui registrare il server di configurazione.
        ![insieme di credenziali](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Scarica e installa il server MySQL e VMware PowerCLI. 
11. Dopo la convalida, specifica l'indirizzo IP o l'FQDN dell'host vSphere o del server vCenter. Mantiene la porta predefinita e specifica un nome descrittivo per il server in Azure.
12. Specifica l'account creato per l'individuazione automatica e le credenziali usate per installare automaticamente il servizio Mobility. Per le macchine Windows, l'account deve avere privilegi di amministratore locale nelle VM.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Al termine della registrazione, Contoso verifica nel portale di Azure che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere circa 15 minuti. 
8. Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le VM.

### <a name="set-up-the-target"></a>Configurare la destinazione

Ora Contoso specifica le impostazioni di replica di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** seleziona le impostazioni di destinazione.
2. Site Recovery verifica che nel percorso di destinazione specificato siano disponibili un account di archiviazione di Azure e una rete.

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Ora Contoso può creare criteri di replica.

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** crea i criteri **ContosoMigrationPolicy**.
2. Usa le impostazioni predefinite:
    - **Soglia RPO**. Impostazione predefinita di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**. Impostazione predefinita di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**. Impostazione predefinita di un'ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.

        ![Creare criteri di replica](./media/contoso-migration-rehost-vm/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione. 

    ![Associare i criteri di replica](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Abilitare la replica per WEBVM

A questo punto Contoso può abilitare la replica per le VM. Inizia con WebVM.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine.
2. Indica che intende abilitare le VM, selezionare il server vCenter e il server di configurazione.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Seleziona le impostazioni di destinazione, inclusi il gruppo di risorse e la rete di Azure, e l'account di archiviazione.

     ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Contoso seleziona **WebVM** per la replica, controlla i criteri di replica e abilita la replica.

    - In questa fase Contoso seleziona solo WEBVM perché devono essere selezionate la rete virtuale e la subnet e Contoso posiziona le VM dell'app in subnet diverse.
    - Quando viene abilitata la replica, Site Recovery installa automaticamente il servizio Mobility nella VM.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.
6. In **Informazioni di base** nel portale di Azure, Contoso può visualizzare la struttura per le VM con replica in Azure.


### <a name="enable-replication-for-sqlvm"></a>Abilitare la replica per SQLVM

Ora Contoso può avviare la replica della macchina SQLVM, seguendo lo stesso processo di prima.

1. Seleziona le impostazioni di origine.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Specifica le impostazioni di destinazione.

     ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Seleziona SQLVM per la replica. 

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Applica gli stessi criteri di replica usati per WEBVM e abilita la replica.

    ![Visualizzazione dell'infrastruttura](./media/contoso-migration-rehost-vm/essentials.png)

**Servono altre informazioni?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Altre informazioni per [abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Passaggio 4: Eseguire la migrazione delle macchine virtuali 

Contoso effettua un rapido failover di test e poi un failover completo per la migrazione delle VM.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

Un failover di test consente di verificare che tutti gli elementi funzionino come previsto. 

1. Contoso effettua un failover di test al punto di ripristino più recente disponibile **Elaborato più recente**.
2. Seleziona **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) in modo che Site Recovery arresti la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. 
3. Viene effettuato il failover di test: 

    - Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    - Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    - Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.
    
3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Contoso verifica che la VM sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione. 
4. Dopo aver verificato il failover di test, lo elimina e registra e salva eventuali osservazioni. 

### <a name="create-and-customize-a-recovery-plan"></a>Creare e personalizzare un piano di ripristino

 Dopo aver verificato che il failover di test funzioni come previsto, Contoso crea un piano di ripristino per la migrazione. 

- Un piano di ripristino specifica l'ordine in cui si verifica il failover e indica in che modo le VM di Azure verranno attivate in Azure.
- Poiché l'app è a due livelli, l'azienda personalizza il piano di ripristino in modo che la VM dei dati (SQLVM) venga avviata prima della VM front-end (WEBVM).

1. In **Piani di ripristino (Site Recovery)** > **+Piano di ripristino**, crea un piano e vi aggiunge le macchine virtuali.

    ![Piano di ripristino](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Dopo aver creato il piano, lo personalizza (**Piani di ripristino** > **SmartHotelMigrationPlan** > **Personalizza**).
2.  Rimuove WEBVM da **Gruppo 1: Avvio**.  In questo modo la prima azione di avvio avrà effetto solo su SQLVM.
3.  In **+Gruppo** > **Aggiungi elementi protetti** aggiunge WEBVM a Gruppo 2: Avvio.  Le VM devono essere in due gruppi diversi.


### <a name="migrate-the-vms"></a>Eseguire la migrazione delle macchine virtuali


Contoso può ora effettuare un failover completo per completare la migrazione.

1. Seleziona il piano di ripristino > **Failover**.
2. Sceglie di effettuare il failover al punto di ripristino più recente e tenta di arrestare la VM locale tramite Site Recovery prima che venga attivato il failover. Contoso segue l'avanzamento del failover nella pagina **Processi**.

    ![Failover](./media/contoso-migration-rehost-vm/failover1.png)


3. Dopo il failover, verifica che la VM di Azure venga visualizzata come previsto nel portale di Azure.

    ![Failover](./media/contoso-migration-rehost-vm/failover2.png)  

3. Dopo la verifica, completa la migrazione per ogni VM. Arresta la replica per la macchina virtuale e la relativa fatturazione di Site Recovery.

    ![Failover](./media/contoso-migration-rehost-vm/failover3.png)

**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) su come effettuare un failover di test. 
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) su come creare un piano di ripristino.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) su come effettuare il failover in Azure.

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Al termine della migrazione i livelli dell'app SmartHotel saranno in esecuzione in VM di Azure.

Ora Contoso deve eseguire le operazioni di pulizia seguenti:  

- Rimuovere la macchina WEBVM dall'inventario di vCenter.
- Rimuovere la macchina SQLVM dall'inventario di vCenter.
- Rimuovere WEBVM e SQLVM dai processi di backup locali.
- Aggiornare la documentazione interna per visualizzare la nuova posizione e gli indirizzi IP per le VM.
- Esaminare le risorse che interagiscono con le VM e aggiornare eventuali impostazioni o documenti pertinenti in modo che riflettano la nuova configurazione.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Ora che l'app è in esecuzione, Contoso deve operazionalizzarla e proteggerla completamente in Azure.

### <a name="security"></a>Sicurezza

Il team di sicurezza Contoso esamina le VM di Azure per determinare eventuali problemi di sicurezza.

- Per controllare l'accesso, esamina i gruppi di sicurezza di rete (NSG) per le VM. I gruppi di sicurezza di rete fanno in modo che l'app possa essere raggiunta solo dal traffico consentito.
- Considerano inoltre di proteggere i dati sul disco usando Crittografia dischi di Azure e KeyVault.

[Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sulle procedure di sicurezza per le macchine virtuali.

### <a name="backups"></a>Backup

Contoso ha intenzione di eseguire il backup dei dati nelle VM mediante il servizio Backup di Azure. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

1. Contoso include licenze esistenti per le VM e sfrutta il Vantaggio Azure Hybrid.  Convertirà le VM di Azure esistenti per usufruire di questi prezzi.
2. Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud. [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) su Gestione costi di Azure. 

## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ha eseguito il rehosting dell'app SmartHotel in Azure migrando le VM dell'app alle VM di Azure mediante il servizio Site Recovery. 


## <a name="next-steps"></a>Passaggi successivi

Nel prossimo articolo della serie verrà spiegato in che modo Contoso esegue il rehosting della VM front-end dell'app SmartHotel in Azure mediante il servizio Site Recovery e migra il database a un gruppo di disponibilità AlwaysOn del database SQL di Azure.

