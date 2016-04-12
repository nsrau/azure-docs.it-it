<properties
   pageTitle="Domande frequenti sulla versione di anteprima pubblica di Backup di Azure | Microsoft Azure"
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
	 ms.date="03/30/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Versione di anteprima pubblica del servizio Backup di Azure: Domande frequenti

> [AZURE.SELECTOR]
- [Domande frequenti su Backup per la modalità classica](backup-azure-backup-faq.md)
- [Domande frequenti su Backup per la modalità Azure Resource Manager](backup-azure-backup-ibiza-faq.md)

Questo articolo fornisce informazioni specifiche per la versione di anteprima pubblica del servizio Backup di Azure. L'articolo viene aggiornato di volta in volta con l'aggiunta di nuove domande frequenti e integra l'articolo [Domande frequenti su Backup di Azure](backup-azure-backup-faq). L'articolo Domande frequenti su Backup di Azure include tutte le domande e le risposte relative al servizio Backup di Azure.

È possibile formulare le domande su Backup di Azure nella sezione Disqus di questo articolo o di un articolo correlato. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Cosa include la versione di anteprima pubblica?
La versione di anteprima pubblica introduce l'insieme di credenziali di Servizi di ripristino e il supporto di Azure Resource Manager per la protezione delle macchine virtuali di Azure. L'insieme di credenziali di Servizi di ripristino è un insieme di credenziali di nuova generazione. È lo stesso usato dal servizio Backup di Azure e dal servizio Azure Site Recovery. Può essere considerato come la versione 2 dell'insieme di credenziali.

## Insiemi di credenziali di Backup e Servizi di ripristino

**D1. Se gli insiemi di credenziali di Servizi di ripristino sono la versione 2, gli insiemi di credenziali di Backup, ovvero la versione 1, sono ancora supportati?** <br/> R1. Sì, gli insiemi di credenziali di Backup sono ancora supportati. Per creare gli insiemi di credenziali di Backup si usa il portale classico. Per creare gli insiemi di credenziali di Servizi di ripristino si usa il portale di Azure.

**D2. È possibile eseguire la migrazione di un insieme di credenziali di Backup in un insieme di credenziali di Servizi di ripristino?** <br/> R2. Purtroppo no, al momento non è possibile eseguire la migrazione del contenuto di un insieme di credenziali di Backup in un insieme di credenziali di Servizi di ripristino. Questa funzionalità verrà presto aggiunta, ma non è disponibile nell'anteprima pubblica.

**D3. Gli insiemi di credenziali di Servizi di ripristino supportano macchine virtuali versione 1 o versione 2?** <br/> R3. Gli insiemi di credenziali di Servizi di ripristino supportano macchine virtuali di entrambe le versioni. È possibile eseguire il backup di una macchina virtuale creata nel portale classico (versione 1) oppure nel portale di Azure (versione 2) in un insieme di credenziali di Servizi di ripristino.


## Supporto di Azure Resource Manager per le macchine virtuali di Azure

**D1. Sono previste limitazioni al supporto di Azure Resource Manager per le macchine virtuali di Azure?** <br/> R1. Attualmente i cmdlet di PowerShell per Azure Resource Manager non sono disponibili. Per aggiungere risorse a un gruppo di risorse è necessario usare l'interfaccia utente del portale di Azure.

<!---HONumber=AcomDC_0406_2016-->