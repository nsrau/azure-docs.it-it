---
title: Gestire Azure RemoteApp usando Automazione di Azure | Documentazione Microsoft
description: "Informazioni su come è possibile usare il servizio Automazione di Azure per gestire Azure RemoteApp."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 589f01ef-f9c1-4e7b-a040-1b46862d3544
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e21af65ecb6f0ce70048846a9786271dd4cc985


---
# <a name="managing-azure-remoteapp-using-azure-automation"></a>Gestione di Azure RemoteApp usando Automazione di Azure
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'utilizzo per semplificare la gestione di Azure RemoteApp.

## <a name="what-is-azure-automation"></a>Informazioni su Automazione di Azure
[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute frequentemente, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## <a name="how-can-azure-automation-help-manage-azure-remoteapp"></a>Come può Automazione di Azure aiutare nella gestione di Azure RemoteApp?
RemoteApp può essere gestito in Automazione di Azure usando i cmdlet di PowerShell disponibili negli [strumenti di Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). I cmdlet di PowerShell per RemoteApp sono predefiniti in Automazione di Azure per consentire l'esecuzione di tutte le attività di gestione di RemoteApp dall'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire Azure RemoteApp, seguire i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l' [esercitazione introduttiva](../automation/automation-first-runbook-graphical.md)




<!--HONumber=Nov16_HO3-->


