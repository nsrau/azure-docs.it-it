---
title: Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure
description: Questo articolo offre una panoramica dell'uso di Gestione aggiornamenti di Automazione di Azure per gestire gli aggiornamenti e le patch per le VM di Azure e non di Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 62c661f75aef77117a61be7e802562e6dde17ba5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604684"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure

È possibile usare la soluzione Gestione aggiornamenti per gestire gli aggiornamenti e le patch per le macchine virtuali. In questa esercitazione viene illustrato come valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti, esaminare i risultati della distribuzione e creare un avviso per verificare che gli aggiornamenti vengano applicati correttamente.

Per informazioni sui prezzi, vedere [Prezzi di Automazione per la gestione degli aggiornamenti](https://azure.microsoft.com/pricing/details/automation/).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Visualizzare una valutazione degli aggiornamenti
> * Configurare gli avvisi
> * Pianificare la distribuzione degli aggiornamenti
> * Visualizzare i risultati di una distribuzione

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* La soluzione [Gestione aggiornamenti](automation-update-management.md) abilitata per una o più macchine virtuali.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato Gestione aggiornamenti, verrà visualizzata la pagina Gestione aggiornamenti. Se alcuni aggiornamenti vengono identificati come mancanti, il relativo elenco viene visualizzato nella scheda **Aggiornamenti mancanti**.

In **Collegamento alle informazioni** selezionare il collegamento per aprire l'articolo di supporto per l'aggiornamento. È possibile acquisire informazioni importanti sull'aggiornamento.

![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Facendo clic in altro punto dell'area dell'aggiornamento, viene aperto il riquadro Ricerca log per l'aggiornamento selezionato. La query per la ricerca log è predefinita per tale specifico aggiornamento. È possibile modificare questa query o crearne una nuova per visualizzare informazioni dettagliate sugli aggiornamenti distribuiti o mancanti nell'ambiente.

![Visualizzare lo stato degli aggiornamenti](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurare gli avvisi

In questo passaggio si apprenderà come configurare un avviso per conoscere lo stato della distribuzione di un aggiornamento.

### <a name="alert-conditions"></a>Condizioni di avviso

Nella scheda **Monitoraggio** dell'account di Automazione passare ad **Avvisi** e fare clic su **+ Nuova regola di avviso**.

L'account di Automazione è già selezionato come risorsa. Per modificarlo, fare clic su **Seleziona**. Nella pagina Selezionare una risorsa, scegliere **Account di Automazione** nell'elenco a discesa **Filtra per tipo di risorsa**. Selezionare l'account di Automazione e quindi fare clic su **Fine**.

Fare clic su **Aggiungi condizione** e selezionare il segnale appropriato per la distribuzione degli aggiornamenti. La tabella seguente mostra i dettagli dei due segnali disponibili.

|Nome segnale|Dimensioni|Descrizione|
|---|---|---|
|`Total Update Deployment Runs`|- Nome della distribuzione di aggiornamenti<br>- Stato|Questo segnale invia avvisi sullo stato generale della distribuzione di una distribuzione di aggiornamenti.|
|`Total Update Deployment Machine Runs`|- Nome della distribuzione di aggiornamenti</br>- Stato</br>- Computer di destinazione</br>- ID di esecuzione della distribuzione di aggiornamenti|Questo segnale invia avvisi sullo stato di una distribuzione di aggiornamenti destinata a computer specifici.|

Per una dimensione, selezionare un valore valido dall'elenco. Se il valore desiderato non è presente nell'elenco, fare clic sul segno **\+** accanto alla dimensione e digitare il nome personalizzato. Selezionare quindi il valore da cercare. Se si desidera selezionare tutti i valori per una dimensione, fare clic sul pulsante **Seleziona \*** . Se non si sceglie un valore per la dimensione, Gestione aggiornamenti ignorerà la dimensione.

![Configurare la logica dei segnali](./media/automation-tutorial-update-management/signal-logic.png)

In **Logica avvisi**, per **Soglia**, immettere **1**. Al termine, fare clic su **Fine**.

### <a name="alert-details"></a>Dettagli dell'avviso

In **2. Definire i dettagli dell'avviso** assegnare un nome e una descrizione per l'avviso. Impostare **Gravità** su **Informational(Sev 2)** per un'esecuzione riuscita o su **Informational(Sev 1)** per un'esecuzione non riuscita.

![Configurare la logica dei segnali](./media/automation-tutorial-update-management/define-alert-details.png)

Da **Gruppi di azioni**, selezionare **Crea nuovo**. Un gruppo di azioni è un insieme di azioni che è possibile usare in più avvisi. Le azioni possono includere notifiche tramite posta elettronica, runbook, webhook e molto altro ancora. Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

Nel campo **Nome gruppo di azioni** immettere un nome per l'avviso e un nome breve. Gestione aggiornamenti usa il nome breve anziché un nome completo del gruppo di azioni quando invia le notifiche usando il gruppo specificato.

In **Azioni** immettere un nome per l'azione, ad esempio **Notifica di posta elettronica**. In **Tipo di azione** selezionare **Messaggio di posta elettronica o SMS o Push/voce**. In **Dettagli** selezionare **Modifica dettagli**.

Nel riquadro Messaggio di posta elettronica o SMS o Push/voce specificare un nome. Selezionare la casella di controllo **Posta elettronica** e quindi immettere un indirizzo di posta elettronica valido.

![Configurare un gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/configure-email-action-group.png)

Nel riquadro Messaggio di posta elettronica o SMS o Push/voce fare clic su **OK**. Nel riquadro Aggiungi gruppo di azioni fare clic su **OK**.

Per personalizzare l'oggetto del messaggio di posta elettronica di avviso, in **Crea regola** selezionare **Oggetto del messaggio di posta elettronica** nella sezione **Personalizza azioni**. Al termine, selezionare **Crea regola di avviso**. L'avviso segnala quando una distribuzione di aggiornamenti ha esito positivo e indica i computer inclusi nell'esecuzione della distribuzione di aggiornamenti.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Pianificare quindi una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio per installare gli aggiornamenti. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

>[!NOTE]
>Quando si pianifica una distribuzione degli aggiornamenti, viene creata una risorsa di tipo [pianificazione](shared-resources/schedules.md) collegata al runbook **Patch-MicrosoftOMSComputers** che gestisce la distribuzione degli aggiornamenti nei computer di destinazione. Se si elimina la risorsa di tipo pianificazione dal portale di Azure o si usa PowerShell dopo aver creato la distribuzione, l'eliminazione interrompe la distribuzione degli aggiornamenti pianificati e viene visualizzato un errore quando si prova a riconfigurare la risorsa di pianificazione dal portale. È possibile eliminare la risorsa di tipo pianificazione solo eliminando la pianificazione di distribuzione corrispondente.  

Per pianificare una nuova distribuzione di aggiornamenti per la macchina virtuale, passare a **Gestione aggiornamenti** e quindi selezionare **Pianifica la distribuzione di aggiornamenti**.

In **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

* **Name**: immettere un nome univoco per la distribuzione degli aggiornamenti.

* **Sistema operativo**: selezionare il sistema operativo di destinazione per la distribuzione degli aggiornamenti.

* **Gruppi da aggiornare (anteprima)** : Definire una query che combina sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per altre informazioni, vedere [Gruppi dinamici](automation-update-management-groups.md).

* **Computer da aggiornare**: Selezionare una ricerca salvata o un gruppo importato oppure scegliere **Computer** dall'elenco a discesa e selezionare i singoli computer. Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **Aggiorna idoneità agente**. Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../azure-monitor/platform/computer-groups.md).

* **Classificazione aggiornamenti**: Per ogni prodotto, deselezionare tutte le classificazioni degli aggiornamenti supportate, tranne quelle da includere nella distribuzione degli aggiornamenti. Per questa esercitazione, lasciare tutti i tipi selezionati per tutti i prodotti.

  I tipi di classificazione sono:

   |OS  |Type  |
   |---------|---------|
   |Windows     | Aggiornamenti critici</br>Aggiornamenti per la sicurezza</br>Aggiornamenti cumulativi</br>Feature Pack</br>Service Pack</br>Aggiornamenti della definizione</br>Strumenti</br>Aggiornamenti<br>Driver        |
   |Linux     | Aggiornamenti critici e della sicurezza</br>Altri aggiornamenti       |

   Per una descrizione dei tipi di classificazione, vedere le [Classificazioni di aggiornamenti](automation-view-update-assessments.md#update-classifications).

* **Includi/Escludi aggiornamenti**: apre la pagina Includi/Escludi. Gli aggiornamenti da includere o escludere si trovano in schede separate specificando i numeri di ID degli articoli della Knowledge base. Quando si specificano uno o più numeri di ID, è necessario rimuovere o deselezionare tutte le classificazioni con la distribuzione degli aggiornamenti. In questo modo si garantisce che nessun altro aggiornamento venga incluso nel pacchetto di aggiornamento quando si specificano gli ID per l'aggiornamento.

> [!NOTE]
> Tenere presente che le esclusioni eseguono l'override delle inclusioni. Se ad esempio si definisce una regola di esclusione `*`, Gestione aggiornamenti non installa patch o pacchetti perché sono tutti esclusi. Le patch escluse vengono ancora visualizzate come mancanti dal computer. Per i computer Linux, se si include un pacchetto contenente un pacchetto dipendente che è stato escluso, Gestione aggiornamenti non installa il pacchetto principale.

> [!NOTE]
> Non è possibile specificare aggiornamenti che sono stati sostituiti per l'inclusione nella distribuzione degli aggiornamenti.
>

* **Impostazioni di pianificazione**: apre il riquadro Impostazioni di pianificazione. L'ora di inizio predefinita è 30 minuti dopo il momento corrente. È possibile impostare l'ora di inizio su qualsiasi orario a partire da 10 minuti dal momento corrente.

   È anche possibile specificare se eseguire la distribuzione una sola volta o impostare una pianificazione ricorrente. In **Ricorrenza** selezionare **Una sola volta**. Lasciare l'impostazione predefinita su un 1 giorno e fare clic su **OK**. Queste voci configurano una pianificazione ricorrente.

* **Pre-script e post-script**: selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire i pre-script e i post-script](pre-post-scripts.md).

* **Finestra di manutenzione (minuti)** : Lasciare il valore predefinito. Le finestre di manutenzione controllano la quantità di tempo consentito per l'installazione di aggiornamenti. Quando si specifica una finestra di manutenzione, tenere presenti i dettagli seguenti:

  * Le finestre di manutenzione controllano la quantità di aggiornamenti che vengono installati.
  * Gestione aggiornamenti non interrompe l'installazione di nuovi aggiornamenti se si avvicina la fine di una finestra di manutenzione.
  * Gestione aggiornamenti non termina gli aggiornamenti in corso se viene superata la finestra di manutenzione.
  * Se la finestra di manutenzione viene superata in Windows, il motivo è in genere dovuto all'aggiornamento di un Service Pack la cui installazione impiega molto tempo.

  > [!NOTE]
  > Per evitare che gli aggiornamenti vengano applicati al di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto Unattended-Upgrade per disabilitare gli aggiornamenti automatici. Per informazioni sulla configurazione del pacchetto, vedere l'[argomento Aggiornamenti automatici nella Guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opzioni di riavvio**: consente di specificare le opzioni per la gestione dei riavvii. Sono disponibili le opzioni seguenti:
  * Riavvia se necessario (impostazione predefinita)
  * Riavvia sempre
  * Non riavviare mai
  * Solo riavvio (gli aggiornamenti non verranno installati)

> [!NOTE]
> Le chiavi del Registro di sistema elencate in [Chiavi del Registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare un evento di riavvio se le **Opzioni di riavvio** sono impostate su **Non riavviare mai**.

Al termine della configurazione della pianificazione, fare clic su **Crea**.

![Riquadro di impostazioni della pianificazione di aggiornamenti](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Verrà nuovamente visualizzato il dashboard dello stato. Selezionare **Distribuzioni di aggiornamenti pianificate** per visualizzare la pianificazione della distribuzione appena creata.

> [!NOTE]
> Gestione aggiornamenti supporta la distribuzione degli aggiornamenti proprietari e il pre-download di patch. Questo supporto richiede alcune modifiche nei sistemi a cui vengono applicate le patch. Per informazioni su come configurare queste impostazioni nei sistemi in uso, vedere la sezione sul [supporto del pre-download di aggiornamenti proprietari](automation-configure-windows-update.md).

È anche possibile creare distribuzioni di aggiornamenti a livello di codice. Per informazioni su come creare una distribuzione di aggiornamenti con l'API REST, vedere [Configurazione degli aggiornamenti software - Creazione](/rest/api/automation/softwareupdateconfigurations/create). È anche disponibile un runbook di esempio che può essere usato per creare una distribuzione di aggiornamenti settimanale. Per altre informazioni su questo runbook, vedere [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (Creare una distribuzione di aggiornamenti settimanale per una o più macchine virtuali in un gruppo di risorse).

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** in **Gestione aggiornamenti**. Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione, se questa ha esito positivo, lo stato diventa **Completato**. Se si verificano errori in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.

Selezionare la distribuzione di aggiornamenti completata per visualizzare il dashboard corrispondente.

![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

In **Risultati aggiornamento** è disponibile un riepilogo che fornisce il numero totale di aggiornamenti e i risultati della distribuzione nella macchina virtuale. La tabella a destra offre una suddivisione dettagliata di ogni aggiornamento e dei risultati dell'installazione.

I valori disponibili sono:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato poiché, in base alla durata definita per la finestra di manutenzione, il tempo disponibile non era sufficiente.
* **Riuscito**: aggiornamento completato.
* **Non riuscito**: aggiornamento non riuscito.

Selezionare **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Selezionare **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

Quando la distribuzione degli aggiornamenti ha esito positivo, si riceve un messaggio di posta elettronica di conferma simile al seguente:

![Configurare il gruppo di azioni di posta elettronica](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Caricare una VM per Gestione aggiornamenti
> * Visualizzare una valutazione degli aggiornamenti
> * Configurare gli avvisi
> * Pianificare la distribuzione degli aggiornamenti
> * Visualizzare i risultati di una distribuzione

Passare alla panoramica della soluzione Gestione aggiornamenti.

> [!div class="nextstepaction"]
> [Soluzione Gestione aggiornamenti](automation-update-management.md)