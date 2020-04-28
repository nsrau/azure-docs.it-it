---
title: Mapping dell'area di lavoro di automazione di Azure e Log Analytics
description: Questo articolo descrive i mapping consentiti tra un account di automazione e un'area di lavoro Log Analytics per supportare la soluzione
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 607cebca3e6e8ddd95900ecdbd7041e5f7bb50cc
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165774"
---
# <a name="workspace-mappings"></a>Mapping dell'area di lavoro

Quando si abilitano Gestione aggiornamenti, Rilevamento modifiche e inventario oppure Avvio/Arresto di macchine virtuali durante gli orari di minore attività, sono supportate solo determinate aree per il collegamento di un'area di lavoro di Log Analytics e di un account di automazione nella sottoscrizione. Questo mapping si applica solo all'account di automazione e all'area di lavoro Log Analytics. L'area di lavoro Log Analytics e l'account di automazione devono trovarsi nella stessa sottoscrizione, ma possono trovarsi in diversi gruppi di risorse distribuiti nella stessa area.

Per ulteriori informazioni, vedere [log Analytics area di lavoro e l'account di automazione](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mapping supportati

La tabella seguente mostra i mapping supportati:

|**Area dell'area di lavoro Log Analytics**|**Area di Automazione di Azure**|
|---|---|
|**NOI**||
|Eastus<sup>1</sup>|EastUS2|
|Stati Uniti occidentali 2|Stati Uniti occidentali 2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**Asia Pacifico**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|Europa occidentale|Europa occidentale|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> il mapping di eastus per le aree di lavoro log Analytics agli account di automazione non è un'area esatta per il mapping dell'area, ma è il mapping corretto.

<sup>2</sup> a causa dei vincoli di capacità, l'area non è disponibile quando si creano nuove risorse. Sono inclusi gli account di automazione e le aree di lavoro Log Analytics. Tuttavia, le risorse collegate preesistenti nell'area dovrebbero continuare a funzionare.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Se si decide che non si vuole più integrare l'account di automazione con un'area di lavoro di Log Analytics, è possibile scollegare l'account direttamente dall'portale di Azure. Prima di procedere, è necessario prima rimuovere Gestione aggiornamenti, Rilevamento modifiche e inventario e Avvio/Arresto di macchine virtuali durante gli orari di minore attività se li si utilizza. Se non vengono rimossi, non è possibile completare l'operazione di scollegamento. Esaminare l'articolo per ogni che si sta abilitando per comprendere i passaggi necessari per rimuoverlo.

Dopo averli rimossi, è possibile eseguire la procedura seguente per scollegare l'account di automazione.

> [!NOTE]
> Alcune soluzioni, incluse le versioni precedenti della soluzione di monitoraggio di Azure SQL, potrebbero avere creato asset di automazione e potrebbe essere necessario rimuoverle prima di scollegare l'area di lavoro.

1. Dal portale di Azure aprire l'account di automazione. Nella pagina account di automazione selezionare **area di lavoro collegata** in **risorse correlate**.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)**. Viene visualizzato un messaggio di richiesta per verificare se si vuole continuare.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

4. Se è stato usato Gestione aggiornamenti, facoltativamente è possibile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione.

    * Pianificazioni degli aggiornamenti: ogni ha un nome che corrisponde a una distribuzione di aggiornamenti creata.
    * Gruppi di lavoro ibridi creati per la soluzione, ognuno con un nome simile `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`a.

5. Se è stato usato Avvio/Arresto di macchine virtuali durante gli orari di minore attività, è possibile rimuovere facoltativamente gli elementi seguenti che non sono necessari dopo la rimozione.

    * Avviare e arrestare le pianificazioni di runbook delle VM
    * Avviare e arrestare i runbook delle VM
    * variables

In alternativa, è possibile scollegare l'area di lavoro dall'account di automazione nell'area di lavoro.

1. Nell'area di lavoro selezionare **account di automazione** in **risorse correlate**. 
2. Nella pagina account di automazione selezionare **Scollega account**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire l'onboarding di Gestione aggiornamenti e Rilevamento modifiche e inventario:

    * Da una [macchina virtuale](../automation-onboard-solutions-from-vm.md)
    * Dall' [account di automazione](../automation-onboard-solutions-from-automation-account.md)
    * Quando si [esplorano più computer](../automation-onboard-solutions-from-browse.md)
    * Da un [Runbook](../automation-onboard-solutions.md)

* Informazioni su come eseguire l'onboarding del Avvio/Arresto di macchine virtuali durante gli orari di minore attività:

    * [Panoramica di Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md)
