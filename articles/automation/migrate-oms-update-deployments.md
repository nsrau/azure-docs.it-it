---
title: Eseguire la migrazione delle distribuzioni di aggiornamenti di OMS in Azure
description: Questo articolo descrive come eseguire la migrazione delle distribuzioni di aggiornamenti di OMS esistenti in Azure
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9469a5827765a9b82469ac1eedc66666231d82d6
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117001"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Eseguire la migrazione delle distribuzioni di aggiornamenti di OMS in Azure

Il portale di Operations Management Suite (OMS) sarà presto [deprecato](../log-analytics/log-analytics-oms-portal-transition.md). Tutte le funzionalità precedentemente disponibili nel portale di OMS per Gestione aggiornamenti sono ora disponibili nel portale di Azure. Questo articolo contiene le informazioni necessarie per la migrazione al portale di Azure.

## <a name="key-information"></a>Informazioni chiave

* Le distribuzioni esistenti continueranno a funzionare. Dopo avere creato nuovamente la distribuzione in Azure, è possibile eliminare la distribuzione precedente da OMS.
* Tutte le funzionalità presenti in OMS sono disponibili in Azure. Per altre informazioni su Gestione aggiornamenti, vedere [Panoramica sulla soluzione Gestione aggiornamenti](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Accedere al portale di Azure

Dall'area di lavoro OMS fare clic su **Apri in Azure**. Si passerà così all'area di lavoro di Log Analytics usata in precedenza da OMS.

![Apri in Azure -Portale di OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Nel portale di Azure fare clic su **Account di automazione**.

![Log Analytics](media/migrate-oms-update-deployments/log-analytics.png)

In Account di automazione fare clic su **Gestione aggiornamenti** per aprire Gestione aggiornamenti.

![Gestione degli aggiornamenti](media/migrate-oms-update-deployments/azure-automation.png)

In futuro sarà possibile passare direttamente al portale di Azure seguendo questa procedura: in **Tutti i servizi** selezionare **Account di automazione** nella sezione **Strumenti di gestione**, selezionare l'account di automazione appropriato e quindi fare clic su **Gestione aggiornamenti**.

## <a name="recreate-existing-deployments"></a>Creare nuovamente le distribuzioni esistenti

Tutte le distribuzioni di aggiornamenti create nel portale di OMS hanno una [ricerca salvata](../log-analytics/log-analytics-computer-groups.md), definita anche gruppo di computer, con lo stesso nome della distribuzione di aggiornamenti esistente. La ricerca salvata contiene l'elenco dei computer per cui è stata pianificata la distribuzione di aggiornamenti.

![Gestione aggiornamenti](media/migrate-oms-update-deployments/oms-deployment.png)

Per usare la ricerca salvata esistente, seguire questa procedura:

Per creare una nuova distribuzione di aggiornamenti, passare al portale di Azure, selezionare l'account di automazione usato e fare clic su **Gestione aggiornamenti**. Fare clic su **Pianifica la distribuzione di aggiornamenti**.

![Pianificare la distribuzione di aggiornamenti](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Si apre il riquadro **Nuova distribuzione di aggiornamenti**. Specificare i valori per le proprietà descritte nella tabella seguente e quindi fare clic su **Crea**:

Per i computer da aggiornare, selezionare la ricerca salvata usata per la distribuzione di OMS esistente.

| Proprietà | Descrizione |
| --- | --- |
|Nome |Nome univoco che identifica la distribuzione degli aggiornamenti. |
|Sistema operativo| Selezionare **Linux** o **Windows**.|
|Computer da aggiornare |Per i computer da aggiornare, selezionare la ricerca salvata usata per la distribuzione di OMS esistente. |
|Classificazioni degli aggiornamenti|Selezionare tutte le classificazioni degli aggiornamenti necessarie. CentOS non supporta questa operazione per impostazione predefinita.|
|Aggiornamenti da escludere|Immettere gli aggiornamenti da escludere. Per Windows, immettere l'articolo della Knowledge Base senza il prefisso **KB**. Per Linux, immettere il nome del pacchetto o usare un carattere jolly.  |
|Impostazioni di pianificazione|Selezionare l'ora di inizio e quindi selezionare **Una sola volta** o **Ricorrente** per la ricorrenza.|| Finestra di manutenzione |Numero di minuti impostato per gli aggiornamenti. Il valore non può essere inferiore a 30 minuti o superiore a 6 ore. |

Fare clic su **Distribuzioni di aggiornamenti pianificate** per visualizzare lo stato della nuova distribuzione di aggiornamenti creata.

![Nuova distribuzione di aggiornamenti](media/migrate-oms-update-deployments/new-update-deployment.png)

Come accennato in precedenza, dopo la configurazione delle nuove distribuzioni tramite il portale di Azure, le distribuzioni esistenti possono essere rimosse dal portale di OMS.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Gestione aggiornamenti in Azure, vedere [Soluzione Gestione aggiornamenti](automation-update-management.md).