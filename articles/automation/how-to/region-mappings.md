---
title: Aree supportate per l'area di lavoro Log Analytics collegata
description: Questo articolo descrive i mapping di area supportati tra un account di Automazione e un'area di lavoro Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/12/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: a8b5f14147ee748f5d32d794f51d5437eeddfb51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261189"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Aree supportate per l'area di lavoro Log Analytics collegata

In Automazione di Azure è possibile abilitare le funzionalità Gestione aggiornamenti, Rilevamento modifiche e inventario e Avvio/Arresto di macchine virtuali durante gli orari di minore attività per le macchine virtuali. Sono tuttavia supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione nella sottoscrizione. I mapping di area si applicano solo all'account di Automazione e all'area di lavoro Log Analytics. L'area di lavoro Log Analytics e l'account di Automazione devono trovarsi nella stessa sottoscrizione, ma possono trovarsi in gruppi di risorse diversi distribuiti nella stessa area. Per altre informazioni, vedere [Area di lavoro Log Analytics e account di Automazione](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mapping supportati

La tabella seguente mostra i mapping supportati:

|**Area dell'area di lavoro Log Analytics**|**Area di Automazione di Azure**|
|---|---|
|**Stati Uniti**||
|EastUS<sup>1</sup>|EastUS2|
|Stati Uniti occidentali 2|Stati Uniti occidentali 2|
|WestCentralUS|WestCentralUS|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**Asia Pacifico**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>2</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|Europa occidentale|Europa occidentale|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>2</sup>|USGovArizona|

<sup>1</sup> Il mapping di EastUS per le aree di lavoro Log Analytics agli account di Automazione non è un mapping esatto da area a area, ma è il mapping corretto.

<sup>2</sup> in quest'area è supportata solo gestione aggiornamenti e altre funzionalità come rilevamento modifiche e inventario non sono disponibili in questo momento.

## <a name="unlink-a-workspace"></a>Scollegare un'area di lavoro

Se si decide che non si vuole più integrare l'account di Automazione con un'area di lavoro Log Analytics, è possibile scollegare l'account direttamente dal portale di Azure. Prima di procedere, è necessario [rimuovere](move-account.md#remove-features) le funzionalità Gestione aggiornamenti, Rilevamento modifiche e inventario e Avvio/Arresto di macchine virtuali durante gli orari di minore attività se sono in uso. Se non vengono rimosse, non è possibile completare l'operazione di scollegamento. 

Dopo aver rimosso le funzionalità è possibile seguire questa procedura per scollegare l'account di Automazione.

> [!NOTE]
> È possibile che alcune funzionalità, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di Automazione che devono essere rimossi prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di Automazione. Nella pagina dell'account di Automazione selezionare **Area di lavoro collegata** in **Risorse correlate**.

2. Nella pagina Scollega area di lavoro fare clic su **Scollega area di lavoro**. Viene visualizzato un messaggio di richiesta per verificare se si vuole continuare.

3. Mentre Automazione di Azure scollega l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

4. Se è stato usato Gestione aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari:

    * Pianificazioni degli aggiornamenti: ognuna ha un nome corrispondente alla distribuzione dell'aggiornamento creata.
    * Gruppi di ruoli di lavoro ibridi creati per la funzionalità: ognuno ha un nome simile a `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`.

5. Se è stata usata la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività, facoltativamente è possibile rimuovere gli elementi seguenti che non sono più necessari:

    * Avviare e arrestare le pianificazioni di runbook delle VM
    * Avviare e arrestare i runbook delle VM
    * variables

In alternativa, è possibile scollegare l'area di lavoro dall'account di Automazione nell'area di lavoro.

1. Nell'area di lavoro selezionare **Account di Automazione** in **Risorse correlate**. 
2. Nella pagina Account di Automazione selezionare **Scollega account**.

## <a name="next-steps"></a>Passaggi successivi

* Vedere informazioni aggiuntive su Gestione aggiornamenti in [Panoramica di Gestione aggiornamenti](../automation-update-management.md).
* Scoprire di più su Rilevamento modifiche e inventario in [Panoramica di Rilevamento modifiche e inventario](../change-tracking.md).
* Vedere altre informazioni su Avvio/Arresto di macchine virtuali durante gli orari di minore attività in [Panoramica di Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md).
