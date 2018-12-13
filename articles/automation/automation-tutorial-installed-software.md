---
title: Individuare il software installato nei computer con Automazione di Azure | Microsoft Docs
description: Usare l'inventario per individuare il software installato nei computer dell'ambiente.
services: automation
keywords: inventario, automazione, modifica, gestione
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.component: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 6ff8bccc7037782b2709340312f0602b4cd7ab1f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834333"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Individuare il software installato nei computer Azure e non Azure

In questa esercitazione viene illustrato come individuare il software installato nell'ambiente. È possibile raccogliere e visualizzare l'inventario per il software, i file, i daemon Linux, i servizi di Windows e le chiavi del Registro di sistema di Windows presenti nei computer. Tenendo traccia delle configurazioni dei computer, è possibile individuare i problemi operativi dell'ambiente e conoscere meglio lo stato dei computer.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la soluzione
> * Caricare una macchina virtuale di Azure
> * Eseguire l'onboarding di una macchina virtuale non di Azure
> * Visualizzare il software installato
> * Cercare il software installato nei log di inventario

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [account di Automazione](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare Rilevamento modifiche e inventario

Per questa esercitazione, prima di tutto è necessario abilitare Rilevamento modifiche e inventario. Se in precedenza è stata abilitata la soluzione **Rilevamento modifiche**, questo passaggio non è necessario.

Passare all'account di Automazione e selezionare **Inventario** in **GESTIONE DELLA CONFIGURAZIONE**.

Scegliere l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Banner di configurazione di caricamento di Inventario](./media/automation-tutorial-installed-software/enableinventory.png)

Per abilitare la soluzione, configurare la posizione, l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione.

L'area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Inventario.
L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.

L'abilitazione della soluzione può richiedere fino a 15 minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser.
Dopo l'abilitazione della soluzione, le informazioni sul software installato e sulle modifiche nella macchina virtuale passano a Log Analytics.
Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

## <a name="onboard-a-vm"></a>Eseguire l'onboarding di una macchina virtuale

Nell'account di Automazione passare a **Inventario** in **GESTIONE DELLA CONFIGURAZIONE**.

Selezionare **+ Aggiungi macchina virtuale di Azure** per visualizzare la pagina **Macchine virtuali** che consente di selezionare una macchina virtuale esistente dall'elenco. Selezionare la macchina virtuale di cui si vuole eseguire l'onboarding. Nella pagina visualizzata fare clic su **Abilita** per abilitare la soluzione sulla macchina virtuale. L'agente di gestione Microsoft viene distribuito nella macchina virtuale e configura l'agente in modo che comunichi con l'area di lavoro di Log Analytics configurata durante l'abilitazione della soluzione. L'onboarding potrebbe richiedere alcuni minuti. A questo punto è possibile selezionare una nuova macchina virtuale dall'elenco ed eseguire l'onboarding di un'altra macchina virtuale.

## <a name="onboard-a-non-azure-machine"></a>Eseguire l'onboarding di un computer non di Azure

Per aggiungere computer non Azure, installare l'agente per [Windows](../azure-monitor/platform/agent-windows.md) o [Linux](automation-linux-hrw-install.md), in base al sistema operativo in uso. Dopo l'installazione dell'agente, passare all'account di Automazione e quindi a **Inventario** in **GESTIONE DELLA CONFIGURAZIONE**. Quando si fa clic su **Gestisci computer**, viene visualizzato un elenco di computer che inviano report all'area di lavoro di Log Analytics in cui non è abilitata la soluzione. Selezionare l'opzione appropriata per l'ambiente.

* **Abilita in tutti i computer disponibili**: questa opzione abilita la soluzione su tutti i computer che attualmente inviano report all'area di lavoro di Log Analytics.
* **Abilita in tutti i computer disponibili e futuri**: questa opzione abilita la soluzione su tutti i computer che inviano report all'area di lavoro di Log Analytics e successivamente su tutti i computer futuri aggiunti all'area di lavoro.
* **Abilita nei computer selezionati**: questa opzione abilita la soluzione solo nei computer selezionati.

![Gestisci computer](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Visualizzare il software installato

Dopo avere abilitato la soluzione Rilevamento modifiche e inventario, è possibile visualizzare i risultati nella pagina **Inventario**.

Dall'account di Automazione selezionare **Inventario** in **GESTIONE DELLA CONFIGURAZIONE**.

Nella pagina **Inventario** fare clic sulla scheda **Software**.

Nella scheda**Software** è presente una tabella che elenca il software trovato. Il software è raggruppato per nome e versione.

I dettagli generali di ogni record software sono visibili nella tabella. Queste dettagli includono il nome del software, la versione, l'autore, l'ora dell'ultimo aggiornamento (ora dell'aggiornamento più recente segnalata da un computer del gruppo) e i computer (numero di computer con tale software).

![Inventario del software](./media/automation-tutorial-installed-software/inventory-software.png)

Fare clic su una riga per visualizzare le proprietà del record software e i nomi dei computer con tale software.

Per cercare un software o un gruppo di software specifico, è possibile eseguire una ricerca nella casella di testo immediatamente sopra l'elenco di software.
Il filtro consente di eseguire la ricerca in base al nome, alla versione o all'autore del software.

Ad esempio, la ricerca di "Contoso" restituisce tutti i software con un nome, un autore o una versione contenente "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Cercare il software installato nei log di inventario

Inventario genera dati di log che vengono inviati a Log Analytics. Per eseguire ricerche nei log tramite l'esecuzione di query, selezionare **Log Analytics** nella parte superiore della finestra **Inventario**.

I dati di Inventario vengono archiviati sotto il tipo **ConfigurationData**.
L'esempio seguente di query di Log Analytics restituisce i risultati dell'inventario in cui l'autore equivale a "Microsoft Corporation".

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Per altre informazioni sull'esecuzione e sulla ricerca nei file di log in Log Analytics, vedere [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md).

### <a name="single-machine-inventory"></a>Inventario di un singolo computer

Per visualizzare l'inventario software di un solo computer, è possibile accedere a Inventario dalla pagina delle risorse della VM di Azure o usare Log Analytics per filtrare in base al computer corrispondente.
La query di Log Analytics di esempio seguente restituisce l'elenco dei software per un computer di nome ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come visualizzare l'inventario software, ad esempio come:

> [!div class="checklist"]
> * Abilitare la soluzione
> * Caricare una macchina virtuale di Azure
> * Eseguire l'onboarding di una macchina virtuale non di Azure
> * Visualizzare il software installato
> * Cercare il software installato nei log di inventario

Per altre informazioni, passare alla panoramica della soluzione Rilevamento modifiche e inventario.

> [!div class="nextstepaction"]
> [Soluzione di gestione del cambiamento e inventario](automation-change-tracking.md)