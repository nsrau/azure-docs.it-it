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
	ms.date="02/22/2016"
	ms.author="raynew"/>

#  Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Site Recovery

Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una panoramica rapida, vedere [Che cos'è Azure Site Recovery?](site-recovery-overview.md).

## Panoramica

In questo articolo viene descritto come utilizzare Site Recovery per eseguire in Azure la migrazione o il failover di istanze di Windows in esecuzione in AWS. L'articolo riepiloga i passaggi descritti in dettaglio in [Eseguire la replica di macchine virtuali VMware o server fisici in Azure](site-recovery-vmware-to-azure-classic.md). L'articolo collegato è la versione avanzata più recente dello scenario che replica VM VMware o server fisici Windows/Linux in Azure. È consigliabile leggere l'articolo collegato per istruzioni dettagliate su ogni fase della distribuzione.

>[AZURE.NOTE] Per la migrazione tra le aree, è consigliabile **non usare più** le istruzioni contenute in questo [articolo legacy](site-recovery-vmware-to-azure-classic-legacy.md).

## Introduzione

Di seguito sono riportati i prerequisiti necessari per iniziare:

- **Server di gestione**: una VM locale che esegue Windows Server 2012 R2 e funge da server di gestione. Installare in questo server i componenti di Site Recovery (inclusi il server di configurazione e il server di elaborazione). Per altre informazioni, vedere [Considerazioni relative al server di elaborazione](site-recovery-vmware-to-azure-classic.md#management-server-considerations) e [Prerequisiti locali](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
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

10. [ Eseguire un failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover). Dopo il completamento della replica iniziale, è possibile eseguire un failover non pianificato da un’area di Azure a un’altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
		
## Passaggi successivi

Inviare commenti o domande nel [forum di Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0309_2016-->