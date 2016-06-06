<properties
	pageTitle="Gestire Gestione API di Azure usando Automazione di Azure"
	description="Informazioni su come è possibile usare il servizio Automazione di Azure per gestire Gestione API di Azure."
	services="api-management, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="csand"/>



#Gestione di Gestione API di Azure usando Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'uso per semplificare la gestione di Gestione API di Azure.

## Informazioni su Automazione di Azure

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.


## In che modo Gestione API di Azure può essere gestito con Automazione di Azure?

Gestione API può essere gestito in Automazione di Azure tramite l'[API cmdlet di Windows PowerShell per Gestione API di Azure](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/). All'interno di Automazione di Azure è possibile scrivere gli script dei flussi di lavoro di PowerShell per eseguire molte delle attività di Gestione API mediante i cmdlet. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Di seguito sono riportati alcuni esempi di utilizzo di Gestione API con Automazione:
* [Gestione API di Azure: utilizzo di PowerShell per il backup e ripristino](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e del modo in cui può essere usato per gestire Gestione API di Azure, usare i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l'[esercitazione iniziale](../automation/automation-first-runbook-graphical.md) di Automazione di Azure.

<!---HONumber=AcomDC_0525_2016-->