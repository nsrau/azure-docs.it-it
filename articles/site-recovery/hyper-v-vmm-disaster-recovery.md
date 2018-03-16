---
title: Configurazione del ripristino di emergenza per macchine virtuali Hyper-V sui siti locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza per macchine virtuali Hyper-V sui siti locali con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 13dcc0794c1d89bd27c79cbe6636397da4f008f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Configurare il ripristino di emergenza per macchine virtuali Hyper-V in un sito locale secondario

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questo articolo viene illustrato come configurare il ripristino di emergenza in un sito secondario, per macchine virtuali Hyper-V locali gestite nei cloud di System Center Virtual Machine Manager (VMM). In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Preparare il server VMM e gli host Hyper-V locali
> * Creare un insieme di credenziali di Servizi di ripristino per Site Recovery 
> * Configurare gli ambienti di replica di origine e di destinazione. 
> * Configurare il mapping di rete 
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale

## <a name="prerequisites"></a>prerequisiti

Per completare questo scenario:

- Esaminare [l'architettura e i componenti dello scenario](hyper-v-vmm-architecture.md).
- Assicurarsi che i server VMM e gli host Hyper-V rispettino i [requisiti di supporto](hyper-v-vmm-secondary-support-matrix.md).
- Assicurarsi che le macchine virtuali da replicare siano conformi al [supporto del computer replicato](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Preparare i server VMM per il mapping di rete.

### <a name="prepare-for-network-mapping"></a>Preparare il mapping di rete

[Mapping di rete](hyper-v-vmm-network-mapping.md) esegue il mapping tra le reti VM di VMM locali in cloud di origine e destinazione. Il mapping esegue queste operazioni:

- Connette le macchine virtuali a reti di macchine virtuali di destinazione appropriate dopo il failover. 
- Posiziona in modo ottimale le macchine virtuali di replica nei server host Hyper-V di destinazione. 
- Se non si configura il mapping di rete, le macchine virtuali di replica non verranno connesse alla rete di una macchina virtuale dopo il failover.

Preparare VMM come segue:

1. Assicurarsi di disporre di [reti logiche VMM](https://docs.microsoft.com/system-center/vmm/network-logical) nei server VMM di origine e di destinazione.
    - La rete logica nel server di origine deve essere associata al cloud di origine in cui si trovano gli host Hyper-V.
    - La rete logica nel server di destinazione deve essere associata al cloud di destinazione.
1. Assicurarsi di disporre di [reti VM](https://docs.microsoft.com/system-center/vmm/network-virtual) nei server VMM di origine e di destinazione. Le reti VM devono essere collegate alla rete logica in ogni posizione.
2. Connettere le macchine virtuali negli host Hyper-V di origine alla rete VM di origine. 


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.
2. Selezionare **Nel sito di ripristino**, quindi fare clic su **Yes, with Hyper-V** (Sì, con Hyper-V).
3. Selezionare **Sì** per indicare che si usa VMM per gestire gli host Hyper-V.
4. Selezionare **Sì** se si dispone di un server VMM secondario. Se si distribuisce la replica tra cloud in un unico server VMM, fare clic su **No**. Fare quindi clic su **OK**.


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Installare il provider di Azure Site Recovery nei server VMM, rilevare e registrare il server nell'insieme di credenziali.

1. Fare clic su **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM.
3. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
4. Scaricare il file di installazione del provider di Azure Site Recovery.
5. Scaricare la chiave di registrazione, È necessario seguire questa procedura quando si installa il provider. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Impostare l'origine](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Installare il provider su ogni server VMM. Negli host Hyper-V non è necessario installare niente in modo esplicito.

### <a name="install-the-azure-site-recovery-provider"></a>Installare il provider di Azure Site Recovery

1. Eseguire il file di installazione del provider in ogni server VMM. Se VMM viene distribuito in un cluster, procedere come segue per la prima installazione:
    -  Installare il provider in un nodo attivo e completare l'installazione per registrare il server VMM nell'insieme di credenziali.
    - Quindi, installare il provider negli altri nodi. Tutti i nodi del cluster devono eseguire la stessa versione del provider.
2. Il programma di installazione esegue alcune verifiche dei prerequisiti e chiede l'autorizzazione per arrestare il servizio VMM. Il servizio VMM verrà riavviato automaticamente al termine dell'installazione. Se si esegue l'installazione in un cluster VMM, verrà richiesto di interrompere il ruolo Cluster.
3. In **Microsoft Update** è possibile acconsentire per specificare esplicitamente che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
4. In **Installazione** accettare o modificare il percorso predefinito di installazione e quindi fare clic su **Installa**.
5. Al termine dell'installazione fare clic su **Registra** per registrare il server nell'insieme di credenziali.

    ![Percorso di installazione](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. In **Vault name**verificare il nome dell'insieme di credenziali in cui verrà registrato il server. Fare clic su **Avanti**.
7. Nella pagina **Connessione del proxy**, specificare la modalità di connessione ad Azure del provider in esecuzione sul server VMM.
   - È possibile specificare che il provider debba connettersi direttamente a Internet o tramite un proxy. Se necessario, specificare le impostazioni proxy.
   - Se si usa un proxy, viene creato automaticamente un account RunAs di VMM (DRAProxyAccount) con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione. È possibile modificare le impostazioni dell'account RunAs nella console di VMM > **Impostazioni** > **Sicurezza** > **Account RunAs**.
   - Riavviare il servizio VMM per aggiornare le modifiche.
8. In **Chiave di registrazione** selezionare il codice scaricato e copiato nel server VMM.
9. L'impostazione di crittografia non è rilevante per questo scenario. 
10. In **Nome server**specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In un cluster, specificare il nome del ruolo relativo al cluster VMM.
11. In **Sincronizza i metadati cloud** scegliere se sincronizzare i metadati per tutti i cloud presenti nel server VMM. È necessario eseguire questa azione solo una volta in ogni server. Se non si desidera sincronizzare tutti i cloud, lasciare deselezionata questa opzione. È possibile sincronizzare ogni cloud singolarmente nelle proprietà del cloud sulla console VMM.
12. Fare clic su **Avanti** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Site Recovery. Il server viene visualizzato in **Server** > **Server VMM** nell'insieme di credenziali.
13. Quando il server compare nell'insieme di credenziali, in **Origine** > **Prepara origine** selezionare il server VMM, quindi selezionare il cloud in cui si trova l'host Hyper-V. Fare quindi clic su **OK**.


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare il server VMM di destinazione e il cloud:

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare il server VMM di destinazione.
2. Verranno visualizzati i cloud VMM sincronizzati con Site Recovery. Selezionare il cloud di destinazione.

   ![Destinazione](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

Prima di iniziare, assicurarsi che tutti gli host che usano i criteri abbiano lo stesso sistema operativo. Se gli host eseguono versioni diverse di Windows Server, saranno necessari più criteri di replica.

1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.
2. In **Criteri di creazione e associazione**specificare il nome dei criteri. Il tipo di origine e di destinazione deve essere **Hyper-V**.
3. In **Versione host Hyper-V** selezionare il sistema operativo in esecuzione nell'host.
4. In **Tipo di autenticazione** e **Porta di autenticazione** specificare come viene autenticato il traffico tra il server host Hyper-V primario e quello di ripristino.
    - Selezionare **Certificato** a meno che non sia configurato un ambiente Kerberos funzionante. Azure Site Recovery configura automaticamente i certificati per l'autenticazione HTTPS. Non è necessario intervenire manualmente.
    - Per impostazione predefinita, le porte 8083 e 8084 (per i certificati) sono aperte in Windows Firewall per i server host Hyper-V.
    - Se si seleziona **Kerberos**, verrà usato un ticket Kerberos per l'autenticazione reciproca dei server host. Kerberos è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2 o successivo.
1. In **Frequenza di copia**specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.
2. In **Conservazione del punto di recupero**specificare la durata in ore dell'intervallo di conservazione per ogni punto di recupero. I computer replicati possono essere ripristinati in qualsiasi punto all'interno di un intervallo.
3. In **Frequenza snapshot coerenti con l'app** specificare la frequenza, da 1 a 12 ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Hyper-V usa due tipi di snapshot:
    - **Snapshot standard**: fornisce uno snapshot incrementale dell'intera macchina virtuale.
    - **Snapshot coerente con l'app**: è uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Il servizio Copia Shadow del volume assicura che lo stato dell'app sia coerente quando viene creato lo snapshot. L'abilitazione di snapshot coerenti con l'app influisce sulle prestazioni dell'app su macchine virtuali di origine. Impostare un valore inferiore al numero di punti di ripristino aggiuntivi configurati.
4. In **Compressione trasferimento dati**, specificare se i dati replicati che vengono trasferiti devono essere compressi.
5. Selezionare **Eliminare la macchina virtuale di replica** per specificare che la macchina virtuale di replica deve essere eliminata nel caso in cui si disabiliti la protezione per la VM di origine. Con questa impostazione abilitata, quando si disabilita la protezione per la macchina virtuale di origine, questa viene rimossa dalla console di Site Recovery, le impostazioni di Site Recovery relative a VMM vengono rimosse dalla console VMM e la replica viene eliminata.
6. Se si esegue la replica in rete, in **Metodo di replica iniziale** specificare se si preferisce avviare la replica iniziale o pianificarla. Per risparmiare larghezza di banda di rete, è opportuno pianificarla al di fuori dell'orario di lavoro. Fare quindi clic su **OK**.

     ![Criteri di replica](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. I nuovi criteri vengono associati automaticamente al cloud VMM. In **Criteri di replica** fare clic su **OK**. 


## <a name="enable-replication"></a>Abilitare la replica

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**. 
2. In **Origine** selezionare il server VMM e il cloud in cui si trovano gli host Hyper-V da replicare. Fare quindi clic su **OK**.
3. In **Destinazione** verificare il cloud e il server VMM secondario.
4. In **Macchine virtuali** selezionare nell'elenco le macchine virtuali da proteggere.


È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire un'esercitazione sul ripristino di emergenza](hyper-v-vmm-test-failover.md)
