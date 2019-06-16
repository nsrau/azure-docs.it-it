---
title: Mapping di area di lavoro di Log Analitica e automazione di Azure
description: Questo articolo vengono descritti i mapping tra un Account di automazione e un'area di lavoro di Log Analitica consentiti per supportare soluzioni
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8afe8fed8912dd365071f1c4c5560c9f5578dcd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133108"
---
# <a name="workspace-mappings"></a>Mapping area di lavoro

Quando si abilitano soluzioni quali gestione aggiornamenti, rilevamento modifiche e inventario o avviare/arrestare VM durante la soluzione di orari di minore attività, sono supportati solo determinate aree geografiche per il collegamento a un'area di lavoro di Log Analitica e un Account di automazione. Questo mapping si applica solo all'Account di automazione e l'area di lavoro di Log Analitica. Le risorse di creazione di report all'area di lavoro di Log Analitica o Account di automazione possono trovarsi in altre aree.

## <a name="supported-mappings"></a>Mapping supportati

La tabella seguente mostra i mapping supportati:

|**Area dell'area di lavoro di Log Analytics**|**Area di Automazione di Azure**|
|---|---|
|**Stati Uniti**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
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

<sup>1</sup> EastUS mapping per le aree di lavoro di Log Analitica per gli account di automazione non è un mapping di un'area a un'altra esatto ma il mapping corretto.

<sup>2</sup> a causa di limiti di capacità dell'area non è disponibile durante la creazione di nuove risorse. Sono inclusi gli account di automazione e Log Analitica aree di lavoro. Tuttavia, le risorse collegate preesistente nell'area continueranno a funzionare.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Se si decide di che non si desidera più integrare l'account di automazione con un'area di lavoro di Log Analitica, è possibile scollegare l'account direttamente dal portale di Azure. Prima di procedere, è necessario innanzitutto rimuovere la gestione degli aggiornamenti, rilevamento modifiche e inventario o avviare/arrestare VM durante le soluzioni di orari di minore attività, se li si stia usando. Se non vengono rimossi, questo processo sarà possibile procedere. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> È possibile che alcune soluzioni, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di automazione e che debbano essere rimosse prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di Automazione e nella pagina Account di automazione selezionare **Area di lavoro collegata** nella sezione **Risorse correlate** a sinistra.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)** . Si riceverà una richiesta di verifica per determinare se che si desidera continuare.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Aggiornare le pianificazioni - Ogni elemento avrà un nome corrispondente alle distribuzioni di aggiornamenti create.

* Gruppi di ruoli di lavoro ibridi creati per la soluzione - Ogni elemento verrà denominato in modo analogo a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Se è stata usata la soluzione per avviare/arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM
* Avviare e arrestare i runbook delle VM
* Variabili

In alternativa, è possibile scollegare anche l'area di lavoro dall'Account di automazione dall'area di lavoro di Log Analitica. Sull'area di lavoro, selezionare **Account di automazione** sotto **le risorse correlate**. Nella pagina Account di automazione, selezionare **scollegare l'account**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come caricare le soluzioni seguenti:

Aggiornare gestione e rilevamento delle modifiche e inventario:

* Da un [macchina virtuale](../automation-onboard-solutions-from-vm.md)
* Da di [account di automazione](../automation-onboard-solutions-from-automation-account.md)
* Quando [esplorazione più computer](../automation-onboard-solutions-from-browse.md)
* Da un [runbook](../automation-onboard-solutions.md)

Avviare/arrestare VM durante gli orari di minore attività

* [Distribuire Avvia/Arresta macchine virtuali durante orari di minore attività](../automation-solution-vm-management.md)