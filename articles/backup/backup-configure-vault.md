<properties
	pageTitle="Configurare i servizi del Backup di Azure per preparare il backup di un computer che esegue Windows Server | Microsoft Azure"
	description="Usare questa esercitazione per apprendere come usare il servizio di backup nell'offerta cloud di Microsoft Azure per eseguire il backup di Windows Server nel cloud."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="11/26/2015" ms.author="jimpark"; "aashishr"/>

# Preparare l'ambiente per il backup di macchine virtuali Windows

Questo articolo illustra i passaggi preparatori per l'uso di Backup di Azure con server Windows. Per eseguire il backup di un server o un client Windows in Azure, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
>[AZURE.NOTE]In precedenza era necessario creare o acquisire un certificato X.509 v3 per registrare il server di backup. I certificati sono ancora supportati, ma per rendere più semplice la registrazione dell'insieme di credenziali di Azure con un server è possibile generare una credenziale di insieme direttamente dalla pagina Avvio rapido.

## Prima di iniziare
Per eseguire il backup dei file e dei dati da Windows Server in Azure, è necessario:

- **Creare un insieme di credenziali per il backup**: creare un insieme di credenziali nel [portale di gestione di Backup di Azure](http://manage.windowsazure.com).
- **Scaricare le credenziali dell'insieme di credenziali**: dalla pagina Dashboard dell'insieme di credenziali di Backup di Azure scaricare l'insieme di credenziali che verrà usato per registrare il computer Windows nell'insieme di credenziali per il backup.
- **Installare Azure Backup Agent e registrare il server**: dalla pagina Dashboard fare clic sul collegamento per scaricare l'[agente di Backup di Azure](http://aka.ms/azurebackup_agent). Installare l'agente e registrare il server nell'insieme di credenziali per il backup usando le credenziali dell'insieme di credenziali.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Passaggi successivi
- [Backup di Windows Server o Windows Client](backup-azure-backup-windows-server.md)
- [Gestione di Windows Server o Windows Client](backup-azure-manage-windows-server.md)
- [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
- [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)
- [Forum di Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_1203_2015-->