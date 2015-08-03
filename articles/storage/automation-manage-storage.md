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
	ms.date="05/20/2015"
	ms.author="jolevy"/>



#Gestione di Archiviazione di Azure mediante Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'utilizzo per semplificare la gestione di BLOB, tabelle e code di Archiviazione di Azure.


## Informazioni su Automazione di Azure

[Automazione di Azure](http://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività a esecuzione prolungata, manuali, soggette a errori e ripetute frequentemente per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali in base alla crescita dell'organizzazione. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio permette di ridurre i costi operativi e di liberare risorse dello staff IT/DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.


## Come può Automazione di Azure aiutare nella gestione di Archiviazione di Azure?

Archiviazione di Azure può essere gestito in Automazione di Azure usando i cmdlet di PowerShell disponibili negli [strumenti di Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). I cmdlet di PowerShell per l'Archiviazione sono predefiniti in Automazione di Azure, per consentire l'esecuzione di tutte le attività di gestione di BLOB, tabelle e code dall'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

La [raccolta di Runbook di Automazione di Azure](http://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene diversi Runbook del team di prodotto e della community per iniziare ad automatizzare la gestione di Archiviazione di Azure, altri servizi di Azure e sistemi di terze parti. I Runbook della raccolta includono i seguenti:

 * [Rimuovere i BLOB di Archiviazione di Azure precedenti a X giorni](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Scaricare un BLOB da Archiviazione di Azure ad Automazione di Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Creare copie dei dischi di dati delle macchine virtuali di Azure in un Servizio cloud di Azure](https://gallery.technet.microsoft.com/scriptcenter/Make-copies-of-Azure-VM-065a6394)


## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire BLOB, tabelle e code di Azure, seguire i collegamenti seguenti per altre informazioni su Automazione di Azure.

Vedere l'[esercitazione introduttiva](../automation-create-runbook-from-samples.md) di Automazione di Azure
 

<!---HONumber=July15_HO4-->