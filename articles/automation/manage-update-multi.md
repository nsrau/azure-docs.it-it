---
title: "Gestire gli aggiornamenti per più macchine virtuali Azure | Microsoft Docs"
description: Questo argomento descrive come gestire gli aggiornamenti per le macchine virtuali di Azure.
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: 80a6caff51631637825d560d270198be0336e806
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Gestire gli aggiornamenti per più macchine virtuali Azure

Gestione aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Azure.
Dall'account di [Automazione di Azure](automation-offering-get-started.md) è possibile, in modo rapido, caricare le macchine virtuali, valutare lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente a tutte le macchine virtuali per cui è abilitata la gestione degli aggiornamenti.

## <a name="prerequisites"></a>Prerequisiti

Per usare Gestione aggiornamenti, è necessario:

* Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Introduzione ad Automazione di Azure](automation-offering-get-started.md).

* Una macchina virtuale o un computer con uno dei sistemi operativi supportati installato.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

La gestione degli aggiornamenti è supportato nei sistemi operativi seguenti.

### <a name="windows"></a>Windows

* Windows Server 2008 e versioni successive e distribuzioni di aggiornamenti in Windows Server 2008 R2 SP1 e versioni successive.  Le opzioni di installazione Server Core e Nano Server non sono supportate.

    > [!NOTE]
    > Il supporto per la distribuzione degli aggiornamenti in Windows Server 2008 R2 SP1 richiede .NET Framework 4.5 e WMF 5.0 o versioni successive.
    > 
* I sistemi operativi client di Windows non sono supportati.

Gli agenti Windows devono essere configurati per comunicare con un server Windows Server Update Services (WSUS) o avere accesso a Microsoft Update.

> [!NOTE]
> L'agente Windows non può essere gestito contemporaneamente da System Center Configuration Manager.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) e 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) e 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)  
* Ubuntu 12.04 LTS e versioni x86/x64 più recenti   

> [!NOTE]  
> Per evitare che gli aggiornamenti vengano applicati di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto Unattended-Upgrade per disabilitare gli aggiornamenti automatici. Per informazioni sulla configurazione, vedere l'[argomento Aggiornamenti automatici nella Guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.

> [!NOTE]
> Un agente OMS per Linux configurato per l'invio di report a più aree di lavoro OMS non è supportato con questa soluzione.  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>Abilitare la gestione degli aggiornamenti per le macchine virtuali Azure

1. Nel Portale di Azure aprire l'account di Automazione.
2. Sul lato sinistro della schermata selezionare **Gestione aggiornamenti**.
3. Nella parte superiore della schermata fare clic su **Aggiungi macchina virtuale di Azure**.
    ![Caricare le macchine virtuali](./media/manage-update-multi/update-onboard-vm.png)
4. Selezionare una macchina virtuale da caricare. Viene visualizzata la schermata **Abilita Gestione aggiornamenti**.
5. Fare clic su **Abilita**.

   ![Abilitare la gestione degli aggiornamenti](./media/manage-update-multi/update-enable.png)

La gestione degli aggiornamenti è abilitata per la macchina virtuale.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Abilitare la gestione degli aggiornamenti per computer e macchine virtuali non di Azure

Per istruzioni su come abilitare la gestione degli aggiornamenti per computer e macchine virtuali Windows non di Azure, vedere [Connettere computer Windows al servizio Log Analytics in Azure](../log-analytics/log-analytics-windows-agents.md).

Per istruzioni su come abilitare la gestione degli aggiornamenti per computer e macchine virtuali Linux non di Azure, vedere [Connettere i computer Linux a Operations Management Suite (OMS)](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato **Gestione aggiornamenti**, viene visualizzata la schermata **Gestione aggiornamenti**. È possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

## <a name="data-collection"></a>Raccolta dei dati

Gli agenti installati in macchine virtuali e computer raccolgono i dati sugli aggiornamenti e li inviano a Gestione aggiornamenti di Azure.

### <a name="supported-agents"></a>Agenti supportati

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso. |
| Account di archiviazione di Azure |No |Archiviazione di Azure non include informazioni sugli aggiornamenti del sistema. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Per ogni computer Windows gestito viene eseguita un'analisi due volte al giorno. Ogni 15 minuti, l'API Windows viene chiamata per eseguire una query per la data/ora dell'ultimo aggiornamento e determinare se lo stato è stato modificato. In caso affermativo viene avviata un'analisi di conformità.  Per ogni computer Linux gestito viene eseguita un'analisi ogni 3 ore.

La visualizzazione dei dati aggiornati dei computer gestiti nel dashboard può richiedere da 30 minuti a 6 ore.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio.
È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Pianificare una nuova distribuzione di aggiornamenti per una o più macchine virtuali facendo clic su **Pianifica la distribuzione di aggiornamenti** nella parte superiore della schermata **Gestione aggiornamenti**. Nella schermata **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

* **Nome**: specificare un nome univoco per identificare la distribuzione di aggiornamenti.
* **Tipo di sistema operativo**: selezionare Windows o Linux.
* **Computer da aggiornare**: selezionare le macchine virtuali da aggiornare.

  ![Selezionare le macchine virtuali da aggiornare](./media/manage-update-multi/update-select-computers.png)

* **Classificazioni aggiornamenti**: selezionare i tipi di software inclusi nella distribuzione di aggiornamenti. I tipi di classificazione sono:
  * Aggiornamenti critici
  * Aggiornamenti della sicurezza
  * Aggiornamenti cumulativi
  * Feature Pack
  * Service Pack
  * Aggiornamenti della definizione
  * Strumenti
  * Aggiornamenti
* **Impostazioni di pianificazione**: è possibile accettare la data e l'ora predefinite, ovvero 30 minuti dopo l'ora corrente o specificare un'ora diversa.
   È anche possibile specificare se eseguire la distribuzione una sola volta o impostare una pianificazione ricorrente. Per impostare una pianificazione ricorrente, fare clic sull'opzione Ricorrente in Ricorrenza.

   ![Schermata di impostazioni della pianificazione di aggiornamenti](./media/manage-update-multi/update-set-schedule.png)

* **Finestra di manutenzione (minuti)**: specificare il periodo di tempo nel quale eseguire la distribuzione di aggiornamenti.  In questo modo è possibile garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

Dopo avere configurato la pianificazione, fare clic sul pulsante **Crea**. Viene nuovamente visualizzato il dashboard di stato.
Si noti che la tabella **Pianificata** mostra la pianificazione della distribuzione appena creata.

> [!WARNING]
> Per gli aggiornamenti che richiedono un riavvio, la macchina virtuale verrà riavviata automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** nella schermata **Gestione aggiornamenti**.
Se la distribuzione è in corso, viene visualizzato lo stato **In corso**. Quando la distribuzione viene completata correttamente, lo stato diventa **Completato**.
Se si verifica un errore in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.

![Stato della distribuzione degli aggiornamenti ](./media/manage-update-multi/update-view-results.png)

Fare clic sulla distribuzione di aggiornamenti completata per visualizzare il dashboard della distribuzione.

Il riquadro **Risultati aggiornamento** include un riepilogo del numero totale di aggiornamenti e dei risultati della distribuzione nella macchina virtuale.
La tabella di destra visualizza una descrizione dettagliata di ogni aggiornamento e i risultati dell'installazione che possono corrispondere a uno dei valori seguenti:

* Tentativo non eseguito: l'aggiornamento non è stato installato a causa di tempo disponibile non sufficiente basato sulla durata della finestra di manutenzione specificata.
* Completato: l'aggiornamento è stato completato
* Non riuscito: l'aggiornamento non è riuscito

Fare clic su **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Fare clic sul riquadro **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Fare clic su **Errori** per visualizzare informazioni dettagliate sugli errori della distribuzione.

Per informazioni dettagliate sui log, sull'output e sugli errori, vedere [Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla gestione degli aggiornamenti, vedere [Gestione degli aggiornamenti](../operations-management-suite/oms-solution-update-management.md).