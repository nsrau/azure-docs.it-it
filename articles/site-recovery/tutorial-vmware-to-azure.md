---
title: Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5810ff908d48fc4ff742d734e7c2457fdfe8cb03
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali

In questa esercitazione viene illustrato come configurare il ripristino di emergenza in Azure per le macchine virtuali VMware in locale che esegue Windows. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Specificare l'origine della replica e destinazione.
> * Configurare l'ambiente di replica di origine, inclusi i componenti, il ripristino del sito locale e l'ambiente di replica di destinazione.
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](tutorial-prepare-on-premises-vmware.md)

Prima di iniziare, è utile [esaminare l'architettura](concepts-vmware-to-azure-architecture.md) per uno scenario di ripristino di emergenza.


## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. In **insiemi di credenziali di servizi di ripristino**, fare clic sul nome dell'insieme di credenziali, **ContosoVMVault**.
2. In **Introduzione**, fare clic su Site Recovery. Quindi fare clic su **preparare l'infrastruttura**.
3. In **obiettivi della protezione dati** > **dove sono i computer che si trovano**selezionare **locale**.
4. In * * in cui si desidera replicare le macchine, selezionare **in Azure**.
5. In **sono computer virtualizzati**selezionare **Sì, con VMware vSphere Hypervisor**. Fare quindi clic su **OK**.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Per configurare l'ambiente di origine, aver scaricato il file di installazione unificata di Site Recovery. Eseguire il programma di installazione per installare i componenti di ripristino del sito locale, registrare i server VMware nell'insieme di credenziali e individuare le macchine virtuali locali.

### <a name="verify-on-premises-site-recovery-requirements"></a>Verificare i requisiti di ripristino del sito locale

Una macchina virtuale di VMware locale singolo e a disponibilità elevata, è necessario per i componenti di Site Recovery di host locale. Componenti includono il server di configurazione, un server di elaborazione e un server di destinazione master.

- Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Il server di elaborazione viene installato anche il servizio Mobility nelle macchine virtuali da replicare, ed esegue l'individuazione automatica delle macchine virtuali VMware in locale.
- Il server di destinazione master gestisce i dati di replica durante il failback da Azure.

La macchina virtuale deve soddisfare i requisiti seguenti.

| **Requisito** | **Dettagli** |
|-----------------|-------------|
| Numero di core CPU| 8 |
| RAM | 12 GB |
| Numero di dischi | 3 - Disco del sistema operativo, disco della cache del server di elaborazione, unità di conservazione (per il failback) |
| Spazio libero su disco (cache del server di elaborazione) | 600 GB |
| Spazio libero su disco (disco di conservazione) | 600 GB |
| Versione del sistema operativo | Windows Server 2012 R2 |
| Impostazioni locali del sistema operativo | Inglese (en-us) |
| Versione di VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Ruoli di Windows Server | Non abilitare questi ruoli: Active Directory Domain Services, Internet Information Services, Hyper-V |
| Tipo di scheda di interfaccia di rete | VMXNET3 |
| Tipo di indirizzo IP | statico |
| Porte | 443 (orchestrazione del canale di controllo)<br/>9443 (trasporto dei dati)|

Eseguire anche queste operazioni: 
- Verificare che l'orologio di sistema nella macchina virtuale sia sincronizzato con un Server. L'orario deve essere sincronizzato entro i 15 minuti. L'installazione non riesce se è maggiore.
l'installazione non riesce.
- Assicurarsi che il server di configurazione macchina virtuale può accedere a questi URL:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Assicurarsi che le regole firewall basate sull'indirizzo IP consentano la comunicazione in Azure.
    - Consenti il [intervalli IP Data Center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653), la porta 443 (HTTPS) e porta 9443 (la replica dei dati).
    - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).


### <a name="download-the-site-recovery-unified-setup-file"></a>Scaricare il file di installazione unificata di Site Recovery

1. Nell'insieme di credenziali > **preparare l'infrastruttura**, fare clic su **origine**.
1. In **origine Prepare**, fare clic su **+ server di configurazione**.
2. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
3. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
4. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Impostare l'origine](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>Configurare il server di configurazione

1. Eseguire il file di installazione per l'Installazione unificata.
2. In **Prima di iniziare** selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione) e quindi fare clic su **Avanti**.

3. In **Third Party Software License** (Licenza software di terze parti) fare clic su **Accetto** per scaricare e installare MySQL e quindi fare clic su **Avanti**.

4. In **Registrazione** selezionare la chiave di registrazione scaricata dall'insieme di credenziali.

5. In **Impostazioni Internet** specificare in che modo il provider in esecuzione nel server di configurazione si connette ad Azure Site Recovery tramite Internet.

   - Per connettersi al proxy attualmente configurato nel computer, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
   - Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
   - Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connect with custom proxy settings** (Connetti con le impostazioni proxy personalizzate) e specificare indirizzo, porta e credenziali.

   ![Firewall](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo per assicurarsi che sia possibile eseguire l'installazione. Se viene visualizzato un avviso relativo al **Global time sync check** (Controllo della sincronizzazione ora globale), verificare che l'ora del clock di sistema, nelle impostazioni di **Data e ora**, corrisponda al fuso orario.

   ![Prerequisiti](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. In **MySQL Configuration** (Configurazione MySQL) creare le credenziali per l'accesso all'istanza del server MySQL che viene installata.

8. In **Dettagli ambiente** selezionare **Sì** per proteggere le macchine virtuali VMware. Il programma di installazione verifica che PowerCLI 6.0 sia installato.

9. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. L'unità selezionata deve avere almeno 5 GB di spazio su disco disponibile, ma è consigliabile usare un'unità cache con almeno 600 GB di spazio disponibile.

10. In **Network Selection** (Selezione rete) specificare il listener, ovvero la scheda di rete e la porta SSL, in cui il server di configurazione deve inviare e ricevere i dati di replica. La porta 9443 è la porta predefinita per l'invio e la ricezione del traffico di replica, ma è possibile modificare il numero di porta in base ai requisiti dell'ambiente. Viene aperta anche la porta 443, usata per orchestrare le operazioni di replica. Non usare la porta 443 per inviare o ricevere traffico di replica.

11. Esaminare le informazioni nella pagina **Riepilogo** e fare clic su **Installa**. Il programma di installazione installa il server di configurazione e registra il servizio Azure Site Recovery.

    ![Summary](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    Al termine dell'installazione verrà generata una passphrase. Sarà necessaria quando si abilita la replica, è quindi consigliabile copiarla e conservarla in un luogo sicuro. Il server viene visualizzato nel riquadro **Impostazioni** > **Server** nell'insieme di credenziali.

### <a name="configure-automatic-discovery"></a>Configurare l'individuazione automatica

Per individuare le macchine virtuali, il server di configurazione deve connettersi ai server VMware locali. Ai fini di questa esercitazione, aggiungere il server vCenter o gli host vSphere usando un account con privilegi di amministratore nel server. È stato creato questo account nel [esercitazione precedente](tutorial-prepare-on-premises-vmware.md). 

Per aggiungere l'account:

1. Nel server di configurazione macchina virtuale, avviare **CSPSConfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella *percorso di installazione*\home\svsystems\bin.

2. Fare clic su **Gestisci account** > **Aggiungi account**.

   ![Aggiungi account](./media/tutorial-vmware-to-azure/credentials1.png)

3. In **Dettagli dell'account** aggiungere l'account che verrà usato per l'individuazione automatica.

   ![Dettagli](./media/tutorial-vmware-to-azure/credentials2.png)

Per aggiungere il server VMware:

1. Aprire il [portale di Azure](https://portal.azure.com) e fare clic su **Tutte le risorse**.
2. Fare clic sull'insieme di credenziali di Servizi di ripristino denominato **ContosoVMVault**.
3. Fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Origine**
4. Selezionare **+ vCenter**, per connettersi a un host ESXi vCenter server o vSphere.
5. In **Aggiungi vCenter** specificare un nome descrittivo per il server. Quindi specificare l'indirizzo IP o il nome di dominio completo.
6. Mantenere l'impostazione della porta 443, a meno che i server VMware non ascoltino le richieste su una porta diversa.
7. Selezionare l'account da usare per la connessione al server. Fare clic su **OK**.

Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le VM.

> [!NOTE]
> Possono trascorrere 15 minuti o più prima che il nome dell'account venga visualizzato nel portale. Per aggiornarlo immediatamente, fare clic su **Server di configurazione** > ***nome del server*** > **Aggiorna server**.

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare se per la destinazione deve essere usato il modello di distribuzione classica o Resource Manager.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Destinazione](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. Aprire il [portale di Azure](https://portal.azure.com) e fare clic su **Tutte le risorse**.
2. Fare clic sull'insieme di credenziali di Servizi di ripristino denominato **ContosoVMVault**.
3. Per creare criteri di replica, fare clic su **Infrastruttura di Site Recovery** > **Criteri di replica** > **+Criteri di replica**.
4. In **Creare i criteri di replica** specificare il nome dei criteri **VMwareRepPolicy**.
5. In **Soglia RPO** usare il valore predefinito di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
6. In **Conservazione del punto di ripristino** usare il valore predefinito di 24 ore per la durata del periodo di conservazione per ogni punto di ripristino. Per questa esercitazione, selezionare 72 ore. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
7. In **Frequenza snapshot coerenti con l'app** usare il valore predefinito di 60 minuti per la frequenza con cui vengono creati snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

   ![Criterio](./media/tutorial-vmware-to-azure/replication-policy.png)

I criteri vengono automaticamente associati al server di configurazione. Per impostazione predefinita vengono creati automaticamente criteri corrispondenti per il failback. Se, ad esempio, il criterio di replica è **rep-policy**, il criterio di failback sarà **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.

## <a name="enable-replication"></a>Abilitare la replica

Quando viene abilitata la replica per una macchina virtuale, Site Recovery installa il servizio Mobility. Le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.

Per abilitare la replica, procedere come descritto di seguito:

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host.
5. Selezionare il server di elaborazione (server di configurazione). Quindi fare clic su **OK**.
6. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le VM di cui viene effettuato il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure per le VM di cui viene effettuato il failover.
7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati.
8. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover.
9. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer.
10. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.
11. In **Proprietà** > **Configura proprietà** selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
12. In **Impostazioni della replica** > **Configurare le impostazioni di replica** verificare che siano stati selezionati i criteri di replica corretti.
13. Fare clic su **Abilita la replica**.

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

Per monitorare le macchine virtuali aggiunte è possibile controllare l'ora dell'ultima individuazione di macchine virtuali in **Server di configurazione**
> **Ora ultimo contatto**. Per aggiungere VM senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e fare clic su **Aggiorna**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](site-recovery-test-failover-to-azure.md)
