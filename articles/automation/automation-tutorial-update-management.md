---
title: Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure in Automazione di Azure
description: Questo articolo illustra come usare Gestione aggiornamenti per gestire aggiornamenti e patch per le VM di Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 79cffa7aedd0fc04dd4a747ef28bc67cacf37905
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204889"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure

Questo articolo descrive come usare la funzionalità [Gestione aggiornamenti](automation-update-management.md) di Automazione di Azure per gestire aggiornamenti e patch per le VM di Azure. Per informazioni sui prezzi, vedere [Prezzi di Automazione per la gestione degli aggiornamenti](https://azure.microsoft.com/pricing/details/automation/).

> [!NOTE]
> Gestione aggiornamenti supporta la distribuzione degli aggiornamenti proprietari e il pre-download di patch. Questo supporto richiede alcune modifiche nei sistemi a cui vengono applicate le patch. Per informazioni su come configurare queste impostazioni nei sistemi in uso, vedere [Configurare le impostazioni di Windows Update per Gestione aggiornamenti di Automazione di Azure](automation-configure-windows-update.md).

Prima di usare le procedure descritte in questo articolo, assicurarsi di aver abilitato Gestione aggiornamenti nelle macchine virtuali tramite una di queste tecniche:

* [Abilitare Gestione aggiornamenti da un account di Automazione](automation-onboard-solutions-from-automation-account.md)
* [Abilitare Gestione aggiornamenti dal portale di Azure](automation-onboard-solutions-from-browse.md)
* [Abilitare Gestione aggiornamenti da un runbook](automation-onboard-solutions.md)
* [Abilitare Gestione aggiornamenti da una macchina virtuale di Azure](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitare l'ambito della distribuzione

Gestione aggiornamenti usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer nei quali ricevere gli aggiornamenti. Per altre informazioni, vedere [Limitare l'ambito di distribuzione di Gestione aggiornamenti](automation-scope-configurations-update-management.md).

## <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Per visualizzare una valutazione degli aggiornamenti:

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**. 

2. Gli aggiornamenti per l'ambiente in uso sono elencati nella pagina Gestione aggiornamenti. Se alcuni aggiornamenti vengono identificati come mancanti, il relativo elenco viene visualizzato nella scheda **Aggiornamenti mancanti**.

3. In **Collegamento alle informazioni** selezionare il collegamento per un aggiornamento per aprire l'articolo del supporto tecnico con informazioni importanti sull'aggiornamento.

    ![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Fare clic in altro punto dell'area dell'aggiornamento per aprire il riquadro Ricerca log. La query per la ricerca log è predefinita per tale specifico aggiornamento. È possibile modificare questa query o creare una query personalizzata per visualizzare informazioni dettagliate.

    ![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurare gli avvisi

Eseguire la procedura sotto riportata per configurare avvisi di notifica dello stato della distribuzione di un aggiornamento:

1. Nella scheda **Monitoraggio** dell'account di Automazione passare ad **Avvisi** e fare clic su **+ Nuova regola di avviso**.

2. Nella pagina Crea regola di avviso l'account di Automazione è già selezionato come risorsa. Per cambiarlo, fare clic su **Modifica risorsa**. 

3. Nella pagina Selezionare una risorsa, scegliere **Account di Automazione** nell'elenco a discesa **Filtra per tipo di risorsa**. 

4. Selezionare l'account di Automazione da configurare e quindi fare clic su **Fine**.

5. Fare clic su **Aggiungi condizione** e selezionare il segnale appropriato per la distribuzione degli aggiornamenti. La tabella seguente mostra i dettagli dei due segnali disponibili.

    |Nome segnale|Dimensioni|Descrizione
    |---|---|---|
    |`Total Update Deployment Runs`|- Nome della distribuzione di aggiornamenti<br>- Stato    |Avvisi sullo stato generale di una distribuzione di aggiornamenti.|
    |`Total Update Deployment Machine Runs`|- Nome della distribuzione di aggiornamenti</br>- Stato</br>- Computer di destinazione</br>- ID di esecuzione della distribuzione di aggiornamenti    |Avvisi sullo stato di una distribuzione di aggiornamenti destinata a computer specifici.|

6. Per una dimensione, selezionare un valore valido dall'elenco. Se il valore voluto non è presente nell'elenco, fare clic su **\+** accanto alla dimensione e digitare il nome personalizzato. Selezionare quindi il valore da cercare. Se si desidera selezionare tutti i valori per una dimensione, fare clic sul pulsante **Seleziona \*** . Se non si sceglie un valore per la dimensione, Gestione aggiornamenti ignorerà la dimensione.

    ![Configurare la logica dei segnali](./media/automation-tutorial-update-management/signal-logic.png)

7. In **Logica avvisi** immettere un valore nei campi **Aggregazione temporale** e **Soglia** e quindi fare clic su **Fine**.

8. Nel riquadro successivo immettere un nome e una descrizione per l'avviso.

9. Impostare il campo **Gravità** su **Informazioni (gravità 2)** per un'esecuzione riuscita o su **Informazioni (gravità 1)** per un'esecuzione non riuscita.

    ![Configurare la logica dei segnali](./media/automation-tutorial-update-management/define-alert-details.png)

10. Fare clic su **Sì** o su **No**, a seconda che si voglia abilitare la regola di avviso o meno.

11. Se non si vogliono avvisi per questa regola, selezionare **Elimina avvisi**.

## <a name="configure-action-groups-for-your-alerts"></a>Configurare gruppi di azioni per gli avvisi

Dopo aver configurato gli avvisi, è possibile configurare un gruppo di azioni da usare in più avvisi. Le azioni possono includere notifiche tramite posta elettronica, runbook, webhook e molto altro ancora. Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

1. Selezionare un avviso e quindi selezionare **Crea nuovo** in **Gruppi di azioni**. 

2. Immettere un nome completo e un nome breve per il gruppo di azioni. Gestione aggiornamenti usa il nome breve quando invia notifiche usando il gruppo specificato.

3. In **Azioni** immettere un nome che specifichi l'azione, ad esempio **Notifica via posta elettronica**. 

4. Per **Tipo azione** selezionare il tipo appropriato, ad esempio **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce). 

5. Fare clic su **Modifica dettagli**.

6. Compilare il riquadro per il tipo di azione. Se ad esempio si usa **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce), immettere un nome di azione, selezionare la casella di controllo **Posta elettronica**, immettere un indirizzo di posta elettronica valido e quindi fare clic su **OK**.

    ![Configurare un gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. Nel riquadro Aggiungi gruppo di azioni fare clic su **OK**.

8. Per i messaggi di posta elettronica di avviso è possibile personalizzare l'oggetto. Selezionare **Personalizza azioni** in **Crea regola** e quindi selezionare **Oggetto messaggio di posta elettronica**. 

9. Al termine, fare clic su **Crea regola di avviso**. 

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Quando si pianifica una distribuzione degli aggiornamenti, viene creata una risorsa di tipo [pianificazione](shared-resources/schedules.md) collegata al runbook **Patch-MicrosoftOMSComputers** che gestisce la distribuzione degli aggiornamenti nei computer di destinazione. È necessario pianificare una distribuzione che rispetti la pianificazione delle versioni e l'intervallo di servizio per installare gli aggiornamenti. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

>[!NOTE]
>Se si elimina la risorsa di tipo pianificazione dal portale di Azure o si usa PowerShell dopo aver creato la distribuzione, l'eliminazione interrompe la distribuzione degli aggiornamenti pianificati e viene visualizzato un errore quando si prova a riconfigurare la risorsa di pianificazione dal portale. È possibile eliminare la risorsa di tipo pianificazione solo eliminando la pianificazione di distribuzione corrispondente.  

Per pianificare una nuova distribuzione di aggiornamenti:

1. Nell'account di Automazione passare a **Gestione aggiornamenti** in **Gestione aggiornamenti** e quindi selezionare **Pianifica la distribuzione di aggiornamenti**.

2. In **Nuova distribuzione di aggiornamenti** usare il campo **Nome** per immettere un nome univoco per la distribuzione.

3. Selezionare il sistema operativo di destinazione per la distribuzione degli aggiornamenti.

4. Nell'area **Gruppi da aggiornare (anteprima)** definire una query che combini sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per altre informazioni, vedere [Usare i gruppi dinamici con Gestione aggiornamenti](automation-update-management-groups.md).

5. Nell'area **Computer da aggiornare** selezionare una ricerca salvata o un gruppo importato oppure scegliere **Computer** dall'elenco a discesa e selezionare computer singoli. Con questa opzione è possibile visualizzare l'idoneità dell'agente di Log Analytics per ogni computer. Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../azure-monitor/platform/computer-groups.md).

6. Usare l'area **Classificazioni aggiornamenti** per specificare [classificazioni degli aggiornamenti](automation-view-update-assessments.md#work-with-update-classifications) per i prodotti. Per ogni prodotto, deselezionare tutte le classificazioni degli aggiornamenti supportate, tranne quelle da includere nella distribuzione degli aggiornamenti.

7. Usare l'area **Includi/Escludi aggiornamenti** per selezionare aggiornamenti specifici per la distribuzione. La pagina Includi/Escludi visualizza gli aggiornamenti in base agli ID degli articoli della Knowledge Base da includere o escludere. 
    
   > [!IMPORTANT]
   > Tenere presente che le esclusioni eseguono l'override delle inclusioni. Se, ad esempio, si definisce la regola di esclusione `*`, Gestione aggiornamenti esclude tutte le patch o i pacchetti dall'installazione. Le patch escluse vengono ancora visualizzate come mancanti dai computer. Per i computer Linux, se si include un pacchetto contenente un pacchetto dipendente che è stato escluso, Gestione aggiornamenti non installa il pacchetto principale.

   > [!NOTE]
   > Non è possibile indicare aggiornamenti sostituiti perché vengano inclusi nella distribuzione degli aggiornamenti.

8. Selezionare **Impostazioni pianificazione**. L'ora di inizio predefinita è 30 minuti dopo il momento corrente. È possibile impostare l'ora di inizio su qualsiasi orario a partire da 10 minuti dal momento corrente.

9. Usare il campo **Ricorrenza** per specificare se la distribuzione viene eseguita una sola volta o in base a una pianificazione ricorrente e quindi fare clic su **OK**.

10. Nell'area **Pre-script e post-script (anteprima)** selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire pre-script e post-script](pre-post-scripts.md).
    
11. Usare il campo **Finestra di manutenzione (minuti)** per specificare la quantità di tempo consentita per l'installazione di aggiornamenti. Quando si specifica una finestra di manutenzione, tenere presenti i dettagli seguenti:

    * Le finestre di manutenzione controllano la quantità di aggiornamenti che vengono installati.
    * Gestione aggiornamenti non interrompe l'installazione di nuovi aggiornamenti se si avvicina la fine di una finestra di manutenzione.
    * Gestione aggiornamenti non termina gli aggiornamenti in corso se viene superata la finestra di manutenzione.
    * Se la finestra di manutenzione viene superata in Windows, il motivo è in genere dovuto all'aggiornamento di un Service Pack la cui installazione impiega molto tempo.

    > [!NOTE]
    > Per evitare che gli aggiornamenti vengano applicati al di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto `Unattended-Upgrade` in modo da disabilitare gli aggiornamenti automatici. Per informazioni su come configurare il pacchetto, vedere l'[argomento sugli aggiornamenti automatici nella guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Usare il campo **Opzioni di riavvio** per specificare la modalità di gestione dei riavvii durante la distribuzione. Sono disponibili le opzioni seguenti: 
    * Riavvia se necessario (impostazione predefinita)
    * Riavvia sempre
    * Non riavviare mai
    * Solo riavvio - Gli aggiornamenti non verranno installati

    > [!NOTE]
    > Le chiavi del Registro di sistema elencate in [Chiavi del Registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare un evento di riavvio se le **Opzioni di riavvio** sono impostate su **Non riavviare mai**.

13. Al termine della configurazione della pianificazione delle distribuzioni, fare clic su **Crea**.

    ![Riquadro di impostazioni della pianificazione di aggiornamenti](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Verrà nuovamente visualizzato il dashboard dello stato. Selezionare **Distribuzioni di aggiornamenti pianificate** per visualizzare la pianificazione di distribuzioni creata.

## <a name="schedule-an-update-deployment-programmatically"></a>Pianificare una distribuzione di aggiornamenti a livello di codice

Per informazioni su come creare una distribuzione di aggiornamenti con l'API REST, vedere [Configurazione degli aggiornamenti software - Creazione](/rest/api/automation/softwareupdateconfigurations/create). 

È possibile usare un runbook di esempio per creare una distribuzione di aggiornamenti settimanale. Per altre informazioni su questo runbook, vedere [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (Creare una distribuzione di aggiornamenti settimanale per una o più macchine virtuali in un gruppo di risorse).

## <a name="check-deployment-status"></a>Controllare lo stato della distribuzione

Dopo avere avviato la distribuzione pianificata, è possibile visualizzarne lo stato nella scheda **Distribuzioni di aggiornamenti** in **Gestione aggiornamenti**. Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione, se questa ha esito positivo, lo stato passa a **Completata**. Se si verificano errori in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.

## <a name="view-results-of-a-completed-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti completata

Al termine della distribuzione, è possibile selezionarla per visualizzarne il dashboard.

![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

In **Risultati aggiornamento** è disponibile un riepilogo che indica il numero totale di aggiornamenti e i risultati della distribuzione nelle macchine virtuali di destinazione. La tabella a destra offre una suddivisione dettagliata degli aggiornamenti e dei risultati dell'installazione per ognuno.

I valori disponibili sono:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato perché il tempo disponibile non era sufficiente, in base alla durata della finestra di manutenzione specificata.
* **Non selezionato**: l'aggiornamento non è stato selezionato per la distribuzione.
* **Completato**: l'aggiornamento è stato completato.
* **Non riuscito**: l'aggiornamento non è riuscito.

Selezionare **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Selezionare **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nelle macchine virtuali di destinazione.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="view-the-deployment-alert"></a>Visualizzazione dell'avviso sulla distribuzione

Al termine della distribuzione degli aggiornamenti, viene visualizzato l'avviso specificato durante la configurazione della distribuzione. Di seguito è riportato un messaggio di posta elettronica di esempio che conferma una patch.

![Configurare il gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle configurazioni dell'ambito, vedere [Limitare l'ambito di distribuzione di Gestione aggiornamenti](automation-scope-configurations-update-management.md).
* Se è necessario eseguire ricerche nei log archiviati nell'area di lavoro Log Analytics, vedere [Ricerche nei log di Monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md).
* Al termine delle distribuzioni, vedere [Scollegare l'area di lavoro dall'account di Automazione per Gestione aggiornamenti](automation-unlink-workspace-update-management.md).
* Per eliminare macchine virtuali da Gestione aggiornamenti, vedere [Rimuovere macchine virtuali da Gestione aggiornamenti](automation-remove-vms-from-update-management.md).
* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi di Gestione aggiornamenti](troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per risolvere problemi relativi all'agente di aggiornamento Linux, vedere[Risolvere i problemi dell'agente di aggiornamento Linux](troubleshoot/update-agent-issues-linux.md).