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
	ms.date="03/16/2015"
	ms.author="csand"/>



#Gestione di Gestione API di Azure usando Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'uso per semplificare la gestione di Gestione API di Azure.

## Informazioni su Automazione di Azure

[Automazione di Azure](http://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute frequentemente, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.


## In che modo Gestione API di Azure può essere gestito con Automazione di Azure?

Gestione API può essere gestito in Automazione di Azure tramite l'[API REST Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx). All'interno di Automazione di Azure è possibile scrivere gli script dei flussi di lavoro di PowerShell per eseguire molte delle attività di Gestione API mediante l'API REST. È anche possibile abbinare tali chiamate API REST in Automazione di Azure ai cmdlet per altri servizi di Azure allo scopo di automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.


## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e del modo in cui può essere usato per gestire Gestione API di Azure, usare i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l'[esercitazione introduttiva](automation-create-runbook-from-samples.md) di Automazione di Azure.
* Leggere il post di blog della community relativo al [modulo PowerShell per l'API REST di Gestione API di \#Azure](https://alexandrebrisebois.wordpress.com/2014/08/17/powershell-module-for-the-azure-api-management-rest-apis/).

<!--HONumber=54-->