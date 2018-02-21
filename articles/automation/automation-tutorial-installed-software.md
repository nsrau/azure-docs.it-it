---
title: Individuare il software installato nei computer con Automazione di Azure | Microsoft Docs
description: Usare l'inventario per individuare il software installato nei computer dell'ambiente.
services: automation
keywords: inventario, automazione, modifica, gestione
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: tutorial
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bdd638d0612a8ddee1a0ddb4fd4579f8da14b887
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Individuare il software installato nei computer Azure e non Azure

In questa esercitazione viene illustrato come individuare il software installato nell'ambiente. È possibile raccogliere e visualizzare l'inventario per il software, i file, i daemon Linux, i servizi di Windows e le chiavi del Registro di sistema di Windows presenti nei computer. Tenendo traccia delle configurazioni dei computer, è possibile individuare i problemi operativi dell'ambiente e conoscere meglio lo stato dei computer.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Caricare una VM per Rilevamento modifiche e inventario
> * Visualizzare il software installato
> * Cercare il software installato nei log di inventario

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [account di automazione](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare Rilevamento modifiche e inventario

Per questa esercitazione, prima di tutto è necessario abilitare Rilevamento modifiche e inventario per la VM. Se è già stata abilitata un'altra soluzione di automazione per una VM, questo passaggio non è necessario.

1. Nel menu a sinistra selezionare **Macchine virtuali** e selezionare una macchina virtuale dall'elenco
2. Nel menu a sinistra, nella sezione **Operazioni** fare clic su **Inventario**. Viene aperta la pagina **Abilita Rilevamento modifiche e inventario**.

Viene eseguita una convalida per determinare se Inventario è abilitato per la macchina virtuale.
La convalida include controlli per un'area di lavoro di Log Analytics e un account di Automazione collegato e verifica se la soluzione è presente nell'area di lavoro.

L'area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Inventario.
L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.

Il processo di convalida controlla anche se nella macchina virtuale è presente Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido.
L'agente consente di comunicare con la macchina virtuale e ottenere informazioni sul software installato.
Il processo di convalida controlla anche se nella macchina virtuale è presente Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido per runbook di Automazione.

Se non vengono soddisfatti questi prerequisiti, viene visualizzato un banner che offre l'opzione per abilitare la soluzione.

![Banner di configurazione di caricamento di Inventario](./media/automation-tutorial-installed-software/enableinventory.png)

Per abilitare la soluzione, fare clic sul banner.
Se risultano mancanti dopo la convalida, i prerequisiti seguenti vengono aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automazione](./automation-offering-get-started.md)
* [Ruolo di lavoro ibrido per runbook](./automation-hybrid-runbook-worker.md) abilitato nella macchina virtuale

Viene aperta la schermata **Rilevamento modifiche e inventario**. Configurare la posizione, l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione.

![Finestra di abilitazione della soluzione Rilevamento modifiche](./media/automation-tutorial-installed-software/installed-software-enable.png)

L'abilitazione della soluzione può richiedere fino a 15 minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser.
Dopo l'abilitazione della soluzione, le informazioni sul software installato e sulle modifiche nella macchina virtuale passano a Log Analytics.
Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

## <a name="view-installed-software"></a>Visualizzare il software installato

Dopo avere abilitato la soluzione Rilevamento modifiche e inventario, è possibile visualizzare i risultati nella pagina **Inventario**.

Dalla VM selezionare **Inventario** in **OPERAZIONI**.

Nella pagina **Inventario** fare clic sulla scheda **Software**.

Nella scheda **Software** è presente una tabella con l'elenco del software trovato. Il software è raggruppato per nome e versione.

I dettagli generali di ogni record software sono visibili nella tabella. Queste dettagli includono il nome del software, la versione, l'autore, l'ora dell'ultimo aggiornamento (ora dell'aggiornamento più recente segnalata da un computer del gruppo) e i computer (numero di computer con tale software).

![Inventario del software](./media/automation-tutorial-installed-software/inventory-software.png)

Fare clic su una riga per visualizzare le proprietà del record software e i nomi dei computer con tale software.

Per cercare un software o un gruppo di software specifico, è possibile eseguire una ricerca nella casella di testo immediatamente sopra l'elenco di software.
Il filtro consente di eseguire la ricerca in base al nome, alla versione o all'autore del software.

Ad esempio, la ricerca di "Contoso" restituisce tutti i software con un nome, un autore o una versione contenente "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Cercare il software installato nei log di inventario

Inventario genera dati di log che vengono inviati a Log Analytics. Per eseguire ricerche nei log tramite l'esecuzione di query, selezionare **Log Analytics** nella parte superiore della finestra **Inventario**.

I dati di Inventario vengono archiviati sotto il tipo **ConfigurationData**.
La query di Log Analytics di esempio seguente restituisce gli autori contenenti "Microsoft" e il numero di record software (raggruppati per SoftwareName e Computer) per ogni autore.

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Per altre informazioni sull'esecuzione e sulla ricerca nei file di log in Log Analytics, vedere [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventario di un singolo computer

Per visualizzare l'inventario software di un solo computer, è possibile accedere a Inventario dalla pagina delle risorse della VM di Azure o usare Log Analytics per filtrare in base al computer corrispondente. La query di Log Analytics di esempio seguente restituisce l'elenco dei software per un computer di nome ContosoVM.

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come visualizzare l'inventario software, ad esempio come:

> [!div class="checklist"]
> * Caricare una VM per Rilevamento modifiche e inventario
> * Visualizzare il software installato
> * Cercare il software installato nei log di inventario

Per altre informazioni, passare alla panoramica della soluzione Rilevamento modifiche e inventario.

> [!div class="nextstepaction"]
> [Soluzione di gestione del cambiamento e inventario](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)