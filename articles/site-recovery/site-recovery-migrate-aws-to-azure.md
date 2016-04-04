<properties
	pageTitle="Migrazione di macchine virtuali Windows da Amazon Web Services ad Azure con Site Recovery | Microsoft Azure"
	description="Questo articolo descrive come eseguire la migrazione di macchine virtuali Windows in esecuzione in Amazon Web Services (AWA) ad Azure usando Azure Site Recovery."
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
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Eseguire la migrazione delle macchine virtuali di Amazon Web Services (AWS) in Azure con Azure Site Recovery

## Panoramica

Questo articolo descrive come usare Site Recovery per eseguire la migrazione delle istanze di Windows in esecuzione in AWS ad Azure. Prima di iniziare, tenere presente quanto segue:

- È possibile eseguire la migrazione solo in questo momento. Ciò significa che si può eseguire il failover da AWS ad Azure ma non da AWS ad Azure.
- Questo articolo riassume e usa molte procedure descritte per intero in [Eseguire la replica di macchine virtuali VMware e server fisici in Azure](site-recovery-vmware-to-azure-classic.md) che fornisce le istruzioni migliorate più recenti per configurare la replica. È consigliabile seguire le istruzioni dettagliate illustrate in questo articolo durante l'esecuzione della migrazione.
- **Non usare più** le istruzioni contenute in questo [articolo legacy](site-recovery-vmware-to-azure-classic-legacy.md).

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## Prerequisiti

Di seguito sono riportati i prerequisiti necessari per iniziare:

- **Server di gestione**: una macchina virtuale locale che esegue Windows Server 2012 R2 e funge da server di gestione. Installare in questo server i componenti di Site Recovery (inclusi il server di configurazione e il server di elaborazione). Per altre informazioni, vedere le sezioni [Considerazioni relative al server di gestione](site-recovery-vmware-to-azure-classic.md#management-server-considerations) e [Prerequisiti locali](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Istanze VM EC2**: le istanze di cui si desidera eseguire la migrazione e da proteggere.

## Passaggi di distribuzione

1. [Creare un insieme di credenziali.](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [Distribuire un server di gestione](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server). 
3. Dopo aver distribuito il server di gestione, confermare che è in grado di comunicare con le istanze EC2 di cui si vuole eseguire la migrazione.
4. [Creare un gruppo di protezione](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Un gruppo di protezione contiene computer protetti che condividono le stesse impostazioni di replica. Specificare le impostazioni di replica per un gruppo e queste verranno applicate a tutte le macchine virtuali che si aggiungono al gruppo. 
5. [Installare il servizio Mobility](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Le regole del firewall nelle istanze EC2 di cui si desidera eseguire la migrazione devono essere configurate per consentire l’installazione push del servizio. Il gruppo di sicurezza delle istanze EC2 deve avere le seguenti regole:

	![Regole del firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Abilitare la protezione dei computer](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Aggiungere al gruppo di replica i computer che si vuole proteggere.

	![Abilitazione della protezione](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. È possibile individuare le istanze EC2 di cui si desidera eseguire la migrazione in Azure utilizzando l'indirizzo IP privato dell'istanza che è possibile ottenere dalla console EC2. Nel gruppo di protezione è possibile aggiungere l'indirizzo IP privato di ogni istanza.

	![Abilitazione della protezione](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	Dopo avere aggiunto un computer al gruppo, la protezione verrà abilitata e la replica iniziale verrà eseguita in base alle impostazioni del gruppo di protezione.

8. [Eseguire un failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover). Al termine della replica iniziale, è possibile eseguire un failover non pianificato da AWS ad Azure per ogni VM. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali da AWS ad Azure. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
		
## Passaggi successivi

Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Azure Site Recovery](site-recovery-overview.md)

<!---HONumber=AcomDC_0323_2016-->