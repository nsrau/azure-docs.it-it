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
ms.openlocfilehash: c073cb28f5c37a402c04d5315d7f8e18de8a1b26
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="manage-updates-for-multiple-machines"></a>Gestire gli aggiornamenti per più macchine virtuali

È possibile usare Gestione aggiornamenti per gestire gli aggiornamenti e le patch per le macchine virtuali Windows e Linux. Dall'account di [Automazione di Azure](automation-offering-get-started.md) è possibile:

- Eseguire l'onboarding di macchine virtuali.
- Valutare lo stato degli aggiornamenti disponibili.
- Pianificare l'installazione degli aggiornamenti necessari.
- Esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente a tutte le macchine virtuali per cui è abilitata la soluzione Gestione aggiornamenti.

## <a name="prerequisites"></a>Prerequisiti

Per usare Gestione aggiornamenti è necessario:

* Creare un account RunAs di Automazione di Azure. Per istruzioni su come crearne uno, vedere [Introduzione ad Automazione di Azure](automation-offering-get-started.md).

* Una macchina virtuale o un computer con uno dei sistemi operativi supportati installato.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

La gestione degli aggiornamenti è supportato nei sistemi operativi seguenti.

### <a name="windows"></a>Windows

* Windows Server 2008 e versioni successive e distribuzioni di aggiornamenti in Windows Server 2008 R2 SP1 e versioni successive. Le opzioni di installazione Server Core e Nano Server non sono supportate.

  Il supporto per la distribuzione di aggiornamenti in Windows Server 2008 R2 SP1 richiede .NET Framework 4.5 e Windows Management Framework 5.0 o versioni successive.

* I sistemi operativi client di Windows non sono supportati.

Gli agenti Windows devono essere configurati per comunicare con un server Windows Server Update Services (WSUS) o avere accesso a Microsoft Update.

> [!NOTE]
> L'agente Windows non può essere gestito contemporaneamente da System Center Configuration Manager.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) e 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) e 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)  
* Ubuntu 12.04 LTS e versioni più recenti (x86/x64)   

> [!NOTE]  
> Per evitare che gli aggiornamenti vengano applicati al di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto Unattended-Upgrade per disabilitare gli aggiornamenti automatici. Per altre informazioni, vedere l'[argomento Aggiornamenti automatici nella Guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.

Questa soluzione non supporta un agente di OMS per Linux configurato per l'invio di report a più aree di lavoro di Operations Management Suite.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Abilitare la gestione degli aggiornamenti per le macchine virtuali Azure

1. Nel Portale di Azure aprire l'account di Automazione.
2. Nel riquadro sinistro selezionare **Gestione aggiornamenti**.
3. Nella parte superiore della finestra selezionare **Aggiungi macchina virtuale di Azure**.
   ![Scheda per l'aggiunta di una VM di Azure](./media/manage-update-multi/update-onboard-vm.png)
4. Selezionare una macchina virtuale da caricare. Verrà visualizzata la finestra di dialogo **Abilita Gestione aggiornamenti**.
5. Selezionare **Abilita**.

   ![Finestra di dialogo Abilita Gestione aggiornamenti](./media/manage-update-multi/update-enable.png)

La gestione degli aggiornamenti è abilitata per la macchina virtuale.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Abilitare la gestione degli aggiornamenti per computer e macchine virtuali non di Azure

Per istruzioni su come abilitare la gestione degli aggiornamenti per computer e macchine virtuali Windows non di Azure, vedere [Connettere computer Windows al servizio Log Analytics in Azure](../log-analytics/log-analytics-windows-agents.md).

Per istruzioni su come abilitare Gestione aggiornamenti per computer e macchine virtuali Linux non di Azure, vedere [Connettere computer Linux a Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Visualizzare i computer associati all'account di Automazione
Dopo avere abilitato la gestione degli aggiornamenti per i computer, è possibile visualizzare le rispettive informazioni facendo clic su **Computer**. Sono disponibili le informazioni relative ai computer, ad esempio *Nome*, *Conformità*, *Ambiente*, *Tipo di sistema operativo*, *Aggiornamenti critici o della sicurezza* e *Altri aggiornamenti*. 

  ![Visualizzare la scheda Computer](./media/manage-update-multi/update-computers-tab.png)

Per i computer abilitati di recente alla gestione degli aggiornamenti, è possibile che non siano ancora disponibili valutazioni. Lo stato relativo alla conformità per tali computer sarà *Valutazione non eseguita*.  Ecco un elenco di valori per lo stato relativo alla conformità:
* Conforme: computer senza aggiornamenti critici o della sicurezza mancanti.
* Non conforme: computer in cui non è presente almeno un aggiornamento critico o della sicurezza.
* Valutazione non eseguita: i dati relativi alla valutazione dell'aggiornamento non sono stati ricevuti dal computer entro l'intervallo di tempo previsto.  Per i computer Linux si tratta delle ultime tre ore e per i computer Windows delle ultime 12 ore.  

## <a name="view-an-update-assessment"></a>Visualizzare una valutazione degli aggiornamenti

Dopo l'abilitazione di Gestione aggiornamenti, verrà visualizzata la finestra di dialogo **Gestione aggiornamenti**. È possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

## <a name="collect-data"></a>Raccogliere i dati

Gli agenti installati in macchine virtuali e computer raccolgono i dati sugli aggiornamenti e li inviano a Gestione aggiornamenti di Azure.

### <a name="supported-agents"></a>Agenti supportati

La tabella seguente descrive le origini connesse supportate da questa soluzione:

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso. |
| Account di archiviazione di Azure |No |Archiviazione di Azure non include informazioni sugli aggiornamenti del sistema. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Per ogni computer Windows gestito viene eseguita un'analisi due volte al giorno. Ogni 15 minuti viene chiamata l'API Windows per eseguire una query su data/ora dell'ultimo aggiornamento e determinare se lo stato è stato modificato. In caso affermativo viene avviata un'analisi di conformità. Per ogni computer Linux gestito viene eseguita un'analisi ogni 3 ore.

La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio.
È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Pianificare una nuova distribuzione di aggiornamenti per una o più macchine virtuali selezionando **Pianifica la distribuzione di aggiornamenti** nella parte superiore della finestra di dialogo **Gestione aggiornamenti**. Nel riquadro **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti.

* **Nome**: specificare un nome univoco per identificare la distribuzione di aggiornamenti.
* **Tipo di sistema operativo**: selezionare Windows o Linux.
* **Computer da aggiornare**: selezionare le macchine virtuali da aggiornare.

  ![Riquadro Nuova distribuzione di aggiornamenti](./media/manage-update-multi/update-select-computers.png)

* **Classificazioni aggiornamenti**: selezionare i tipi di software inclusi nella distribuzione di aggiornamenti. I tipi di classificazione sono:
  * Aggiornamenti critici
  * Aggiornamenti della sicurezza
  * Aggiornamenti cumulativi
  * Feature Pack
  * Service Pack
  * Aggiornamenti della definizione
  * Strumenti
  * Aggiornamenti
* **Impostazioni pianificazione**: è possibile accettare la data e l'ora predefinite, ossia 30 minuti dopo l'ora corrente, oppure specificare un momento diverso.
   Si può anche specificare se la distribuzione deve essere eseguita una sola volta o in base a una pianificazione ricorrente. Per configurare una pianificazione ricorrente, selezionare l'opzione **Ricorrente** sotto **Ricorrenza**.

   ![Finestra di dialogo Impostazioni pianificazione](./media/manage-update-multi/update-set-schedule.png)

* **Finestra di manutenzione (minuti)**: specificare il periodo di tempo in cui si vuole eseguire la distribuzione di aggiornamenti. Questa impostazione consente di garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

Dopo aver configurato la pianificazione, tornare al dashboard di stato selezionando il pulsante **Crea**. La tabella **Pianificata** mostra la pianificazione della distribuzione appena creata.

> [!WARNING]
> Per gli aggiornamenti che richiedono un riavvio, la macchina virtuale verrà riavviata automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo l'avvio della distribuzione pianificata, è possibile visualizzarne lo stato nella scheda **Distribuzioni di aggiornamenti** della finestra di dialogo **Gestione aggiornamenti**.
Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Dopo che è stata completata, lo stato verrà modificato in **Riuscito**.
Se la distribuzione di uno o più aggiornamenti ha esito negativo, lo stato sarà **Partially failed** (Operazione riuscita parzialmente).

![Stato della distribuzione di aggiornamenti](./media/manage-update-multi/update-view-results.png)

Per visualizzare il dashboard per una distribuzione di aggiornamenti, selezionare la distribuzione completata.

Il riquadro **Risultati aggiornamento** mostra il numero totale di aggiornamenti e i risultati della distribuzione nella macchina virtuale.
La tabella a destra offre una suddivisione dettagliata di ogni aggiornamento e dei risultati dell'installazione, che possono corrispondere a uno dei valori seguenti.

* Tentativo non eseguito: l'aggiornamento non è stato installato perché il tempo disponibile in base alla finestra di manutenzione specificata non è stato sufficiente.
* Riuscito: l'aggiornamento è stato completato.
* Non riuscito: l'aggiornamento non è riuscito.

Per visualizzare tutte le voci di log create dalla distribuzione, selezionare **Tutti i log**.

Per visualizzare il flusso del processo del runbook che gestisce la distribuzione di aggiornamenti nella macchina virtuale di destinazione, selezionare il riquadro **Output**.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Gestione aggiornamenti, ad esempio su log, output ed errori, vedere [Soluzione Gestione aggiornamenti in OMS](../operations-management-suite/oms-solution-update-management.md).

