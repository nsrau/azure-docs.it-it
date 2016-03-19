<properties
	pageTitle="Gestire Archiviazione di Azure usando Automazione di Azure"
	description="Informazioni su come è possibile usare il servizio Automazione di Azure per gestire l'Archiviazione di Azure su vasta scala."
	services="storage, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="eamono"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="jolevy"/>



#Gestione di Archiviazione di Azure mediante Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'utilizzo per semplificare la gestione di BLOB, tabelle e code di Archiviazione di Azure.


## Informazioni su Automazione di Azure

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività a esecuzione prolungata, manuali, soggette a errori e ripetute frequentemente per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali in base alla crescita dell'organizzazione. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio permette di ridurre i costi operativi e di liberare risorse dello staff IT/DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.


## Come può Automazione di Azure aiutare nella gestione di Archiviazione di Azure?

Archiviazione di Azure può essere gestito in Automazione di Azure usando i cmdlet di PowerShell disponibili in [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). I cmdlet di PowerShell per l'Archiviazione sono predefiniti in Automazione di Azure, per consentire l'esecuzione di tutte le attività di gestione di BLOB, tabelle e code dall'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

La [raccolta di Runbook di Automazione di Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene diversi Runbook del team di prodotto e della community per iniziare ad automatizzare la gestione di Archiviazione di Azure, altri servizi di Azure e sistemi di terze parti. I Runbook della raccolta includono i seguenti:

 * [Remove Azure Storage Blobs that are Certain Days Old Using Automation Service (Rimuovere i BLOB di archiviazione di Azure di un determinato numero di giorni usando il servizio di automazione)](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Download a Blob from Azure Storage (Scaricare un BLOB da Archiviazione di Azure)](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Backup all disks for a single Azure VM or for all VMs in a Cloud Service (Eseguire il backup di tutti i dischi per una singola macchina virtuale di Azure o per tutte le macchine virtuali in un servizio cloud)](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)


## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire BLOB, tabelle e code di Azure, seguire i collegamenti seguenti per altre informazioni su Automazione di Azure.

Vedere l'esercitazione di Automazione di Azure [Creazione e importazione di un runbook in Automazione di Azure](../automation/automation-creating-importing-runbook.md).
 

<!---HONumber=AcomDC_0224_2016-->