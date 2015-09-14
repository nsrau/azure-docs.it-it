<properties
	pageTitle="Gestire il bus di servizio di Azure mediante Automazione di Azure"
	description="Informazioni su come è possibile usare il servizio Automazione di Azure per gestire il bus di servizio di Azure."
	services="service-bus, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="csand"/>



# Gestione del bus di servizio di Azure mediante Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'uso per semplificare la gestione del bus di servizio di Azure.

## Informazioni su Automazione di Azure

[Automazione di Azure](http://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute frequentemente, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## In che modo Automazione di Azure può facilitare la gestione del bus di servizio di Azure

Il bus di servizio può essere gestito in Automazione di Azure mediante l'[API REST del bus di servizio](https://msdn.microsoft.com/library/azure/hh780717.aspx). In Automazione di Azure è possibile scrivere script di flussi di lavoro di PowerShell per eseguire molte delle attività del bus di servizio mediante l'API REST. È anche possibile abbinare tali chiamate API REST in Automazione di Azure ai cmdlet per altri servizi di Azure allo scopo di automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire il bus di servizio di Azure, visitare i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l'[esercitazione introduttiva](../automation-create-runbook-from-samples.md) di Automazione di Azure
* Vedere l'articolo [Gestire il bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
 

<!---HONumber=September15_HO1-->