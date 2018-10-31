---
title: Gestire gli aggiornamenti per più macchine virtuali Azure
description: Questo articolo descrive come gestire gli aggiornamenti per le macchine virtuali di Azure.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e51c0178691279d803e9a1afcd4147f1d6ed767e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985251"
---
# <a name="manage-updates-for-multiple-machines"></a>Gestire gli aggiornamenti per più macchine virtuali

È possibile usare la soluzione Gestione aggiornamenti per gestire gli aggiornamenti e le patch per le macchine virtuali Windows e Linux. Dall'account di [Automazione di Azure](automation-offering-get-started.md) è possibile:

- Eseguire l'onboarding delle macchine virtuali
- Valutare lo stato degli aggiornamenti disponibili
- Pianificare l'installazione degli aggiornamenti necessari
- Esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente a tutte le macchine virtuali per cui è abilitata la soluzione Gestione aggiornamenti

## <a name="prerequisites"></a>Prerequisiti

Per usare Gestione aggiornamenti è necessario:

- Creare un account RunAs di Automazione di Azure. Per informazioni su come crearne uno, vedere [Introduzione ad Automazione di Azure](automation-offering-get-started.md).
- Una macchina virtuale o un computer con uno dei sistemi operativi supportati installato.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Gestione aggiornamenti è supportata nei sistemi operativi seguenti:

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Supporta solo le valutazioni degli aggiornamenti.         |
|Windows Server 2008 R2 SP1 e versioni successive     |È necessario Windows PowerShell 4.0 o versioni successive. ([Scaricare WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 è consigliato per la sua maggiore affidabilità. ([Scaricare WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 12.04 LTS, 14.04 LTS e 16.04 LTS (x86/x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

> [!NOTE]
> Per evitare che gli aggiornamenti vengano applicati al di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto Unattended-Upgrade per disabilitare gli aggiornamenti automatici. Per altre informazioni, vedere l'[argomento Aggiornamenti automatici nella Guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.

Questa soluzione non supporta un agente di Log Analytics per Linux configurato per l'invio di report a più aree di lavoro di Log Analytics di Azure.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Abilitare Gestione aggiornamenti per le macchine virtuali di Azure

Nel portale di Azure aprire l'account di Automazione e selezionare **Gestione aggiornamenti**.

Selezionare **Aggiungi macchina virtuale di Azure**.

![Scheda per l'aggiunta di una VM di Azure](./media/manage-update-multi/update-onboard-vm.png)

Selezionare una macchina virtuale da caricare. 

In **Abilita Gestione aggiornamenti** selezionare **Abilita** per eseguire l'onboarding della macchina virtuale.

![Finestra di dialogo Abilita Gestione aggiornamenti](./media/manage-update-multi/update-enable.png)

Al termine dell'onboarding, Gestione aggiornamenti è abilitata per la macchina virtuale.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Abilitare Gestione aggiornamenti per computer e macchine virtuali non di Azure

Per informazioni su come abilitare Gestione aggiornamenti per computer e macchine virtuali Windows non di Azure, vedere [Connettere computer Windows al servizio Log Analytics in Azure](../log-analytics/log-analytics-windows-agent.md).

Per informazioni su come abilitare Gestione aggiornamenti per computer e macchine virtuali Linux non di Azure, vedere [Connettere computer Linux a Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Visualizzare i computer associati all'account di Automazione

Dopo avere abilitato Gestione aggiornamenti per i computer, è possibile visualizzarne le informazioni facendo clic su **Computer**. È possibile visualizzare informazioni su *nome del computer*, *stato di conformità*, *ambiente*, *tipo di sistema operativo*, *aggiornamenti critici e della sicurezza installati*, *altri aggiornamenti installati* e *idoneità dell'agente di aggiornamento* per i computer in uso.

  ![Visualizzare la scheda Computer](./media/manage-update-multi/update-computers-tab.png)

È possibile che i computer abilitati di recente per Gestione aggiornamenti non siano stati ancora valutati. Lo stato di conformità per tali computer è **Valutazione non eseguita**. Di seguito è riportato un elenco di valori possibili per lo stato di conformità:

- **Conforme**: computer senza aggiornamenti critici o della sicurezza mancanti.

- **Non conforme**: computer in cui non è presente almeno un aggiornamento critico o della sicurezza.

- **Valutazione non eseguita**: i dati relativi alla valutazione dell'aggiornamento non sono stati ricevuti dal computer entro l'intervallo di tempo previsto. Per i computer Linux, l'intervallo di tempo previsto rientra nelle ultime 3 ore. Per i computer Windows, l'intervallo di tempo previsto rientra nelle ultime 12 ore.

Per visualizzare lo stato dell'agente, selezionare il collegamento nella colonna **UPDATE AGENT READINESS** (Idoneità agente di aggiornamento). Se si seleziona questa opzione, viene visualizzata la pagina **Ruolo di lavoro ibrido** in cui è visualizzato lo stato del ruolo di lavoro ibrido. L'immagine seguente illustra un esempio di un agente che non è stato connesso a Gestione aggiornamenti per un lungo periodo di tempo:

![Visualizzare la scheda Computer](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Visualizzare una valutazione degli aggiornamenti

Dopo aver abilitato Gestione aggiornamenti, verrà visualizzata la schermata **Gestione aggiornamenti**. È possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

## <a name="collect-data"></a>Raccogliere i dati

Gli agenti installati in macchine virtuali e computer raccolgono i dati sugli aggiornamenti. e li inviano a Gestione aggiornamenti di Azure.

### <a name="supported-agents"></a>Agenti supportati

La tabella seguente descrive le origini connesse supportate da questa soluzione:

| Origine connessa | Supportato | DESCRIZIONE |
| --- | --- | --- |
| Agenti di Windows |Yes |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e quindi avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Yes |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e quindi avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Yes |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso. |
| Account di archiviazione di Azure |No  |Archiviazione di Azure non include informazioni sugli aggiornamenti del sistema. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Per ogni computer Windows gestito viene eseguita un'analisi due volte al giorno. Ogni 15 minuti viene chiamata l'API Windows per eseguire una query su data/ora dell'ultimo aggiornamento e determinare se lo stato è cambiato. Se lo stato è cambiato, viene avviata un'analisi della conformità. Per ogni computer Linux gestito viene eseguita un'analisi ogni 3 ore.

La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Per pianificare una nuova distribuzione di aggiornamenti per una o più macchine virtuali, selezionare **Pianifica la distribuzione di aggiornamenti** in **Gestione aggiornamenti**.

Nel riquadro **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

- **Nome**: immettere un nome univoco per identificare la distribuzione di aggiornamenti.
- **Sistema operativo**: selezionare **Windows** o **Linux**.
- **Gruppi da aggiornare (anteprima)**: definire una query basata su una combinazione di sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per altre informazioni, vedere [Gruppi dinamici](automation-update-management.md#using-dynamic-groups)
- **Computer da aggiornare**: selezionare una ricerca salvata, gruppo importato, o selezionare le macchine virtuali, per scegliere i computer che si desidera aggiornare. Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **AGGIORNA IDONEITÀ AGENTE**. È possibile visualizzare lo stato di integrità del computer prima di pianificare la distribuzione degli aggiornamenti. Per altre informazioni sui diversi metodi di creazione di gruppi di computer in Log Analytics, vedere [gruppi di Computer in Log Analytics](../log-analytics/log-analytics-computer-groups.md)

  ![Riquadro Nuova distribuzione di aggiornamenti](./media/manage-update-multi/update-select-computers.png)

- **Classificazioni aggiornamenti**: selezionare i tipi di software da includere nella distribuzione di aggiornamenti. Per una descrizione dei tipi di classificazione, vedere le [classificazioni degli aggiornamenti](automation-update-management.md#update-classifications). I tipi di classificazione sono:
  - Aggiornamenti critici
  - Aggiornamenti della sicurezza
  - Aggiornamenti cumulativi
  - Feature Pack
  - Service Pack
  - Aggiornamenti della definizione
  - Strumenti
  - Aggiornamenti

- **Includi/Escludi aggiornamenti**: apre la pagina **Includi/Escludi**. Gli aggiornamenti da includere o escludere si trovano in schede separate. Per altre informazioni sulla modalità di gestione dell'inclusione, vedere il [comportamento dell'inclusione](automation-update-management.md#inclusion-behavior)

- **Impostazioni pianificazione**: è possibile accettare la data e l'ora predefinite, ossia 30 minuti dopo l'ora corrente, ma anche specificare un momento diverso.

   Si può anche specificare se la distribuzione deve essere eseguita una sola volta o in base a una pianificazione ricorrente. Per configurare una pianificazione ricorrente, selezionare **Ricorrente** in **Ricorrenza**.

   ![Finestra di dialogo Impostazioni pianificazione](./media/manage-update-multi/update-set-schedule.png)

- **Pre-script e post-script**: selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire i pre-script e i post-script](pre-post-scripts.md).
- **Finestra di manutenzione (minuti)**: specificare il periodo di tempo in cui deve avvenire la distribuzione di aggiornamenti. Questa impostazione consente di garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

- **Controllo riavvio:** questa impostazione determina come vengono gestiti i riavvii per la distribuzione degli aggiornamenti.

   |Opzione|DESCRIZIONE|
   |---|---|
   |Riavvia se necessario| **(Impostazione predefinita)** Se necessario, viene eseguito il riavvio se la finestra di manutenzione lo consente.|
   |Riavvia sempre|Il computer viene riavviato indipendentemente dal fatto che il riavvio sia richiesto o no. |
   |Non riavviare mai|I riavvi non sono eseguiti, indipendentemente dal fatto che il riavvio sia necessario.|
   |Riavvia solamente: gli aggiornamenti non verranno installati|Questa opzione ignora l'installazione degli aggiornamenti e esegue solo il riavvio.|

Dopo avere configurato la pianificazione, selezionare il pulsante **Crea** per tornare al dashboard di stato. Nella tabella **Pianificata** appare la pianificazione della distribuzione appena creata.

> [!NOTE]
> Gestione aggiornamenti supporta la distribuzione degli aggiornamenti proprietari e il pre-download di patch. Ciò richiede modifiche dei sistemi sui quali sono applicate gli aggiornamenti. Vedere la sezione sul [supporto del pre-download di aggiornamenti proprietari](automation-update-management.md#firstparty-predownload) per vedere come configurare queste impostazioni nei propri sistemi.

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** in **Gestione aggiornamenti**.

Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione lo stato viene modificato in **Completata**.

Se la distribuzione di uno o più aggiornamenti ha esito negativo, lo stato sarà **Partially failed** (Operazione riuscita parzialmente).

![Stato della distribuzione di aggiornamenti](./media/manage-update-multi/update-view-results.png)

Per visualizzare il dashboard per una distribuzione di aggiornamenti, selezionare la distribuzione completata.

Il riquadro **Risultati aggiornamento** indica il numero totale di aggiornamenti e i risultati della distribuzione per la macchina virtuale. La tabella a destra offre una suddivisione dettagliata di ogni aggiornamento e dei risultati dell'installazione, che possono corrispondere a uno dei valori seguenti.

- **Tentativo non eseguito**: l'aggiornamento non è stato installato perché il tempo disponibile in base alla finestra di manutenzione specificata non è stato sufficiente.
- **Completato**: l'aggiornamento è stato completato.
- **Non riuscito**: l'aggiornamento non è riuscito.

Per visualizzare tutte le voci di log create dalla distribuzione, selezionare **Tutti i log**.

Per visualizzare il flusso del processo del runbook che gestisce la distribuzione di aggiornamenti nella macchina virtuale di destinazione, selezionare il riquadro dell'output.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Gestione aggiornamenti, ad esempio su log, output ed errori, vedere [Soluzione Gestione aggiornamenti in Azure](../operations-management-suite/oms-solution-update-management.md).
