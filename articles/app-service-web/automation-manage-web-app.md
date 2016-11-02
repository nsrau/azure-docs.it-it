<properties
	pageTitle="Gestire app Web di Azure usando Automazione di Azure | Microsoft Azure"
	description="Informazioni su come è possibile usare il servizio Automazione di Azure per gestire App Web di Azure."
	services="app-service\web, automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/29/2016"
	ms.author="magoedte;csand"/>

#Gestire App Web di Azure usando Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'uso per semplificare la gestione di App Web di Azure.

## Informazioni su Automazione di Azure

[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.


## In che modo Automazione di Azure può rappresentare un aiuto nella gestione di App Web di Azure?

È possibile gestire le app Web in Automazione di Azure usando i cmdlet di PowerShell disponibili nei [moduli di Azure PowerShell](../powershell-install-configure.md). È possibile [installare questi cmdlet di PowerShell per app Web in Automazione di Azure](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/) per eseguire tutte le attività di gestione delle app Web dall'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Di seguito sono riportati alcuni esempi di gestione dei servizi app con Automazione:

* [Script per la gestione di app Web](https://azure.microsoft.com/documentation/scripts/)

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire App Web di Azure, consultare i seguenti collegamenti per altre informazioni su Automazione di Azure.

* Vedere l'[esercitazione introduttiva](../automation/automation-first-runbook-graphical.md) di Automazione di Azure

<!---HONumber=AcomDC_0803_2016-->