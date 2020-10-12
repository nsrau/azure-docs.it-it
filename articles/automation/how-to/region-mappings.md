---
title: Aree supportate per l'area di lavoro Log Analytics collegata
description: Questo articolo descrive i mapping dell'area supportati tra un account di automazione e un'area di lavoro di Log Analytics in relazione a determinate funzionalità di automazione di Azure.
ms.date: 09/03/2020
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: dd831789d5f09ca6a20cce13659d6c479845f74e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440661"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Aree supportate per l'area di lavoro Log Analytics collegata

In automazione di Azure è possibile abilitare le funzionalità di Gestione aggiornamenti, Rilevamento modifiche e inventario e Avvio/Arresto di macchine virtuali durante gli orari di minore attività per i server e le macchine virtuali. Queste funzionalità hanno una dipendenza da un'area di lavoro Log Analytics e pertanto richiedono il collegamento dell'area di lavoro con un account di automazione. Tuttavia, solo determinate aree sono supportate per collegarle insieme. In generale, il mapping *non* è applicabile se si prevede di collegare un account di automazione a un'area di lavoro in cui non sono abilitate queste funzionalità.

Questo articolo fornisce i mapping supportati per abilitare e usare correttamente queste funzionalità nell'account di automazione.

Per altre informazioni, vedere [log Analytics area di lavoro e l'account di automazione](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mapping supportati

> [!NOTE]
> Come illustrato nella tabella seguente, può esistere un solo mapping tra Log Analytics e automazione di Azure.

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

* Vedere informazioni aggiuntive su Gestione aggiornamenti in [Panoramica di Gestione aggiornamenti](../update-management/update-mgmt-overview.md).
* Scoprire di più su Rilevamento modifiche e inventario in [Panoramica di Rilevamento modifiche e inventario](../change-tracking.md).
* Vedere altre informazioni su Avvio/Arresto di macchine virtuali durante gli orari di minore attività in [Panoramica di Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md).
