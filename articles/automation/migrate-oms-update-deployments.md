---
title: Eseguire la migrazione delle distribuzioni di aggiornamento dei log di Monitoraggio di Azure al portale di Azure
description: Questo articolo illustra come eseguire la migrazione delle distribuzioni di aggiornamento dei log di Monitoraggio di Azure al portale di Azure.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 9c8238e6e0b52a625c76f79fa0dd5a91dd640fb8
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447846"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Eseguire la migrazione delle distribuzioni di aggiornamento dei log di Monitoraggio di Azure al portale di Azure

Il portale di Operations Management Suite (OMS) sarà presto [deprecato](../azure-monitor/platform/oms-portal-transition.md). Tutte le funzionalità precedentemente disponibili nel portale di OMS per Gestione aggiornamenti sono ora disponibili nel portale di Azure mediante i log di Monitoraggio di Azure. Questo articolo contiene le informazioni necessarie per eseguire la migrazione al portale di Azure.

## <a name="key-information"></a>Informazioni chiave

* Le distribuzioni esistenti continueranno a funzionare. Dopo avere creato nuovamente la distribuzione in Azure, è possibile eliminare la distribuzione precedente.
* Tutte le funzionalità esistenti in OMS sono disponibili in Azure. Per altre informazioni sulla gestione aggiornamenti, vedere [Gestione aggiornamenti in automazione di Azure](update-management/update-mgmt-overview.md).

## <a name="access-the-azure-portal"></a>Accedere al portale di Azure

1. Dall'area di lavoro fare clic su **Apri in Azure**. 

    ![Aprire in Azure - Analisi dei log](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. Nel portale di Azure fare clic su **Account di automazione**.

    ![Log di Monitoraggio di Azure](media/migrate-oms-update-deployments/log-analytics.png)

3. Nell'account di Automazione di Azure fare clic su **Gestione aggiornamenti**.

    ![Gestione degli aggiornamenti](media/migrate-oms-update-deployments/azure-automation.png)

4. Nel portale di Azure selezionare **Account di automazione** in **Tutti i servizi**. 

5. In **Strumenti di gestione** selezionare l'account di automazione appropriato e fare clic su **Gestione aggiornamenti**.

## <a name="recreate-existing-deployments"></a>Creare nuovamente le distribuzioni esistenti

Tutte le distribuzioni di aggiornamenti create nel portale di OMS hanno una [ricerca salvata](../azure-monitor/platform/computer-groups.md), definita anche gruppo di computer, con lo stesso nome della distribuzione di aggiornamenti esistente. La ricerca salvata contiene l'elenco dei computer per cui è stata pianificata la distribuzione di aggiornamenti.

![Gestione degli aggiornamenti](media/migrate-oms-update-deployments/oms-deployment.png)

Per usare la ricerca salvata esistente, seguire questa procedura:

1. Per creare una nuova distribuzione di aggiornamenti, passare al portale di Azure, selezionare l'account di automazione usato e fare clic su **Gestione aggiornamenti**. Fare clic su **Pianifica la distribuzione di aggiornamenti**.

    ![Pianificare la distribuzione di aggiornamenti](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. Si apre il riquadro Nuova distribuzione di aggiornamenti. Specificare i valori per le proprietà descritte nella tabella seguente e quindi fare clic su **Crea**:

3. Per **Computer da aggiornare**, selezionare la ricerca salvata usata per la distribuzione di OMS.

    | Proprietà | Descrizione |
    | --- | --- |
    |Nome |Nome univoco che identifica la distribuzione degli aggiornamenti. |
    |Sistema operativo| Selezionare **Linux** o **Windows**.|
    |Computer da aggiornare |Selezionare una ricerca salvata o un gruppo importato, oppure scegliere Computer dall'elenco a discesa e selezionare i singoli computer. Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **AGGIORNA IDONEITÀ AGENTE**.</br> Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../azure-monitor/platform/computer-groups.md) |
    |Classificazioni degli aggiornamenti|Selezionare tutte le classificazioni degli aggiornamenti necessarie. CentOS non supporta questa operazione per impostazione predefinita.|
    |Aggiornamenti da escludere|Immettere gli aggiornamenti da escludere. Per Windows, immettere l'articolo della Knowledge Base senza il prefisso **KB**. Per Linux, immettere il nome del pacchetto o usare un carattere jolly.  |
    |Impostazioni di pianificazione|Selezionare l'ora di inizio e quindi selezionare **Una sola volta** o **Ricorrente** per la ricorrenza. | 
    | Finestra di manutenzione |Numero di minuti impostato per gli aggiornamenti. Il valore non può essere inferiore a 30 minuti o superiore a 6 ore. |
    | Controllo riavvio| Determina come vengono gestiti i riavvii.</br>Le opzioni disponibili sono:</br>Riavvia se necessario (opzione predefinita)</br>Riavvia sempre</br>Non riavviare mai</br>Riavvia solamente: gli aggiornamenti non verranno installati|

4. Fare clic su **Distribuzioni di aggiornamenti pianificate** per visualizzare lo stato della nuova distribuzione di aggiornamenti creata.

    ![Nuova distribuzione di aggiornamenti](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Come accennato in precedenza, dopo la configurazione delle nuove distribuzioni tramite il portale di Azure, le distribuzioni esistenti possono essere rimosse dal portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Gestione aggiornamenti in Azure, vedere [Gestione aggiornamenti in automazione di Azure](update-management/update-mgmt-overview.md).
