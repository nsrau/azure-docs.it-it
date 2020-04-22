---
title: Mapping dell'area di lavoro di Automazione di Azure e Log AnalyticsAzure Automation and Log Analytics workspace mappings
description: In questo articolo vengono descritti i mapping consentiti tra un account di automazione e un'area di lavoro di Log Analytics per supportare la soluzione
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681843"
---
# <a name="workspace-mappings"></a>Mapping dell'area di lavoro

Quando si abilitano soluzioni come Gestione aggiornamenti, Rilevamento modifiche e inventario o Avvia/Arresta macchine virtuali durante le ore non lavorative, solo alcune aree sono supportate per il collegamento di un'area di lavoro di Log Analytics e di un account di automazione. Questo mapping si applica solo all'account di automazione e all'area di lavoro di Log Analytics. Le risorse che segnalano all'account di automazione o all'area di lavoro di Log Analytics possono risiedere in altre aree.

## <a name="supported-mappings"></a>Mapping supportati

La tabella seguente mostra i mapping supportati:

|**Area dell'area di lavoro Log Analytics**|**Area di Automazione di Azure**|
|---|---|
|**Noi**||
|EastUS<sup>1</sup>|EastUS2|
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

<sup>1</sup> il mapping EastUS per le aree di lavoro di Log Analytics agli account di automazione non è un mapping esatto tra aree, ma è il mapping corretto.

<sup>2</sup> A causa dei vincoli di capacità, l'area non è disponibile durante la creazione di nuove risorse. Sono inclusi gli account di automazione e le aree di lavoro di Log Analytics.This includes Log accounts and Log Analytics workspaces. Tuttavia, le risorse collegate preesistenti nella regione dovrebbero continuare a funzionare.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Se si decide di non integrare più l'account di automazione con un'area di lavoro di Log Analytics, è possibile scollegare l'account direttamente dal portale di Azure.If you decide that you no longer to integrate your Automation account with a Log Analytics workspace, you can unlink your account directly from the Azure portal. Prima di procedere, è necessario rimuovere gestione aggiornamenti, rilevamento delle modifiche e inventario e avviare/arrestare le macchine virtuali durante le soluzioni non ore se vengono create. Se non vengono rimossi, non è possibile completare l'operazione di scollegamento. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> Alcune soluzioni, incluse le versioni precedenti della soluzione di monitoraggio SQL di Azure, potrebbero aver creato asset di automazione e potrebbe essere necessario rimuovere gli asset prima di scollegare l'area di lavoro.

1. Dal portale di Azure aprire l'account di automazione. Nella pagina Account di automazione selezionare **Area di lavoro collegata** in **Risorse correlate**.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)**. Viene visualizzato un messaggio di verifica se si desidera continuare.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

4. Se è stata utilizzata la soluzione di gestione degli aggiornamenti, è possibile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

    * Pianificazioni di aggiornamento: ognuna ha un nome che corrisponde a una distribuzione di aggiornamento creata.
    * Gruppi di lavoro ibridi creati per `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`la soluzione: ognuno ha un nome simile a .

5. Se è stata usata la soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative, è possibile rimuovere facoltativamente gli elementi seguenti che non sono necessari dopo la rimozione della soluzione.

    * Avviare e arrestare le pianificazioni di runbook delle VM
    * Avviare e arrestare i runbook delle VM
    * variables

In alternativa, è possibile scollegare l'area di lavoro dall'account Di automazione all'interno dell'area di lavoro. 

1. Nell'area di lavoro selezionare **Account di automazione** in **Risorse correlate**. 
2. Nella pagina Account di automazione selezionare **Scollega account**.

## <a name="next-steps"></a>Passaggi successivi

* Scopri come eseguire l'onboarding delle soluzioni di gestione degli aggiornamenti e rilevamento delle modifiche e dell'inventario:

    * Da una [macchina virtuale](../automation-onboard-solutions-from-vm.md)
    * Dal tuo [account Automation](../automation-onboard-solutions-from-automation-account.md)
    * Durante [la navigazione di più macchine](../automation-onboard-solutions-from-browse.md)
    * Da un [runbook](../automation-onboard-solutions.md)

* Informazioni su come eseguire l'onboarding della soluzione Start/Stop VMs durante l'orario di ufficio:Learn how to onboard the Start/Stop VMs during off hours solution:

    * [Distribuire le macchine virtuali di avvio/arresto durante le ore lavorativeDeploy Start/Stop VMs during off hours](../automation-solution-vm-management.md)
