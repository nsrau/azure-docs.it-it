---
title: Come creare distribuzioni di aggiornamenti per Gestione aggiornamenti di automazione di Azure
description: Questo articolo descrive come pianificare le distribuzioni degli aggiornamenti e controllarne lo stato.
services: automation
ms.subservice: update-management
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 4336ba272dd83ad2a35060c1c7524a564b928484
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717694"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Come distribuire gli aggiornamenti ed esaminare i risultati

Questo articolo descrive come pianificare una distribuzione degli aggiornamenti ed esaminare il processo dopo il completamento della distribuzione.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Quando si pianifica una distribuzione degli aggiornamenti, viene creata una risorsa di tipo [pianificazione](../shared-resources/schedules.md) collegata al runbook **Patch-MicrosoftOMSComputers** che gestisce la distribuzione degli aggiornamenti nei computer di destinazione. È necessario pianificare una distribuzione che rispetti la pianificazione delle versioni e l'intervallo di servizio per installare gli aggiornamenti. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

>[!NOTE]
>Se si elimina la risorsa di tipo pianificazione dal portale di Azure o si usa PowerShell dopo aver creato la distribuzione, l'eliminazione interrompe la distribuzione degli aggiornamenti pianificati e viene visualizzato un errore quando si prova a riconfigurare la risorsa di pianificazione dal portale. È possibile eliminare la risorsa di tipo pianificazione solo eliminando la pianificazione di distribuzione corrispondente.  

Per pianificare una nuova distribuzione di aggiornamenti:

1. Nell'account di Automazione passare a **Gestione aggiornamenti** in **Gestione aggiornamenti** e quindi selezionare **Pianifica la distribuzione di aggiornamenti**.

2. In **nuova distribuzione aggiornamenti**, nel campo **nome** immettere un nome univoco per la distribuzione.

3. Selezionare il sistema operativo di destinazione per la distribuzione degli aggiornamenti.

4. Nell'area **Gruppi da aggiornare (anteprima)** definire una query che combini sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per altre informazioni, vedere [Usare i gruppi dinamici con Gestione aggiornamenti](update-mgmt-groups.md).

5. Nell'area **Computer da aggiornare** selezionare una ricerca salvata o un gruppo importato oppure scegliere **Computer** dall'elenco a discesa e selezionare computer singoli. Con questa opzione è possibile visualizzare l'idoneità dell'agente di Log Analytics per ogni computer. Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../../azure-monitor/platform/computer-groups.md).

6. Usare l'area **Classificazioni aggiornamenti** per specificare [classificazioni degli aggiornamenti](update-mgmt-view-update-assessments.md#work-with-update-classifications) per i prodotti. Per ogni prodotto, deselezionare tutte le classificazioni degli aggiornamenti supportate, tranne quelle da includere nella distribuzione degli aggiornamenti.

    Se la distribuzione ha lo scopo di applicare solo un set di aggiornamenti selezionato, è necessario deselezionare tutte le classificazioni degli aggiornamenti preselezionate quando si configura l'opzione **Includi/Escludi aggiornamenti** come descritto nel passaggio successivo. In questo modo si garantisce che nei computer di destinazione siano installati solo gli aggiornamenti specificati per *includere* in questa distribuzione.

7. Usare l'area **Includi/Escludi aggiornamenti** per aggiungere o escludere gli aggiornamenti selezionati dalla distribuzione. Nella pagina **Includi/Escludi** immettere i numeri ID degli articoli della KB da includere o escludere.

   > [!IMPORTANT]
   > Tenere presente che le esclusioni eseguono l'override delle inclusioni. Se, ad esempio, si definisce la regola di esclusione `*`, Gestione aggiornamenti esclude tutte le patch o i pacchetti dall'installazione. Le patch escluse vengono ancora visualizzate come mancanti dai computer. Per i computer Linux, se si include un pacchetto contenente un pacchetto dipendente che è stato escluso, Gestione aggiornamenti non installa il pacchetto principale.

   > [!NOTE]
   > Non è possibile indicare aggiornamenti sostituiti perché vengano inclusi nella distribuzione degli aggiornamenti.

8. Selezionare **Impostazioni pianificazione**. L'ora di inizio predefinita è 30 minuti dopo il momento corrente. È possibile impostare l'ora di inizio su qualsiasi orario a partire da 10 minuti dal momento corrente.

9. Usare il campo **ricorrenza** per specificare se la distribuzione viene eseguita una sola volta o utilizza una pianificazione ricorrente, quindi selezionare **OK**.

10. Nell'area **Pre-script e post-script (anteprima)** selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire pre-script e post-script](update-mgmt-pre-post-scripts.md).
    
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

13. Al termine della configurazione della pianificazione della distribuzione, selezionare **Crea**.

    ![Riquadro di impostazioni della pianificazione di aggiornamenti](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. Verrà nuovamente visualizzato il dashboard dello stato. Selezionare **Distribuzioni di aggiornamenti pianificate** per visualizzare la pianificazione di distribuzioni creata.

## <a name="schedule-an-update-deployment-programmatically"></a>Pianificare una distribuzione di aggiornamenti a livello di codice

Per informazioni su come creare una distribuzione di aggiornamenti con l'API REST, vedere [Configurazione degli aggiornamenti software - Creazione](/rest/api/automation/softwareupdateconfigurations/create).

È possibile usare un runbook di esempio per creare una distribuzione di aggiornamenti settimanale. Per altre informazioni su questo runbook, vedere [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (Creare una distribuzione di aggiornamenti settimanale per una o più macchine virtuali in un gruppo di risorse).

## <a name="check-deployment-status"></a>Controllare lo stato della distribuzione

Dopo avere avviato la distribuzione pianificata, è possibile visualizzarne lo stato nella scheda **Distribuzioni di aggiornamenti** in **Gestione aggiornamenti**. Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione, se questa ha esito positivo, lo stato passa a **Completata**. Se si verificano errori in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.

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