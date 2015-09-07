<properties
	pageTitle="Eseguire la migrazione di macchine virtuali IaaS da un’area di Azure a un’altra"
	description="Utilizzare Site Recovery per la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="raynew"/>

#  Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure


## Panoramica

Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

In questo articolo viene descritto come utilizzare Site Recovery per la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra. L’articolo utilizza la maggior parte dei passaggi descritti in [Configurare la protezione tra macchine virtuali VMware o server fisici locali e Azure](site-recovery-vmware-to-azure.md). È consigliabile leggere l'articolo per istruzioni dettagliate su ogni fase della distribuzione.

## Introduzione

Di seguito sono riportati i prerequisiti necessari per iniziare:

- **Server di configurazione**: una macchina virtuale di Azure che funge da server di configurazione. Il server di configurazione coordina la comunicazione tra computer locali e server Azure.
- **Server di destinazione master**: una macchina virtuale di Azure che funge da server master di destinazione. Il server riceve e conserva i dati replicati da computer protetti.
- **Server di elaborazione**: una macchina virtuale che esegue Windows Server 2012 R2. Le macchine virtuali protette trasmettono dati di replica a questo server.
- **Macchine virtuali IaaS**: le VM di cui si desidera eseguire la migrazione.

- Ulteriori informazioni su questi componenti sono disponibili in [Che cosa occorre?](site-recovery-vmware-to-azure.md#what-do-i-need)
- Leggere inoltre le linee guida sulla [pianificazione della capacità](site-recovery-vmware-to-azure.md#capacity-planning) e assicurarsi di disporre di tutti i [prerequisiti di distribuzione](site-recovery-vmware-to-azure.md#before-you-start) prima di iniziare.

## Passaggi di distribuzione

1. [Creare un insieme di credenziali](site-recovery-vmware-to-azure.md/#step-1-create-a-vault)
2. [Distribuire un server di configurazione](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) come una VM di Azure.
3. [Distribuire il server di destinazione master](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) come una VM di Azure.
4. [Distribuire un server di elaborazione](site-recovery-vmware-to-azure.md#step-4-deploy-the-on-premises-process-server). Si noti che:

	- È necessario distribuire il server di elaborazione nella stessa rete/subnet virtuale delle VM IaaS di cui si desidera eseguire la migrazione. ![VM IaaS](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure1.png)

	- Dopo aver distribuito il server di elaborazione, confermare che è in grado di comunicare con le macchine virtuali di cui si desidera eseguire la migrazione.
	- In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Le regole del firewall sulle macchine virtuali IaaS di cui si desidera eseguire la migrazione devono essere configurate per consentire l’installazione push del servizio. 

	- Dopo la distribuzione e la registrazione del server di elaborazione con il server di configurazione nell'insieme di credenziali di Site Recovery, esso deve essere visualizzato nella scheda **Server di configurazione** nella console di Site Recovery. Questo può richiedere fino a 15 minuti.
	
		![server di elaborazione](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure2.png)

5. [Installare gli aggiornamenti più recenti](site-recovery-vmware-to-azure.md#step-5-install-latest-updates). Verificare che tutti i server del componente installati siano aggiornati.
6. [Creare un gruppo di protezione](site-recovery-vmware-to-azure.md#step-7-create-a-protection-group). Per poter avviare la protezione delle macchine virtuali migrate utilizzando Site Recovery, è necessario configurare un gruppo protezione. Specificare le impostazioni di replica per un gruppo e queste verranno applicate a tutte le macchine virtuali che si aggiungono a tale gruppo. 
7. [Configurare macchine virtuali](site-recovery-vmware-to-azure.md#step-8-set-up-machines-you-want-to-protect). È necessario installare il servizio di mobilità in ogni VM (automaticamente o manualmente).
8. [Passaggio 8: Abilitare la protezione per le macchine virtuali](site-recovery-vmware-to-azure.md#step-9-enable-protection). È possibile abilitare la protezione delle VM aggiungendole a un gruppo di protezione. Si noti che:

	- È possibile individuare le macchine virtuali IaaS di cui si desidera eseguire la migrazione in Azure utilizzando il rispettivo indirizzo IP privato. Cercare questo indirizzo nel dashboard della macchina virtuale in Azure.
	-  Nella scheda del gruppo di protezione creato, fare clic su Aggiungi macchine > Macchine fisiche ![Individuazione EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure3.png)
	- Specificare l'indirizzo IP privato della macchina virtuale.
		- ![Individuazione EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure4.png)
	- La protezione verrà abilitata e la replica iniziale verrà eseguita in base alle impostazioni di replica iniziali del gruppo di protezione.
9. [Passaggio 9: Eseguire un failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover). Dopo il completamento della replica iniziale, è possibile eseguire un failover non pianificato da un’area di Azure a un’altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
		
## Passaggi successivi

Inviare commenti o domande nel [forum di Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=August15_HO9-->