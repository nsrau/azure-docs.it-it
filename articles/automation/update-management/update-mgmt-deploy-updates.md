---
title: Come creare distribuzioni di aggiornamenti per Gestione aggiornamenti di automazione di Azure
description: Questo articolo descrive come pianificare le distribuzioni degli aggiornamenti e controllarne lo stato.
services: automation
ms.subservice: update-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: f5f7d86ff668a151bdf83908c3199d01a0a53246
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073751"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Come distribuire gli aggiornamenti ed esaminare i risultati

Questo articolo descrive come pianificare una distribuzione degli aggiornamenti ed esaminare il processo dopo il completamento della distribuzione. È possibile configurare una distribuzione degli aggiornamenti da una macchina virtuale di Azure selezionata, dal server abilitato per l'arco selezionato o dall'account di automazione in tutti i computer e i server configurati.

In ogni scenario, la distribuzione creata ha come destinazione il computer o il server selezionato oppure, nel caso in cui venga creata una distribuzione dall'account di automazione, è possibile fare riferimento a uno o più computer. Quando si pianifica una distribuzione degli aggiornamenti da una macchina virtuale di Azure o da un server abilitato per Arc, i passaggi sono gli stessi della distribuzione dall'account di automazione, con le eccezioni seguenti:

* Il sistema operativo viene preselezionato automaticamente in base al sistema operativo del computer
* Il computer di destinazione da aggiornare è impostato automaticamente come destinazione
* Quando si configura la pianificazione, è possibile specificare l' **aggiornamento ora**, si verifica una volta o una pianificazione ricorrente.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

La pianificazione di una distribuzione di aggiornamenti crea una risorsa [Schedule](../shared-resources/schedules.md) collegata al Runbook **patch-MicrosoftOMSComputers** che gestisce la distribuzione degli aggiornamenti nel computer o nei computer di destinazione. È necessario pianificare una distribuzione che rispetti la pianificazione delle versioni e l'intervallo di servizio per installare gli aggiornamenti. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È ad esempio possibile includere aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

>[!NOTE]
>Se si elimina la risorsa di tipo pianificazione dal portale di Azure o si usa PowerShell dopo aver creato la distribuzione, l'eliminazione interrompe la distribuzione degli aggiornamenti pianificati e viene visualizzato un errore quando si prova a riconfigurare la risorsa di pianificazione dal portale. È possibile eliminare la risorsa di tipo pianificazione solo eliminando la pianificazione di distribuzione corrispondente.  

Per pianificare una nuova distribuzione degli aggiornamenti, seguire questa procedura. A seconda della risorsa selezionata, ovvero l'account di automazione, il server con Arc abilitato e la macchina virtuale di Azure, i passaggi seguenti si applicano a tutti con differenze minime durante la configurazione della pianificazione della distribuzione.

1. Nel portale per pianificare una distribuzione per:

   * Uno o più computer, passare a **account di automazione** e selezionare l'account di automazione con gestione aggiornamenti abilitati dall'elenco.
   * Per una VM di Azure, passare a **macchine virtuali** e selezionare la VM dall'elenco.
   * Per un server abilitato per Arc, passare a **Server-Azure Arc** e selezionare il server dall'elenco.

2. A seconda della risorsa selezionata, per passare a Gestione aggiornamenti:

   * Se è stato selezionato l'account di automazione, passare a **Gestione aggiornamenti** in **Gestione aggiornamenti**, quindi selezionare **Pianifica distribuzione aggiornamenti**.
   * Se è stata selezionata una macchina virtuale di Azure, passare a **guest + host Updates**e quindi selezionare **Vai a gestione aggiornamenti**.
   * Se è stato selezionato un server abilitato per Arc, passare a **Gestione aggiornamenti**e quindi selezionare **Pianifica distribuzione aggiornamenti**.

3. In **nuova distribuzione aggiornamenti**, nel campo **nome** immettere un nome univoco per la distribuzione.

4. Selezionare il sistema operativo di destinazione per la distribuzione degli aggiornamenti.

    > [!NOTE]
    > Questa opzione non è disponibile se è stata selezionata una macchina virtuale di Azure o un server abilitato per l'arco. Il sistema operativo viene identificato automaticamente.

5. Nell'area **Gruppi da aggiornare (anteprima)** definire una query che combini sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per altre informazioni, vedere [Usare i gruppi dinamici con Gestione aggiornamenti](update-mgmt-groups.md).

    > [!NOTE]
    > Questa opzione non è disponibile se è stata selezionata una macchina virtuale di Azure o un server abilitato per l'arco. Il computer viene indirizzato automaticamente alla distribuzione pianificata.

6. Nell'area **Computer da aggiornare** selezionare una ricerca salvata o un gruppo importato oppure scegliere **Computer** dall'elenco a discesa e selezionare computer singoli. Con questa opzione è possibile visualizzare l'idoneità dell'agente di Log Analytics per ogni computer. Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../../azure-monitor/platform/computer-groups.md). È possibile includere fino a un massimo di 500 computer in una distribuzione di aggiornamenti pianificati.

    > [!NOTE]
    > Questa opzione non è disponibile se è stata selezionata una macchina virtuale di Azure o un server abilitato per l'arco. Il computer viene indirizzato automaticamente alla distribuzione pianificata.

7. Usare l'area **Classificazioni aggiornamenti** per specificare [classificazioni degli aggiornamenti](update-mgmt-view-update-assessments.md#work-with-update-classifications) per i prodotti. Per ogni prodotto, deselezionare tutte le classificazioni degli aggiornamenti supportate, tranne quelle da includere nella distribuzione degli aggiornamenti.

    Se la distribuzione ha lo scopo di applicare solo un set di aggiornamenti selezionato, è necessario deselezionare tutte le classificazioni degli aggiornamenti preselezionate quando si configura l'opzione **Includi/Escludi aggiornamenti** come descritto nel passaggio successivo. In questo modo si garantisce che nei computer di destinazione siano installati solo gli aggiornamenti specificati per *includere* in questa distribuzione.

8. Usare l'area **Includi/Escludi aggiornamenti** per aggiungere o escludere gli aggiornamenti selezionati dalla distribuzione. Nella pagina **Includi/Escludi** immettere i numeri ID degli articoli della KB da includere o escludere.

   > [!IMPORTANT]
   > Tenere presente che le esclusioni eseguono l'override delle inclusioni. Se, ad esempio, si definisce la regola di esclusione `*`, Gestione aggiornamenti esclude tutte le patch o i pacchetti dall'installazione. Le patch escluse vengono ancora visualizzate come mancanti dai computer. Per i computer Linux, se si include un pacchetto contenente un pacchetto dipendente che è stato escluso, Gestione aggiornamenti non installa il pacchetto principale.

   > [!NOTE]
   > Non è possibile indicare aggiornamenti sostituiti perché vengano inclusi nella distribuzione degli aggiornamenti.

9. Selezionare **Impostazioni pianificazione**. L'ora di inizio predefinita è 30 minuti dopo il momento corrente. È possibile impostare l'ora di inizio su qualsiasi orario a partire da 10 minuti dal momento corrente.

    > [!NOTE]
    > Questa opzione è diversa se è stato selezionato un server abilitato per l'arco. È possibile selezionare **Aggiorna ora** o ora di inizio 20 minuti in futuro.

10. Utilizzare la **ricorrenza** per specificare se la distribuzione viene eseguita una sola volta o utilizza una pianificazione ricorrente, quindi selezionare **OK**.

11. Nell'area **Pre-script e post-script (anteprima)** selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire pre-script e post-script](update-mgmt-pre-post-scripts.md).

12. Usare il campo **Finestra di manutenzione (minuti)** per specificare la quantità di tempo consentita per l'installazione di aggiornamenti. Quando si specifica una finestra di manutenzione, tenere presenti i dettagli seguenti:

    * Le finestre di manutenzione controllano la quantità di aggiornamenti che vengono installati.
    * Gestione aggiornamenti non interrompe l'installazione di nuovi aggiornamenti se si avvicina la fine di una finestra di manutenzione.
    * Gestione aggiornamenti non termina gli aggiornamenti in corso se viene superata la finestra di manutenzione. Gli eventuali aggiornamenti rimanenti da installare non vengono tentati. Se questa situazione si verifica in modo coerente, è necessario rivalutare la durata della finestra di manutenzione.
    * Se la finestra di manutenzione viene superata in Windows, il motivo è in genere dovuto all'aggiornamento di un Service Pack la cui installazione impiega molto tempo.

    > [!NOTE]
    > Per evitare che gli aggiornamenti vengano applicati al di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto `Unattended-Upgrade` in modo da disabilitare gli aggiornamenti automatici. Per informazioni su come configurare il pacchetto, vedere l'[argomento sugli aggiornamenti automatici nella guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Usare il campo **Opzioni di riavvio** per specificare la modalità di gestione dei riavvii durante la distribuzione. Sono disponibili le opzioni seguenti: 
    * Riavvia se necessario (impostazione predefinita)
    * Riavvia sempre
    * Non riavviare mai
    * Solo riavvio - Gli aggiornamenti non verranno installati

    > [!NOTE]
    > Le chiavi del Registro di sistema elencate in [Chiavi del Registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare un evento di riavvio se le **Opzioni di riavvio** sono impostate su **Non riavviare mai**.

14. Al termine della configurazione della pianificazione della distribuzione, selezionare **Crea**.

    ![Riquadro di impostazioni della pianificazione di aggiornamenti](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Al termine della configurazione della pianificazione della distribuzione per un server abilitato per l'arco selezionato, selezionare **Verifica + crea**.

15. Verrà nuovamente visualizzato il dashboard dello stato. Selezionare **pianificazioni di distribuzione** per visualizzare la pianificazione della distribuzione creata.

## <a name="schedule-an-update-deployment-programmatically"></a>Pianificare una distribuzione di aggiornamenti a livello di codice

Per informazioni su come creare una distribuzione di aggiornamenti con l'API REST, vedere [Configurazione degli aggiornamenti software - Creazione](/rest/api/automation/softwareupdateconfigurations/create).

È possibile usare un runbook di esempio per creare una distribuzione di aggiornamenti settimanale. Per altre informazioni su questo runbook, vedere [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (Creare una distribuzione di aggiornamenti settimanale per una o più macchine virtuali in un gruppo di risorse).

## <a name="check-deployment-status"></a>Controllare lo stato della distribuzione

Una volta avviata la distribuzione pianificata, è possibile visualizzarne lo stato nella scheda **cronologia** in **Gestione aggiornamenti**. Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione, se questa ha esito positivo, lo stato passa a **Completata**. Se si verificano errori in uno o più aggiornamenti della distribuzione, lo stato è **non riuscito**.

## <a name="view-results-of-a-completed-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti completata

Al termine della distribuzione, è possibile selezionarlo per visualizzarne i risultati.

[![Dashboard dello stato di distribuzione dell'aggiornamento per una distribuzione specifica](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

In **Risultati aggiornamento** è disponibile un riepilogo che indica il numero totale di aggiornamenti e i risultati della distribuzione nelle macchine virtuali di destinazione. La tabella a destra offre una suddivisione dettagliata degli aggiornamenti e dei risultati dell'installazione per ognuno.

I valori disponibili sono:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato perché il tempo disponibile non era sufficiente, in base alla durata della finestra di manutenzione specificata.
* **Non selezionato**: l'aggiornamento non è stato selezionato per la distribuzione.
* **Completato**: l'aggiornamento è stato completato.
* **Non riuscito**: l'aggiornamento non è riuscito.

Selezionare **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Selezionare **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nelle macchine virtuali di destinazione.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare avvisi per notificare i risultati della distribuzione degli aggiornamenti, vedere [creare avvisi per gestione aggiornamenti](update-mgmt-configure-alerts.md).