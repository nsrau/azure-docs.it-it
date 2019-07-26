---
title: Mapping dell'area di lavoro di automazione di Azure e Log Analytics
description: Questo articolo descrive i mapping consentiti tra un account di automazione e un'area di lavoro Log Analytics per supportare la soluzione
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd4500b4ef6492f0b6499bb1e9aa1a773313e860
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498376"
---
# <a name="workspace-mappings"></a>Mapping dell'area di lavoro

Quando si abilitano soluzioni come Gestione aggiornamenti, Rilevamento modifiche e inventario o la soluzione di avvio/arresto di macchine virtuali durante gli orari di indisponibilità, sono supportate solo alcune aree per collegare un'area di lavoro Log Analytics e un account di automazione. Questo mapping si applica solo all'account di automazione e all'area di lavoro Log Analytics. Le risorse che inviano report all'account di automazione o all'area di lavoro Log Analytics possono risiedere in altre aree.

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

<sup>1</sup> il mapping di eastus per le aree di lavoro log Analytics agli account di automazione non è un'area esatta per il mapping dell'area, ma è il mapping corretto.

<sup>2</sup> a causa dei vincoli di capacità, l'area non è disponibile quando si creano nuove risorse. Sono inclusi gli account di automazione e le aree di lavoro Log Analytics. Tuttavia, le risorse collegate preesistenti nell'area dovrebbero continuare a funzionare.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Se si decide di non voler più integrare l'account di automazione con un'area di lavoro di Log Analytics, è possibile scollegare l'account direttamente dall'portale di Azure. Prima di procedere, è necessario prima di tutto rimuovere le Gestione aggiornamenti, Rilevamento modifiche e inventario oppure le VM di avvio/arresto durante gli orari di indisponibilità se vengono usate. Se non vengono rimossi, non sarà possibile continuare questo processo. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> È possibile che alcune soluzioni, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di automazione e che debbano essere rimosse prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di Automazione e nella pagina Account di automazione selezionare **Area di lavoro collegata** nella sezione **Risorse correlate** a sinistra.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)** . Verrà visualizzato un messaggio di richiesta per verificare se si vuole continuare.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Aggiornare le pianificazioni - Ogni elemento avrà un nome corrispondente alle distribuzioni di aggiornamenti create.

* Gruppi di lavoro ibridi creati per la soluzione. ognuno verrà denominato in modo `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`analogo a.

Se è stata usata la soluzione per avviare/arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM
* Avviare e arrestare i runbook delle VM
* Variabili

In alternativa, è anche possibile scollegare l'area di lavoro dall'account di automazione dall'area di lavoro Log Analytics. Nell'area di lavoro selezionare **account di automazione** in **risorse correlate**. Nella pagina account di automazione selezionare **Scollega account**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire l'onboarding delle soluzioni seguenti:

Gestione aggiornamenti e Rilevamento modifiche e inventario:

* Da una [macchina virtuale](../automation-onboard-solutions-from-vm.md)
* Dall' [account di automazione](../automation-onboard-solutions-from-automation-account.md)
* Quando si [esplorano più computer](../automation-onboard-solutions-from-browse.md)
* Da un [Runbook](../automation-onboard-solutions.md)

Avviare/arrestare VM durante gli orari di minore attività

* [Distribuire le VM di avvio/arresto durante gli orari di indisponibilità](../automation-solution-vm-management.md)
