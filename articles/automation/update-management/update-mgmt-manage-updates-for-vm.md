---
title: Gestire gli aggiornamenti e le patch per le macchine virtuali in automazione di Azure
description: Questo articolo descrive come usare Gestione aggiornamenti per gestire gli aggiornamenti e le patch per le macchine virtuali di Azure e non Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450529"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Gestire gli aggiornamenti e le patch per le macchine virtuali

Gli aggiornamenti software in automazione di Azure Gestione aggiornamenti offrono un set di strumenti e risorse che consentono di gestire la complessa attività di monitoraggio e applicazione degli aggiornamenti software ai computer in Azure e nel cloud ibrido. Un processo efficace di gestione degli aggiornamenti software è necessario per mantenere l'efficienza operativa, superare i problemi di sicurezza e ridurre i rischi di una maggiore minaccia per la sicurezza informatica. Tuttavia, a causa della natura mutevole della tecnologia e della continua comparsa di nuove minacce per la sicurezza, una gestione efficiente degli aggiornamenti software richiede un'attenzione costante e ininterrotta.

> [!NOTE]
> Gestione aggiornamenti supporta la distribuzione degli aggiornamenti di terze parti e il loro pre-download. Questo supporto richiede modifiche ai sistemi aggiornati. Per informazioni su come configurare queste impostazioni nei sistemi in uso, vedere [Configurare le impostazioni di Windows Update per Gestione aggiornamenti di Automazione di Azure](update-mgmt-configure-wuagent.md).

Prima di provare a gestire gli aggiornamenti per le macchine virtuali, assicurarsi di aver abilitato Gestione aggiornamenti su di essi usando uno dei metodi seguenti:

* [Abilitare Gestione aggiornamenti da un account di Automazione](update-mgmt-enable-automation-account.md)
* [Abilitare Gestione aggiornamenti dal portale di Azure](update-mgmt-enable-portal.md)
* [Abilitare Gestione aggiornamenti da un runbook](update-mgmt-enable-runbook.md)
* [Abilitare Gestione aggiornamenti da una macchina virtuale di Azure](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitare l'ambito della distribuzione

Gestione aggiornamenti usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer nei quali ricevere gli aggiornamenti. Per altre informazioni, vedere [Limitare l'ambito di distribuzione di Gestione aggiornamenti](update-mgmt-scope-configuration.md).

## <a name="compliance-assessment"></a>Valutazione della conformità

Prima di distribuire gli aggiornamenti software nei computer, rivedere i risultati della valutazione della conformità degli aggiornamenti per i computer abilitati. Per ogni aggiornamento software, il relativo stato di conformità viene registrato e, al termine della valutazione, viene raccolto e trasmesso in blocco ai log di monitoraggio di Azure.

In un computer Windows l'analisi della conformità viene eseguita ogni 12 ore per impostazione predefinita. Oltre all'analisi pianificata, l'analisi per la conformità degli aggiornamenti viene avviata entro 15 minuti dall'Log Analytics agente per Windows da riavviare, prima dell'installazione dell'aggiornamento e dopo l'installazione dell'aggiornamento. È inoltre importante consultare le indicazioni su come [configurare il client di Windows Update](update-mgmt-configure-wuagent.md) con gestione aggiornamenti per evitare problemi che ne impediscono la gestione corretta.

Per un computer Linux, l'analisi della conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente di Log Analytics per Linux viene riavviato, un'analisi di conformità viene avviata entro 15 minuti.

I risultati di conformità vengono presentati in Gestione aggiornamenti per ogni computer valutato. Per una nuova macchina virtuale abilitata per la gestione, possono essere necessari fino a 30 minuti prima che il dashboard visualizzi dati aggiornati.

Vedere [monitorare gli aggiornamenti software](update-mgmt-view-update-assessments.md) per informazioni su come visualizzare i risultati di conformità.

## <a name="deploy-updates"></a>Distribuire gli aggiornamenti

Una volta esaminati i risultati di conformità, la fase di distribuzione degli aggiornamenti software è il processo di distribuzione degli aggiornamenti software. Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Vedere [distribuire gli aggiornamenti software](update-mgmt-deploy-updates.md) per informazioni su come pianificare una distribuzione degli aggiornamenti.

## <a name="review-update-deployments"></a>Esaminare le distribuzioni degli aggiornamenti

Al termine della distribuzione, esaminare il processo per determinare l'esito positivo della distribuzione degli aggiornamenti per computer o gruppo di destinazione. Vedere [esaminare lo stato della distribuzione](update-mgmt-deploy-updates.md#check-deployment-status) per informazioni su come è possibile monitorare lo stato della distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare avvisi per notificare i risultati della distribuzione degli aggiornamenti, vedere [creare avvisi per gestione aggiornamenti](update-mgmt-configure-alerts.md).

* È possibile [eseguire query sui log di monitoraggio di Azure](update-mgmt-query-logs.md) per analizzare le valutazioni degli aggiornamenti, le distribuzioni e altre attività di gestione correlate. Include query predefinite che consentono di iniziare.