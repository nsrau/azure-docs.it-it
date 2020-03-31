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
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849514"
---
# <a name="workspace-mappings"></a>Mapping dell'area di lavoro

Quando si abilitano soluzioni come Gestione aggiornamenti, Rilevamento modifiche e inventario o Avvia/Arresta macchine virtuali durante la soluzione fuori orario, sono supportate solo determinate aree per il collegamento di un'area di lavoro di Log Analytics e di un account di automazione. Questo mapping si applica solo all'account di automazione e all'area di lavoro di Log Analytics. Le risorse che segnalano all'account di automazione o all'area di lavoro di Log Analytics possono risiedere in altre aree.

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

<sup>2</sup> A causa dei vincoli di capacità, l'area non è disponibile durante la creazione di nuove risorse. Sono inclusi gli account di automazione e le aree di lavoro di Log Analytics.This includes Automation Accounts and Log Analytics workspaces. Tuttavia, le risorse collegate preesistenti nella regione dovrebbero continuare a funzionare.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Se si decide di non integrare più l'account di Automazione con un'area di lavoro di Log Analytics, è possibile scollegare l'account direttamente dal portale di Azure.If you decide you no longer wish to integrate your Automation account with a Log Analytics workspace, you can unlink your account directly from the Azure portal. Prima di procedere, è necessario rimuovere gestione aggiornamenti, rilevamento delle modifiche e inventario o le macchine virtuali di avvio/arresto durante le soluzioni non ore se vengono in uso. Se non li rimuovi, questo processo sarà impedito di procedere. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> È possibile che alcune soluzioni, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di automazione e che debbano essere rimosse prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di Automazione e nella pagina Account di automazione selezionare **Area di lavoro collegata** nella sezione **Risorse correlate** a sinistra.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)**. Riceverai un messaggio che ti chiederà di continuare.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Aggiornare le pianificazioni - Ogni elemento avrà un nome corrispondente alle distribuzioni di aggiornamenti create.

* Gruppi di lavoro ibridi creati per la `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`soluzione: a ognuno verrà assegnato un nome simile a ).

Se è stata usata la soluzione per avviare/arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM
* Avviare e arrestare i runbook delle VM
* Variabili

In alternativa, è anche possibile scollegare l'area di lavoro dall'account di automazione dall'area di lavoro di Log Analytics. Nell'area di lavoro selezionare **Account di automazione** in **Risorse correlate**. Nella pagina Account di automazione selezionare **Scollega account**.

## <a name="next-steps"></a>Passaggi successivi

Scopri come eseguire l'onboarding delle seguenti soluzioni:

Gestione degli aggiornamenti, rilevamento delle modifiche e inventario:

* Da una [macchina virtuale](../automation-onboard-solutions-from-vm.md)
* Dal tuo [account Automation](../automation-onboard-solutions-from-automation-account.md)
* Durante [la navigazione di più macchine](../automation-onboard-solutions-from-browse.md)
* Da un [runbook](../automation-onboard-solutions.md)

Avviare/arrestare VM durante gli orari di minore attività

* [Distribuire le macchine virtuali di avvio/arresto durante le ore non lavorativeDeploy Start/Stop VMs during off-hours](../automation-solution-vm-management.md)
