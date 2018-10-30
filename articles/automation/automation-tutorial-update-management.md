---
title: Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure
description: Questo articolo offre una panoramica dell'uso di Gestione aggiornamenti di Automazione di Azure per gestire gli aggiornamenti e le patch per le VM Windows di Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 6046781f59b64dcec4769686a2acd710c7b68965
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987308"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Gestire gli aggiornamenti di Windows con Automazione di Azure

È possibile usare la soluzione Gestione aggiornamenti per gestire gli aggiornamenti e le patch per le macchine virtuali. In questa esercitazione viene illustrato come valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti, esaminare i risultati della distribuzione e creare un avviso per verificare che gli aggiornamenti vengano applicati correttamente.

Per informazioni sui prezzi, vedere [Prezzi di Automazione per la gestione degli aggiornamenti](https://azure.microsoft.com/pricing/details/automation/).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Caricare una VM per Gestione aggiornamenti
> * Visualizzare una valutazione degli aggiornamenti
> * Configurare gli avvisi
> * Pianificare la distribuzione degli aggiornamenti
> * Visualizzare i risultati di una distribuzione

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare il credito Azure mensile per i sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [account di Automazione di Azure](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

Prima di tutto, abilitare Gestione aggiornamenti nella macchina virtuale per questa esercitazione:

1. Nel menu sinistro del portale di Azure selezionare **Macchine virtuali**. Selezionare una VM dall'elenco.
2. Nella pagina della macchina virtuale, in **OPERAZIONI**, selezionare **Gestione aggiornamenti**. Verrà aperto il riquadro **Abilita Gestione aggiornamenti**.

Viene eseguita una convalida per determinare se Gestione aggiornamenti è abilitato per la macchina virtuale. La convalida include controlli per un'area di lavoro di Log Analytics di Azure e un account di Automazione collegato e verifica se la soluzione Gestione aggiornamenti è presente nell'area di lavoro.

L'area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) consente di raccogliere i dati generati da funzionalità e servizi come Gestione aggiornamenti. L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.

Il processo di convalida controlla anche se nella macchina virtuale è presente Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido per runbook di Automazione. L'agente consente di comunicare con Automazione di Azure e ottenere informazioni sullo stato dell'aggiornamento. L'agente richiede che la porta 443 sia aperta per comunicare con il servizio Automazione di Azure e scaricare gli aggiornamenti.

Se risultano mancanti durante l'onboarding, i prerequisiti seguenti vengono aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Account di Automazione](./automation-offering-get-started.md)
* [Ruolo di lavoro ibrido per runbook](./automation-hybrid-runbook-worker.md) (abilitato nella macchina virtuale)

In **Gestione aggiornamenti** configurare la posizione, l'area di lavoro di Log Analytics e l'account di Automazione da usare. Selezionare quindi **Abilita**. Se queste opzioni non sono disponibili, un'altra soluzione di automazione è abilitata per la macchina virtuale. In tal caso, è necessario usare lo stesso account di Automazione e la stessa area di lavoro.

![Finestra di abilitazione della soluzione Gestione aggiornamenti](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

L'abilitazione della soluzione può richiedere alcuni minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser. Dopo l'abilitazione della soluzione, le informazioni sugli aggiornamenti mancanti nella macchina virtuale passano a Log Analytics. Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

## <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato Gestione aggiornamenti, verrà visualizzata la schermata **Gestione aggiornamenti**. Se mancano alcuni aggiornamenti, viene visualizzato un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

In **COLLEGAMENTO ALLE INFORMAZIONI** selezionare il collegamento dell'aggiornamento per aprire l'articolo di supporto per l'aggiornamento in una nuova finestra, dove si possono trovare importanti informazioni relative all'aggiornamento.

![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Facendo clic in altro punto dell'area dell'aggiornamento, viene aperto il riquadro **Ricerca log** per l'aggiornamento selezionato. La query per la ricerca log è predefinita per tale specifico aggiornamento. È possibile modificare questa query o crearne una nuova per visualizzare informazioni dettagliate sugli aggiornamenti distribuiti o mancanti nell'ambiente.

![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurare gli avvisi

In questo passaggio viene illustrato come configurare un avviso per segnalare il completamento della distribuzione di aggiornamenti tramite una query di Log Analytics o tramite la verifica del runbook master per Gestione aggiornamenti per le distribuzioni non riuscite.

### <a name="alert-conditions"></a>Condizioni di avviso

Per ogni tipo di avviso è necessario definire diverse condizioni di avviso.

#### <a name="log-analytics-query-alert"></a>Avviso basato su query di Log Analytics

Per le distribuzioni riuscite è possibile creare un avviso basato su una query di Log Analytics. Per le distribuzioni non riuscite è possibile usare i passaggi della sezione [Avviso del runbook](#runbook-alert) per segnalare gli errori del runbook master che orchestra le distribuzioni degli aggiornamenti. È possibile scrivere una query personalizzata per altri avvisi, per soddisfare le esigenze di numerosi scenari diversi.

Nel portale di Azure passare a **Monitoraggio** e quindi selezionare **Crea avviso**.

In **1. Definire la condizione dell'avviso**, fare clic su **Selezionare la destinazione**. In **Filtra per tipo di risorsa** selezionare **Log Analytics**. Selezionare l'area di lavoro di Log Analytics e quindi selezionare **Fine**.

![Creare un avviso](./media/automation-tutorial-update-management/create-alert.png)

Selezionare **Aggiungi criteri**.

In **Configurare la logica dei segnali** selezionare **Custom log search** (Ricerca log personalizzata) nella tabella. Immettere la query seguente nella casella di testo **Query di ricerca**:

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```
Questa query restituisce i computer e il nome dell'operazione di aggiornamento completata nell'intervallo di tempo specificato.

In **Logica avvisi**, per **Soglia**, immettere **1**. Al termine, fare clic su **Fine**.

![Configurare la logica dei segnali](./media/automation-tutorial-update-management/signal-logic.png)

#### <a name="runbook-alert"></a>Avviso del runbook

Per distribuzioni non riuscite, è necessario segnalare l'errore del runbook master.
Nel portale di Azure passare a **Monitoraggio** e quindi selezionare **Crea avviso**.

In **1. Definire la condizione dell'avviso**, fare clic su **Selezionare la destinazione**. In **Filtra per tipo di risorsa** selezionare **Account di automazione**. Selezionare l'account di automazione e quindi selezionare **Fine**.

Per **Nome runbook** fare clic sul segno **\+** e immettere **Patch-MicrosoftOMSComputers** come nome personalizzato. Per **Stato** scegliere **Non riuscito** o fare clic sul segno **\+** per immettere **Non riuscito**.

![Configurare la logica del segnale per i runbook](./media/automation-tutorial-update-management/signal-logic-runbook.png)

In **Logica avvisi**, per **Soglia**, immettere **1**. Al termine, fare clic su **Fine**.

### <a name="alert-details"></a>Dettagli dell'avviso

In **2. Definire i dettagli dell'avviso** assegnare un nome e una descrizione per l'avviso. Impostare **Gravità** su **Informational(Sev 2)** per un'esecuzione riuscita o su **Informational(Sev 1)** per un'esecuzione non riuscita.

![Configurare la logica dei segnali](./media/automation-tutorial-update-management/define-alert-details.png)

In **3. Definire il gruppo di azioni** selezionare **Nuovo gruppo di azioni**. Un gruppo di azioni è un insieme di azioni che è possibile usare in più avvisi. È ad esempio possibile usare notifiche tramite posta elettronica, runbook, webhook e molto altro ancora. Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md).

Nella casella **Nome gruppo di azioni** immettere un nome per l'avviso e un nome breve. Il nome breve viene usato al posto del nome completo di un gruppo di azioni quando le notifiche vengono inviate usando questo gruppo.

In **Azioni** immettere un nome per l'azione, ad esempio **Notifiche tramite posta elettronica**. In **TIPO DI AZIONE** selezionare **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce). In **DETTAGLI** selezionare **Modifica dettagli**.

Nel riquadro **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce) immettere un nome. Selezionare la casella di controllo **Posta elettronica** e quindi immettere un indirizzo di posta elettronica valido.

![Configurare un gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/configure-email-action-group.png)

Nel riquadro **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce) selezionare **OK**. Nel riquadro **Aggiungi gruppo di azioni** selezionare **OK**.

Per personalizzare l'oggetto del messaggio di posta elettronica di avviso, in **Crea regola**, in **Personalizza azioni**, selezionare **Oggetto messaggio di posta elettronica**. Al termine, selezionare **Crea regola di avviso**. L'avviso segnala quando una distribuzione di un aggiornamento ha esito positivo e indica i computer inclusi nella distribuzione.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Pianificare quindi una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio per installare gli aggiornamenti. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Per pianificare una nuova distribuzione di aggiornamenti per la macchina virtuale, passare a **Gestione aggiornamenti** e quindi selezionare **Pianifica la distribuzione di aggiornamenti**.

In **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

* **Nome**: immettere un nome univoco per la distribuzione di aggiornamenti.

* **Sistema operativo**: selezionare il sistema operativo di destinazione per la distribuzione degli aggiornamenti.

* **Gruppi da aggiornare (anteprima)**: definire una query basata su una combinazione di sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per altre informazioni, vedere [Gruppi dinamici](automation-update-management.md#using-dynamic-groups)

* **Computer da aggiornare**: selezionare una ricerca salvata o un gruppo importato, oppure scegliere un computer dall'elenco a discesa e selezionare i singoli computer. Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **AGGIORNA IDONEITÀ AGENTE**. Per altre informazioni sui diversi metodi di creazione di gruppi di computer in Log Analytics, vedere [gruppi di Computer in Log Analytics](../log-analytics/log-analytics-computer-groups.md)

* **Classificazioni aggiornamenti**: selezionare i tipi di software inclusi nella distribuzione di aggiornamenti. Per questa esercitazione, lasciare tutti i tipi selezionati.

  I tipi di classificazione sono:

   |OS  |type  |
   |---------|---------|
   |Windows     | Aggiornamenti critici</br>Aggiornamenti della sicurezza</br>Aggiornamenti cumulativi</br>Feature Pack</br>Service Pack</br>Aggiornamenti della definizione</br>Strumenti</br>Aggiornamenti        |
   |Linux     | Aggiornamenti critici e della sicurezza</br>Altri aggiornamenti       |

   Per una descrizione dei tipi di classificazione, vedere le [classificazioni degli aggiornamenti](automation-update-management.md#update-classifications).

* **Includi/Escludi aggiornamenti**: apre la pagina **Includi/Escludi**. Gli aggiornamenti da includere o escludere si trovano in schede separate. Per altre informazioni sulla modalità di gestione dell'inclusione, vedere il [comportamento dell'inclusione](automation-update-management.md#inclusion-behavior)

* **Impostazioni di pianificazione**: consente di aprire la pagina **Impostazioni di pianificazione**. L'ora di inizio predefinita è 30 minuti dopo il momento corrente. È possibile impostare l'ora di inizio su qualsiasi orario a partire da 10 minuti dal momento corrente.

   È anche possibile specificare se eseguire la distribuzione una sola volta o impostare una pianificazione ricorrente. In **Ricorrenza** selezionare **Una sola volta**. Mantenere l'impostazione predefinita di 1 giorno e selezionare **OK**. Viene configurata una pianificazione ricorrente.

* **Pre-script e post-script**: selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire i pre-script e i post-script](pre-post-scripts.md).
* **Finestra di manutenzione (minuti)**: lasciare il valore predefinito. È possibile impostare il periodo di tempo nel quale eseguire la distribuzione di aggiornamenti. Questa impostazione consente di garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

* **Opzioni di riavvio**: questa impostazione determina come vengono gestiti i riavvii. Le opzioni disponibili sono:
  * Riavvia se necessario (opzione predefinita)
  * Riavvia sempre
  * Non riavviare mai
  * Riavvia solamente: gli aggiornamenti non verranno installati

Al termine della configurazione della pianificazione, selezionare **Crea**.

![Riquadro di impostazioni della pianificazione di aggiornamenti](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Verrà nuovamente visualizzato il dashboard dello stato. Selezionare **Distribuzioni di aggiornamenti pianificate** per visualizzare la pianificazione della distribuzione creata.

> [!NOTE]
> Gestione aggiornamenti supporta la distribuzione degli aggiornamenti proprietari e il pre-download di patch. Ciò richiede modifiche dei sistemi sui quali sono applicate le patch. Vedere la sezione sul [supporto del pre-download di aggiornamenti proprietari](automation-update-management.md#firstparty-predownload) per vedere come configurare queste impostazioni nei propri sistemi.

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** in **Gestione aggiornamenti**. Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione, se questa ha esito positivo, lo stato diventa **Completato**. Quando si verificano errori in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.

Selezionare la distribuzione di aggiornamenti completata per visualizzare il dashboard della distribuzione.

![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

In **Risultati aggiornamento** è disponibile un riepilogo che fornisce il numero totale di aggiornamenti e i risultati della distribuzione nella macchina virtuale. La tabella a destra offre una suddivisione dettagliata di ogni aggiornamento e dei risultati dell'installazione.

L'elenco seguente mostra i valori disponibili:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato a causa di tempo disponibile non sufficiente basato sulla durata della finestra di manutenzione specificata.
* **Completato**: l'aggiornamento è stato completato.
* **Non riuscito**: l'aggiornamento non è riuscito.

Selezionare **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Selezionare **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

Dopo il completamento della distribuzione dell'aggiornamento, viene inviato un messaggio simile a quello nell'esempio seguente per indicare la riuscita della distribuzione:

![Configurare il gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Caricare una VM per Gestione aggiornamenti
> * Visualizzare una valutazione degli aggiornamenti
> * Configurare gli avvisi
> * Pianificare la distribuzione degli aggiornamenti
> * Visualizzare i risultati di una distribuzione

Passare alla panoramica della soluzione Gestione aggiornamenti.

> [!div class="nextstepaction"]
> [Soluzione Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
