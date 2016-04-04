<properties
	pageTitle="Eseguire la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra con Site Recovery | Microsoft Azure"
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
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery

## Panoramica

In questo articolo viene descritto come utilizzare Site Recovery per la migrazione di VM di Azure tra aree di Azure. Prima di iniziare, tenere presente quanto segue:

- È possibile eseguire la migrazione solo in questo momento. Ciò significa che si può eseguire il failover di VM da un’area di Azure a un’altra ma non da VM ad Azure.
- Questo articolo riassume e usa molte procedure descritte per intero in [Eseguire la replica di macchine virtuali VMware e server fisici in Azure](site-recovery-vmware-to-azure-classic.md) che fornisce le istruzioni migliorate più recenti per configurare la replica. È consigliabile seguire le istruzioni dettagliate illustrate in questo articolo durante l'esecuzione della migrazione.
- **Non usare più** le istruzioni contenute in questo [articolo legacy](site-recovery-vmware-to-azure-classic-legacy.md).

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Prerequisiti

Per la distribuzione è necessario quanto segue:

- **Server di gestione**: una VM locale che esegue Windows Server 2012 R2 e agisce da server di gestione. Installare in questo server i componenti di Site Recovery (inclusi il server di configurazione e il server di elaborazione). Per altre informazioni, vedere [Considerazioni relative al server di gestione](site-recovery-vmware-to-azure-classic.md#management-server-considerations) e [Prerequisiti locali](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Macchine virtuali IaaS**: le VM di cui si desidera eseguire la migrazione.

## Passaggi di distribuzione

1. [Creare un insieme di credenziali](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault).
2. [Distribuire un server di gestione](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server).
3. Dopo avere distribuito il server di gestione, verificare che possa comunicare con le VM di cui si vuole eseguire la migrazione.
4. [Creare un gruppo di protezione](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Un gruppo di protezione contiene computer protetti che condividono le stesse impostazioni di replica. Specificare le impostazioni di replica per un gruppo e queste verranno applicate a tutte le macchine virtuali che si aggiungono al gruppo.
5. [Installare il servizio Mobility](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Si noti che le regole del firewall sulle macchine virtuali IaaS di cui si vuole eseguire la migrazione devono essere configurate per consentire l'installazione push del servizio.
6. [Abilitare la protezione dei computer](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Aggiungere al gruppo di replica i computer che si vuole proteggere. 
7. È possibile individuare le macchine virtuali IaaS di cui si desidera eseguire la migrazione in Azure utilizzando il rispettivo indirizzo IP privato. Cercare questo indirizzo nel dashboard della macchina virtuale in Azure.
8. Nella scheda del gruppo di protezione creato fare clic su **Aggiungi computer** > **Computer fisici**.

	![Individuazione EC2](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. Specificare l'indirizzo IP privato della macchina virtuale.

	![Individuazione EC2](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
	Dopo avere aggiunto un computer al gruppo, la protezione verrà abilitata e la replica iniziale verrà eseguita in base alle impostazioni del gruppo di protezione.

10. [Eseguire un failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover). Dopo il completamento della replica iniziale, è possibile eseguire un failover non pianificato da un’area di Azure a un’altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
		
## Passaggi successivi

Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Azure Site Recovery](site-recovery-overview.md).

<!---HONumber=AcomDC_0323_2016-->