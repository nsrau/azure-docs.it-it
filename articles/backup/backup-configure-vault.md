<properties
	pageTitle="Configurare i servizi del Backup di Azure per preparare il backup di Windows Server | Microsoft Azure"
	description="Usare questa esercitazione per apprendere come usare il servizio di backup nell'offerta cloud di Microsoft Azure per eseguire il backup di Windows Server nel cloud."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="08/21/2015" ms.author="jimpark"; "aashishr"/>

# Configurare il Backup di Azure per preparare il backup di Windows Server

Questo articolo illustra la procedura per abilitare la funzionalità Backup di Azure. Per eseguire il backup di Server Windows o Client Windows in Azure, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
>[AZURE.NOTE]In precedenza era necessario creare o acquisire un certificato X.509 v3 per registrare il server di backup. I certificati sono ancora supportati, ma per rendere più semplice la registrazione dell'insieme di credenziali di Azure con un server è possibile generare una credenziale di insieme direttamente dalla pagina Avvio rapido.

## Prima di iniziare
Per eseguire il backup dei file e dei dati da Windows Server in Azure, è necessario:

- **Creare un insieme di credenziali per il backup**: creare un insieme di credenziali nella console Backup di Azure.
- **Scaricare le credenziali di insieme**: in Backup di Azure caricare il certificato di gestione creato nell'insieme di credenziali.
- **Installare l'agente di Backup di Azure e registrare il server** - Da Backup di Azure, installare l'agente e registrare il server nell'archivio di backup.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Passaggi successivi
- [Backup di Windows Server o Windows Client](backup-azure-backup-windows-server.md)
- [Gestione di Windows Server o Windows Client](backup-azure-manage-windows-server.md)
- [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
- [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)
- [Forum di Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=September15_HO1-->