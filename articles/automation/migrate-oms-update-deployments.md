---
title: Eseguire la migrazione delle distribuzioni di aggiornamenti di OMS in Azure
description: Questo articolo descrive come eseguire la migrazione delle distribuzioni di aggiornamenti di OMS esistenti in Azure
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4d11dfcb66a545cbecc80b6bdad558ca6d328ed2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999687"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Eseguire la migrazione delle distribuzioni di aggiornamenti di OMS in Azure

Il portale di Operations Management Suite (OMS) sarà presto [deprecato](../azure-monitor/platform/oms-portal-transition.md). Tutte le funzionalità precedentemente disponibili nel portale di OMS per Gestione aggiornamenti sono ora disponibili nel portale di Azure. Questo articolo contiene le informazioni necessarie per la migrazione al portale di Azure.

## <a name="key-information"></a>Informazioni chiave

* Le distribuzioni esistenti continueranno a funzionare. Dopo avere creato nuovamente la distribuzione in Azure, è possibile eliminare la distribuzione precedente da OMS.
* Tutte le funzionalità presenti in OMS sono disponibili in Azure. Per altre informazioni su Gestione aggiornamenti, vedere [Panoramica sulla soluzione Gestione aggiornamenti](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Accedere al portale di Azure

Dall'area di lavoro OMS fare clic su **Apri in Azure**. Si passerà così all'area di lavoro Log Analytics usata in precedenza da OMS.

![Apri in Azure -Portale di OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Nel portale di Azure fare clic su **Account di automazione**.

![Log di Monitoraggio di Azure](media/migrate-oms-update-deployments/log-analytics.png)

In Account di automazione fare clic su **Gestione aggiornamenti** per aprire Gestione aggiornamenti.

![Gestione degli aggiornamenti](media/migrate-oms-update-deployments/azure-automation.png)

In futuro sarà possibile passare direttamente al portale di Azure seguendo questa procedura: in **Tutti i servizi** selezionare **Account di automazione** nella sezione **Strumenti di gestione**, selezionare l'account di automazione appropriato e quindi fare clic su **Gestione aggiornamenti**.

## <a name="recreate-existing-deployments"></a>Creare nuovamente le distribuzioni esistenti

Tutte le distribuzioni di aggiornamenti create nel portale di OMS hanno una [ricerca salvata](../azure-monitor/platform/computer-groups.md), definita anche gruppo di computer, con lo stesso nome della distribuzione di aggiornamenti esistente. La ricerca salvata contiene l'elenco dei computer per cui è stata pianificata la distribuzione di aggiornamenti.

![Gestione degli aggiornamenti](media/migrate-oms-update-deployments/oms-deployment.png)

Per usare la ricerca salvata esistente, seguire questa procedura:

Per creare una nuova distribuzione di aggiornamenti, passare al portale di Azure, selezionare l'account di automazione usato e fare clic su **Gestione aggiornamenti**. Fare clic su **Pianifica la distribuzione di aggiornamenti**.

![Pianificare la distribuzione di aggiornamenti](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Si apre il riquadro **Nuova distribuzione di aggiornamenti**. Specificare i valori per le proprietà descritte nella tabella seguente e quindi fare clic su **Crea**:

Per i computer da aggiornare, selezionare la ricerca salvata usata per la distribuzione di OMS esistente.

| Proprietà | DESCRIZIONE |
| --- | --- |
|NOME |Nome univoco che identifica la distribuzione degli aggiornamenti. |
|Sistema operativo| Selezionare **Linux** o **Windows**.|
|Computer da aggiornare |Selezionare una ricerca salvata o un gruppo importato, oppure scegliere Computer dall'elenco a discesa e selezionare i singoli computer. Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **AGGIORNA IDONEITÀ AGENTE**.</br> Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../azure-monitor/platform/computer-groups.md) |
|Classificazioni degli aggiornamenti|Selezionare tutte le classificazioni degli aggiornamenti necessarie. CentOS non supporta questa operazione per impostazione predefinita.|
|Aggiornamenti da escludere|Immettere gli aggiornamenti da escludere. Per Windows, immettere l'articolo della Knowledge Base senza il prefisso **KB**. Per Linux, immettere il nome del pacchetto o usare un carattere jolly.  |
|Impostazioni di pianificazione|Selezionare l'ora di inizio e quindi selezionare **Una sola volta** o **Ricorrente** per la ricorrenza. | 
| Finestra di manutenzione |Numero di minuti impostato per gli aggiornamenti. Il valore non può essere inferiore a 30 minuti o superiore a 6 ore. |
| Controllo riavvio| Determina come vengono gestiti i riavvii.</br>Le opzioni disponibili sono:</br>Riavvia se necessario (opzione predefinita)</br>Riavvia sempre</br>Non riavviare mai</br>Riavvia solamente: gli aggiornamenti non verranno installati|

Fare clic su **Distribuzioni di aggiornamenti pianificate** per visualizzare lo stato della nuova distribuzione di aggiornamenti creata.

![Nuova distribuzione di aggiornamenti](media/migrate-oms-update-deployments/new-update-deployment.png)

Come accennato in precedenza, dopo la configurazione delle nuove distribuzioni tramite il portale di Azure, le distribuzioni esistenti possono essere rimosse dal portale di OMS.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Gestione aggiornamenti in Azure, vedere [Soluzione Gestione aggiornamenti](automation-update-management.md).
