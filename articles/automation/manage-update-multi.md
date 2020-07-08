---
title: Gestire gli aggiornamenti per più macchine virtuali in Automazione di Azure
description: Questo articolo illustra come gestire gli aggiornamenti per più macchine virtuali.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: d08afc6e501fd76167e0939633442213958f0d49
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834629"
---
# <a name="manage-updates-for-multiple-vms"></a>Gestire gli aggiornamenti per più macchine virtuali

È possibile usare Gestione aggiornamenti di Automazione di Azure per gestire gli aggiornamenti e le patch per le macchine virtuali Windows e Linux. Dall'account di [Automazione di Azure](automation-offering-get-started.md) è possibile:

- Abilitare le macchine virtuali per la gestione degli aggiornamenti.
- Valutare lo stato degli aggiornamenti disponibili.
- Pianificare l'installazione degli aggiornamenti necessari.
- Esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente a tutte le macchine virtuali per cui è abilitata la soluzione Gestione aggiornamenti.

Per informazioni sui requisiti di sistema per Gestione aggiornamenti, vedere i [requisiti di gestione aggiornamenti client](automation-update-management.md#client-requirements).

## <a name="prerequisites"></a>Prerequisiti

* Una macchina virtuale o un computer con uno dei sistemi operativi supportati installato.
* Accesso a un repository di aggiornamenti per le macchine virtuali Linux abilitate per Gestione aggiornamenti.

## <a name="enable-update-management-for-azure-vms"></a>Abilitare Gestione aggiornamenti per le macchine virtuali di Azure

1. Nel portale di Azure aprire l'account di Automazione e selezionare **Gestione aggiornamenti**.

2. Selezionare **Aggiungi macchina virtuale di Azure**.

    ![Scheda per l'aggiunta di una VM di Azure](./media/manage-update-multi/update-onboard-vm.png)

3. Selezionare una macchina virtuale da abilitare e selezionare **Abilita** in **Abilita Gestione aggiornamenti**.

    ![Finestra di dialogo Abilita Gestione aggiornamenti](./media/manage-update-multi/update-enable.png)

    Al termine dell'operazione, Gestione aggiornamenti è abilitato nella macchina virtuale.

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>Abilitare Gestione aggiornamenti per computer e macchine virtuali non di Azure

Per abilitare le macchine virtuali con Gestione aggiornamenti, è necessario installare l'agente si Log Analytics per Windows e Linux nelle macchine virtuali in esecuzione nella rete aziendale o in un altro ambiente cloud. Vedere [Panoramica dell'agente di Log Analytics](../azure-monitor/platform/log-analytics-agent.md) per esaminare i requisiti e i metodi supportati per distribuire l'agente nei computer ospitati all'esterno di Azure.

## <a name="view-computers-attached-to-your-automation-account"></a>Visualizzare i computer associati all'account di Automazione

Dopo avere abilitato Gestione aggiornamenti per i computer, è possibile visualizzarne le informazioni facendo clic su **Computer**. È possibile visualizzare informazioni su nome del computer, stato di conformità, ambiente, tipo di sistema operativo, aggiornamenti critici e della sicurezza installati, altri aggiornamenti installati e idoneità dell'agente di aggiornamento per i computer in uso.

  ![Visualizzare la scheda Computer](./media/manage-update-multi/update-computers-tab.png)

È possibile che i computer abilitati di recente per Gestione aggiornamenti non siano stati ancora valutati. Lo stato di conformità per tali computer è `Not assessed`. Di seguito è riportato un elenco di valori possibili per lo stato di conformità:

- `Compliant`: computer senza aggiornamenti critici o della sicurezza mancanti.
- `Non-compliant`: computer in cui non è presente almeno un aggiornamento critico o della sicurezza.
- `Not assessed`: i dati relativi alla valutazione dell'aggiornamento non sono stati ricevuti dal computer entro l'intervallo di tempo previsto. Per i computer Linux, l'intervallo di tempo previsto è l'ultima ora. Per i computer Windows, l'intervallo di tempo previsto sono le ultime 12 ore.

Per visualizzare lo stato dell'agente, selezionare il collegamento nella colonna **Idoneità agente di aggiornamento**. Se si seleziona questa opzione, viene visualizzato il riquadro Ruolo di lavoro ibrido in cui è visualizzato lo stato del ruolo di lavoro ibrido. L'immagine seguente illustra un esempio di un agente che non è stato connesso a Gestione aggiornamenti per un lungo periodo di tempo:

![Visualizzare la scheda Computer](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Visualizzare una valutazione degli aggiornamenti

Dopo aver abilitato Gestione aggiornamenti, verrà visualizzata la schermata Gestione aggiornamenti. È possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

## <a name="collect-data"></a>Raccogliere i dati

Gli agenti installati in macchine virtuali e computer raccolgono i dati sugli aggiornamenti. e li inviano a Gestione aggiornamenti di Azure.

### <a name="supported-agents"></a>Agenti supportati

La tabella seguente descrive le origini connesse supportate da Gestione aggiornamenti:

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e quindi avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e quindi avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso. |
| Account di archiviazione di Azure |No |Archiviazione di Azure non include informazioni sugli aggiornamenti del sistema. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Quando un computer ha completato l'analisi di conformità degli aggiornamenti, l'agente inoltra le informazioni in blocco ai log di Monitoraggio di Azure. In un computer Windows l'analisi della conformità viene eseguita ogni 12 ore per impostazione predefinita.

Oltre all'analisi pianificata, l'analisi della conformità degli aggiornamenti viene avviata entro 15 minuti dal momento del riavvio di MMA e prima e dopo l'installazione degli aggiornamenti.

Per un computer Linux, l'analisi della conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente MMA viene riavviato, viene avviata un'analisi della conformità entro 15 minuti.

La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

>[!NOTE]
>Quando si pianifica una distribuzione degli aggiornamenti, viene creata una risorsa di tipo [pianificazione](shared-resources/schedules.md) collegata al runbook **Patch-MicrosoftOMSComputers** che gestisce la distribuzione degli aggiornamenti nei computer di destinazione. Se si elimina la risorsa di tipo pianificazione dal portale di Azure o si usa PowerShell dopo aver creato la distribuzione, la distribuzione degli aggiornamenti pianificati viene interrotta e viene visualizzato un errore quando si prova a riconfigurarla dal portale. È possibile eliminare la risorsa di tipo pianificazione solo eliminando la pianificazione di distribuzione corrispondente.
>

Per pianificare una nuova distribuzione di aggiornamenti per una o più macchine virtuali, selezionare **Pianifica la distribuzione di aggiornamenti** in **Gestione aggiornamenti**.

Nel riquadro **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

- **Name**: immettere un nome univoco per identificare la distribuzione di aggiornamenti.
- **Sistema operativo**: selezionare **Windows** o **Linux**.
- **Gruppi da aggiornare**: Definire una query basata su una combinazione di sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per le macchine virtuali non di Azure, le ricerche salvate vengono usate per creare un gruppo dinamico da includere nella distribuzione. Per altre informazioni, vedere [Gruppi dinamici](automation-update-management-groups.md).
- **Computer da aggiornare**: selezionare una ricerca salvata, un gruppo importato oppure selezionare Computer, per scegliere i computer che si vuole aggiornare.

   >[!NOTE]
   >Se si seleziona l'opzione Ricerca salvata, non vengono restituite le identità del computer, bensì solo i nomi. Se sono presenti più macchine virtuali con lo stesso nome in più gruppi di risorse, vengono restituite nei risultati. È consigliabile usare l'opzione **Gruppi da aggiornare** per assicurarsi di includere macchine virtuali univoche corrispondenti ai criteri.

   Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **Aggiorna idoneità agente**. È possibile visualizzare lo stato di integrità del computer prima di pianificare la distribuzione degli aggiornamenti. Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../azure-monitor/platform/computer-groups.md)

  ![Riquadro Nuova distribuzione di aggiornamenti](./media/manage-update-multi/update-select-computers.png)

- **Classificazione aggiornamenti**: selezionare i tipi di software da includere nella distribuzione di aggiornamenti. Per una descrizione dei tipi di classificazione, vedere le [classificazioni degli aggiornamenti](automation-view-update-assessments.md#work-with-update-classifications). I tipi di classificazione sono:
  - Aggiornamenti critici
  - Aggiornamenti per la sicurezza
  - Aggiornamenti cumulativi
  - Feature Pack
  - Service Pack
  - Aggiornamenti della definizione
  - Strumenti
  - Aggiornamenti

- **Includi/Escludi aggiornamenti**: apre la pagina Includi/Escludi. Gli aggiornamenti da includere o escludere si trovano in schede separate. Per altre informazioni su come viene gestita l'inclusione, vedere [Pianificare la distribuzione degli aggiornamenti](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Tenere presente che le esclusioni eseguono l'override delle inclusioni. Se ad esempio si definisce una regola di esclusione `*`, non vengono installate patch o pacchetti perché vengono tutti esclusi. Le patch escluse vengono ancora visualizzate come mancanti dal computer. Per i computer Linux, se un pacchetto è incluso, ma ha un pacchetto dipendente che è stato escluso, il pacchetto non viene installato.

> [!NOTE]
> Non è possibile specificare aggiornamenti che sono stati sostituiti per l'inclusione nella distribuzione degli aggiornamenti.

- **Impostazioni di pianificazione**: è possibile accettare la data e l'ora predefinite, ossia 30 minuti dopo l'ora corrente, ma anche specificare un momento diverso.

   Si può anche specificare se la distribuzione deve essere eseguita una sola volta o in base a una pianificazione ricorrente. Per configurare una pianificazione ricorrente, selezionare **Ricorrente** in **Ricorrenza**.

   ![Finestra di dialogo Impostazioni pianificazione](./media/manage-update-multi/update-set-schedule.png)

- **Pre-script e post-script**: selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire i pre-script e i post-script](pre-post-scripts.md).
- **Finestra di manutenzione (minuti)** : specificare il periodo di tempo nel quale eseguire la distribuzione di aggiornamenti. Questa impostazione consente di garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

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

- `Not attempted`: l'aggiornamento non è stato installato perché il tempo disponibile in base alla finestra di manutenzione specificata non è stato sufficiente.
- `Succeeded`: aggiornamento completato.
- `Failed`: aggiornamento non riuscito.

Per visualizzare tutte le voci di log create dalla distribuzione, selezionare **Tutti i log**.

Per visualizzare il flusso del processo del runbook che gestisce la distribuzione di aggiornamenti nella macchina virtuale di destinazione, selezionare il riquadro dell'output.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="next-steps"></a>Passaggi successivi

* Se è necessario cercare i log di aggiornamento, vedere [Eseguire query sui log di Gestione aggiornamenti](automation-update-management-query-logs.md).
