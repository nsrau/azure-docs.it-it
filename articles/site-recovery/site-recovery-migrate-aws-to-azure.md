<properties
	pageTitle="Migrazione di macchine virtuali Windows da Amazon Web Services ad Azure con Site Recovery | Microsoft Azure"
	description="Utilizzare Azure Site Recovery per eseguire la migrazione di macchine virtuali Windows in esecuzione in Amazon Web Services (AWA) ad Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Eseguire la migrazione delle macchine virtuali di Amazon Web Services (AWS) in Azure con Azure Site Recovery


## Panoramica

Azure Site Recovery favorisce la strategia di continuità aziendale e di ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino delle macchine virtuali in diverse distribuzioni. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

In questo articolo viene descritto come utilizzare Site Recovery per eseguire in Azure la migrazione o il failover di istanze di Windows in esecuzione in AWS. L’articolo utilizza la maggior parte dei passaggi descritti in [Configurare la protezione tra macchine virtuali VMware o server fisici locali e Azure](site-recovery-vmware-to-azure-classic-legacy.md). È consigliabile leggere l'articolo per istruzioni dettagliate su ogni fase della distribuzione.

## Introduzione

Di seguito sono riportati i prerequisiti necessari per iniziare:

- **Server di configurazione**: una macchina virtuale di Azure che funge da server di configurazione. Il server di configurazione coordina la comunicazione tra computer locali e server Azure.
- **Server di destinazione master**: una macchina virtuale di Azure che funge da server master di destinazione. Il server riceve e conserva i dati replicati da computer protetti.
- **Server di elaborazione**: una macchina virtuale che esegue Windows Server 2012 R2. Le macchine virtuali protette trasmettono dati di replica a questo server.
- **Istanze VM EC2**: le istanze di cui si desidera eseguire la migrazione e da proteggere.

- Ulteriori informazioni su questi componenti sono disponibili in [Che cosa occorre?](site-recovery-vmware-to-azure-classic-legacy.md#what-do-i-need)
- Leggere inoltre le linee guida sulla [pianificazione della capacità](site-recovery-vmware-to-azure-classic-legacy.md#capacity-planning) e assicurarsi di disporre di tutti i [prerequisiti di distribuzione](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start) prima di iniziare.

## Passaggi di distribuzione

1. [Creare un insieme di credenziali](site-recovery-vmware-to-azure-classic-legacy.md#step-1-create-a-vault)
2. [Distribuire un server di configurazione](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) come una VM di Azure.
3. [Distribuire il server di destinazione master](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) come una VM di Azure.
4. [Distribuire un server di elaborazione](site-recovery-vmware-to-azure-classic-legacy.md#step-4-deploy-the-on-premises-process-server). Si noti che:

	- È necessario distribuire il server di elaborazione sulla stessa subnet/Amazon Virtual Private Cloud delle istanze EC2. ![Istanze EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration1.png)

	- Dopo aver distribuito il server di elaborazione, confermare che è in grado di comunicare con le istanze EC2 di cui si desidera eseguire la migrazione.
	- In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Le regole del firewall nelle istanze EC2 di cui si desidera eseguire la migrazione devono essere configurate per consentire l’installazione push del servizio. Il gruppo di sicurezza delle istanze EC2 deve avere le seguenti regole:

		![Regole del firewall](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration2.png)

	- Dopo la distribuzione e la registrazione del server di elaborazione con il server di configurazione nell'insieme di credenziali di Site Recovery, esso deve essere visualizzato nella scheda **Server di configurazione** nella console di Site Recovery. Questo può richiedere fino a 15 minuti.
	
		![server di elaborazione](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration3.png)

5. [Installare gli aggiornamenti più recenti](site-recovery-vmware-to-azure-classic-legacy.md#step-5-install-latest-updates). Verificare che tutti i server del componente installati siano aggiornati.
6. [Creare un gruppo di protezione](site-recovery-vmware-to-azure-classic-legacy.md#step-7-create-a-protection-group). Per poter avviare la protezione delle istanze migrate utilizzando Site Recovery, è necessario configurare un gruppo protezione. Specificare le impostazioni di replica per un gruppo e queste verranno applicate a tutte le istanze che si aggiungono a tale gruppo. 
7. [Configurare macchine virtuali](site-recovery-vmware-to-azure-classic-legacy.md#step-8-set-up-machines-you-want-to-protect). È necessario installare il servizio di mobilità in ogni istanza (automaticamente o manualmente).
8. [Abilitare la protezione delle macchine virtuali](site-recovery-vmware-to-azure-classic-legacy.md#step-9-enable-protection). È possibile abilitare la protezione delle istanze aggiungendole a un gruppo di protezione. Si noti che:

	- È possibile individuare le istanze EC2 di cui si desidera eseguire la migrazione in Azure utilizzando l'indirizzo IP privato dell'istanza che è possibile ottenere dalla console EC2.
	-  Nella scheda del gruppo di protezione creato, fare clic su Aggiungi macchine > Macchine fisiche ![Individuazione EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration4.png)
	- Specificare l'indirizzo IP privato dell'istanza.
		- ![Individuazione EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration5.png)
	- La protezione verrà abilitata e la replica iniziale verrà eseguita in base alle impostazioni di replica iniziali del gruppo di protezione
9. [Eseguire un failover non pianificato](site-recovery-failover-classic-legacy.md#run-an-unplanned-failover). Dopo il completamento della replica iniziale, è possibile eseguire un failover non pianificato da AWS ad Azure. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali da AWS ad Azure. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
		
## Passaggi successivi

Inviare commenti o domande nel [forum di Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0121_2016-->