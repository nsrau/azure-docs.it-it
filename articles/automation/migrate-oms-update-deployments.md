---
title: Eseguire la migrazione delle distribuzioni degli aggiornamenti di OMS in Azure
description: Questo articolo descrive come eseguire la migrazione delle distribuzioni di aggiornamenti di OMS esistenti in Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679995"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Eseguire la migrazione delle distribuzioni degli aggiornamenti di OMS in Azure

Il portale di Operations Management Suite (OMS) sarà presto [deprecato](../azure-monitor/platform/oms-portal-transition.md). Tutte le funzionalità disponibili nel portale di OMS per Gestione aggiornamenti sono disponibili nella portale di Azure, tramite i log di monitoraggio di Azure. Questo articolo fornisce le informazioni necessarie per eseguire la migrazione al portale di Azure.

## <a name="key-information"></a>Informazioni chiave

* Le distribuzioni esistenti continueranno a funzionare. Dopo avere creato nuovamente la distribuzione in Azure, è possibile eliminare la distribuzione precedente da OMS.
* Tutte le funzionalità presenti in OMS sono disponibili in Azure. Per altre informazioni su Gestione aggiornamenti, vedere [Panoramica sulla soluzione Gestione aggiornamenti](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Accedere al portale di Azure

Dall'area di lavoro OMS fare clic su **Apri in Azure**. Questa selezione consente di passare all'area di lavoro Log Analytics usata da OMS.

![Apri in Azure -Portale di OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Nel portale di Azure fare clic su **Account di automazione**.

![Log di Monitoraggio di Azure](media/migrate-oms-update-deployments/log-analytics.png)

Nell'account di automazione fare clic su **Gestione aggiornamenti**.

![Gestione degli aggiornamenti](media/migrate-oms-update-deployments/azure-automation.png)

Nella portale di Azure selezionare account di **automazione** in **tutti i servizi**. 

In **strumenti di gestione**selezionare l'account di automazione appropriato e fare clic su **Gestione aggiornamenti**.

## <a name="recreate-existing-deployments"></a>Creare nuovamente le distribuzioni esistenti

Tutte le distribuzioni di aggiornamenti create nel portale di OMS hanno una [ricerca salvata](../azure-monitor/platform/computer-groups.md), definita anche gruppo di computer, con lo stesso nome della distribuzione di aggiornamenti esistente. La ricerca salvata contiene l'elenco dei computer per cui è stata pianificata la distribuzione di aggiornamenti.

![Gestione degli aggiornamenti](media/migrate-oms-update-deployments/oms-deployment.png)

Per usare la ricerca salvata esistente, seguire questa procedura:

Per creare una nuova distribuzione di aggiornamenti, passare al portale di Azure, selezionare l'account di automazione usato e fare clic su **Gestione aggiornamenti**. Fare clic su **Pianifica la distribuzione di aggiornamenti**.

![Pianificare la distribuzione di aggiornamenti](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Si apre il riquadro Nuova distribuzione di aggiornamenti. Specificare i valori per le proprietà descritte nella tabella seguente e quindi fare clic su **Crea**:

Per **aggiornare i computer**, selezionare la ricerca salvata usata dalla distribuzione OMS esistente.

| Proprietà | Descrizione |
| --- | --- |
|Nome |Nome univoco che identifica la distribuzione degli aggiornamenti. |
|Sistema operativo| Selezionare **Linux** o **Windows**.|
|Computer da aggiornare |Selezionare una ricerca salvata o un gruppo importato, oppure scegliere Computer dall'elenco a discesa e selezionare i singoli computer. Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **AGGIORNA IDONEITÀ AGENTE**.</br> Per informazioni sui diversi metodi di creazione di gruppi di computer nei log di monitoraggio di Azure, vedere [gruppi di computer nei log di monitoraggio di Azure](../azure-monitor/platform/computer-groups.md) |
|Classificazioni degli aggiornamenti|Selezionare tutte le classificazioni degli aggiornamenti necessarie. CentOS non supporta questa operazione per impostazione predefinita.|
|Aggiornamenti da escludere|Immettere gli aggiornamenti da escludere. Per Windows, immettere l'articolo della Knowledge Base senza il prefisso **KB**. Per Linux, immettere il nome del pacchetto o usare un carattere jolly.  |
|Impostazioni di pianificazione|Selezionare l'ora di inizio e quindi selezionare **Una sola volta** o **Ricorrente** per la ricorrenza. | 
| Finestra di manutenzione |Numero di minuti impostato per gli aggiornamenti. Il valore non può essere inferiore a 30 minuti o superiore a 6 ore. |
| Controllo riavvio| Determina come vengono gestiti i riavvii.</br>Le opzioni disponibili sono:</br>Riavvia se necessario (opzione predefinita)</br>Riavvia sempre</br>Non riavviare mai</br>Riavvia solamente: gli aggiornamenti non verranno installati|

Fare clic su **Distribuzioni di aggiornamenti pianificate** per visualizzare lo stato della nuova distribuzione di aggiornamenti creata.

![Nuova distribuzione di aggiornamenti](media/migrate-oms-update-deployments/new-update-deployment.png)

Come accennato in precedenza, dopo la configurazione delle nuove distribuzioni tramite il portale di Azure, le distribuzioni esistenti possono essere rimosse dal portale di OMS.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle Gestione aggiornamenti in Azure, vedere [Gestione aggiornamenti](automation-update-management.md).
