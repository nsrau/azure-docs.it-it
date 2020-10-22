---
title: Individuare quale software è installato nelle VM con Automazione di Azure | Microsoft Docs
description: Questo articolo descrive il software installato nelle VM nell'ambiente.
services: automation
keywords: inventario, automazione, rilevamento delle modifiche
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 0c083f4576e123ea14d837ed3915c56d18d84623
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207564"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Individuare quale software è installato nelle VM

Questa esercitazione illustra come usare la funzionalità Rilevamento modifiche e inventario di Automazione di Azure per individuare quale software è installato nell'ambiente. È possibile raccogliere e visualizzare l'inventario per il software, i file, i daemon Linux, i servizi di Windows e le chiavi del Registro di sistema di Windows presenti nei computer. Tenendo traccia delle configurazioni dei computer, è possibile individuare i problemi operativi dell'ambiente e conoscere meglio lo stato dei computer.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare il rilevamento delle modifiche e l'inventario
> * Abilitare una VM di Azure
> * Abilitare una VM non di Azure
> * Visualizzare il software installato
> * Cercare il software installato nei log di inventario

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [account di Automazione](./index.yml) per contenere i runbook watcher e azione e l'attività watcher.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da abilitare per la funzionalità.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

Per questa esercitazione, prima di tutto è necessario abilitare Rilevamento modifiche e inventario. Se la funzionalità è già stata abilitata, questo passaggio non è necessario.

>[!NOTE]
>Se i campi sono inattivi, significa che per la VM è abilitata un'altra funzionalità di Automazione di Azure ed è necessario usare la stessa area di lavoro e lo stesso account di Automazione.

1. Passare all'account di Automazione e selezionare **Inventario** o **Rilevamento modifiche** in **Gestione della configurazione**.

2. Scegliere l'area di lavoro [Log Analytics](../azure-monitor/log-query/log-query-overview.md). Questa area di lavoro raccoglie i dati generati da funzionalità come Rilevamento modifiche e inventario. L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Selezionare l'account di Automazione da usare.

4. Configurare la posizione per la distribuzione.

5. Fare clic su **Abilita** per distribuire la funzionalità per la VM. 

    ![Banner di configurazione dell'inventario](./media/automation-tutorial-installed-software/enableinventory.png)

Durante la configurazione viene effettuato il provisioning della macchina virtuale con l'agente di Log Analytics per Windows e un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md). L'abilitazione di Rilevamento modifiche e inventario può richiedere fino a 15 minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser.

Dopo l'abilitazione della funzionalità, le informazioni sul software installato e sulle modifiche nella macchina virtuale passano ai log di Monitoraggio di Azure. Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Aggiungere una VM di Azure a Rilevamento modifiche e inventario

1. Nell'account di Automazione passare a **Inventario** o a **Rilevamento modifiche** in **Gestione della configurazione**.

2. Selezionare **+ Aggiungi macchina virtuale di Azure**.

3. Selezionare la propria VM nell'elenco. 

4. Fare clic su**Abilita** per abilitare Rilevamento modifiche e inventario nella VM. L'agente di Log Analytics per Windows viene distribuito nella macchina virtuale e la configura per comunicare con l'area di lavoro Log Analytics. L'operazione di configurazione può richiedere alcuni minuti. 

5. A questo punto, se si desidera, è possibile selezionare una nuova macchina virtuale nell'elenco da abilitare per la funzionalità.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Aggiungere un computer non di Azure a Rilevamento modifiche e inventario

Per abilitare i computer non di Azure per la funzionalità:

1. Installare l'[agente di Log Analytics per Windows](../azure-monitor/platform/agent-windows.md) o l'[agente di Log Analytics per Linux](automation-linux-hrw-install.md), in base al sistema operativo in uso. 

2. Passare all'account di Automazione e selezionare **Inventario** o **Rilevamento modifiche** in **Gestione della configurazione**. 

3. Fare clic su **Gestisci computer**. Viene visualizzato un elenco di computer che inviano dati all'area di lavoro Log Analytics ma in cui non è abilitata la funzionalità Rilevamento modifiche e inventario. Selezionare l'opzione appropriata per l'ambiente:

    * **Abilita in tutti i computer disponibili**: questa opzione abilita la funzionalità in tutti i computer che attualmente inviano dati all'area di lavoro Log Analytics.
    * **Abilita in tutti i computer disponibili e futuri**: questa opzione abilita la funzionalità in tutti i computer che inviano dati all'area di lavoro Log Analytics e successivamente in tutti i computer futuri aggiunti all'area di lavoro.
    * **Abilita nei computer selezionati**: questa opzione abilita la funzionalità solo nei computer selezionati.

    ![Gestisci computer](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Visualizzare il software installato

Dopo avere abilitato la funzionalità Rilevamento modifiche e inventario, è possibile visualizzare i risultati nella pagina Inventario.

1. Nell'account di Automazione selezionare **Inventario** in **Gestione configurazione**.

2. Nella pagina Inventario fare clic sulla scheda **Software**.

3. Notare una tabella che include l'elenco di software individuato. Il software è raggruppato per nome e versione. I dettagli generali di ogni record software sono visibili nella tabella. Queste dettagli includono il nome del software, la versione, l'autore, l'ora dell'ultimo aggiornamento (ora dell'aggiornamento più recente segnalata da un computer del gruppo) e i computer (numero di computer con tale software).

    ![Inventario del software](./media/automation-tutorial-installed-software/inventory-software.png)

4. Fare clic su una riga per visualizzare le proprietà del record software e i nomi dei computer con tale software.

5. Per cercare un software o un gruppo di software specifico, è possibile eseguire una ricerca nella casella di testo immediatamente sopra l'elenco di software.
Il filtro consente di eseguire la ricerca in base al nome, alla versione o all'autore del software. Ad esempio, la ricerca di **Contoso** restituisce tutti i software con un nome, un autore o una versione contenente **Contoso**.

## <a name="search-inventory-logs-for-installed-software"></a>Cercare il software installato nei log di inventario

Rilevamento modifiche e inventario genera dati di log che vengono inviati a Monitoraggio di Azure. Per eseguire ricerche nei log tramite l'esecuzione di query, selezionare **Log Analytics** nella parte superiore della finestra Inventario. I dati di Inventario vengono archiviati nel tipo `ConfigurationData`.

La seguente query di Log Analytics di esempio restituisce i risultati dell'inventario per l'autore Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Per altre informazioni sull'esecuzione dei file di log e sulla ricerca al loro interno nei log di Monitoraggio di Azure, vedere [Log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Vedere l'inventario del software per un singolo computer

Per visualizzare l'inventario software per un solo computer, è possibile accedere all'inventario dalla pagina delle risorse della macchina virtuale di Azure o usare i log di Monitoraggio di Azure per filtrare i dati in base al computer corrispondente. La query di Log Analytics di esempio seguente restituisce l'elenco dei software per un computer denominato **ContosoVM**.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come visualizzare l'inventario software:

> [!div class="checklist"]
> * Abilitare il rilevamento delle modifiche e l'inventario
> * Abilitare una VM di Azure
> * Abilitare una VM non di Azure
> * Visualizzare il software installato
> * Cercare il software installato nei log di inventario

Per altre informazioni, passare alla panoramica della funzionalità Rilevamento modifiche e inventario.

> [!div class="nextstepaction"]
> [Panoramica di Rilevamento modifiche e inventario](change-tracking/overview.md)
