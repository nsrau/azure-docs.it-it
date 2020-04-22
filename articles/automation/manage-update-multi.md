---
title: Gestire gli aggiornamenti per più macchine virtuali Azure
description: Questo articolo descrive come gestire gli aggiornamenti per le macchine virtuali di Azure e non di Azure.This article describes how to manage updates for Azure and non-Azure virtual machines.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 5376562d9df35539a33f6746b387a1ff7083b8f1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676435"
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Gestire gli aggiornamenti per più macchine virtuali Azure

È possibile usare Gestione aggiornamenti di Automazione di Azure per gestire gli aggiornamenti e le patch per le macchine virtuali Windows e Linux.You can use Azure Automation Update Management to manage updates and patches for your Windows and Linux virtual machines. Dall'account di [Automazione di Azure](automation-offering-get-started.md) è possibile:

- Eseguire l'onboarding di macchine virtuali.
- Valutare lo stato degli aggiornamenti disponibili.
- Pianificare l'installazione degli aggiornamenti necessari.
- Esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente a tutte le macchine virtuali per le quali è abilitata la gestione degli aggiornamenti.

Per informazioni sui requisiti di sistema per la gestione degli aggiornamenti, vedere [Requisiti del client di gestione degli aggiornamenti](automation-update-management.md#clients).

## <a name="prerequisites"></a>Prerequisiti

* Una macchina virtuale o un computer con uno dei sistemi operativi supportati installato.
* Accesso a un repository di aggiornamento per le macchine virtuali Linux in onboarding per la gestione degli aggiornamenti.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Abilitare Gestione aggiornamenti per le macchine virtuali di Azure

Nel portale di Azure aprire l'account di Automazione e selezionare **Gestione aggiornamenti**.

Selezionare **Aggiungi macchina virtuale di Azure**.

![Scheda per l'aggiunta di una VM di Azure](./media/manage-update-multi/update-onboard-vm.png)

Selezionare una macchina virtuale da caricare.

In **Abilita Gestione aggiornamenti** selezionare **Abilita** per eseguire l'onboarding della macchina virtuale.

![Finestra di dialogo Abilita Gestione aggiornamenti](./media/manage-update-multi/update-enable.png)

Al termine dell'onboarding, Gestione aggiornamenti è abilitata per la macchina virtuale.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Abilitare Gestione aggiornamenti per computer e macchine virtuali non di Azure

L'agente di Log Analytics per Windows e Linux deve essere installato nelle macchine virtuali in esecuzione nella rete aziendale o in un altro ambiente cloud per abilitarle con Gestione aggiornamenti. Per informazioni sui requisiti di sistema e sui metodi supportati per distribuire l'agente in computer ospitati all'esterno di Azure, vedere [Panoramica dell'agente log Analytics.](../azure-monitor/platform/log-analytics-agent.md)

## <a name="view-computers-attached-to-your-automation-account"></a>Visualizzare i computer associati all'account di Automazione

Dopo avere abilitato Gestione aggiornamenti per i computer, è possibile visualizzarne le informazioni facendo clic su **Computer**. È possibile visualizzare informazioni su nome del computer, stato di conformità, ambiente, tipo di sistema operativo, aggiornamenti critici e della sicurezza installati, altri aggiornamenti installati e idoneità dell'agente di aggiornamento per i computer in uso.

  ![Visualizzare la scheda Computer](./media/manage-update-multi/update-computers-tab.png)

È possibile che i computer abilitati di recente per Gestione aggiornamenti non siano stati ancora valutati. Lo stato di conformità `Not assessed`per tali computer è . Di seguito è riportato un elenco di valori possibili per lo stato di conformità:

- `Compliant`: computer in cui non mancano aggiornamenti critici o di sicurezza.
- `Non-compliant`: computer in cui manca almeno un aggiornamento critico o di protezione.
- `Not assessed`: i dati di valutazione degli aggiornamenti non sono stati ricevuti dal computer entro l'intervallo di tempo previsto. Per i computer Linux, l'intervallo di tempo previsto è l'ultima ora. Per i computer Windows, l'intervallo di tempo previsto è le ultime 12 ore.

Per visualizzare lo stato dell'agente, selezionare il collegamento nella colonna **Aggiorna disponibilità agente.** Se si seleziona questa opzione, viene visualizzata la pagina Ruolo di lavoro ibrido in cui è visualizzato lo stato del ruolo di lavoro ibrido. L'immagine seguente illustra un esempio di un agente che non è stato connesso a Gestione aggiornamenti per un lungo periodo di tempo:

![Visualizzare la scheda Computer](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Visualizzare una valutazione degli aggiornamenti

Dopo aver abilitato Gestione aggiornamenti, viene visualizzato il riquadro Gestione aggiornamenti. È possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

## <a name="collect-data"></a>Raccogliere i dati

Gli agenti installati in macchine virtuali e computer raccolgono i dati sugli aggiornamenti. e li inviano a Gestione aggiornamenti di Azure.

### <a name="supported-agents"></a>Agenti supportati

La tabella seguente descrive le origini connesse supportate da questa soluzione:

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e quindi avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e quindi avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso. |
| Account di archiviazione di Azure |No |Archiviazione di Azure non include informazioni sugli aggiornamenti del sistema. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Dopo che un computer ha completato un'analisi per la conformità degli aggiornamenti, l'agente inoltra le informazioni in blocco ai log di Monitoraggio di Azure.After a computer completes a scan for update compliance, the agent forwards the information in bulk to Azure Monitor logs. In un computer Windows l'analisi della conformità viene eseguita ogni 12 ore per impostazione predefinita.

Oltre all'analisi pianificata, l'analisi della conformità degli aggiornamenti viene avviata entro 15 minuti dal momento del riavvio di MMA e prima e dopo l'installazione degli aggiornamenti.

Per un computer Linux, l'analisi di conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente MMA viene riavviato, viene avviata un'analisi della conformità entro 15 minuti.

La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

>[!NOTE]
>Quando si pianifica una distribuzione degli aggiornamenti, viene creata una risorsa di tipo [pianificazione](shared-resources/schedules.md) collegata al runbook **Patch-MicrosoftOMSComputers** che gestisce la distribuzione degli aggiornamenti nei computer di destinazione. Se si elimina la risorsa di tipo pianificazione dal portale di Azure o si usa PowerShell dopo aver creato la distribuzione, la distribuzione degli aggiornamenti pianificati viene interrotta e viene visualizzato un errore quando si prova a riconfigurarla dal portale. È possibile eliminare la risorsa di tipo pianificazione solo eliminando la pianificazione di distribuzione corrispondente.
>

Per pianificare una nuova distribuzione di aggiornamenti per una o più macchine virtuali, selezionare **Pianifica la distribuzione di aggiornamenti** in **Gestione aggiornamenti**.

Nel riquadro **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

- **Nome**: immettere un nome univoco per identificare la distribuzione di aggiornamenti.
- **Sistema operativo**: selezionare **Windows** o **Linux**.
- **Gruppi da aggiornare:** definire una query in base a una combinazione di sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per le macchine virtuali non Azure, le ricerche salvate vengono usate per creare un gruppo dinamico da includere nella distribuzione. Per ulteriori informazioni, vedere [Gruppi dinamici](automation-update-management-groups.md).
- **Computer da aggiornare**: selezionare una ricerca salvata, gruppo importato, o selezionare le macchine virtuali, per scegliere i computer che si desidera aggiornare.

   >[!NOTE]
   >Se si seleziona l'opzione Ricerca salvata, non viene restituito solo le identità del computer, ma solo i nomi. Se si dispone di più macchine virtuali con lo stesso nome in più gruppi di risorse, vengono restituite nei risultati. L'uso dell'opzione **Gruppi da aggiornare** è consigliato per assicurarsi di includere macchine virtuali univoche corrispondenti ai criteri.

   Se si sceglie **Macchine**, la disponibilità del computer viene visualizzata nella colonna **Aggiorna preparazione agente.** È possibile visualizzare lo stato di integrità del computer prima di pianificare la distribuzione degli aggiornamenti. Per informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere Gruppi di computer nei log di [Monitoraggio di AzureTo](../azure-monitor/platform/computer-groups.md) learn about the different methods of creating computer groups in Azure Monitor logs, see Computer groups in Azure Monitor logs

  ![Riquadro Nuova distribuzione di aggiornamenti](./media/manage-update-multi/update-select-computers.png)

- **Classificazioni aggiornamenti**: selezionare i tipi di software da includere nella distribuzione di aggiornamenti. Per una descrizione dei tipi di classificazione, vedere [Aggiornare le classificazioni](automation-view-update-assessments.md#update-classifications). I tipi di classificazione sono:
  - Aggiornamenti critici
  - Aggiornamenti per la sicurezza
  - Aggiornamenti cumulativi
  - Feature Pack
  - Service Pack
  - Aggiornamenti della definizione
  - Strumenti
  - Aggiornamenti

- **Includi/Escludi aggiornamenti**: apre la pagina Includi/Escludi. Gli aggiornamenti da includere o escludere si trovano in schede separate. Per ulteriori informazioni sulla modalità di gestione dell'inclusione, vedere [Pianificazione di una distribuzione](automation-tutorial-update-management.md#schedule-an-update-deployment)di aggiornamento .

> [!NOTE]
> Tenere presente che le esclusioni eseguono l'override delle inclusioni. Se ad esempio si definisce una regola di esclusione `*`, non vengono installate patch o pacchetti perché vengono tutti esclusi. Le patch escluse vengono ancora visualizzate come mancanti dal computer. Per i computer Linux, se un pacchetto è incluso, ma ha un pacchetto dipendente che è stato escluso, il pacchetto non viene installato.

> [!NOTE]
> Non è possibile includere aggiornamenti che sono stati sostituiti nella distribuzione degli aggiornamenti.
>

- **Impostazioni pianificazione**: è possibile accettare la data e l'ora predefinite, ossia 30 minuti dopo l'ora corrente, ma anche specificare un momento diverso.

   Si può anche specificare se la distribuzione deve essere eseguita una sola volta o in base a una pianificazione ricorrente. Per configurare una pianificazione ricorrente, selezionare **Ricorrente** in **Ricorrenza**.

   ![Finestra di dialogo Impostazioni pianificazione](./media/manage-update-multi/update-set-schedule.png)

- **Pre-script e post-script**: selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire i pre-script e i post-script](pre-post-scripts.md).
- **Finestra di manutenzione (minuti)**: specificare il periodo di tempo in cui deve avvenire la distribuzione di aggiornamenti. Questa impostazione consente di garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

- **Controllo riavvio:** questa impostazione determina come vengono gestiti i riavvii per la distribuzione degli aggiornamenti.

   |Opzione|Descrizione|
   |---|---|
   |Riavvia se necessario| **(Impostazione predefinita)** Se necessario, viene eseguito il riavvio se la finestra di manutenzione lo consente.|
   |Riavvia sempre|Il computer viene riavviato indipendentemente dal fatto che il riavvio sia richiesto o no. |
   |Non riavviare mai|I riavvi non sono eseguiti, indipendentemente dal fatto che il riavvio sia necessario.|
   |Riavvia solamente: gli aggiornamenti non verranno installati|Questa opzione ignora l'installazione degli aggiornamenti e esegue solo il riavvio.|

Dopo avere configurato la pianificazione, selezionare il pulsante **Crea** per tornare al dashboard di stato. Nella tabella **Pianificata** appare la pianificazione della distribuzione appena creata.

> [!NOTE]
> Gestione aggiornamenti supporta la distribuzione degli aggiornamenti proprietari e il pre-download di patch. Ciò richiede modifiche dei sistemi sui quali sono applicate gli aggiornamenti. Vedere la sezione sul [supporto del pre-download di aggiornamenti proprietari](automation-configure-windows-update.md#pre-download-updates) per vedere come configurare queste impostazioni nei propri sistemi.

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** in **Gestione aggiornamenti**.

Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione lo stato viene modificato in **Completata**.

Se la distribuzione di uno o più aggiornamenti ha esito negativo, lo stato sarà **Partially failed** (Operazione riuscita parzialmente).

![Stato della distribuzione di aggiornamenti](./media/manage-update-multi/update-view-results.png)

Per visualizzare il dashboard per una distribuzione di aggiornamenti, selezionare la distribuzione completata.

Il riquadro Risultati aggiornamento indica il numero totale di aggiornamenti e i risultati della distribuzione per la macchina virtuale. La tabella a destra offre una suddivisione dettagliata di ogni aggiornamento e dei risultati dell'installazione, che possono corrispondere a uno dei valori seguenti.

- `Not attempted`: l'aggiornamento non è stato installato perché era disponibile tempo insufficiente in base alla finestra di manutenzione definita.
- `Succeeded`: l'aggiornamento è riuscito.
- `Failed`: l'aggiornamento non è riuscito.

Per visualizzare tutte le voci di log create dalla distribuzione, selezionare **Tutti i log**.

Per visualizzare il flusso del processo del runbook che gestisce la distribuzione di aggiornamenti nella macchina virtuale di destinazione, selezionare il riquadro dell'output.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui registri, l'output e gli errori di Gestione aggiornamenti, vedere Eseguire query sui record di [aggiornamento per Gestione aggiornamenti.](automation-update-management-query-logs.md)
