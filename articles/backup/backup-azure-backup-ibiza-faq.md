<properties
   pageTitle="Insieme di credenziali di Servizi di ripristino: domande frequenti | Microsoft Azure"
   description="Questa versione delle domande frequenti supporta la versione di anteprima pubblica del servizio Backup di Azure. Risposte alle domande frequenti su agente di backup, backup e conservazione, ripristino, sicurezza e altre domande frequenti sulla soluzione Backup di Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="soluzione di backup; servizio di backup"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="08/21/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Insieme di credenziali di Servizi di ripristino: domande frequenti

> [AZURE.SELECTOR]
- [Domande frequenti su Backup per la modalità classica](backup-azure-backup-faq.md)
- [Domande frequenti su Backup per la modalità Azure Resource Manager](backup-azure-backup-ibiza-faq.md)

In questo articolo fornisce informazioni specifiche per l'insieme di credenziali di Servizi di ripristino e integra [Servizio Backup di Azure: Domande frequenti](backup-azure-backup-faq.md). L'articolo Domande frequenti su Backup di Azure include tutte le domande e le risposte relative al servizio Backup di Azure.

È possibile formulare le domande su Backup di Azure nella sezione Disqus di questo articolo o di un articolo correlato. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Gli insiemi di credenziali di Servizi di ripristino sono basati su Resource Manager. Gli insiemi di credenziali di Backup (modalità classica) sono ancora supportati? <br/>
Sì, gli insiemi di credenziali di Backup sono ancora supportati. Creare gli insiemi di credenziali per il backup nel [portale classico](https://manage.windowsazure.com). Creare insiemi di credenziali di Servizi di ripristino nel [portale di Azure](https://portal.azure.com). Tuttavia, è consigliabile creare un insieme di credenziali di Servizi di ripristino perché tutti gli miglioramenti futuri saranno disponibili solo nell'insieme di credenziali di Servizi di ripristino.

## È possibile eseguire la migrazione di un insieme di credenziali per il backup in un insieme di credenziali di Servizi di ripristino? <br/>
Purtroppo no, al momento non è possibile eseguire la migrazione del contenuto di un insieme di credenziali di Backup in un insieme di credenziali di Servizi di ripristino. Questa funzionalità verrà presto aggiunta, ma non è disponibile nell'anteprima pubblica.

## Gli insiemi di credenziali di Servizi di ripristino supportano le macchine virtuali in modalità classica o quelle basate su Resource Manager? <br/>
Gli insiemi di credenziali di Servizi di ripristino supportano entrambi modelli. È possibile eseguire il backup di una macchina virtuale creata nel portale classico (VM in modalità classica) oppure nel portale di Azure (basata su Azure Resource Manager) in un insieme di credenziali di Servizi di ripristino.

## È stato eseguito il backup delle macchine virtuali in modalità classica nell'insieme di credenziali per il backup. Si vuole eseguire la migrazione delle macchine virtuali dalla modalità classica alla modalità Resource Manager. Come è possibile eseguirne il backup nell'insieme di credenziali di Servizi di ripristino?
Per i backup di macchine virtuali in modalità classica nell'insieme di credenziali per il backup non viene eseguita automaticamente la migrazione nell'insieme di credenziali di Servizi di ripristino nel caso di migrazione delle macchine virtuali dalla modalità classica alla modalità Resource Manager. Per la migrazione di backup di macchine virtuali, seguire questa procedura:

1. Nell'insieme di credenziali per il backup passare alla scheda **Elementi protetti** e selezionare la macchina virtuale. Fare clic su [Arresta protezione](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Lasciare *deselezionata* l'opzione **Elimina i dati di backup associati**.
2. Eseguire la migrazione della macchina virtuale dalla modalità classica alla modalità Resource Manager. Assicurarsi di eseguire la migrazione alla modalità Resource Manager anche della risorsa di archiviazione e della rete corrispondenti alla macchina virtuale.
3. Creare un insieme di credenziali di Servizi di ripristino e configurare il backup della macchina virtuale di cui è stata eseguita la migrazione usando l'azione **Backup** nella parte superiore del dashboard dell'insieme di credenziali. Altre informazioni su come [abilitare il backup nell'insieme di credenziali di Servizi di ripristino](backup-azure-vms-first-look-arm.md)

<!---HONumber=AcomDC_0928_2016-->