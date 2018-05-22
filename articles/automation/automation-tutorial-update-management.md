---
title: Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure
description: Questo articolo offre una panoramica dell'uso di Gestione aggiornamenti di Automazione di Azure per gestire gli aggiornamenti e le patch per le VM Windows di Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>Gestire gli aggiornamenti di Windows con Automazione di Azure

Gestione aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali.
In questa esercitazione viene illustrato come valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti, esaminare i risultati della distribuzione e creare un avviso per verificare che gli aggiornamenti vengano applicati correttamente.

Per informazioni sui prezzi, vedere [Prezzi di Automazione per la gestione degli aggiornamenti](https://azure.microsoft.com/pricing/details/automation/)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Caricare una VM per Gestione aggiornamenti
> * Visualizzare una valutazione degli aggiornamenti
> * Configurare gli avvisi
> * Pianificare la distribuzione degli aggiornamenti
> * Visualizzare i risultati di una distribuzione

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare il credito Azure mensile per i sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [account di automazione](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-update-management"></a>Abilitare Gestione aggiornamenti

Prima di tutto è necessario abilitare Gestione aggiornamenti nella macchina virtuale per questa esercitazione.

1. Nel menu a sinistra nel portale di Azure selezionare **Macchine virtuali** e selezionare una macchina virtuale nell'elenco
2. Nella pagina della macchina virtuale fare clic su **Gestione aggiornamenti**  nella sezione **Operazioni**. Viene visualizzata la pagina **Abilita Gestione aggiornamenti**.

Viene eseguita una convalida per determinare se Gestione aggiornamenti è abilitata per la macchina virtuale. La convalida include controlli per un'area di lavoro di Log Analytics e un account di Automazione collegato e verifica se la soluzione Gestione aggiornamenti è presente nell'area di lavoro.

L'area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Gestione aggiornamenti. L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.

Il processo di convalida controlla anche se nella macchina virtuale è presente Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido per runbook di Automazione.
L'agente consente di comunicare con Automazione di Azure e ottenere informazioni sullo stato dell'aggiornamento. L'agente richiede che la porta 443 sia aperta per comunicare con il servizio Automazione di Azure e scaricare gli aggiornamenti.

Se risultano mancanti durante l'onboarding, i prerequisiti seguenti vengono aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Account di Automazione](./automation-offering-get-started.md)
* [Ruolo di lavoro ibrido per runbook](./automation-hybrid-runbook-worker.md) abilitato nella macchina virtuale

Viene visualizzata la schermata **Gestione aggiornamenti**. Configurare la posizione, l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione.

![Finestra di abilitazione della soluzione Gestione aggiornamenti](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

L'abilitazione della soluzione può richiedere alcuni minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser.
Dopo l'abilitazione della soluzione, le informazioni sugli aggiornamenti mancanti nella macchina virtuale passano a Log Analytics.
Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

## <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato **Gestione aggiornamenti**, viene visualizzata la schermata **Gestione aggiornamenti**.
Se mancano alcuni aggiornamenti, è possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

Selezionare il **COLLEGAMENTO ALLE INFORMAZIONI** dell'aggiornamento per aprire l'articolo di supporto per l'aggiornamento in una nuova finestra, dove si possono trovare importanti informazioni relative all'aggiornamento.

![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Facendo clic in altro punto dell'area dell'aggiornamento, viene aperta la finestra **Ricerca log** per l'aggiornamento selezionato. La query per la ricerca log è predefinita per tale specifico aggiornamento. È possibile modificare questa query o crearne una nuova per visualizzare informazioni dettagliate sugli aggiornamenti distribuiti o mancanti nell'ambiente.

![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>Configurare gli avvisi

In questo passaggio si configura un avviso per comunicare quando gli aggiornamenti sono stati distribuiti correttamente. L'avviso creato si basa su una query di Log Analytics. È possibile scrivere query personalizzate per altri avvisi, per soddisfare le esigenze di numerosi scenari diversi. Nel portale di Azure passare a **Monitoraggio** e fare clic su **Crea avviso**. Verrà visualizzata la pagina **Crea regola**.

In **1. Definire la condizione dell'avviso** fare clic su **+ Selezionare la destinazione**. In **Filtra per tipo di risorsa** selezionare **Log Analytics**. Scegliere l'area di lavoro di Log Analytics e fare clic su **Fine**.

![Creare un avviso](./media/automation-tutorial-update-management/create-alert.png)

Fare clic sul pulsante **+ Aggiungi criteri** per aprire la pagina **Configura logica dei segnali**. Scegliere **Custom log search** (Ricerca log personalizzata) nella tabella. Immettere la query seguente nella casella di testo **Query di ricerca**. Questa query restituisce i computer e il nome dell'operazione di aggiornamento completata nell'intervallo di tempo specificato.

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

Immettere **1** per **Soglia** in Logica avvisi. Al termine, fare clic su **Fine**.

![Configurare la logica dei segnali](./media/automation-tutorial-update-management/signal-logic.png)

In **2. Definire i dettagli dell'avviso** assegnare all'avviso un nome descrittivo e una descrizione. Impostare **Gravità** su **Informazioni (gravità 2)** perché l'avviso si riferisce a un'operazione riuscita.

![Configurare la logica dei segnali](./media/automation-tutorial-update-management/define-alert-details.png)

In **3. Definire il gruppo di azioni** fare clic su **+ Nuovo gruppo di azioni**. Un gruppo di azioni è un insieme di azioni che è possibile usare in più avvisi. È ad esempio possibile usare notifiche tramite posta elettronica, runbook, webhook e molto altro ancora. Per informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md)

Nella casella **Nome gruppo di azione** assegnare un nome descrittivo e un nome breve. Il nome breve viene usato al posto del nome completo di un gruppo di azioni quando le notifiche vengono inviate usando questo gruppo.

In **Azioni** assegnare all'azione un nome descrittivo, ad esempio **Notifiche di posta elettronica** e in **TIPO DI AZIONE** selezionare **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce). In **DETTAGLI** selezionare **Modifica dettagli**.

Nella pagina **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce) assegnare un nome. Selezionare la casella di controllo **Posta elettronica** e immettere un indirizzo di posta elettronica valido da usare.

![Configurare il gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/configure-email-action-group.png)

Fare clic su **OK** nella pagina **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce) per chiuderla e fare clic su **OK** per chiudere la pagina **Aggiungi gruppo di azioni**.

È possibile personalizzare l'oggetto del messaggio di posta elettronica inviato facendo clic su **Oggetto messaggio di posta elettronica** in **Personalizza azioni** nella pagina **Crea regola**. Al termine, fare clic su **Crea regola di avviso**. Verrà creata una regola che avvisa quando una distribuzione di un aggiornamento ha esito positivo e indica i computer inclusi nella distribuzione.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Una volta configurati gli avvisi, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio per l'installazione degli aggiornamenti.
È possibile scegliere i tipi di aggiornamento da includere nella distribuzione.
È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

> [!WARNING]
> Quando gli aggiornamenti richiedono un riavvio, la macchina virtuale viene riavviata automaticamente.

Pianificare una nuova distribuzione di aggiornamenti per la macchina virtuale tornando a **Gestione aggiornamenti** e selezionando **Pianifica la distribuzione di aggiornamenti** nella parte superiore della schermata.

Nella schermata **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

* **Nome**: specificare un nome univoco per la distribuzione di aggiornamenti.

* **Sistema operativo**: scegliere il sistema operativo di destinazione per la distribuzione degli aggiornamenti.

* **Classificazioni aggiornamenti**: selezionare i tipi di software inclusi nella distribuzione di aggiornamenti. Per questa esercitazione, lasciare tutti i tipi selezionati.

  I tipi di classificazione sono:

   |OS  |type  |
   |---------|---------|
   |Windows     | Aggiornamenti critici</br>Aggiornamenti della sicurezza</br>Aggiornamenti cumulativi</br>Feature Pack</br>Service Pack</br>Aggiornamenti della definizione</br>Strumenti</br>Aggiornamenti        |
   |Linux     | Aggiornamenti critici e della sicurezza</br>Altri aggiornamenti       |

   Per una descrizione dei tipi di classificazione, vedere le [classificazioni degli aggiornamenti](automation-update-management.md#update-classifications).

* **Impostazioni pianificazione**: consente di aprire la pagina Impostazioni di pianificazione. L'ora di inizio predefinita è 30 minuti dopo il momento corrente. È possibile impostare qualsiasi orario a partire da 10 minuti dal momento corrente.

   È anche possibile specificare se eseguire la distribuzione una sola volta o impostare una pianificazione ricorrente.
   Selezionare **Una sola volta** in **Ricorrenza**. Lasciare l'impostazione predefinita su un 1 giorno e fare clic su **OK**. Viene configurata una pianificazione ricorrente.

* **Finestra di manutenzione (minuti)**: lasciare il valore predefinito. È possibile specificare il periodo di tempo nel quale eseguire la distribuzione di aggiornamenti. Questa impostazione consente di garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

![Schermata di impostazioni della pianificazione di aggiornamenti](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Dopo avere completato la configurazione della pianificazione, fare clic sul pulsante **Crea**. Si torna al dashboard dello stato. Selezionare **Distribuzioni di aggiornamenti pianificate** per visualizzare la pianificazione della distribuzione creata.

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** nella schermata **Gestione aggiornamenti**.
Viene visualizzato lo stato **In corso** quando la distribuzione è in corso.
Quando la distribuzione viene completata correttamente, lo stato diventa **Completato**.
Quando si verificano errori in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.
Fare clic sulla distribuzione di aggiornamenti completata per visualizzare il dashboard della distribuzione.

![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Il riquadro **Risultati aggiornamento** include un riepilogo che fornisce il numero totale di aggiornamenti e i risultati della distribuzione nella macchina virtuale.
La tabella a destra offre una suddivisione dettagliata di ogni aggiornamento e dei risultati dell'installazione.
L'elenco seguente mostra i valori disponibili:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato a causa di tempo disponibile non sufficiente basato sulla durata della finestra di manutenzione specificata.
* **Completato**: l'aggiornamento è stato completato
* **Non riuscito**: l'aggiornamento non è riuscito

Fare clic su **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Fare clic sul riquadro **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Fare clic su **Errori** per visualizzare informazioni dettagliate sugli errori della distribuzione.

Dopo il completamento della distribuzione dell'aggiornamento, viene inviato un messaggio simile a quello nell'immagine seguente per indicare la riuscita della distribuzione.

![Configurare il gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Caricare una VM per Gestione aggiornamenti
> * Visualizzare una valutazione degli aggiornamenti
> * Configurare gli avvisi
> * Pianificare la distribuzione degli aggiornamenti
> * Visualizzare i risultati di una distribuzione

Passare alla panoramica della soluzione Gestione aggiornamenti.

> [!div class="nextstepaction"]
> [Soluzione Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
