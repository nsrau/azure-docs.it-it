---
title: Gestire il bus di servizio di Azure mediante Automazione di Azure| Microsoft Docs
description: Informazioni su come usare il servizio Automazione di Azure per gestire il bus di servizio di Azure.
services: service-bus, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand

---
# Gestione del bus di servizio di Azure mediante Automazione di Azure
Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'uso per semplificare la gestione del bus di servizio di Azure.

## Informazioni su Automazione di Azure
[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi e la configurazione preferita per lo stato. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## In che modo Automazione di Azure può facilitare la gestione del bus di servizio di Azure
Il bus di servizio può essere gestito in Automazione di Azure mediante l'[API REST del bus di servizio](https://msdn.microsoft.com/library/azure/mt639375.aspx). In Automazione di Azure è possibile eseguire script di PowerShell per completare molte delle attività del bus di servizio tramite le API REST. È anche possibile abbinare tali chiamate API REST in Automazione di Azure ai cmdlet per altri servizi di Azure allo scopo di automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Di seguito sono riportati alcuni esempi di uso di PowerShell per gestire il bus di servizio:

* [Cmdlet di PowerShell personalizzati per gestire le code del bus di servizio](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Come creare code, argomenti e sottoscrizioni del bus di servizio tramite uno script di PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Creazione di spazi dei nomi del bus di servizio di Azure con PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

Il modulo PowerShell per l'uso con il bus di servizio di Azure nei runbook di automazione può essere scaricato da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).

## Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire il bus di servizio di Azure, visitare i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l'[esercitazione introduttiva](../automation/automation-first-runbook-graphical.md) di Automazione di Azure
* Vedere come [gestire il bus di servizio con PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)

<!---HONumber=AcomDC_0803_2016-->